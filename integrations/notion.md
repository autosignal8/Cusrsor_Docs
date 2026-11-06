> 原文：https://cursor.com/docs/integrations/notion

# Notion

把 Cursor 连接到 Notion，这样你可以把规格、任务和评论委派给 Agent。由于该集成基于 Cursor SDK 构建，每个 Agent 都使用驱动 Cursor 的同一套运行时、harness 和模型。

设置、聊天、@提及和任务分配都在 Notion 中进行。Cursor 在安全、沙箱化的 [Cloud Agent](https://cursor.com/docs/cloud-agent.md) 虚拟机中运行 Agent，因此当你处理其他工作时，它会在后台继续工作。

参见 Notion 的完整文档，见[此处](https://www.notion.com/help/connect-cursor-to-notion)。

Notion 中的 Cursor 处于 beta，面向拥有 Notion Business 或 Enterprise 方案的所有 Cursor 用户提供。

## 开始使用

### 要求

在 Notion 中连接 Cursor 之前，请确认你具备：

- 已将 GitHub 连接到你的 Cursor 账号，用于仓库访问和 pull request
- 一个 Cursor User API Key，在设置过程中创建

Cursor 在 Notion 中的用量通过你现有的账号计费，不另收费。

### 安装

1. 在 Notion 中，前往侧边栏里的 Agents → New Agent → Cursor。
2. 为你的 Agent 选择一个起点：

- 从默认模板开始：常见任务的模板（例如 Code Q&A 和 Bug Triage）可以帮助你针对特定工作流定制 Cursor。
- 从零开始：使用标准的 Cursor harness 设置你自己的指令、触发器和连接。

3. 从你的[仪表盘](https://cursor.com/dashboard/api)创建或取出 Cursor API key，并填入 Notion。
4. 添加 Agent 需要的页面、数据库和 Notion 空间，例如你的工程任务数据库和规格页面。
5. 保存该 Agent 并开始工作。

Cursor 现已连接，你的 Agent 可以从任务或 Agent 聊天中编写代码并打开 pull request。

### 权限

1. 该连接绑定到你的个人 API key。这意味着 Cursor 的访问和活动关联的是你个人的 Cursor 账号，而不是整个工作区。了解更多 →
2. 仓库、环境和连接来自你的 Cursor Agent 设置。请在 Cursor 仪表盘中配置这些内容。
3. 每个 Cursor Agent 只能访问你在 Notion 中共享给它的内容。权限在 Notion 中按 Agent 设置，不会从启动某次运行的人那里继承。

## 如何使用

把 Cursor 用到已经放在 Notion 中的任务看板、规格文档和其他上下文上。

如果你试图在 Cursor 没有权限的工作区中使用它，你应当设置一个弹窗来授予它访问权限。

### 提及 Agent

从一份规格文档或页面开始。留下一条评论并 @提及 Cursor。让它实现这份规格、完善计划，或回答一个问题。

Cursor 会在评论中回复。点击 **View chat** 打开完整对话。

第一次在某个页面上提及该 Agent 时，请在出现提示时批准页面访问。

示例提示词：

- 「为这份规格制定计划。等待批准。」
- 「审阅这篇文档，并标出任何不清楚的地方。」
- 「把这些内容变成一份具体任务清单，然后为每一项打开 PR。」

### 分配任务

在任务数据库或看板上，把任务分配给 Cursor。该 Agent 会：

- 在工作过程中更新任务状态
- 以评论形式发布进度
- 链接到它创建的任何页面或 PR

## 排查问题

### Agent 无法访问页面或数据库

- 确认该页面或数据库列在 **Tools & access** 中，而不只是在指令中被提及。
- 确认该页面没有被限制为仅限该 Agent 不属于的某个组访问。
- 如果该页面是私有的，请直接与该 Agent 共享。

### GitHub 连接失败，或 Agent 无法推送

- 确认该 token 具有 **Contents: read/write** 和 **Pull requests: read/write** 权限范围。
- 检查你的 GitHub 组织是否要求 SSO 或组织批准。
- 确认设置中的默认仓库正确。
- 如果 token 已过期，请重新生成，然后重新连接。

### 我 @提及它时，Agent 没有触发

- 确认 **agent mentioned** 触发器已开启。
- 在**评论**中提及该 Agent，而不是在页面正文中。评论最为可靠。
- 确认该 Agent 可以访问你提及它的那个页面。

## 常见问题

### 计费如何运作？

Cursor 在 Notion 中的用量通过你现有的 Cursor 账号计费，方式与 [Cloud Agent](https://cursor.com/docs/cloud-agent.md) 用量相同。所有 Cursor 用户都可以使用，不另收费。你还需要 Notion Business 或 Enterprise 方案。

### 我可以在哪里查看 Cursor 的活动？

结果会出现在 Agent 所运行的 Notion 页面或任务上。你也可以在 Notion 的 Agent 视图中，或在 Cursor 中找到你的聊天会话。

### 从 Notion 启动的 Cursor 运行是否遵守零数据保留（ZDR）？

是的。Agent 运行会遵守你在 Cursor 中已有的隐私设置。

---

## 站点地图

[所有文档页面概览](/llms.txt)
