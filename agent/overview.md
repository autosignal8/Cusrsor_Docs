> 原文：https://cursor.com/docs/agent/overview

# Cursor Agent

Agent 是 Cursor 的助手，可以独立完成复杂的编程任务、运行终端命令并编辑代码。在侧边栏中用 Cmd+I 打开。

进一步了解[Agent 如何工作](https://cursor.com/learn/agents.md)，以及它如何帮你更快地构建。

对于更大的一块工作，例如一个功能或一次迁移，创建一个 [Project](https://cursor.com/docs/agent/projects.md)：协调 Agent 规划工作，并把它委派给其他 Agent。

## Agent 如何工作

Agent 由三个部分组成：

1. **指令**：系统提示词，以及引导 Agent 行为的 [Rules](https://cursor.com/docs/rules.md)
2. **工具**：文件编辑、代码库搜索、终端执行等
3. **模型**：你为这项任务选择的 Agent 模型

Cursor 的 Agent 会为我们支持的每个模型编排这些部分，并针对每一种前沿模型专门调整指令和工具。新模型发布时，你可以专注于构建软件，由 Cursor 处理与模型相关的优化。

## 工具

工具是 Agent 的基本组成部分。它们用来搜索你的代码库和网络以找到相关信息、编辑文件、运行终端命令，以及做更多事情。

要了解工具调用在底层如何工作，参见[工具调用基础](https://cursor.com/learn/tool-calling.md)。

Agent 在一项任务中可以发起的工具调用次数没有限制。

### 搜索文件和文件夹

按名称搜索文件、读取目录结构，并在文件中查找精确的关键词或模式。

### 网络

生成搜索查询并执行网络搜索。

### 获取 Rules

按类型和描述检索特定的 [Rules](https://cursor.com/docs/rules.md)。

### 读取文件

智能读取文件内容。也支持图像文件（.png、.jpg、.gif、.webp、.svg），并把它们纳入对话上下文，供具备视觉能力的模型分析。

### 编辑文件

建议对文件的编辑并自动应用。

### 运行 shell 命令

执行终端命令并监视输出。默认情况下，Cursor 使用第一个可用的终端配置文件。

要设置首选的终端配置文件：

1. 打开命令面板（`Cmd/Ctrl+Shift+P`）
2. 搜索 “Terminal: Select Default Profile”
3. 选择你想要的配置文件

### 浏览器

控制浏览器以截图、测试应用并验证视觉改动。Agent 可以浏览页面、与元素交互，并捕获当前状态用于分析。详情见[浏览器文档](https://cursor.com/docs/agent/tools/browser.md)。

### 图像生成

根据文字描述或参考图像生成图像。可用于创建界面草图、产品素材，以及把架构图可视化。图像默认保存到项目的 `assets/` 文件夹，并在聊天中内联显示。

### 提出问题

在任务过程中提出澄清问题。在等待你回复时，Agent 会继续读文件、做编辑或运行命令。你的回答一到就会被纳入。

## Checkpoints

Checkpoints 会在 Agent 会话期间保存代码库的快照。Agent 在做出重大改动之前会自动创建它们，捕获所有已修改文件的状态。

如果 Agent 走错了方向，点击聊天时间线中的任意 Checkpoint，预览当时的文件，然后恢复，即可把所有文件还原到该状态。你也可以从先前请求上的 `Restore Checkpoint` 按钮恢复，或在悬停于一条消息时用 + 按钮恢复。恢复 Checkpoint 只会还原文件，不会从对话中删除消息。

Checkpoints 适用于探索性工作、复杂重构，以及你希望有安全回退点的迭代开发。

Checkpoints 存储在本地，并且与 Git 分开。只把它们用于撤销 Agent 的改动；永久的版本控制请使用 Git。

## 排队消息

Agent 工作时，你有两种跟它说话的方式。把消息加入队列，它会等当前任务完成。[立即发送后续消息](https://cursor.com/docs/agent/overview.md#steer-a-running-agent)，则会在 Agent 的下一次工具调用时引导当前这一轮。

[Media](/docs-static/images/agent/planning/agent-queue.mp4)

### 使用队列

1. Agent 工作时，输入你的下一条指令
2. 按 Enter 把它加入队列
3. 消息会按顺序出现在当前任务下方
4. 拖动以按需调整排队消息的顺序
5. Agent 在完成后会按顺序处理它们

### 键盘快捷键

Agent 工作时：

- 按 Enter 将消息加入队列（它会等到 Agent 完成当前任务）
- 按 Cmd+Enter 立即发送，绕过队列

### 立即发送

使用 Cmd+Enter 立即发送时，你的消息会附加到聊天中最近的一条用户消息上，并马上处理，而不在队列中等待。

- 你的消息会附在工具结果上并立即发送
- 这让紧急的后续消息响应更快
- 当你需要打断或重定向 Agent 当前的工作时使用它

### 引导正在运行的 Agent

你可以在 Agent 工作时发送后续消息来引导它，而不打断它。输入后续内容并点击 **Send now**，或按两次 Enter。消息会在 Agent 的下一次工具调用时送达，而不是在动作进行到一半时切断，从而保留进行中的工作，并让 Agent 保持在任务上。

此功能现已在 [cursor.com/agents](https://cursor.com/agents) 提供，并正在 [Agents Window](https://cursor.com/docs/agent/agents-window.md) 中推出。按 Tab 则把消息排到这一轮之后。

在 [CLI](https://cursor.com/docs/cli/overview.md) 中，Agent 工作时按 Enter 会在一个安全边界引导当前运行；再按一次 Enter 会打断这一轮。

## 侧边聊天

打开一个[侧边聊天](https://cursor.com/help/ai-features/side-chats.md)，以便提问或调查一条旁支，而不打断主对话。在聊天输入框中输入 `/side` 或 `/btw`，后面可以跟上你的问题，或使用聊天面板顶部的加号按钮。

侧边聊天是一段持久的 Agent 对话。它把父线程当作隐藏的参考上下文，并保留自己的记录。在主线程中 @ 提及侧边聊天，即可把那段上下文拉回来。

## 对话搜索

从 [Agents Window](https://cursor.com/docs/agent/agents-window.md) 搜索过往的 Agent 记录。在 Mac 上按 Cmd+K，或在 Windows 和 Linux 上按 Ctrl+K 打开命令面板，然后跨对话搜索。Cursor 会建立本地搜索索引。

要在当前打开的对话中查找文本，在 Mac 上按 Cmd+F，或在 Windows 和 Linux 上按 Ctrl+F。

## 用 /goal 设定目标

Agent 把每条消息都当作一件新工作。使用 `/goal` 给 Agent 一个长期目标，让它一直做到完全完成：

```text
/goal fix all flaky tests and make CI green
```

在 [CLI](https://cursor.com/docs/cli/overview.md) 中，Ctrl+C 会暂停该目标。当你希望 Agent 遵循一套打法时，把目标与 [Custom Mode](https://cursor.com/docs/agent/prompting.md#custom-modes) 搭配使用；或与内置的 [`/loop`](https://cursor.com/docs/skills.md#built-in-cursor-skills) Skill 搭配，按固定或可变间隔运行一段提示词或一个 Skill。如果你不指定固定间隔，由 Agent 决定何时醒来。想结束时停止循环。

`/goal` 正在推出。如果你看不到它，在新聊天中试一次。

---

## 站点地图

[所有文档页面概览](/llms.txt)
