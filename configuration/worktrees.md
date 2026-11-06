> 原文：https://cursor.com/docs/configuration/worktrees

# Worktrees

本页描述的 UI 原生 Worktrees 功能仅在 Agents Window 中可用。在 IDE 中，请使用下面的 Worktree Skills 命令。

[Media](/docs-static/images/configuration/worktrees/cursor-worktrees-2.mp4)

Worktrees 让 Agent 在隔离的 Git 检出中工作。每个任务都有自己的文件、依赖和改动，而你的主检出保持不变。

当你希望在同一个仓库上启动多个 Agent 且互不冲突时，使用 Worktrees。

## 在 Agents Window 中创建 worktree

从 Agents Window 启动 Agent 或把 Agent 移入 worktree 时，Cursor 会为该 Agent 创建单独的检出。Agent 在 worktree 内继续任务，因此改动与主检出隔离。

Agent 完成后，在 Agents Window 中查看结果。你可以继续在 worktree 中工作，从该检出创建一次提交或 PR，或把结果带回主工作区。

## worktree 设置如何工作？

你可以用 `.cursor/worktrees.json` 自定义 worktree 设置。Cursor 在 Agents Window、IDE 或 [Cursor CLI](https://cursor.com/docs/cli/using.md#cli-worktrees) 中创建 worktree 时会检查此文件。

Cursor 按以下顺序查找 `.cursor/worktrees.json`：

1. 在 worktree 路径中
2. 在项目的根路径中

### 配置选项

`worktrees.json` 文件支持三个设置键：

- **`setup-worktree-unix`**：适用于 macOS 和 Linux 的命令或脚本路径。在 Unix 系统上，它优先于 `setup-worktree`。
- **`setup-worktree-windows`**：适用于 Windows 的命令或脚本路径。在 Windows 上，它优先于 `setup-worktree`。
- **`setup-worktree`**：适用于所有操作系统的通用回退。

每个键接受以下二者之一：

- **shell 命令数组**：在 worktree 中按顺序执行
- **字符串文件路径**：相对于 `.cursor/worktrees.json` 的脚本文件路径

## 示例设置配置

### 使用命令数组

#### Node.js 项目

```json
{
  "setup-worktree": [
    "npm ci",
    "cp $ROOT_WORKTREE_PATH/.env .env"
  ]
}
```

我们不建议把依赖符号链接到 worktree 中。这可能在主 worktree 中引起问题。请改用 `bun`、`pnpm` 或 `uv` 这类快速的包管理器。

#### 带虚拟环境的 Python 项目

```json
{
  "setup-worktree": [
    "python -m venv venv",
    "source venv/bin/activate && pip install -r requirements.txt",
    "cp $ROOT_WORKTREE_PATH/.env .env"
  ]
}
```

#### 带数据库迁移的项目

```json
{
  "setup-worktree": [
    "npm ci",
    "cp $ROOT_WORKTREE_PATH/.env .env",
    "npm run db:migrate"
  ]
}
```

#### 构建并链接依赖

```json
{
  "setup-worktree": [
    "pnpm install",
    "pnpm run build",
    "cp $ROOT_WORKTREE_PATH/.env.local .env.local"
  ]
}
```

### 使用脚本文件

对于更复杂的设置，引用脚本文件而不是内联命令：

```json
{
  "setup-worktree-unix": "setup-worktree-unix.sh",
  "setup-worktree-windows": "setup-worktree-windows.ps1",
  "setup-worktree": [
    "echo 'Using generic fallback. For better support, define OS-specific scripts.'"
  ]
}
```

把脚本放在 `.cursor/` 目录中，与 `worktrees.json` 放在一起。

**setup-worktree-unix.sh**（Unix 和 macOS）：

```bash
#!/bin/bash
set -e

# Install dependencies
npm ci

# Copy environment file
cp "$ROOT_WORKTREE_PATH/.env" .env

# Run database migrations
npm run db:migrate

echo "Worktree setup complete!"
```

**setup-worktree-windows.ps1**（Windows）：

```powershell
$ErrorActionPreference = 'Stop'

# Install dependencies
npm ci

# Copy environment file
Copy-Item "$env:ROOT_WORKTREE_PATH\.env" .env

# Run database migrations
npm run db:migrate

Write-Host "Worktree setup complete!"
```

### 特定操作系统的配置

你可以为不同操作系统提供不同的设置命令：

```json
{
  "setup-worktree-unix": [
    "npm ci",
    "cp $ROOT_WORKTREE_PATH/.env .env",
    "chmod +x scripts/*.sh"
  ],
  "setup-worktree-windows": [
    "npm ci",
    "copy %ROOT_WORKTREE_PATH%\\.env .env"
  ]
}
```

### 调试

如果要调试 worktree 设置，打开编辑器中的 Output 面板并选择 `Worktrees Setup`。

## Cursor 如何发现已有的 worktree？

Cursor 3.5 会为机器上的 worktree 根目录以及每个工作区子目录保留一个修改时间检查点。启动时，除非这些时间戳证明自上次发现以来没有任何变化，否则 Cursor 会重新扫描文件系统。这样可以避免漏掉 Cursor 关闭期间创建的新 worktree，并去掉了旧的 `worktree.discoveryComplete` 标志。

## Worktrees 清理

本节中的清理行为适用于 Cursor 3.5 及更高版本。

Cursor 可以自动清理较旧的 worktree，以限制磁盘占用。清理按间隔运行，并保留最新的 worktree，数量不超过为整台机器配置的上限；设备上的每个工作区都计入这同一个上限。

```json
{
  "cursor.worktreeCleanupIntervalHours": 6,
  "cursor.worktreeMaxCount": 25
}
```

使用这些机器范围的设置来控制清理：

- **`cursor.worktreeCleanupIntervalHours`**：Cursor 检查旧 worktree 的频率。如果上次成功运行早于该间隔，Cursor 3.5 会在重启后安排一次延迟清理以追上进度。
- **`cursor.worktreeMaxCount`**：Cursor 在清理较旧 worktree 之前保留的最大数量。默认上限是每台机器 25 个 worktree，所有工作区都计入同一个限制。

Cursor 在每次清理时都会重新发现 worktree 根目录，因此在管理器之外创建的 worktree（例如由 `/worktree` Skills 或 `git worktree add` 创建的 worktree）也有资格被删除。当创建 worktree 会超过上限时，Cursor 会对一连串事件做防抖，并立即开始清理，而不是等待下一个间隔。

## IDE 中的 Worktree Skills

在 IDE 中，你可以使用 `/worktree` 和 `/best-of-n` 命令在隔离的 worktree 中运行任务。

### 用 `/worktree` 做一次隔离运行

当你希望 Cursor 在单独的检出中完成该聊天的其余部分时，用 `/worktree` 开始任务。

- 让实验性编辑远离主检出
- 运行安装、构建和测试，而不打扰当前分支
- 在有简单清理路径的情况下做有风险的重构

```text
/worktree fix the failing auth tests and update the login copy
```

在很多情况下，你可以直接从 worktree 提交并推送。让 Agent：

```text
Commit and push these changes, then open a PR
```

如果你想把改动带入主检出以便测试，使用 `/apply-worktree`。当你用完隔离检出后，使用 `/delete-worktree`。

如果你想查看仓库中的所有 worktree，运行：

```bash
git worktree list
```

### 用 `/best-of-n` 比较多个模型

`/best-of-n` 同时在多个模型上运行同一任务。每次运行都有自己的 worktree，因此候选结果彼此隔离，也与主检出隔离。

```text
/best-of-n sonnet,gpt,composer fix the flaky logout test
```

在以下情况使用它：

- 在同一提示词上比较不同模型
- 为困难的改动尝试多种方法
- 在应用任何内容之前选出最强的结果

`/best-of-n` 只比较各次运行。它不会替你把改动合并回主检出。选出优胜者之后，你可以直接从 worktree 提交并推送，或使用 `/apply-worktree` 把改动带入主检出。


---

## 站点地图

[所有文档页面概览](/llms.txt)
