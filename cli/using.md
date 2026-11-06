> 原文：https://cursor.com/docs/cli/using

# 在 CLI 中使用 Agent

## 模式

CLI 支持与编辑器相同的[模式](https://cursor.com/docs/agent/overview.md)。使用斜杠命令或 `--mode` 标志切换模式。

### Plan Mode

使用 Plan Mode 在写代码前设计做法。Agent 会提出澄清问题来完善你的计划。

- 按 Shift+Tab 轮换到 Plan Mode
- 使用 `/plan` 切换到 Plan Mode
- 以 `--plan` 或 `--mode=plan` 标志启动

### Ask Mode

使用 Ask Mode 在不做改动的情况下探索代码。Agent 会搜索你的代码库并给出答案，而不会编辑文件。

- 使用 `/ask` 切换到 Ask Mode
- 以 `--mode=ask` 标志启动

## 提示词

清晰陈述意图有助于获得最好的结果。例如，你可以使用提示词「不要写任何代码」，确保 Agent 不会编辑任何文件。这在实现之前规划任务时通常很有帮助。

Agent 具备文件操作、搜索、运行 shell 命令和访问网络的工具。

## MCP

Agent 支持 [MCP (Model Context Protocol)](/marketplace)，以扩展功能和集成。CLI 会自动检测并遵循你的 `mcp.json` 配置文件，启用你为编辑器配置的同一批 MCP 服务器和工具。

## ACP

Agent 也支持 [ACP (Agent Client Protocol)](https://cursor.com/docs/cli/acp.md)，用于自定义客户端集成。使用 `agent acp` 将 Cursor CLI 作为 ACP 服务器运行，通过 `stdio` 进行 JSON-RPC 消息通信。

## Rules

CLI Agent 支持与编辑器相同的 [Rules 系统](https://cursor.com/docs/rules.md)。你可以在 `.cursor/rules` 目录中创建规则，为 Agent 提供上下文和指导。这些规则会根据其配置自动加载并应用，让你可以为项目的不同部分或特定文件类型定制 Agent 的行为。

CLI 也会读取项目根目录下的 `AGENTS.md` 和 `CLAUDE.md`（如果存在），并与 `.cursor/rules` 一起作为规则应用。

## 与 Agent 协作

### 导航

可以使用上箭头（ArrowUp）访问之前的消息，并在其中循环切换。

### 输入快捷键

- Shift+Tab — 在模式之间轮换（Agent、Plan、Ask）
- Shift+Enter — 插入换行而不是提交，便于编写多行提示词。
- Ctrl+D — 退出 CLI。遵循标准 shell 行为，需要按两次才能退出。
- Ctrl+J 或 +Enter — 插入换行的通用替代方式，在所有终端中都可用。

Shift+Enter 在 iTerm2、Ghostty、Kitty、Warp 和 Zed 中可用。tmux 用户请改用 Ctrl+J。配置选项和故障排除见[终端设置](https://cursor.com/docs/cli/reference/terminal-setup.md)。

### 审查

用 Ctrl+R 审查改动。按 i 添加后续指令。用 ArrowUp/ArrowDown 滚动，用 ArrowLeft/ArrowRight 切换文件。

### 选择上下文

用 @ 选择要纳入上下文的文件和文件夹。运行 `/summarize` 可以释放上下文窗口中的空间。`/compress` 仍是其别名。

### Custom Modes

从 `/` 菜单中选择一个 [Skill](https://cursor.com/docs/skills.md)，按 Enter 将其附加到一条消息。改为按 Option+Enter，可以将其作为 Custom Mode 调用，并保持激活直到你退出。

## 交接给 Cloud Agent

把对话推送到 [Cloud Agent](https://cursor.com/docs/cloud-agent.md)，让它在你离开时继续运行。在任意消息前加上 `&`，即可发送到云端。在 [cursor.com/agents](https://cursor.com/agents) 的 Web 或移动端接手。

```bash
# Send a task to Cloud Agent mid-conversation
& refactor the auth module and add comprehensive tests
```

## CLI Worktrees

传入 `-w` 或 `--worktree [name]`，让 Agent 在新的 Git worktree 中运行，而不是直接编辑当前检出。Cursor 会把这些检出创建在 `~/.cursor/worktrees/<reponame>/<name>` 下，与编辑器创建的 worktree 放在一起。如果省略 `name`，Cursor 会生成一个。

Cursor 使用与编辑器 worktree 相同的保留规则清理 CLI worktree。清理设置和限制见[旧的 worktree 如何清理？](https://cursor.com/docs/configuration/worktrees.md#worktrees-cleanup)。

需要明确的仓库根目录时，搭配 `--workspace <path>`。否则 CLI 使用当前工作目录。`--worktree` 只改变 Agent 在该项目内编辑文件的位置。

```bash
# Create a temporary worktree from the current repository with a generated name
agent --worktree "upgrade the test runner and fix any broken snapshots"

# Create a named worktree from another repository
agent --workspace ~/src/my-app --worktree auth-fix "fix the flaky auth test and open a PR"
```

## 历史

用 `--resume [thread id]` 从已有线程继续，以加载先前的上下文。

要恢复最近的对话，使用 `agent resume`、`--continue` 或 `/resume` 斜杠命令。

你也可以运行 `agent ls` 打开先前的聊天并恢复其中一个。

## 命令批准

在运行终端命令之前，CLI 会请你批准（y）或拒绝（n）执行。

## 非交互模式

使用 `-p` 或 `--print` 以非交互模式运行 Agent。这会把响应打印到控制台。

在非交互模式下，你可以以非交互方式调用 Agent。这让你可以把它集成到脚本、CI 流水线等场景中。

你可以把它与 `--output-format` 结合，控制输出格式。例如，使用 `--output-format json` 获得结构化输出，便于在脚本中解析；或使用 `--output-format text` 获得 Agent 最终响应的纯文本输出。

在非交互模式下，Cursor 拥有完整的写入权限。

---

## 站点地图

[所有文档页面概览](/llms.txt)
