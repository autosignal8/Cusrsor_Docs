> 原文：https://cursor.com/docs/origin/mirror-github

# 镜像 GitHub 仓库

Origin 目前以早期 beta 发布。你可以创建仓库，用 git 推送和拉取，从 GitHub 镜像，浏览并搜索代码，打开并合并 pull request，并与你的 Cursor 团队共享。

镜像会把 GitHub 仓库复制到 Origin，并在 GitHub 仓库变化时保持 Origin 更新。GitHub 仍是事实来源。当代码已经在 GitHub 上，而你希望对该历史使用 Origin 的浏览、搜索和 Agent 工作流时，使用这种方式。

## 前提条件

- 拥有 [Origin 访问权限](https://cursor.com/docs/origin.md#who-can-access)的 Cursor 账号，方案为 Premium 或更高、Teams、Enterprise，或旧版付费 Cursor 方案
- [Cursor GitHub app](https://cursor.com/docs/integrations/github.md)已连接到拥有该仓库的组织或账号
- 对你要同步的仓库拥有 GitHub 管理员权限（启用镜像所必需）

## 同步仓库

1. 打开 [cursor.com/codebase](https://cursor.com/codebase)
2. 选择 **Sync from GitHub**
3. 选择 GitHub 组织和仓库
4. 确认同步

同步完成后，打开 Origin 副本以浏览文件和提交历史。

[媒体](/docs-static/images/origin/sync-from-github.mp4)

之后可以在仓库的 **Settings → General** 标签页确认同步状态。已同步的仓库会把 Origin 显示为镜像，把 GitHub 显示为来源。

## 会同步的内容

| 包含 | 不包含 |
| --------------------------------------------- | ------------------------------------ |
| Git 历史、分支和标签 | GitHub Issues |
| 你可以在 Origin 上浏览和搜索的代码 | GitHub Actions 工作流和密钥 |
| 你可以在 Cursor 中审查的 GitHub pull request |                                      |
| 持续更新，使 Origin 保持最新 |                                      |

Issues 和 CI 配置留在 GitHub。Origin 也不会同步名为 `origin` 或 `origin/...` 的 GitHub 分支，因此它们不能覆盖你仅在 Origin 上的工作区。

## 克隆 URL

绿色的 **Code** 按钮复制的是每个 Origin 仓库都使用的同一 HTTPS 远程：

```text
https://origin.cursor.com/{owner}/{repo}.git
```

推送到此远程会进入 GitHub。Origin 在 GitHub 接受推送后更新。见[克隆、推送与拉取](https://cursor.com/docs/origin/git.md)。

## 仅存在于 Origin 的 `origin/` 分支

`origin/` 分支是你在 Origin 副本上的工作区。GitHub 不可用时用它们继续工作，或存放任何你只想留在 Origin 上的其他 git 状态。用 `origin push local` 推送它们，而不是 `git push origin`。

Origin 不会同步名为 `origin` 或 `origin/...` 的 GitHub 分支。这是有意为之，因此同名的 GitHub 分支不能覆盖你用 `origin push local` 推送的内容。

见[镜像仓库上仅存在于 Origin 的分支](https://cursor.com/docs/origin/git.md#forge-local-branches-on-mirrored-repos)。

## 镜像之后

- 在 [cursor.com/codebase](https://cursor.com/codebase) 浏览和搜索
- 当你想从 Origin 做本地检出时，用绿色的 **Code** 按钮克隆 Origin 远程
- 把 [Cloud Agents](https://cursor.com/docs/origin/agents.md) 挂到 Origin 仓库。在镜像仓库上，这些 Agent 会打开 GitHub pull request
- 在 Cursor 中审查 [GitHub pull request](https://cursor.com/docs/origin/pull-requests.md#mirrored-github-repositories)

## 如果 GitHub 不可达

用 `origin/` 分支和 `origin push local` 在 Origin 副本上继续工作。GitHub 中断期间，`/local` 是受支持的写入路径。

对已经同步过的仓库，通过同一 HTTPS 克隆 URL 的读取仍可能可用。Origin 提供它已经持有的副本。Git LFS batch 不会故障转移。尚未完成首次同步的仓库仍然需要 GitHub。

## 与 GitHub 分离

要停止同步，打开 **Settings → General**，在 **Danger Zone** 下选择 **Detach from GitHub**。这会停止同步，并把 Origin 副本转换为独立的 Origin 托管仓库：Origin 成为事实来源，推送到 Origin 远程不再流向 GitHub。你的 GitHub 仓库不受影响。

## 同步延迟

如果浏览内容看起来过时：

- 确认 GitHub app 仍有访问权限（[GitHub 集成](https://cursor.com/docs/integrations/github.md)）
- 确认你是源仓库的 GitHub 管理员
- 从 [cursor.com/codebase](https://cursor.com/codebase) 重新运行 **Sync from GitHub**，或在 **Settings → General** 查看同步状态

## 何时不要镜像

如果你只想在 GitHub PR 上获得自动审查评论，[Bugbot](https://cursor.com/docs/bugbot.md) 可以做到这一点，无需迁移存储。当你希望对该 GitHub 历史使用 Origin 的浏览、搜索和 Cloud Agents 时，再进行镜像。


---

## 站点地图

[所有文档页面概览](/llms.txt)
