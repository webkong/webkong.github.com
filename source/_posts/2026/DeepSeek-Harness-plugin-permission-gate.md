---
layout: post
title: DeepSeek Harness 钩子与权限门禁实战：让工具按你的规则运行
author: webkong
categories:
  - AI
tags:
  - AI
  - DeepSeek
  - Agent
  - dsh
  - 权限
date: 2026-09-07 13:00:00
---

> 前两篇解决「怎么把能力加进 dsh」，这篇回答「怎么给能力立规矩」。我用 dsh 的钩子系统写一个权限门禁插件：通过 `tools/pre-execute` 对一次工具调用返回「允许 / 拒绝 / 询问」，再把它和一个审批策略插件接起来。看完你就能让 Agent 的任何操作都走你的规则。

<!-- more -->

[上一篇](https://blog.webkong.cn/deepseek-harness-plugin-services-events/)讲清楚了 `tools` 流水线。里面最值得单独展开的就是 `tools/pre-execute`——它是 **waterfall**，是 dsh 的「可重排策略层」，钩子、权限、沙箱、plan-mode 全都挂在这儿。

这篇就用它写一个真正的权限门禁。

# 为什么权限要挂在水管上，而不是写进工具里

新手最容易想到的做法是：在 `execute` 里 `if (args.path.includes('/etc')) throw`。但我上篇说过，dsh 的纪律是**把部署策略从工具里剥离出来**。原因有三：

1. **跨工具复用。** 权限判断跟哪个工具无关——「这个操作能不能执行」对所有工具都成立。写在每个工具里就是重复。
2. **不污染工具。** 工具只该关心「怎么执行」，不该关心「行不行、要不要问用户」。职责分离。
3. **可替换。** 换一套权限规则，你只在 `pre-execute` 上换插件，一行都不改工具。

于是 dsh 把审批、钩子、沙箱都做成了 `tools/pre-execute` 上的**独立插件**。工具和策略服务零耦合。

# 最小权限门禁

先看最核心的钩子插件。它监听 `tools/pre-execute`，返回类型化决策：

```ts
import type { Context } from '@deepseek-ai/cordis'
import type { PreToolDecision, ToolExecution } from '@deepseek-ai/dsh-tools'

declare function isAllowed(exec: ToolExecution): Promise<boolean>

export const name = 'permission-gate'

export function apply(ctx: Context) {
  ctx.on('tools/pre-execute', async (exec, next): Promise<PreToolDecision> => {
    if (!(await isAllowed(exec))) {
      return { kind: 'deny', reason: 'Denied by policy.' }
    }
    return next()
  })
}
```

这里 `isAllowed(exec)` 是占位的策略函数——实际里它会读你的规则（哪些工具、哪些路径、哪些命令行允许）。关键在后面两行：

- `return { kind: 'deny', reason: '...' }` —— **否决**这次调用。这是 waterfall 的「不调 `next()` 直接返回」——所谓「有意短路」。
- `return next()` —— **放行**给后续监听器。这是 waterfall 的纪律：你只是作为策略层之一，决定权还在链条上。

`PreToolDecision` 是 `allow` / `ask` / `deny` 三种决策。所以一个门禁插件能返回「允许」「拒绝」或「询问用户」。

> 这里有个陷阱要记住：hook 插件拦截的是 `tools/pre-execute` 这个扩展点，**它本身不等于权限门禁**。沙箱、plan-mode 也挂在这儿。你不能因为写了 `pre-execute` 就以为那是权限系统——它只是「一个可拦截点」。

# 三种决策落地

光有 `allow` / `deny` 不够，真正有意思的是「还能问用户」。dsh 的审批机制叫 **user approval**，通过 `ctx.approval` 服务触发。

先看询问怎么触发。审批策略有两种：

| 策略 | 行为 |
|---|---|
| `ask`（默认） | 把问题委托给响应者链；无人响应 → 落回 `unavailable`（fail closed） |
| `never` | 确定性返回 `rejected`，不问任何人（CI、无人值守跑的严格模式） |

一个完整的门禁插件，可以在 `pre-execute` 里返回 `ask`，让审批系统去决定：

```ts
export function apply(ctx: Context) {
  ctx.on('tools/pre-execute', async (exec, next): Promise<PreToolDecision> => {
    // 危险命令需要用户确认
    if (exec.name === 'bash') {
      return { kind: 'ask', reason: `Run this command? ${exec.arguments.command}` }
    }
    return next()
  })
}
```

`ask` 的决策会走到 `ctx.approval.request(...)`，其结果是闭合的：

| 结果 | 含义 |
|---|---|
| `allowed-once` | 只授权被问的那一次操作（唯一的放行） |
| `rejected` | 显式拒绝 |
| `cancelled` | 请求被撤销 |
| `unavailable` | 没有负责的响应者 / 响应者抛异常（fail closed） |

注意 `fail closed`（失败即拒绝）：**缺失、不负责、抛异常或不合规的响应者，得到的都是 `unavailable`，而不是放行。** 这是安全感设计的核心——宁可拒绝，不可误放。

# 单调守卫：不许撤销的最终拒绝

`pre-execute` 是 waterfall，意味着它的决策是「可重排的策略层」——后面的监听器可能推翻前面的。但有些拒绝是**底线**，绝不能被绕过。

dsh 因此单独提供 `ctx.tools.guard()`：

```ts
ctx.tools.guard((exec) => {
  if (exec.name === 'bash' && exec.arguments.command.includes('rm -rf /')) {
    return false // 单调拒绝，任何后续监听器都无法撤销
  }
  return true
})
```

`guard()` 用于设置**最终单调拒绝**，后续监听器无法再把它改回来。这跟 `pre-execute` 的「可协商」完全不同。

两者的分工一句话总结：

> **需要「可以商量」的策略 → 用 `pre-execute`（waterfall）；需要「绝对不许」的底线 → 用 `ctx.tools.guard()`（单调守卫）。**

上篇的流水线图里，`pre-execute` 之后紧接着就是守卫。`pre-execute` 先跑，守卫在它之后、`execute` 之前。所以即使 `pre-execute` 链条里有人放行，撞上守卫依旧会被打死。

# 换个思路：让回答者自己决定

上面是「门禁主动问」。还有一种更解耦的做法——把审批做成 **response 者**，挂在 `approval/request` 上。

`approval/request` 是一个 **waterfall**，各响应者决定是否「认领」这次请求。认清它就返回一个结果；不认就调用 `next()` 委托给下一个：

```ts
export function apply(ctx: Context) {
  ctx.on('approval/request', async (req, next) => {
    if (req.toolName !== 'bash') return next()   // 不是我的事，委托
    if (req.reason?.includes('rm -rf')) return 'rejected'
    return 'allowed-once'
  })
}
```

第一个响应者占据唯一的决策槽位。这种设计最有价值的地方是：**UI 通道、本地策略、自动化桥接（ACP）都可以作为响应者共存**，它们通过共同的结果词汇协作，互相不认知。

`approval/request` 返回的结果是 `allowed-once | rejected | cancelled | unavailable`。发起方（比如 `dsh-tools`、`dsh-tool-bash`）消费这个闭合结果，**除非是 `allowed-once`，否则一律拒绝**。

# 一个完整的例子

把上面串起来，一个「危险命令必须确认」的完整策略长这样：

```ts
import type { Context } from '@deepseek-ai/cordis'
import type { PreToolDecision } from '@deepseek-ai/dsh-tools'

export const name = 'safe-bash-gate'
export const inject = ['tools', 'approval']

export function apply(ctx: Context) {
  // 1) 底线：任何情况下都不许 rm -rf /
  ctx.tools.guard((exec) => {
    if (exec.name === 'bash' && exec.arguments.command.includes('rm -rf /')) {
      return false
    }
    return true
  })

  // 2) 策略：bash 命令需要询问用户
  ctx.on('tools/pre-execute', async (exec, next): Promise<PreToolDecision> => {
    if (exec.name === 'bash') {
      return { kind: 'ask', reason: 'This command will run in your workspace:' }
    }
    return next()
  })
}
```

注意 `inject` 里我加了 `'approval'`——因为我们要用到审批服务。组合里也得有对应的提供方。挂了它之后，任何 `bash` 调用都会先被询问；`rm -rf /` 则连问都不问，直接被打死。

# 更多可拦截点

`pre-execute` 只是冰山一角。`tools` 的执行路径上还有一整个扩展点家族，每个都有明确分工：

| 扩展点 | 模式 | 典型用途 |
|---|---|---|
| `tools/pre-execute` | waterfall | 钩子、权限、沙箱（可重排策略） |
| `ctx.tools.guard()` | 单调守卫 | 不允许撤销的最终拒绝 |
| `tools/execute` | waterfall | 超时、重试、指标（环绕分发） |
| `tools/post-execute` | waterfall | 接受 / 替换结果、附加上下文 |
| `tools/result` | emit | 只观察，不可变的归一化结果 |

再往上，Agent 级也有 `agent/pre-step`、`agent/request`、`agent/turn-stopping` 等钩子，分别对应「步骤前」「请求前」「回合停止前」。dsh 的扩展点就是一张完整的地图——每个产品功能（钩子、`/goal`、context 压缩、plan mode、子代理、MCP、cron 调度……）都落在某个扩展点的一个监听器上，没有一行修改循环本身。这就是它「微内核」声明的来源。

# 一些思考

- **策略分层是门艺术。** dsh 把「可商量」(`pre-execute`) 和「不可商量」(`guard`) 分开，是安全感与灵活性的平衡。你有想留给用户判断的空间，也有绝不妥协的底线。很多框架要么全灵活、要么全强硬，做成这样分层的少。
- **fail closed 比 fail open 安全得多。** 审批系统「缺失响应者 → unavailable → 拒绝」这个设计，符合安全默认的原则：不知道该不该允许时，宁可拦下来。
- **插件化权限是真解放。** 以前加个权限，要么改工具源码，要么求上游合并；现在只是往 `pre-execute` 上挂一个插件，工具完全不用动。这大概是 dsh 「一切皆插件」最直接的收益。

三篇下来，从「写 hello」到「服务与事件」再到「权限门禁」，你已经能看懂 dsh 的插件、服务、事件、流水线这整套机制了。下一步不妨自己 clone 源码，试着给 dsh 加一个真正的自定义工具，或者把某个内置能力换成你自己的实现——那才是真正上手 dsh 的开始。欢迎留言交流。
