> 原文：https://cursor.com/docs/customize-cursor

# 自定义 Cursor

Plugins、Skills 和 MCP 让你按自己的工作流自定义 Cursor。**Customize** 页面把它们集中到一处。

在 Cursor 的侧边栏中打开 **Customize**，即可在用户、团队或工作区级别添加并管理扩展。你可以安装官方和社区 Plugins，连接 MCP 服务器（包括你自己的），并控制每个作用域中哪些 Rules、Skills、Subagents、Commands 和 Hooks 处于启用状态。

## 在 Customize 中可以做什么

在 Customize 页面，你可以：

- **浏览并安装**来自 [Cursor Marketplace](/marketplace) 的 Plugins、Skills 和 MCP，一键完成
- **安装 Team MCP 服务器**，它们通过团队的 [默认团队 Marketplace](https://cursor.com/docs/plugins.md#default-team-marketplace) 共享
- **查看团队排行榜**，了解你的团队和社区中最受欢迎的 Plugins、Skills 和 MCP
- **添加并管理** Plugins、Skills、MCP、Subagents、Rules、Commands 和 Hooks，无需在不同的设置页面之间切换
- **按作用域筛选**，查看为你、你的工作区或你的团队安装了什么
- **打开插件 Canvas**，使用团队可复用的共享设置模板

更多内容见[更新日志](/changelog/customize)。

## 扩展组件

Cursor 扩展由可组合的部分构成。Plugins 通常会把其中若干项打包在一起，你也可以单独添加每个组件。

| 组件          | 说明                                                                                                                                                                                                                                                                                         |
| :------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Plugins**   | 可分发的捆绑包，打包了 Rules、Skills、Subagents、Commands、MCP 服务器和 Hooks。从 [Marketplace](/marketplace) 或[团队 Marketplace](https://cursor.com/docs/plugins.md#team-marketplaces) 安装。                                                                                            |
| **Rules**     | 塑造 Agent 如何处理你的代码的持久指令。使用[项目规则](https://cursor.com/docs/rules.md#project-rules)、[用户规则](https://cursor.com/docs/rules.md#user-rules)、[团队规则](https://cursor.com/docs/rules.md#team-rules)或 [`AGENTS.md`](https://cursor.com/docs/rules.md#agentsmd)。 |
| **Skills**    | Agent 在相关时加载的专门能力。Skills 把领域知识、工作流和脚本打包在 `SKILL.md` 文件中。参见 [Agent Skills](https://cursor.com/docs/skills.md)。                                                                                                                                             |
| **Subagents** | Agent 为并行或隔离的工作而委派的专门助手。每个 Subagent 在自己的上下文窗口中运行。参见 [Subagents](https://cursor.com/docs/subagents.md)。                                                                                                                                                  |
| **Hooks**     | 在特定生命周期事件上观察、控制或扩展 Agent 循环的脚本。参见 [Hooks](https://cursor.com/docs/hooks.md)。                                                                                                                                                                                     |
| **Commands**  | 你在 Agent 聊天中用 `/` 调用的可复用提示词。Commands 是定义一项聚焦工作流或操作的 markdown 文件。                                                                                                                                                                                           |

把 Cursor 连接到外部工具和数据源的 MCP 服务器，参见 [Model Context Protocol (MCP)](https://cursor.com/docs/mcp.md)。

## Marketplace 排行榜

Cursor 会显示你团队中最受欢迎的 Plugins、Skills 和 MCP 的排行榜。

在 Customize 页面一键将任意条目加入你的配置，并按你的工作流扩展 Cursor。排行榜帮助你发现队友和社区使用最多的内容，从而快速采用经过验证的配置。

在 [Cursor Marketplace](/marketplace) 浏览完整目录。社区 Plugins 和 MCP 服务器见 [cursor.directory](https://cursor.directory)。

## 了解更多

### Plugins

浏览 Marketplace、安装 Plugins，并设置团队 Marketplace

### Rules

编写引导 Agent 行为的项目规则、用户规则和团队规则

### Skills

把专门的工作流打包到可移植的 SKILL.md 文件中

### MCP

把 Cursor 连接到外部工具、API 和数据源

### Subagents

把复杂任务委派给专门的 Agent

### Hooks

在 Agent 循环的关键节点运行脚本

---

## 站点地图

[所有文档页面概览](/llms.txt)
