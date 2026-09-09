---
layout: post
title: DeepSeek Harness 插件开发实战：从 hello 到面向模型的工具
author: webkong
categories:
  - AI
tags:
  - AI
  - DeepSeek
  - Agent
  - dsh
  - 插件
date: 2026-09-07 11:00:00
---

> 上一篇聊了 DeepSeek Harness（dsh）「一切皆插件」的设计，这篇直接动手：把一个 `greet` 工具注册进 harness，让模型能调用它，并全程通过事件观察结果。整个例子不需要 API Key、不调用模型，跑通就走完了 dsh 插件开发的完整链路。

<!-- more -->

[上一篇](https://webkong.cn/deepseek-harness：一切皆插件的-agent-框架/)里我们摸清了 dsh 的骨架：Agent = Model + Harness，一切能力都是插件。但光懂了架构不算会写插件，这篇是真正落地的教程。

我会带你走完一条最小、但完全真实的路径：**写一个工具插件 → 注册进 `tools` 服务 → 驱动一次调用 → 通过 `tools/result` 事件观察结果**。全程无密钥、不碰模型。

# 从最小工具开始

dsh 的工具就是「模型能调用的一个能力」。要让它被模型看见，你需要做三件事：定义它、把它注册给 `tools` 服务、告诉 harness 你依赖这个服务。

先写一个 `greet-tool.ts`：

```ts
import type { Context } from '@deepseek-ai/cordis'
import { defineTool } from '@deepseek-ai/dsh-tools'

export const name = 'greet-tool'
export const inject = ['tools']

export function apply(ctx: Context) {
  ctx.tools.register(defineTool({
    name: 'greet',
    description: 'Greet the named person.',
    parameters: {
      name: { type: 'string', required: true, description: 'Who to greet' },
    },
    output: {
      schema: { type: 'string' },
      render: (_args, value) => [{ type: 'text', text: value }],
    },
    async execute(args) {
      return `Hello, ${args.name}!`
    },
  }))
}
```

这个插件只做了四件事，但每一件都对应 dsh 的一条核心规则：

1. **`name`** 声明插件名，是元数据，也是日志和调试里识别它的依据。
2. **`inject = ['tools']`** 让 Cordis 内核等 `tools` 服务就绪后再执行 `apply`——这就是上一章说的「加载顺序由依赖决定，而不是文件顺序」。
3. **`ctx.tools.register(defineTool({...}))`** 把工具注册进工具注册表；`defineTool` 是 dsh 第一方工具的类型化辅助函数。
4. **`apply(ctx)`** 是插件的入口，插件在这里做初始化，注册本身就是一个「可逆副作用」。

# `defineTool` 到底做了什么

你可能注意到，我并没有写 JSON Schema，只写了 `parameters: { name: { type: 'string', required: true } }`。这正是 `defineTool` 的聪明之处：

- 它把 `parameters` 规约**转换成向模型展示的 JSON Schema**；
- 它**推导出 `execute(args)` 里 args 的类型**——上面代码里 `args.name` 是 `string`，编辑器会给你类型提示；
- 在 `execute` 真正运行前，它会**校验模型生成的其他参数**（类型、必填键、字面量约束、联合分支）。

注意 `output` 的两部分分工：

- `output.schema` 声明工具返回的**规范值**（这里是 `string`）；
- `output.render(args, value)` 则把规范值**渲染成面向模型的内容块**（`{ type: 'text', text: value }`）。

这个「规范值 / 渲染内容」的分离是 dsh 刻意为之的：规范值用于程序化处理，渲染内容用于模型可见的自然语言。中间值只在执行期间存在，不会被持久化。

# 组合起来跑一次

dsh 的运行是一棵插件树，由一个组合清单（YAML）决定。要把我们的插件跑起来，需要一个最小组合：

```yaml
- name: '@deepseek-ai/dsh-system-prompt'
- name: '@deepseek-ai/dsh-tools'
- name: './tool-logger.ts'
- name: './greet-tool.ts'
```

这里有两个关键点：

1. **`@deepseek-ai/dsh-tools` 依赖 `systemPrompt` 服务**，因为工具需要向系统提示词贡献 schema，所以组合里也要列出该服务的提供方。缺了它，工具插件会一直保持 `PENDING`（等待）状态。
2. 工具插件和日志插件**互相不知道对方存在**——它们靠注册表服务和事件连接。这就是 dsh 「无特权内核」的体现。

# 用它观察一次调用

光注册工具还不够，我想让你看到「事件如何解耦插件」。再写一个观察者插件，什么都不改，只在旁边听：

```ts
import type { Context } from '@deepseek-ai/cordis'
import type {} from '@deepseek-ai/dsh-tools'

export const name = 'tool-logger'
export const inject = ['tools']

export function apply(ctx: Context) {
  ctx.on('tools/result', (exec, result) => {
    const text = result.content
      .map(block => (block.type === 'text' ? block.text : ''))
      .join('')
    console.log(`[tool-logger] ${exec.name} -> ${text}`)
  })
}
```

`import type {} from '@deepseek-ai/dsh-tools'` 这一行不是多余的——它引入该包的**声明合并**，让 `'tools/result'` 事件和它的 payload 具备类型。这和上一篇在 `stats.ts` 里导入类型是同一个手法，只是扩展到了包级别。

我们再用代码驱动一次真实调用，代模型发请求：

```ts
import { brandString } from '@deepseek-ai/dsh-brand'
import type { ToolCallId } from '@deepseek-ai/dsh-llm'

export function apply(ctx: Context) {
  ctx.tools.register(defineTool({ /* 上面的 greet 工具 */ }))

  void (async () => {
    const result = await ctx.tools.execute({
      callId: brandString<ToolCallId>('demo-1'),
      name: 'greet',
      arguments: { name: 'Cordis' },
      signal: new AbortController().signal,
    })
    console.log('tool replied:', JSON.stringify(result.content))
  })()
}
```

运行 `node --import tsx ../../vendor/cordis/bin.js`，你会看到：

```
[tool-logger] greet -> Hello, Cordis!
tool replied: [{"type":"text","text":"Hello, Cordis!"}]
```

注意 logger 先触发：`tools/result` 在结果物化过程中发出，发生在 `execute` 向调用方返回的 promise 兑现**之前**。两个插件完全解耦，一个注册、一个观察，互不越界。

# 最关键的执行参数：`args` 与 `exec`

`defineTool` 的 `execute(args, exec)` 有两个参数，职责完全不同：

- **`args`** 是**已经校验过的参数**，类型从 schema 推导而来。返回的是 `output.schema` 声明的规范值。
- **`exec`** 承载执行上下文，其中 `exec.signal` 是你要遵守的取消信号——信号触发时取消进行中的工作。

它还有几个值得记住的约定：

- **参数帮你校验了。** `defineTool` 会在 `execute` 前校验模型生成的 arguments，所以 `args` 必定匹配 `InferArgs`。你只需手动检查 schema DSL 表达不了的约束（比如非空字符串、正数、跨字段规则）。
- **返回规范 JSON 值。** 工具主体不要返回内容块，也不要让调用方从自然语言里解析 id 和字段。比如返回一个 `{ ok: true, jobId }` 的对象，而不是一段「任务已开始，id 是 xxx」的文本。
- **抛异常 = `isError`。** 基础设施故障就抛异常；成功的领域结果即使表示不理想状态（比如进程非零退出），也应写入规范值，让渲染器去解释。
- **`exec.signal` 要服从。** 这是长时间运行任务的取消核心，后续写后台任务时会重点用到。

# 面对真实文件的工具

上面的 greet 太小儿科了。来个真实的：读文件。为此我稍微展开 `parameters`——两处不同：

1. `path` 是必填 `string`；
2. `limit` 是可选 `number`（不写 `required` 就是可选）。

```ts
import { readFile } from 'node:fs/promises'
import { defineTool } from '@deepseek-ai/dsh-tools'

export function apply(ctx: Context) {
  ctx.tools.register(defineTool({
    name: 'read_file',
    description: 'Read a file from disk.',
    parameters: {
      path: { type: 'string', required: true, description: 'Absolute path' },
      limit: { type: 'number' },
    },
    output: {
      schema: { type: 'string' },
      render: (_args, value) => [{ type: 'text', text: value }],
    },
    async execute(args, exec) {
      return readFile(args.path, { encoding: 'utf8', signal: exec.signal })
    },
  }))
}
```

这个工具和 greet 唯一的实质区别，是 `execute` 里把 `exec.signal` 传给了真实的异步 I/O。读文件这种天然支持取消的操作，就应当这么做。

# 让工具更好用：`output.render` 与 UI

在 dsh 里，一个工具至少要能「被模型调用」，好工具还要能「被 UI 好看地呈现」。`output.render` 管前者，`presentCall` / `presentResult` 管后者。

`presentCall(args)` 返回调用进行中的卡片（`PENDING`），`presentResult(args, { content, isError, meta })` 返回完成后的卡片。dsh 提供几种卡片类型：

- `generic` —— 默认，标题 + 原始参数；
- `terminal` —— 你的调用本身就是 shell 命令（比如 `tool-bash`）；
- `diff` —— 你的调用创建或修改文件（比如 `tool-fs` 的 `write` / `edit`）；
- `read` —— 返回从持久化 `result.meta` 重建的文件窗口；
- `search` —— 返回按文件分组的匹配或路径列表；
- `web` —— 返回完成的 web 检索（`search` / `fetch`）。

有一条硬性规则要记住：**这些展示方法必须是纯函数**。它们会在实时流式和会话回放时都运行，所以不能做 I/O、不能读会话状态、不能用时钟或随机数。diff 必须从 args 派生——因为调用时的展示器手里没有文件的旧内容。

> 我在读官方文档时，最惊讶的是「UI 格式不进入模型结果」这条。你希望的 `console` 围栏、diff、相对化路径，都不应该为了服务 UI 而混进规范值或 Native 内容。`output.render` 负责模型可见的自然语言，`presentationMeta` 和卡片展示器负责可回放、可复现的 UI 状态。两者各司其职，别混。

# 从 hello 走向真实 agent

你写好的工具，本质上就是真实 agent 的一个零件。一个完整的编码 `web` 模式 Agent，无非是这套组合再加上：LLM 适配器、agent loop（智能体循环）、持久化、应用入口。

想把它接进现成的 dsh？只需要一个小的 `--patch` overlay：

```bash
dsh web --patch ./cordis.yml
```

dsh 会把你写的插件和内置的整套能力拼在一起——模型看到 `greet`、`read_file`，能调用，结果也能正确渲染。你从「写一个 hello」起步，到达的却是「给 dsh 加一个属于自己的能力」。

这，就是面向模型工具开发的完整链路。

# 一些思考

- **最小闭环很重要。** 等你真正动手，才会发现 dsh 把「加一个工具」拆成了规约、注册、渲染、观察四步。这个拆解不是故作高深，而是让每一步都能独立替换——换渲染不换执行，换执行不换展示。
- **类型即文档。** `defineTool` 从 `parameters` 推导 `args` 类型，比手写 JSON Schema 少了太多出错空间。这提醒我：好的框架应该是「类型安全内建」，而不是让你自己去维护两份 schema。
- **事件是解耦的黏合剂。** 观察者插件和工具插件完全不认识，却靠 `tools/result` 接上了。这比传统的「A 调用 B」优雅——新增观测逻辑，一行都不用改已有的工具。

如果你也打算给 dsh 接自定义工具，我最建议先按本文跑通 `greet`，再把 `read_file` 这类真实工具加进来，最后才是碰权限、碰 UI 这些进阶话题。

下一篇准备聊聊插件机制更深处：服务注入与事件系统，讲清楚 `ctx.tools`、`ctx.llm` 这些服务是怎么被注入、被替换的，以及 `tools` 流水线里 pre-execute / guards / post-execute / result 各自干什么。欢迎留言交流。
