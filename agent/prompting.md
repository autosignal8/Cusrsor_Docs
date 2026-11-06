> 原文：https://cursor.com/docs/agent/prompting

# 给 Agent 写提示

在聊天输入框中用文字提示词指挥 Agent。你可以附加上下文、图像和语音，并随时切换模型。

## @ 提及

在聊天输入框中输入 `@`，把特定上下文附加到提示词。在 `@` 之后开始输入，Cursor 会显示匹配的建议。

- **文件与文件夹**：`@auth.ts` 或 `@src/components/`，用于包含文件或文件夹（选中文件夹后输入 `/` 可继续向里导航）
- **终端**：`@Terminals`，把终端输出作为上下文
- **聊天**：`@Chats`，引用先前对话中的上下文
- **Git diff**：`@Commit (Diff of Working State)` 表示未提交的改动，`@Branch (Diff with Main)` 表示你整个分支的 diff
- **浏览器**：`@Browser`，附加内置浏览器中的上下文

当你知道哪些文件相关时，使用 @ 提及。如果不确定哪些文件重要，可以跳过——Agent 会通过自己的搜索找到相关文件。

## Custom Modes

在聊天输入框中输入 `/` 来调用一个 [Skill](https://cursor.com/docs/skills.md)。按 Enter 会把该 Skill 附加到一条消息上，随着对话继续，它会逐渐淡出。把任意 Skill 用作 Custom Mode，可以让 Agent 在工作时一直聚焦于它。

从 `/` 菜单中选中该 Skill，然后改为按 Option+Enter（Mac）或 Alt+Enter（Windows）。你也可以在 Skill 条目上选择 **Use as Mode**。进入模式后，即使 Agent 工作数小时，该 Skill 也会在每一轮保持在上下文中，直到你退出该模式。

Custom Modes 适合那些描述工作方式、而不是一次性任务的 Skill。在你浏览多个文件时保持一份代码审查清单处于激活状态，或在整个功能期间保持 `/tdd` 这样的团队打法。

Custom Modes 在 [Agents Window](https://cursor.com/docs/agent/agents-window.md) 和 [CLI](https://cursor.com/docs/cli/overview.md) 中可用。任何带有有效 frontmatter 块的 Skill 都可以支撑一个模式，可选的 `icon` 和 `color` frontmatter 字段用来设定模式徽章的样式。参见[把 Skill 用作 Custom Mode](https://cursor.com/docs/skills.md#using-a-skill-as-a-custom-mode)。

## 图像输入

把图像附加到提示词，为界面工作、调试和设计实现提供视觉上下文。

- **拖放**图像文件到聊天输入框
- 用 Cmd+V **从剪贴板粘贴**，包括截图

这对实现设计稿、调试视觉问题，以及引用错误信息或堆栈跟踪（而无需手工转录）很有用。

## 语音输入

点击聊天输入框中的麦克风图标，用口述代替打字。自然地说，包含文件名和函数名这类技术细节，并在发送前检查转写。

## 上下文用量

每个聊天与模型共享一个固定的上下文窗口。随着你添加文件、运行工具和交换消息，这些 token 会被填满。当窗口接近满时，Cursor 会把对话中较旧的部分压缩成摘要，以便为新对话留出空间。

提示词输入框旁边的上下文环会让你一眼看到窗口有多满。点击该环可打开明细托盘，按类别显示已用 token 总量：

- **系统提示词**：Cursor 为模型内置的指令
- **工具**：Agent 可用的每个工具的定义
- **Rules**：包含在提示词中的项目规则与用户规则
- **Skills**：注入系统上下文的 Skill 描述
- **MCP**：来自已连接 MCP 服务器的指令与目录
- **Subagents**：Agent 可以启动的 Subagent 类型的文档
- **已摘要的对话**：较早轮次的压缩摘要
- **对话**：你的消息、Agent 的回复，以及工具结果

把鼠标悬停在条形中的一段或列表中的一行上，即可高亮该类别。

## 更换模型

使用聊天输入框顶部的模型选择下拉菜单切换模型，或按 Cmd / 在模型之间循环。更改对当前对话此后生效。在 **Cursor Settings > Models** 中设置默认模型。

- **更快的模型**适合快速编辑和常规任务
- **能力更强的模型**更适合复杂推理和多文件重构

你可以在对话中途切换模型，例如较快的模型完成了探索，但实现需要更深的推理。完整列表见[模型与定价](https://cursor.com/docs/models-and-pricing.md)。

---

## 站点地图

[所有文档页面概览](/llms.txt)
