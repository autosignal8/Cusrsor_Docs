> 原文：https://cursor.com/docs/integrations/cursor-blame

# Cursor Blame

了解 AI 如何参与你的代码库，有助于审查、合规和调试。本页说明 Cursor Blame 如何在 git 历史中呈现 AI 与人类的作者身份。

## 概览

Cursor Blame 在传统 git blame 之上增加了 AI 归属，因此你可以看出哪些是 AI 生成的，哪些是人类编写的。它在 [Enterprise 方案](https://cursor.com/contact-sales?source=docs-cursor-blame)上提供，帮助你在审查或回看代码时了解每一行的来源：

- **Tab**：由 Cursor 的 Tab 建议生成或接受的代码
- **Agent**：由 Agent 生成的代码，并带有模型归属
- **Human**：由开发者直接编写的代码

把鼠标悬停在归属于 AI 的行上，即可查看对话摘要。点击即可打开带有详细 AI 归属的完整提交视图。

## 工作原理

Cursor Blame 会分析已提交的代码，以确定 AI 归属。当你查看文件或提交时，它会取回归属数据，标明哪些行来自 AI 辅助、使用了哪些模型，以及产生这些代码的对话。

### AI 归属

一眼看出哪些行来自 AI 辅助，哪些是手工编写的。

### 模型跟踪

Agent 生成的代码会显示是哪个模型产生的，帮助你了解 AI 工具在整个代码库中的使用情况。

### 对话上下文

查看产生 AI 生成代码的对话摘要，并附有指向完整提交详情的链接。

### 贡献构成

在提交视图中查看每位贡献者（AI 模型和人类）的百分比归属。

## 使用 Cursor Blame

### 启用 Cursor Blame

Cursor Blame 在团队级别默认关闭。团队管理员可以在 [Team settings](https://cursor.com/dashboard/settings) 中为所有团队成员启用它。

### 行标注

1. 打开 Command Palette（Cmd+Shift+P），并搜索 "Cursor Blame: Toggle editor decorations"

**编辑器内的幽灵文本**会显示：

- **Author**：提交该更改的团队成员
- **Subject**：提交消息
- **Time**：更改发生的时间（相对时间）

**把鼠标悬停在幽灵文本上**可以看到：

- 带有 AI 共同作者标识的作者
- 对话摘要（针对归属于 AI 的行）
- 提交统计（文件、新增、删除）

**打开提交视图**可以看到：

- 按来源划分的完整 AI 贡献构成
- Composer、每个 Agent 模型以及人类编辑各自的百分比归属
- 带有摘要的相关对话

### 文件 blame 视图

1. 在编辑器中打开一个文件
2. 右键并从上下文菜单中选择 **Cursor Blame > Toggle file blame**，或使用 Command Palette（Cmd+Shift+P）搜索 "Cursor Blame: Open File Blame"
3. 文件 blame 视图会在每一行旁边显示归属

## 隐私方面的考虑

为了提升性能，Cursor Blame 会在本地缓存归属数据。当你查看文件和提交时，数据会从 Cursor 的服务器获取。对话摘要按需获取，显示的是简短描述，而不是完整的对话历史。

## 要求

- Enterprise 方案
- 包含由 Cursor 跟踪的更改的 Git 仓库

### 在 Enterprise 上获取 Cursor Blame

与我们的团队交流，为你的组织启用能识别 AI 的 git blame。

---

## 站点地图

[所有文档页面概览](/llms.txt)
