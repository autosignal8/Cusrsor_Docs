> 原文：https://cursor.com/docs/origin/git

# 克隆、推送与拉取

Origin 目前以早期 beta 发布。你可以创建仓库，用 git 推送和拉取，从 GitHub 镜像，浏览并搜索代码，打开并合并 pull request，并与你的 Cursor 团队共享。

Origin 使用标准 git。在 [cursor.com/codebase](https://cursor.com/codebase) 打开仓库，选择绿色的 **Code** 下拉菜单，并复制克隆 URL。

## 用 HTTPS 克隆

**Code** 菜单包含 **HTTPS** 标签页：

```text
https://origin.cursor.com/{owner}/{repo}.git
```

示例：

```bash
git clone https://origin.cursor.com/acme/checkout.git
```

## 用 SSH 克隆

**SSH** 标签页显示 SSH 克隆 URL：

```text
git@origin.cursor.com:{owner}/{repo}.git
```

示例：

```bash
git clone git@origin.cursor.com:acme/checkout.git
```

SSH 使用登记在你的 Origin 账号上的公钥进行身份验证。用 [Origin CLI](https://cursor.com/docs/origin/cli/reference/commands.md#ssh-keys) 添加一把：

```bash
origin ssh-key add ~/.ssh/id_ed25519.pub -t work-laptop
```

第一次连接到 `origin.cursor.com` 时，会打印服务器的主机密钥指纹，并询问是否继续。在输入 `yes` 之前，把它与 [Origin 的 SSH 主机密钥指纹](https://cursor.com/docs/origin/git.md#ssh-host-key-fingerprints)对照。

## SSH 主机密钥指纹

SSH 客户端第一次连接到 Origin 时，会显示类似这样的提示：

```text
The authenticity of host 'origin.cursor.com (...)' can't be established.
ED25519 key fingerprint is SHA256:EgIVKsf8fXGd7oj12nE0juHrGGPU8QLVfpGM3ymunGM.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

Origin 提供一把主机密钥，ED25519。在回答 `yes` 之前，把提示中的指纹与下列值对照：

- `SHA256:EgIVKsf8fXGd7oj12nE0juHrGGPU8QLVfpGM3ymunGM`（ED25519）
- `MD5:15:84:8d:05:5f:4f:cd:3b:37:19:3b:19:c5:02:30:6f`（ED25519）

OpenSSH 默认打印 SHA256 形式。较旧的客户端，以及 `ssh -o FingerprintHash=md5`，打印 MD5 形式。如果提示显示的指纹不在这里，回答 `no`。该连接没有到达 Origin。

要跳过该提示，把 Origin 的公钥主机密钥添加到 `~/.ssh/known_hosts`。同一行也适用于 CI runner 的 `known_hosts` 文件：

```text
origin.cursor.com ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIFaMKo6HCtmngBwlSH2ATs8+A6eTr+cCON5RKZX/3/MO
```

Cursor 会在 [Origin 发行说明](https://cursor.com/docs/release-notes/origin.md)中提前宣布主机密钥轮换，并在密钥变更时更新本页。如果 SSH 客户端出现 `REMOTE HOST IDENTIFICATION HAS CHANGED` 警告，且没有已宣布的轮换，说明连接没有到达 Origin；请停止，不要更新 `known_hosts`。

## 用 Origin CLI 克隆

同一菜单有 **Origin CLI** 标签页，提供面向 CLI 的设置。请先安装并登录：

```bash
curl -fsSL https://downloads.cursor.com/origin/install.sh | sh
origin auth login
```

见[安装 Origin CLI](https://cursor.com/docs/origin/cli.md)。

## 身份验证

如果尚未登录，请在第一次通过 HTTPS 进行 git 操作之前用 Origin CLI 登录：

```bash
origin auth login
```

然后用 git 克隆、fetch、拉取或推送。通过 SSH 时，则由登记在你账号上的密钥进行身份验证。

## 为已有仓库添加远程

```bash
git remote add origin https://origin.cursor.com/{owner}/{repo}.git
git push -u origin main
```

在评估期间若要让 GitHub 和 Origin 并行：

```bash
git remote set-url --add --push origin https://github.com/acme/checkout.git
git remote set-url --add --push origin https://origin.cursor.com/acme/checkout.git
```

若要把 GitHub 的完整历史复制到 Origin，优先使用[镜像](https://cursor.com/docs/origin/mirror-github.md)。

## 从 GitHub 镜像的仓库

[从 GitHub 镜像](https://cursor.com/docs/origin/mirror-github.md)的仓库使用同一 HTTPS 克隆 URL：

```text
https://origin.cursor.com/{owner}/{repo}.git
```

GitHub 仍是事实来源。对此远程执行 `git push` 会进入 GitHub。Origin 在 GitHub 接受推送后更新。

GitHub 不可用时，用 `origin/*` 分支和 `origin push local` 在 Origin 副本上继续工作。对已经同步过的仓库，通过同一 HTTPS 克隆 URL 的读取仍可能可用。Git LFS batch 不会故障转移。GitHub 中断期间，`/local` 是受支持的写入路径。

## 镜像仓库上仅存在于 Origin 的分支

在镜像仓库上，`origin/*` 分支是你在 Origin 副本上的工作区。GitHub 不可用时用它们继续工作，或存放任何你只想留在 Origin 上的其他 git 状态。这些 ref 留在 Origin 上。它们既不会从 GitHub 同步过来，也不会同步到 GitHub。

Origin 不会同步名为 `origin` 或 `origin/...` 的 GitHub 分支。这是有意为之，因此同名的 GitHub 分支不能覆盖你用 `origin push local` 推送的内容。应当成为 GitHub pull request 的功能分支仍使用普通的 `git push origin`。

### `origin push local`

`origin push local` 写入那些仅存在于 Origin 的分支。它不能替代 `git push origin`。

`git push origin <branch>` 使用克隆 URL，在入站镜像上会落到 GitHub。`origin push local` 则改为执行以下操作：

1. 确认检出的 `origin` 远程是入站镜像的 Origin 仓库
2. 在 `https://origin.cursor.com/{owner}/{repo}.git/local` 创建或更新名为 `origin-local` 的 git 远程
3. 把该远程的 fetch 和 push refspec 限定为 `refs/heads/origin/*`
4. 把每个本地 `origin/*` 分支推送到 `/local`

Origin 不会改写名称。`origin/my-state` 存储为 `refs/heads/origin/my-state`。

```bash
git branch origin/my-state HEAD
origin push local
```

第一次运行之后，`git remote -v` 会包含：

```text
origin-local    https://origin.cursor.com/{owner}/{repo}.git/local (fetch)
origin-local    https://origin.cursor.com/{owner}/{repo}.git/local (push)
```

`git push origin-local` 和 `git fetch origin-local` 使用同一限定 refspec。`origin push local --dry-run` 只打印推送内容而不发送。`--remote <name>` 选择另一个远程名称。不要传入 `--remote origin` 或 `--remote github`；这些远程仍是以 GitHub 为上游的远程。

`origin` 远程必须已经指向 Origin：

```text
https://origin.cursor.com/{owner}/{repo}.git
```

`/local` 只存在于入站镜像。在直接于 Origin 上创建的仓库中，把任意分支推送到 `origin`。

`/local` 按仓库启用。如果 git 报告仓库未找到，说明该仓库尚未启用此端点。按 ref 出现的 `remote rejected` 消息表示服务器拒绝了 `origin/` 之外的 ref。

### 以 `origin/` 开头的分支名称

匹配的是 `origin/` 之后的路径段。恰好名为 `origin` 的分支是保留的，不能推送到 `/local`。`original/backup` 这样的名称也在该命名空间之外。

在入站镜像上：

- **推送到 `/local`。** 只接受 `origin/<name>` ref。其他一律拒绝，并提示把该 ref 推送到上游远程。
- **从 GitHub fetch。** 入站同步会跳过 `origin` 和 `origin/*`。这是有意为之：同名的 GitHub 分支不能覆盖你用 `origin push local` 推送的内容。
- **推送到克隆 URL。** `git push origin origin/my-state` 仍会把 `origin/my-state` 发送到 GitHub。Origin 在下次同步时不会导入该 GitHub 分支。
- **Pull request。** 镜像仓库上的 Cloud Agents 会从你推送到 GitHub 的普通分支打开 [GitHub pull request](https://cursor.com/docs/origin/pull-requests.md#mirrored-github-repositories)。`origin/*` 分支不在 GitHub 上，因此不能作为 GitHub pull request 的 head。

Git 也把 `origin/` 用于远程跟踪 ref：`origin/main` 表示 `origin` 远程上的分支 `main`。用显式名称创建本地分支（`git branch origin/my-state HEAD` 或 `git switch -c origin/my-state`），以免误检出远程跟踪分支。

### 我该何时使用 origin push local？

GitHub 不可用时，或每当你只想把 git 状态留在 Origin 副本上时。创建 `origin/...` 分支并运行 `origin push local`。对于你想放到 GitHub 上的工作，包括 pull request，运行 `git push origin`。

### Origin 会重命名 origin/ 分支吗？

不会。Origin 上的分支名称与本地名称相同。`git fetch origin-local` 把它们跟踪为 `origin-local/origin/<name>`，因为这是 git 命名远程跟踪 ref 的方式，并不是因为 Origin 去掉或改写了前缀。

### 如果 GitHub 上已经有名为 origin/something 的分支呢？

Origin 不会导入它。同步会跳过名为 `origin` 或 `origin/...` 的 GitHub 分支，以免它们覆盖你用 `origin push local` 推送的内容。

## 拉取最新内容

```bash
git pull origin main
```

## 故障排除

如果通过 HTTPS 克隆或推送失败，确认你已用 `origin auth login` 登录。见[安装 Origin CLI](https://cursor.com/docs/origin/cli.md)。

如果通过 SSH 克隆或推送失败，用 `origin ssh-key list` 确认你正在使用的密钥已在账号上，如果连接在主机密钥提示处被拒绝，请核对[主机密钥指纹](https://cursor.com/docs/origin/git.md#ssh-host-key-fingerprints)。

如果安装后 shell 提示 `command not found: origin`，把 `~/.local/bin` 加入 `PATH`（zsh：把 `export PATH="$HOME/.local/bin:$PATH"` 追加到 `~/.zshrc`，然后 `source ~/.zshrc`）。详情在 CLI 页面。


---

## 站点地图

[所有文档页面概览](/llms.txt)
