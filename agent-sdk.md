> 原文：https://cursor.com/docs/agent-sdk

# Agent SDK

Agent SDK 让你用代码定义、测试、评估和部署 Agent。它使用驱动 Bugbot、Security Review、Approval Bot 和 Rollouts 的 Cursor SDK 运行时。

Agent 的行为定义在一个 Markdown 与 TypeScript 文件夹中，把确定性代码与模型判断结合在一起。Agent SDK 让构建有用的 Agent 变得容易，并提供 API，以便在 Slack、pull request、议题跟踪器、Cursor，以及你完成工作的其他地方进行交互。

部署 Agent 之后，打开 [Deployed Agents](https://cursor.com/dashboard/deployed-agents) 来管理它。

## 用你的编程 Agent 开始

把这段提示词粘贴到你的编程 Agent 中。它会安装 CLI，询问你的 Agent 用途，并根据这些文档的 Markdown 版本搭建第一个项目。

```text
Set up my first Agent SDK project.

Read https://cursor.com/docs/agent-sdk.md first. Every page under
https://cursor.com/docs/agent-sdk/ is also available as Markdown when you
add .md to its URL.

1. Check that Node is 22.13 or newer, then run:
   npm install --global @grok/agent-sdk
2. Read the docs index that ships with the package:
   "$(npm root -g)/@grok/agent-sdk/dist/docs/llms.txt"
3. Run agent-sdk login, and wait while I finish the browser sign-in.
4. Ask me what the agent should do and where people will reach it. Then
   follow the bdk-create-agent skill at
   "$(npm root -g)/@grok/agent-sdk/skills/create-agent/SKILL.md"
   to scaffold the project with agent-sdk init.
5. Run agent-sdk validate --dir . and one agent-sdk run turn, then show me
   the reply.
```

## 为什么使用 Agent SDK

你的指令、工具和 eval 都是文件，你可以像其余代码一样审查、测试和版本化它们。你编写指令和工具，Cursor 运行调用它们的模型循环。

## 何时使用 Agent SDK

|                    | Cursor Automations                            | Grok Bot                                                        | Agent SDK                                                                                          |
| ------------------ | --------------------------------------------- | --------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| **你定义的内容**   | 仪表板中的提示词、模型和触发器                 | 应用中的指令、Skills 和已连接的工具                              | 受版本控制的 Markdown 与 TypeScript 项目                                                           |
| **它在哪里运行**   | 由 Cursor 托管                                 | 由 Cursor 托管                                                   | Cursor 托管，或你的基础设施                                                                         |
| **在以下情况选择** | 工作适合计划或事件触发                         | 你想配置并与 Agent 对话，而不写代码                              | 你想用代码包住模型判断，以便团队进行版本控制、审查、测试和 hillclimb                                 |

## 开始使用

安装 `@grok/agent-sdk`，然后搭建并运行一个项目。该软件包以前名为 `@cursor/bdk`。

```bash
npm install --global @grok/agent-sdk
agent-sdk init ./my-bot
cd my-bot
agent-sdk dev
```

[项目布局](https://cursor.com/docs/agent-sdk/reference/project-layout.md)说明 `bot/` 下的每个文件夹。

## 后续步骤

- 逐步[构建你的第一个 PR 审查 Agent](https://cursor.com/docs/agent-sdk/quickstart.md)。
- 从[审查或分拣模板](https://cursor.com/docs/agent-sdk/templates/reviews.md)或[所有者策略审批者](https://cursor.com/docs/agent-sdk/templates/agentic-owners.md)开始。
- [把 Cursor Automation 转换](https://cursor.com/docs/agent-sdk/guides/convert-automation.md)为 Agent SDK 项目。
- 使用编程 Agent[用 Agent 构建 Agent](https://cursor.com/docs/agent-sdk/building-with-agents.md)。
- 在 [CLI 参考](https://cursor.com/docs/agent-sdk/reference/cli.md)中查找命令，在[访问](https://cursor.com/docs/agent-sdk/reference/access.md)中查找已部署 Agent 的角色。

## 架构

Agent SDK 架构

- 你带来代码和想法：Agent as code
  - 提示词为 .md 或 .ts
  - 确定性代码钩子
- 由 Cursor 托管或自托管：
  - 源代码管理 + 议题跟踪器 + Slack 触发器
  - 模型路由
  - Evals
  - MCP 连接
  - 会话 · 模型循环 · Subagents · 工具调用
  - 密钥管理
  - 沙箱
  - 部署
  - 存储 + 产物

---

## 站点地图

[所有文档页面概览](/llms.txt)
