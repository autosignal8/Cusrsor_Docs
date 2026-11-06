> 原文：https://cursor.com/docs/integrations/jetbrains

# JetBrains

通过 [Agent Client Protocol](https://agentclientprotocol.com/)（ACP），在 IntelliJ IDEA、PyCharm、WebStorm 以及其他 JetBrains IDE 中使用 Cursor 的 AI Agent。

ACP 让你留在 JetBrains IDE 中，由 Cursor 处理 Agent 驱动的开发。你可以使用来自 OpenAI、Anthropic、Google 和 Cursor 的前沿模型。

## 前提条件

- 付费的 [Cursor 方案](https://cursor.com/docs/models-and-pricing.md)
- 已启用 [AI Assistant](https://plugins.jetbrains.com/plugin/22282-ai-assistant) 插件的 JetBrains IDE（2025.1+）

## 开始使用

### 打开 AI Chat 插件

在 JetBrains IDE 中打开 AI Chat 面板。你可以在右侧边栏中找到它，或通过 **View** > **Tool Windows** > **AI Chat** 打开。

### 从 ACP 注册表安装 Cursor

在 AI Chat 面板中，打开 Agent 提供方列表，选择 **Add Agent from Registry**。搜索 **Cursor** 并安装。

### 身份验证

安装之后，选择 Cursor 作为你的 Agent 提供方。

### 开始编码

在 AI Chat 面板中发送提示词。Cursor 的 Agent 会读取你的项目、编辑文件、运行终端命令，并直接在你的 JetBrains IDE 中创建代码。

## 你能获得什么

JetBrains IDE 中的 Cursor ACP 提供许多在其他 Cursor 界面上同样可用的 Agent 能力。

- **模型选择**：从适合你的任务的[前沿模型](https://cursor.com/docs/models-and-pricing.md)中选择。不同模型更擅长不同类型的工作；可按需在它们之间切换。
- **文件编辑**：Agent 会读取并写入项目中的文件，更改会反映在 JetBrains 编辑器中。
- **终端命令**：Agent 在 IDE 的集成终端中运行 shell 命令。

## 工作原理

Cursor ACP 使用 [Agent Client Protocol](https://agentclientprotocol.com/)，这是一个把 AI Agent 连接到 IDE 的开放标准。你的 JetBrains IDE 充当 ACP 客户端，Cursor 的 Agent 充当服务器。

当你发送提示词时，AI Chat 插件会通过 ACP 把它转发给 Cursor 的 Agent。Agent 处理你的请求，读取项目文件，并把编辑和终端命令流式传回 IDE。

## 定价

Cursor ACP 使用与你的 Cursor 订阅相同的按用量计费。详情见[定价](https://cursor.com/docs/models-and-pricing.md)。

## 相关内容

### ACP 参考

完整的 ACP 协议细节、传输方式和客户端示例

### 模型

可用的模型及其能力

---

## 站点地图

[所有文档页面概览](/llms.txt)
