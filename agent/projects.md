> 原文：https://cursor.com/docs/agent/projects

# Projects

Project 承担更大的一块工作，例如一个功能、一次迁移或一个完整应用。你通过与它的协调 Agent 聊天来指挥它。协调 Agent 自己不写代码。它规划工作，把工作委派给写代码的 [Agent](https://cursor.com/docs/agent/overview.md)，再把完成的工作交回给你检查。Projects 位于 [Agents Window](https://cursor.com/docs/agent/agents-window.md) 的左侧导航中，并运行在 [Cloud Agents](https://cursor.com/docs/cloud-agent.md) 上。

Projects 在每个包含 Cloud Agents 的方案上可用：Premium 及以上、Teams、Enterprise，以及旧版付费 Cursor 方案。Enterprise 团队需要 Cursor 3.21.9 或更高版本。Projects 在 Free、Plus 或旧版 Hobby 方案上不可用，这些方案不包含 Cloud Agents。它也不能与 Privacy Mode (Legacy) 一起使用，因为 Projects 运行在 Cloud Agents 上，而 Cloud Agents 在运行期间会把代码存放在云端。

## Project 如何工作

Project 会在数月的工作中保持上下文，把任务并行委派给许多 Agent，并在没有提示的情况下做重复性工作。

- **协调 Agent 负责规划与委派。** 它代表你创建并管理 Agent，按工作需要并行运行任意多个。因为它委派而不是亲自执行，所以仍能及时响应你的指挥。
- **Agent 在 Project 内部工作。** 打开协调 Agent 启动的任意 Agent，即可跟进它的工作或直接与它对话。

## 创建 Project

### 在左侧导航中打开 Projects

在 [Agents Window](https://cursor.com/docs/agent/agents-window.md) 中，找到侧边栏里的 **Projects** 分区并点击 **New Project**。

### 为 Project 命名

选择一个图标并输入项目名称。如果名称留空，Project 会叫做 **New Project**。

### 选择工作区与模型

在 **Workspace** 下，选择 Project 所在的仓库。Projects 在云端运行，因此列表显示的是你的 Cloud Agents 可用的仓库。如果尚未连接 GitHub，菜单会提供 **Connect GitHub**。在 **Model** 下，选择协调 Agent 使用的模型。

### 点击 Create Project

Project 会作为与协调 Agent 的一段聊天打开。描述你想构建的东西，之后由协调 Agent 接手。

Projects 最适合超出单次聊天的工作：包含多个 PR 的功能、一次迁移，或你希望在离开时仍被处理的任务。

## 添加和移出聊天

把侧边栏中符合条件的顶层 Cloud Agent 聊天拖到某个 Project 行上即可添加。你也可以打开该聊天的侧边栏菜单并选择 **Add to Project**。如果某个本地聊天可以执行该操作，Cursor 会先把它转换为 Remote Control，然后再添加。之后该聊天会出现在 Project 行下，以及 Project 聊天输入框上方的 **Agents** 列表中。

要把聊天移出，在 **Agents** 列表中打开它的菜单并选择 **Remove from Project**。

## 默认在云端，需要时在本地

Project 在云端自己的计算机上运行，因此合上笔记本电脑也不会让它停止。在云端运行也让 Project 能并行运行比你的笔记本电脑所能支持的更多 Agent。

当某些事情需要在你的机器上测试时，协调 Agent 会启动一个本地 Agent 在那里运行。

## 共享上下文

你不应该每次开始任务都要重新让 Agent 熟悉情况。每个 Project 维护一组文件，这些文件会同步到它的 Agent 所使用的每台云端和本地机器。Agent 会加入调研和产物，以及它们对代码库的了解和你偏好的工作方式。如果一个 Agent 弄清了如何测试某个服务，以后的每个 Agent 都可以使用那些说明。

共享上下文随着 Project 增长，使协调 Agent 随时间变得更有效。

## 订阅

让协调 Agent 监视一个 Slack 频道、按计划运行，或关注你的 pull request。然后它会根据检测到的信号采取行动，而不等待你的提示。例如，连接 [Slack](https://cursor.com/docs/integrations/slack.md)，把协调 Agent 指向一个 bug 报告频道，每次有 bug 进来它就会委派一次修复。

当你要求时，协调 Agent 会创建订阅。一旦至少有一个订阅，聊天输入框上方会出现一个 **Listening** 胶囊。点击它即可看到 Project 正在监听的每个事件，例如频道中发布的消息、仓库中的 PR 活动、分支上的 CI 运行，或每天上午 8:00 这样的计划。不再需要某个订阅时，从同一列表中移除它。

## 相关页面

- [Agents Window](https://cursor.com/docs/agent/agents-window.md)
- [Cloud Agents](https://cursor.com/docs/cloud-agent.md)
- [Subagents](https://cursor.com/docs/subagents.md)
- [Automations](https://cursor.com/docs/cloud-agent/automations.md)
- [Slack 集成](https://cursor.com/docs/integrations/slack.md)

---

## 站点地图

[所有文档页面概览](/llms.txt)
