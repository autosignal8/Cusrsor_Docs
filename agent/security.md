> 原文：https://cursor.com/docs/agent/security

# Agent 安全

由于提示词注入、幻觉和其他问题，AI 的行为可能出乎意料。我们用护栏保护用户，限制 Agent 可以做什么。默认情况下，敏感操作需要你手动批准。本文说明我们的护栏，以及它们对你意味着什么。

这些控制和行为是我们的默认设置。我们建议保持启用。

## 第一方工具调用

Cursor 包含帮助 Agent 写代码的工具：读取文件、编辑文件、运行终端命令、搜索网络等等。

读取文件和搜索代码不需要批准。使用 [.cursorignore](https://cursor.com/docs/reference/ignore-file.md) 阻止 Agent 访问特定文件。可能暴露敏感数据的操作需要你的明确批准。

Agent 可以在不经批准的情况下修改工作区文件，配置文件除外。改动会立即保存到磁盘。请始终使用版本控制，以便你可以还原改动。配置文件（如工作区设置）需要先得到你的批准。

**警告：** 如果你启用了自动重载，Agent 的改动可能会在你审查之前就执行。

默认情况下，终端命令需要你的批准。要让受信任的调用在不提示的情况下运行，请配置 [Run Modes](https://cursor.com/docs/agent/security/run-modes.md)。它们从简单的允许列表一直到 **Auto-review** 分类器，并且是尽力而为的护栏，而不是硬性安全边界。

## 第三方工具调用

你可以使用 [MCP](https://cursor.com/docs/mcp.md) 连接外部工具。所有 MCP 连接都需要你的批准。批准 MCP 连接后，每次工具调用在运行前仍需要单独批准。你可以用 [MCP 允许列表](https://cursor.com/docs/reference/permissions.md#mcp-allowlist-format) 预先批准特定工具。

## 网络请求

攻击者可能利用网络请求窃取数据。我们的工具只向以下目标发出网络请求：

- GitHub
- 直接链接检索
- 网络搜索提供商

在默认设置下，Agent 不能发出任意网络请求。

## 工作区信任

Cursor 支持[工作区信任](https://code.visualstudio.com/docs/editing/workspaces/workspace-trust)，但默认禁用。启用后，它会提示你为新工作区在普通模式或受限模式之间选择。受限模式会破坏 AI 功能。对于不受信任的仓库，请改用基本的文本编辑器。

要启用工作区信任：

1. 打开你的用户 settings.json 文件
2. 添加以下配置：

   ```json
   "security.workspace.trust.enabled": true
   ```

组织可以通过 MDM 解决方案强制执行此设置。

## 负责任的披露

发现漏洞？请发送电子邮件至 [security-reports@cursor.com](mailto:security-reports@cursor.com)，附上详情和复现步骤。

我们会在 5 个工作日内确认漏洞报告。对于严重事件，我们会通过电子邮件通知所有用户。


---

## 站点地图

[所有文档页面概览](/llms.txt)
