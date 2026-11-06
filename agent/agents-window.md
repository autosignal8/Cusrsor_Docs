> 原文：https://cursor.com/docs/agent/agents-window

# Agents Window

Agents Window 是 Cursor 以 Agent 为中心的界面。它提供统一的工作区，让你跨仓库和环境用 Agent 构建，包括本地、云端、远程 SSH 等。它把并行 Agent 的能力，与开发环境的深度和控制结合在一起。

你可以随时切回编辑器，也可以同时打开两者。

## 打开 Agents Window

如果你在编辑器中，输入 Cmd+Shift+P → Open Agents Window，即可打开 Agents Window。

![命令面板中显示 Open Agents Window 命令](/docs-static/images/agent/open-agents-window-final.png)

## 切回 IDE

要回到经典的 Cursor IDE，输入 Cmd+Shift+P → Open IDE。这会在编辑器中打开当前工作区。

![操作菜单中显示 Open IDE 命令](/docs-static/images/agent/open-editor-window-final.png)

如果想在不离开 Agents Window 的情况下查看或编辑文件，可以输入 Cmd+P 搜索文件，或用 Cmd+Shift+F 搜索所有文件。

![Agents Window 中的文件搜索与文件查看](/docs-static/images/agent/file-agents-window-final.png)

## 仅在 Agents Window 中提供的功能

以下功能在 Agents Window 中可用：

- **多工作区：** 在一个地方使用覆盖你所有项目的 Agent。
- **新的 diff 视图：** 审查并提交改动、管理 PR，而无需离开 Cursor。
- **并行 Agent：** 在云端运行许多并行 Agent（并在手机、Web、Slack、GitHub 和 Linear 上与它们协作）。
- **本地与云端之间更容易交接：** 把 Agent 从云端快速移到本地以便迅速迭代，再移回云端，让它继续独立工作。
- **Cloud subagents：** 用 `/in-cloud` 把任务交给[云端 Subagent](https://cursor.com/docs/subagents.md#cloud-subagents)，或让 PR 进入 `/autopilot`，这样长时间运行的工作会在自己的 VM 和分支上进行，而你可以继续在本地工作。
- **Worktrees：** [在隔离的 Git checkout 中运行 Agent](https://cursor.com/docs/configuration/worktrees.md)，让每个任务有自己的文件和改动。

## 在 Agents Window 与编辑器之间选择

当你想并行运行和管理许多 Agent 时，Agents Window 很合适。如果你主要用 Agent 写代码，Agents Window 会帮你站到更高的抽象层。

当你想要经典 IDE、VS Code 扩展，以及灵活分屏以便同时看很多文件时，编辑器很合适。

你可以在两种界面之间移动，我们会继续支持并改进这两种体验。

## Enterprise 访问

Agents Window 随 2026 年 4 月 2 日发布的 Cursor 3 正式可用。发布后的两周内，Enterprise 管理员可以在组织内控制推出：通过 Team settings 把访问权给整个团队，或给特定用户。推出期结束后，所有用户默认都可以访问。

---

## 站点地图

[所有文档页面概览](/llms.txt)
