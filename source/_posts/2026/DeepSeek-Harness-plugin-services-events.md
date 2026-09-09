---
layout: post
title: DeepSeek Harness 插件机制深层：服务注入、事件与 tools 流水线
author: webkong
categories:
  - AI
tags:
  - AI
  - DeepSeek
  - Agent
  - dsh
  - Cordis
date: 2026-09-07 12:00:00
---

> 上一篇动手写出了第一个 dsh 工具。这篇往回退一步，讲透插件之间的「对话方式」：什么是服务、`inject` 到底如何决定启动顺序、事件系统为什么能解耦插件，以及 `tools` 执行时那一层层流水线（pre-execute / guards / execute / post-execute / result）各自干什么。

<!-- more -->

[上一篇](https://webkong.cn/deepseek-harness-plugin-hello/)我们写出了 `greet` 工具，但它是「照葫芦画瓢」。为什么插件要 `inject: ['tools']`？为什么 `ctx.tools.register(...)` 卸载时会自动注销？为什么观察者插件能靠一个 `tools/result` 事件跟工具插件勾搭上？

答案都藏在 dsh 底层的插件系统 **Cordis** 里。这篇把它讲透。

# 插件、Service 与事件

dsh 里有两套插件协作的机制，对应两句话：

- **服务（Service）** 让一个插件把「能力」提供给别的插件用，是**直接调用**。
- **事件（Event）** 让一个插件发出通知，但**无需知道**谁在监听，是**广播**。

`ctx.tools`、`ctx.llm`、`ctx.agents`，都是服务。消费方只写 `'tools'` 这个名字，而不导入其提供方——所以配置可以在不改消费方的前提下换掉提供方。

服务的「提供」和「消费」各有一步。先看提供方（一个 `GreeterService`）：

```ts
import { Service, type Context } from '@deepseek-ai/cordis'

declare module '@deepseek-ai/cordis' {
  interface Context {
    greeter: GreeterService
  }
}

export class GreeterService extends Service {
  constructor(ctx: Context) {
    super(ctx, 'greeter')
  }

  greet(who: string) {
    return `Hello, ${who}!`
  }
}

export const name = 'greeter'

export function apply(ctx: Context) {
  ctx.plugin(GreeterService)
}
```

这代码有两层意思：

- **运行时**：`super(ctx, 'greeter')` 把该实例以 `greeter` 这个名称注册。此后任何插件都能通过 `ctx.greeter` 访问它。
- **编译时**：`declare module '@deepseek-ai/cordis'` 用 TypeScript 声明合并，把 `greeter` 加进 `Context` 接口，让 `ctx.greeter` 处处能通过类型检查。它不生成任何代码；没有它服务照常能用，只是消费方失去类型安全。

注意：**`Service` 子类本身就是插件**（也就是插件三种形态里的「类形态」），所以 `ctx.plugin(GreeterService)` 和挂载普通插件一样。

再看消费方：

```ts
export const name = 'consumer'
export const inject = ['greeter']

export function apply(ctx: Context) {
  console.log(ctx.greeter.greet('world'))
}
```

`inject` 列出这个插件需要的服务。Cordis 会让插件保持 **`PENDING`** 状态，直到列出的每一项服务都存在，所以在 `apply` 里 `ctx.greeter` 必然就绪。

这引出一个反直觉但极重要的结论：**`cordis.yml` 里的加载顺序无关紧要。决定插件何时启动的是依赖关系，不是文件顺序。**

> 把 `cordis.yml` 里两行换个顺序，输出还是 `Hello, world!`。真要删掉 `./greeter.ts`，消费方会默默保持 PENDING，不输出、不崩溃，进程静默以状态码 0 退出。这就是为什么「我的插件怎么没输出」的答案，往往是：它依赖的服务还没到场。

# 为什么「可逆副作用」如此重要

上一章说 `ctx.tools.register(...)` 卸载时会自动注销工具。秘密在于 **effect（副作用）**。

Cordis 插件会因为四种原因被卸载：修改配置、热重载、显式 dispose、所依赖的服务消失。通过 Cordis API 建立的注册都属于 **effect**，插件卸载时自动撤销。所以：

- `ctx.on(event, listener)` —— 监听器随插件一起移除；
- `ctx.plugin(child)` —— 子插件随父插件一起 dispose；
- 服务注册、`ctx.tools.register(...)` —— 同样自动撤销。

对 Cordis 没管理的资源（定时器、连接、watcher），你要手动包在 `ctx.effect()` 里并返回 disposer：

```ts
ctx.effect(() => {
  const timer = setInterval(() => console.log('tick'), 200)
  return () => {
    clearInterval(timer)
    console.log('heartbeat cleaned up')
  }
})
```

effect 主体在加载时运行，它返回的 disposer 在卸载时运行。与你插件生命周期一致的资源，你永远不需要自己调 disposer。

每个加载的插件实例都有一个 **fiber（运行时句柄）**，状态转化如下：

```
PENDING → LOADING → ACTIVE → UNLOADING → DISPOSED
                 ↘ FAILED
```

- `PENDING`：已声明，但所需服务未就绪。
- `FAILED`：`apply` 或配置校验抛了异常。
- `UNLOADING` / `DISPOSED`：disposer 运行中 / 已拆除。

`fiber.dispose()` 会等该插件所有异步清理完成，并递归卸载它挂载的所有子插件。

> dsh 这套「一切回归可撤销」的哲学非常硬核：运行时不存在需要打补丁的特权内核，每一项能力注册都是可逆副作用。这意味着你可以热替换任何插件——卸载旧的 `shell` 提供方，挂上新的，所有注入 `'shell'` 的插件都会自动重启用新实现。

# 事件：五种分发模式

服务适合直接调用，事件适合解耦。dsh 的事件系统尤有意思——它不只有一种「广播」。

先看一个典型事件：一个服务发出通知，另一个插件监听。

```ts
declare module '@deepseek-ai/cordis' {
  interface Context {
    stats: StatsService
  }
  interface Events {
    'stats/report'(name: string, count: number): void
  }
}

export class StatsService extends Service {
  private counts = new Map<string, number>()

  constructor(ctx: Context) {
    super(ctx, 'stats')
  }

  bump(name: string) {
    const next = (this.counts.get(name) ?? 0) + 1
    this.counts.set(name, next)
    this.ctx.emit('stats/report', name, next)
  }
}
```

`interface Context` 和 `interface Events` 两个声明合并是配套的：前者声明服务，后者声明事件名及监听器签名，让 `ctx.emit` 和 `ctx.on` 都有完整类型。事件名用 `namespace/action` 约定，比如 `tools/result`、`session/event`。

`ctx.on()` 也是 effect，监听器随插件消失，所以**绝不需要手动 removeListener**。

`emit` 只是五种分发模式之一。事件用哪种模式，是它约定的一部分：

| 模式 | 调用 | 语义 |
|---|---|---|
| `emit` | `ctx.emit(...)` | 同步广播，不等待、不收集返回值 |
| `parallel` | `await ctx.parallel(...)` | 所有监听器并发运行并一起等待 |
| `serial` | `await ctx.serial(...)` | 按序运行，第一个非 `null`/`false`/`undefined` 返回值胜出并截断 |
| `bail` | `ctx.bail(...)` | `serial` 的同步版本 |
| `waterfall` | `ctx.waterfall(..., next)` | 环绕中间件，可转换或短路 |

每个 harness 事件都在其子系统页面的参考文档里标注了各自模式。`tools/result` 就是同步 `emit`，所以观察者能拿到不可变的最终结果。

# waterfall：dsh 拦截的真相

上面五种模式里，`waterfall` 是理解 dsh「策略与钩子」的关键。它是实现「拦截」的模式：每个监听器收到参数和一个 `next()` continuation，可以转换 `next()` 的返回值，也可以**不调用 `next()` 直接返回**，从而短路后续的链条——Cordis 把后者叫「否决」。

看个例子：

```ts
export function apply(ctx: Context) {
  // 监听器 1：包裹下游结果。
  ctx.on('demo/transform', async (input, next) => {
    const downstream = await next()
    return downstream.toUpperCase()
  })

  // 监听器 2：看到 blocked 就短路。
  ctx.on('demo/transform', async (input, next) => {
    if (input.includes('blocked')) return '** blocked **'
    return next()
  })

  void (async () => {
    console.log(await ctx.waterfall('demo/transform', 'hello', async () => 'hello'))
    console.log(await ctx.waterfall('demo/transform', 'blocked words', async () => 'blocked words'))
  })()
}
```

输出：

```
HELLO
** BLOCKED **
```

第二行怎么来的：监听器 1 先跑，调用 `next()` 从而进入监听器 2；监听器 2 看到 `blocked`，不调 `next()` 直接返回，所以最内层默认逻辑从未运行；返回途中，监听器 1 再把替换文本转成大写。

由此得出 dsh 一条**常设纪律**：

> **只负责观察或只做标注的 waterfall 监听器，必须调用 `next()`。** 不调用 `next()` 直接返回，代表有意短路。如果日志监听器忘了调用 `next()`，会悄无声息地吞掉下游所有默认行为。

harness 用 waterfall 处理那些「协作插件可以包装或作答」的决策：`agent/request` 允许插件替换模型调用配置，`approval/request` 允许策略代替用户作答。

# `tools` 流水线：一次调用的层层关卡

理解了服务、事件、effect 和 waterfall，`tools` 的执行流水线就一目了然了。dsh 里一次工具调用不是「直接执行」，而是要过一整套关卡。看这张官方流程图（文字版）：

```
模型消息含 tool-call
   → tool/call（执行前落日志）
   → presentCall（UI pending 卡片）
   → tools/pre-execute waterfall（钩子 / 权限 / 沙箱）
   → 单调守卫（deny 或 abstain）
   → tools/execute waterfall（超时 / 重试 / 指标，环绕分发）
   → 工具 execute() 主体
   → fs/write-intent 或 fs/edit-intent（仅 tool-fs 变更类）
   → tools/post-execute waterfall（accept / block / replace / add context）
   → 注册表外层规范化（快照失败 → isError）
   → ToolDefinition.finalizeContent（仅内容不变式）
   → tools/result（同步通知，冻结的最终结果）
   → tool/result（落日志的模型可执行结果）
   → presentResult（UI 完成卡片）
```

这些关卡各司其职，又互相独立：

- **`tools/pre-execute`** 是 **waterfall**，承载通用的前置策略。钩子、权限、沙箱、plan-mode 都挂在这里，返回类型化决策（`allow` / `ask` / `deny`）。这是「可重排的策略层」。
- **单调守卫** 用 `ctx.tools.guard()` 设置，是**最终拒绝**，后面的监听器无法把它撤销。需要「严格到不可更改」的拒绝就用它。
- **`tools/execute`** 也是 **waterfall**，专门做**环绕分发**——加超时、重试、指标。只有它能让 `exec.signal` 被替换。
- **`tools/post-execute`** 做**显式结果变换**：接受、替换、屏蔽返回内容、附加模型可见上下文。
- **`tools/result`** 只是**观察**不可变、已归一化的最终结果，不改动它。观测用这个，要改结果才用 post-execute。

这套设计的好处——**钩子可以跨越不同工具家族，而工具本身不必与某个策略服务耦合**。权限策略不用写进每个工具里，它们是挂在 `pre-execute` 上的独立插件。

# 从「会写」到「看懂」

现在回头看上一篇那个 `greet` 工具，一切都清楚了：

- `inject: ['tools']` 让 Cordis 等工具注册表就绪；
- `ctx.tools.register(...)` 注册即副作用，卸载自动撤销；
- 观察者靠 `tools/result`（同步 emit）拿到冻结结果；
- 想加权限？挂一个 `tools/pre-execute` waterfall 监听器，返回 `deny` 或 `ask`，工具一行都不用改。

这就是为什么我说 dsh 是「元框架」而非「产品」：它把扩展点、分发模式、生命周期全部显式化，你往里组装自己的规则，而不是被一个成品框住。

# 一些思考

- **启动顺序不是「代码顺序」，是「依赖拓扑」。** 我第一次写组合时，以为 YAML 里谁先谁后很重要，结果发现真正决定时序的是 `inject`。这个心智模型的转变，是打开 dsh 大门的第一把钥匙。
- **waterfall 的「忘记调 next」是隐性炸弹。** 事件系统越优雅，越要在纪律上小心——一个记日志的监听器忘调 `next()`，就可能静默吞掉所有下游行为。文档里把它列为常设规则，不是没道理。
- **每一层都可独立替换。** 从 pre-execute 到 result，每一关都是独立的扩展点。想换权限策略、换渲染、加指标，都在对应那一层加插件，不用碰工具本身。模块化做到这份上，是 dsh 最让我佩服的地方。

下一篇聊聊最实用的进阶：钩子与权限门禁实战。我会用 `tools/pre-execute` 写一个允许 / 拒绝 / 询问的审批策略插件，让工具按你的规则运行。欢迎留言交流。
