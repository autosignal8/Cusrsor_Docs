> 原文：https://cursor.com/docs/agent/agent-review

# Agent Review

Agent Review 在 Cursor 内部对你的本地改动运行一次专门的代码审查。

## 设置

要配置 Agent Review：

1. 打开 **Cursor Settings**
2. 进入 **Agents**
3. 找到 **Agent Review** 并配置你的偏好

从 Cursor 3.11 起，此设置移至 **Git & PRs** > **Pull Requests**。

Agent Review 也会读取仓库中 `BUGBOT.md` 文件里的规则。要设置这些规则文件，参见 [Bugbot 文档](https://cursor.com/docs/bugbot.md)。

你可以把它设为在每次 Agent 任务之后自动运行，或保持手动，由你自己触发。

## 运行审查

有三种方式开始一次审查：

- **自动**：在设置中启用后，每次产生提交时 Agent Review 都会运行。
- **斜杠命令**：在 Agent 窗口输入框中输入 `/agent-review`，按需触发审查。
- **Source Control 选项卡**：打开 Source Control 选项卡并运行 Agent Review，将所有本地改动与你的主分支比较。这能发现整组改动中的问题，而不只是最新的一处编辑。

[Media](https://ptht05hbb1ssoooe.public.blob.vercel-storage.com/assets/changelog/changelog-2-1-1.mp4)

## 审查深度

Agent Review 支持两种深度。按你需要的审查细致程度来选择。

| 深度 | 速度 | 成本 | 适合 |
| :--- | :--- | :--- | :--- |
| **Quick** | 快 | 低 | 小 diff、格式改动，或快速的健全性检查 |
| **Deep** | 慢 | 高 | 复杂逻辑、对安全敏感的代码，或大规模重构 |

---

## 站点地图

[所有文档页面概览](/llms.txt)
