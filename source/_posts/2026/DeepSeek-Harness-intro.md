---
layout: post
title: DeepSeek Harness：一切皆插件的 Agent 框架
author: webkong
categories:
  - AI
tags:
  - AI
  - DeepSeek
  - Agent
  - dsh
date: 2026-09-07 10:00:00
---

> 2026 年 8 月，深度求索开源了 DeepSeek Harness（dsh）——一个「一切皆插件」的 Agent 框架，给出了这个问题的答案：Agent = Model + Harness。模型是 Agent 的灵魂，而 Harness 给予 Agent 理解环境、使用工具、在真实场景中持续工作的能力。

开始的开始，让我们从一句广为流传的公式说起：

> Agent = Model + Harness

这句话来自 OpenAI，意思是：一个真正可用的 AI Agent，不光要有一个强大的大模型（Model），还需要一整套围绕它的工程能力（Harness）——比如工具调用、文件读写、命令执行、沙箱隔离、权限审批、会话记忆、可观测性等等。模型负责「思考」，Harness 负责「行动」。

DeepSeek Harness（简称 dsh）正是这一理念的开源落地。它不优化模型本身，而是优化模型运行的环境，核心设计原则就六个字：

**一切皆插件（Everything is a Plugin）。**

<!-- more -->

# 什么是 DeepSeek Harness

