> 原文：https://cursor.com/docs/cli/overview

# Cursor CLI

Cursor CLI 让你直接在终端里与 AI Agent 交互，以编写、审查和修改代码。无论你偏好交互式终端界面，还是用于脚本和 CI 流水线的打印自动化，CLI 都在你工作的地方提供编程协助。

## 入门

```bash
# Install (macOS, Linux, WSL)
curl https://cursor.com/install -fsS | bash

# Install (Windows PowerShell)
irm 'https://cursor.com/install?win32=true' | iex

# Run interactive session
agent
```

[Media](https://ptht05hbb1ssoooe.public.blob.vercel-storage.com/assets/uploads/plan-mode.mp4)

## 交互模式

与 Agent 开始一段对话式会话，以描述你的目标、审查建议的改动并批准命令：

```bash
# Start interactive session
agent

# Start with initial prompt
agent "refactor the auth module to use JWT tokens"
```

## 模式

CLI 支持与编辑器相同的模式。用斜杠命令、键盘快捷键或 `--mode` 标志在模式之间切换。

| 模式 | 说明 | 快捷键 |
| :-------- | :----------------------------------------------------------- | :------------------------------------------ |
| **Agent** | 可使用全部工具，用于复杂编程任务 | 默认（不需要 `--mode` 值） |
| **Plan** | 在写代码前用澄清问题设计做法 | Shift+Tab、`/plan`、`--plan`、`--mode=plan` |
| **Ask** | 只读探索，不做改动 | `/ask`、`--mode=ask` |

## 非交互模式

在脚本、CI 流水线或自动化等非交互场景中使用打印模式：

```bash
# Run with specific prompt and model
agent -p "find and fix performance issues" --model "gpt-5"

# Use with git changes included for review
agent -p "review these changes for security issues" --output-format text
```

## 交接给 Cloud Agent

把对话推送到 [Cloud Agent](https://cursor.com/docs/cloud-agent.md)，以便在你离开时继续运行。在任意消息前加上 `&`：

```bash
# Send a task to Cloud Agent mid-conversation
& refactor the auth module and add comprehensive tests
```

在 [cursor.com/agents](https://cursor.com/agents) 的 Web 或移动端接手你的 Cloud Agent 任务。

## 会话

恢复先前的对话，以便在多次交互之间保持上下文：

```bash
# Open previous chats and resume one
agent ls

# Resume latest conversation
agent resume

# Continue the previous session
agent --continue

# Resume specific conversation
agent --resume="chat-id-here"
```

## 沙箱控制

用 `/sandbox` 或 `--sandbox <mode>` 标志（`enabled` 或 `disabled`）配置命令执行设置。通过交互菜单打开或关闭沙箱模式，并控制网络访问。设置会跨会话保留。

[Media](https://ptht05hbb1ssoooe.public.blob.vercel-storage.com/assets/uploads/sandox.mp4)

## sudo 密码提示

运行需要提升权限的命令，而无需离开 CLI。当命令需要 `sudo` 时，Cursor 会显示一个安全的、被遮罩的密码提示。你的密码通过安全的 IPC 通道直接传给 `sudo`；AI 模型永远看不到它。

---

## 站点地图

[所有文档页面概览](/llms.txt)
