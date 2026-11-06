> 原文：https://cursor.com/docs/origin/cli

# 安装 CLI

Origin 目前以早期 beta 发布。你可以创建仓库，用 git 推送和拉取，从 GitHub 镜像，浏览并搜索代码，打开并合并 pull request，并与你的 Cursor 团队共享。

Origin CLI（`origin`）与[CLI](https://cursor.com/docs/cli/overview.md)下记录的 Cursor Agent CLI（`agent`）是分开的。

## macOS、Linux 和 Windows（WSL）

用一条命令安装 Origin CLI：

```bash
curl -fsSL https://downloads.cursor.com/origin/install.sh | sh
```

安装程序把二进制放在 `~/.local/bin/origin`。如果 shell 提示 `command not found: origin`（例如 `zsh: command not found: origin`），把该目录加入 `PATH`。

对于 zsh：

```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

对于 bash：

```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

## Windows

在 PowerShell 中（原生 Windows，不是 WSL）：

```powershell
irm https://downloads.cursor.com/origin/install.ps1 | iex
```

安装程序把 `origin.exe` 放在 `%LOCALAPPDATA%\cursor\bin`，并把该目录加入你的用户 `PATH`。打开一个新终端即可使用。

## 验证

安装之后，验证 Origin CLI 工作正常：

```bash
origin --version
```

## 登录

登录你的 Cursor 账号：

```bash
origin auth login
```

用拥有 Origin 访问权限的 Cursor 账号完成浏览器流程。

登录还会设置 git credential helper，因此针对 Origin 远程的 `git push` 和 `git pull` 无需进一步设置即可工作。

## 管理仓库

CLI 可以创建和删除 Origin 仓库，因此你不必打开 Web UI：

```bash
origin repo create my-project
origin repo delete acme/my-project
```

不带斜杠的 `origin repo create` 会在你账号的命名空间中创建仓库。`origin repo delete` 始终接受完整的 `org/name`。

Cursor Agent 可以驱动相同的命令。让 Agent 把项目存到 Origin，它可以安装 CLI、登录、创建仓库、设置远程并推送。要让另一个编程 Agent 学会该 CLI，运行 `origin agent setup`。它会安装一个 skill，让该 Agent 去阅读 `origin agent prompt`，那是一份针对你所安装版本撰写的用法指南。

每个命令和标志见[命令参考](https://cursor.com/docs/origin/cli/reference/commands.md)、[pull request 命令](https://cursor.com/docs/origin/cli/reference/pull-requests.md)和 [stack 命令](https://cursor.com/docs/origin/cli/reference/stacks.md)。

## 更新

要把 Origin CLI 更新到当前版本：

```bash
origin update
```

## 后续步骤

- [命令](https://cursor.com/docs/origin/cli/reference/commands.md)
- [pull request 命令](https://cursor.com/docs/origin/cli/reference/pull-requests.md)
- [stack 命令](https://cursor.com/docs/origin/cli/reference/stacks.md)
- [创建仓库](https://cursor.com/docs/origin/create-repository.md)
- [克隆、推送与拉取](https://cursor.com/docs/origin/git.md)
- [镜像 GitHub 仓库](https://cursor.com/docs/origin/mirror-github.md)
- [浏览与搜索](https://cursor.com/docs/origin/browse.md)
- [Agents 与自动化](https://cursor.com/docs/origin/agents.md)


---

## 站点地图

[所有文档页面概览](/llms.txt)