DeepSeek Harness 是由 [DeepSeek AI](https://deepseek.com) 开发的 Agent Harness（智能体框架），2026 年 8 月正式开源，以 TypeScript 编写，使用 MIT 许可证，目前处于开发者预览阶段。

- 项目地址：<https://github.com/deepseek-ai/deepseek-harness>
- 官方文档：<https://deepseek-harness.github.io/deepseek-harness/>
- 官网：<https://deepseek.com/harness>

它不是一个又一个聊天界面，也不是模型本身。它是让模型能调用工具、执行任务、跟开发者协作的那层基础设施。

# 一切皆插件

dsh 最核心的设计主张是「一切皆插件」：模型适配器是插件，工具注册表是插件，Session Log 是插件，Agent Loop 本身是插件，沙箱是插件，审批策略是插件，连 UI 都是插件。

模型、工具、技能、会话、沙箱、存储、循环（Agent Loop）、调度、UI 等所有 Agent 能力均由插件提供，并通过 Cordis 内核的服务（Service）与事件（Event）彼此协作。开发者无需改动任何源码，就能在配置层选择、替换、扩展任一能力。

想换模型？装个插件。想换工具注册方式？装个插件。想把 Agent 从「思考 → 行动 → 观察」改成别的循环？还是装个插件。

这套架构的关键在于：运行时不存在需要打补丁的特权内核。每一项能力注册都是可逆副作用，插件卸载时自动撤销。因此扩展 dsh 的方式，就是把新插件挂载到其他插件旁边。

底层由开源插件系统 [Cordis](https://github.com/cordiverse/cordis) 驱动，其设计思想对应论文《A Programming Paradigm for Spatiotemporal Composability》。

> 这可能是我见过最激进的 Agent 框架设计。与 Claude Code、Codex 这类「产品」不同，dsh 是一个「元框架」——你用它组装自己的 Agent 产品。前者你按它的规则用，后者你用它的积木拼。

# Profile 与运行模式

dsh 通过 Profile + 组合包（Bundle）来组装运行时。运行中的 dsh 是一棵插件树，由启动时按序叠加的各层组合而成——先按 profile 列出的顺序应用每个组合包，然后是 profile 的 `cordis.patch.yml`、home 级 patch，最后是任意 `--patch` overlay。

开箱即提供几种运行模式：

| 模式 | 定位 | 能力构成 |
| --- | --- | --- |
| 标准模式（web） | 功能完整的编码 Agent | 编辑、Shell、Skills、计划、目标、子代理工作流 |
| PTC 模式 | 代码组合工具调用 | 具备标准模式全部能力，并通过 Code Mode SDK 呈现工具 |
| 极简模式 | 最小化基准测试 | 仅保留 bash 与 `str_replace_editor` 两个工具 |
| 创造模式 | 自定义 Agent preset | 具备标准模式全部能力，可检查运行时、试验插件 |

还有 headless 模式，跑脚本和 CI 用：一次性运行任务、打印最终答案并退出，不用开浏览器。

# 快速上手

系统要求只一个前提：Node.js `^22.19.0` 或 `>=24`。

最方便的上手方式是直接用 npx：

```bash
$ npx @deepseek-ai/dsh web
```

默认会启动 Web UI（`http://127.0.0.1:3080`），提供完整的图形界面。

如果想体验 headless 一次性任务：

```bash
$ dsh --profile headless "列出这个仓库所有 TODO"
```

从源码跑起来（开发插件建议 clone 源码）：

```bash
git clone https://github.com/deepseek-ai/deepseek-harness.git
cd deepseek-harness
pnpm install
pnpm run build
pnpm dsh web
```

配套的 SDK 也很友好。Python 官方 SDK 自带运行时，无需系统 Node.js：

```bash
pip install deepseek-harness-sdk
```

官方也提供了 TypeScript SDK，可以把 Agent 嵌入到任何工作流。

# 上手之后的三步走

1. **配置模型**：`Settings → Models`，填入 DeepSeek API 密钥。也支持 OpenAI 兼容端点和其他厂商，改完不用重启，模型路由即刻生效。
2. **选择工作区**：`Choose workspace`，指向你的项目目录。没选工作区之前输入框是禁用的——我第一次卡在这，还以为是启动失败了。
3. **发任务**：新开一个会话，让它「总结这个仓库并识别主要包」试试水。

dsh 运行的时候，所有文件读写和命令执行都被限制在工作区内，路径穿越（`../../`）会被拦——这是默认的沙箱隔离，不需要额外配置。

# 写你的第一个插件

光会跑没意思，dsh 的灵魂在插件。用一个最小例子讲清楚它的机制。在仓库里建一个插件目录，写一个 `greet` 工具：

```ts
export const name = 'hello-plugin';

export const inject = ['tools'] as const;

export function apply(ctx, tools) {
  tools.register('hello', {
    description: 'Say hello to the user.',
    input: {
      type: 'object',
      properties: {
        name: { type: 'string' },
      },
      required: ['name'],
    },
  }, async (input) => {
    return `Hello, ${input.name}!`;
  });
}
```

这个插件只干四件事：声明插件名、声明需要的服务（tools）、在 `apply` 入口注册工具、定义工具的入参和执行逻辑。模型通过「描述」知道这个工具存在，通过「入参 schema」知道怎么调用它。

> 提示：装插件前一定看源码和权限。Harness 插件运行在宿主进程里，属于可信代码，安装脚本会下载什么、需要哪些目录和凭据权限，都得先弄清楚——这跟装任何 npm 包一个道理。

# 和主流工具的对比

| 项目 | 定位 | 与 dsh 的关系 |
| --- | --- | --- |
| Claude Code | 闭源商用编码助手 | 功能对标，但 dsh 完全开源、可自托管、能力可替换 |
| Hermes Agent | 自进化个人 Agent（Nous Research） | 侧重跨会话记忆与技能沉淀；dsh 侧重插件化组合与全程可观测 |
| OpenClaw | 本地优先消息型 Agent | 侧重多渠道接入与数字主权；dsh 提供 Web UI / headless / SDK 多形态 |
| LangGraph / AutoGen / CrewAI | Agent 构建框架（编程库） | 框架解决「如何构建」；dsh 是完整 Harness——「如何稳定运行 + 如何替换能力」 |

一句话总结定位差异：dsh 不是又一个「写几个 agent 的库」，而是位于 SDK 与框架之上、解决「Agent 如何可靠运行」的完整一层。

# 一些思考

- **Agent = Model + Harness**。dsh 的定位跟这个公式严丝合缝——它把模型当可替换组件，把工程能力（工具、沙箱、审批、UI）全部做进 Harness 层。
- **约束即代码**。AI 又漏改文件了？把「文件修改必须走工具注册」写成一个插件约束，比维护 AGENTS.md 里的一行文字更硬。约束从「文档约定」升级成了「代码强制」。
- **上手门槛低，生产要谨慎**。它现在还是 0.1 预览版，官方明说「存在破坏性更新」。做技术预研、做内部工具的底座没问题，想用 dsh 替代 Claude Code 的团队，建议等它稳定几个版本再说。

国内团队来说，DeepSeek API 直连 + 相对低价，加上 MIT 协议完全开源能自托管，是它最实在的吸引力。

如果你也在折腾 Agent 相关的基建，欢迎留言交流。下一篇打算写 dsh 的插件开发实战，或者聊聊怎么给它接自定义工具。