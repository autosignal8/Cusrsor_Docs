> 原文：https://cursor.com/docs/origin/cli/reference/stacks

# stack 命令

Origin 目前以早期 beta 发布。你可以创建仓库，用 git 推送和拉取，从 GitHub 镜像，浏览并搜索代码，打开并合并 pull request，并与你的 Cursor 团队共享。

stack 是一串 pull request，其中每一个都建立在它下面的那一个之上。Origin 记录每个 pull request 的父级，因此只把一个分支基于另一个分支是不够的。`origin stack` 使 stack 的本地分支彼此之间以及与 Origin 保持同步。单个 pull request 见 [pull request 命令](https://cursor.com/docs/origin/cli/reference/pull-requests.md)。

## 打开一个 stack

先打开最底部的 pull request。然后从基于其父级分支的分支创建每个子级，并用 `--stack-on` 打开它：

```bash
origin pr create --stack-on 122 --title "Add widget colors" --body-file body.md
origin pr edit 123 --stack-on 122
origin stack view
```

`origin pr edit --stack-on` 更改 pull request 的父级，`origin pr edit --clear-stack` 移除它。在分支有 pull request 之前，`origin stack track --onto <parent>` 在本地记录其父级。

## 子命令

接受 `[target]` 的命令接受 pull request 编号（`13` 或 `#13`）、Origin pull request URL 或分支，并默认使用当前分支。`-R, --repo` 选择仓库，与[每条命令](https://cursor.com/docs/origin/cli/reference/commands.md#targeting-a-repository)上一样。

| 子命令 | 说明 | 用法 |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------ | ------------------------------------- |
| `view [target]` | 把 stack 显示为树，依据此克隆中保存的分支父级 | `origin stack view` |
| `get [target]` | fetch 每个成员，并创建或快进其本地分支。与 `origin pr checkout --stack` 相同 | `origin stack get` |
| `sync` | 快进 trunk，删除已合并和已关闭 pull request 的本地分支，并 restack 堆叠在它们之上的内容 | `origin stack sync` |
| `restack` | 把每个成员 rebase 到其父级的本地顶端上，从根到叶 | `origin stack restack` |
| `restack-remote [target]` | 让 Origin rebase 该 stack 中打开的 pull request 并移动它们的分支 | `origin stack restack-remote` |
| `push` | 带 lease 推送当前分支，或用 `--all` 推送每个成员 | `origin stack push --all` |
| `move [branch]` | 把分支移到新父级上，然后 restack | `origin stack move --onto main` |
| `delete [branch]` | 从其 stack 中移除分支。其子级移到它的父级并被 restack | `origin stack delete add-sizes` |
| `delete-branch [number]` | 删除已合并或已关闭 pull request 在 Origin 上和此克隆中的 head 分支 | `origin stack delete-branch 122` |
| `track [branch]` | 在分支有 pull request 之前，在本地记录其父级 | `origin stack track --onto add-sizes` |
| `untrack [branch]` | 当分支没有 pull request 时，停止跟踪其父级 | `origin stack untrack` |
| `freeze [branch]` | 把分支标为冻结，使 stack 命令永不改写它 | `origin stack freeze add-sizes` |
| `unfreeze [branch]` | 让 stack 命令再次可以改写已冻结的分支 | `origin stack unfreeze add-sizes` |
| `up [member]` | 检出当前分支的子级 | `origin stack up` |
| `down` | 检出当前分支的父级 | `origin stack down` |
| `top [member]` | 检出 stack 的顶部 | `origin stack top` |
| `bottom` | 检出紧挨 trunk 之上的成员 | `origin stack bottom` |

`track`、`untrack`、`freeze` 和 `unfreeze` 只更改此克隆记录的内容。它们不发起网络调用。

## 保持 stack 最新

Origin 从不会替你 rebase 分支。在你向 stack 中的分支推送之前，运行：

```bash
origin stack get
origin stack restack
origin stack push --all
```

`get` fetch 并快进每个成员。`restack` 把每个成员 rebase 到其父级上，从根到叶，`push` 带 lease 推送。当 `push` 因为 lease 失败而拒绝某个成员时，说明别人移动了该分支，因此从 `get` 重新开始。

`restack` 把 stack 的底部 rebase 到你的本地 trunk 分支上，而不是远程的。`origin stack get` 会在本地 trunk 落后时快进它，`origin stack sync` 会 fetch 并快进它，因此先运行其中之一，以便 restack 到最新的 trunk。

发生冲突的 restack 会停在该成员上，工作树中有 git 的冲突标记。解决文件，运行 `git add` 和 `git rebase --continue`，然后再次运行 `origin stack restack`。`origin stack restack --dry-run` 预览冲突，而不移动任何内容。

当每个需要移动的成员都有 pull request 时，`origin stack restack-remote` 改为让 Origin rebase 它们并移动它们的分支，与 Review 中的 **Restack** 按钮相同。此克隆中没有任何变化，因此之后运行 `origin stack get` 以取得新的 head。发生冲突时，Origin 什么也不移动，并指出该成员。在本地 restack 以解决它。

父级合并之后，保持其子级的父级不变：Origin 会把子级的基分支移到 trunk 并保持该 stack。运行 `origin stack sync` 或 `origin stack restack`，然后运行 `origin stack push --all`。合并之后不要运行 `origin pr edit --clear-stack`，因为它会拆开 stack。

## 查看、获取与同步

| 命令 | 选项 | 说明 |
| ------- | ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| `view` | `--refresh` | 同时显示来自 Origin 的标题和状态，并更新此克隆的分支父级以匹配。别人更改了 stack 之后使用它 |
| `get` | `-f, --force` | 把已分叉或超前的本地分支重置为 Origin 的 |
| `get` | `--take-remote` | 凡是此克隆的父级与 Origin 不同的地方，采用 Origin 的父级，丢弃 `push` 尚未发布的本地父级更改 |
| `get` | `--keep-local` | 自上次 `get` 以来父级在这里和 Origin 上都变了的地方，保留你的，以便 `push` 发布 |
| `get` | `--remote <remote>` | 要从中 fetch 的远程（默认：`origin`） |
| `sync` | `--remote <remote>` | 要从中 fetch trunk 的远程（默认：`origin`） |
| `sync` | `--restack` | 之后把每个本地 stack restack 到其父级和已更新的 trunk 上（默认：`true`）。`--no-restack` 让分支留在原处 |
| `sync` | `--dry-run` | 报告 `sync` 将删除和移动的内容。不 fetch、不写入、不 restack |

## Restack

默认情况下，`origin stack restack` 会 rebase 当前分支所属 stack 的每个成员。这些选项收窄范围，或改变它移动分支的方式：

| 选项 | 说明 |
| ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--only` | 只 restack 当前分支 |
| `--upstack` | restack 当前分支及其之上的每个成员。其下的成员保持不动 |
| `--downstack` | restack 当前分支之下的成员以及该分支本身，根优先。其上的成员保持不动 |
| `--dry-run` | 按成员报告 rebase 或合并是否能干净应用。不写入、不推送 |
| `--merge` | 把每个父级合并进其子级，而不是 rebase，从根到叶，并把每个被移动的成员作为快进推送。适用于可能不能强制推送的克隆 |
| `--take-trunk` | 与 `--merge` 一起，当 stack 中还没有任何成员需要更新的 trunk 时，也把更新的 trunk 合并进根 |
| `--remote <remote>` | `--merge` restack 从中读取 trunk 并推送到的远程（默认：`origin`） |
| `--drop-change-ids` | 改写带有 jj `change-id` 头的提交，`git rebase` 会丢掉该头。没有此选项时，`restack` 会拒绝它们 |

`--merge` restack 只在以下情况把 trunk 合并进根：其父级已合并、它之上的某个成员已有更新的 trunk 提交，或你传入 `--take-trunk`。绝不要把 trunk 直接合并进父级仍打开的子级，因为它的 diff 会充满父级所没有的 trunk 提交。

`origin stack restack-remote [target]` 从 stack 根开始，一直 restack 到 `target`（默认：当前分支所属 stack 顶部的打开 pull request）。`--dry-run` 报告 Origin 对每个 pull request 的预览，并且什么也不移动。

## 推送、移动与删除

| 命令 | 选项 | 说明 |
| ---------------- | ------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| `push` | `--all` | 推送 stack 中每个有本地分支的成员，从根到叶 |
| `push` | `--remote <remote>` | 要推送到的远程（默认：`origin`） |
| `move` | `--onto <branch>` | 新父级：另一个成员，或 trunk。必填 |
| `move` | `--only` | 只移动该分支。其子级留在它的旧父级上 |
| `delete` | `-c, --close` | 同时关闭其打开的 pull request |
| `delete` | `-f, --force` | 即使可能丢失工作时也删除：打开的 pull request 保持打开，未推送的提交被丢弃 |
| `delete-branch` | `--merged` | 删除 stack 中每个已合并或已关闭的成员，而不只是一个 pull request |
| `delete-branch` | `--keep-local` | 保留本地分支。删除 Origin 上的分支及其远程跟踪 ref，并停止跟踪本地分支 |
| `delete-branch` | `--remote <remote>` | 要移除其跟踪 ref 的远程（默认：`origin`） |
| `move`, `delete` | `--drop-change-ids` | 改写带有 jj `change-id` 头的提交 |

`origin stack move` 在移动之后 restack。运行 `origin stack push` 来发布它。

## 跟踪与冻结

| 命令 | 选项 | 说明 |
| --------- | ------------------- | ------------------------------------------------------------------------------------------------------ |
| `track` | `--onto <branch>` | 父分支（默认：该分支的上游，当上游是另一个分支时；否则在没有歧义时为 trunk） |
| `track` | `--remote <remote>` | 其分支和 `HEAD` 算作已知的远程（默认：`origin`） |
| `untrack` | `-f, --force` | 同时取消跟踪堆叠在它之上的每个分支 |

`get` 和 `push` 会把你不是作者的分支记录为冻结的，并且没有任何 stack 命令会改写冻结的分支。仅当其作者请你接手时，才运行 `origin stack unfreeze <branch>`。

## 导航

`up`、`down`、`top` 和 `bottom` 检出当前分支所属 stack 中的相邻成员。它们接受 `--remote <remote>`，即要从中 fetch 的远程（默认：`origin`）。

当 stack 在当前分支之上分叉时，`up` 和 `top` 会询问要落到哪个成员。把该成员作为分支或 pull request 编号传入，可以是位置参数，也可以用 `--to`。`--non-interactive`，或 `ORIGIN_NON_INTERACTIVE=1`，会列出选项并以 `2` 退出，而不是询问。

## JSON 与退出码

除 `up`、`down`、`top` 和 `bottom` 之外的每个子命令都接受 `--json <fields>`。`get`、`restack`、`push`、`move` 和 `delete` 上的 `--verbose` 会把 git 自己的输出回显到 stderr。

当 stack 命令拒绝且什么也不更改时，它们以 `2` 退出，例如工作树不干净时。加上 `--json`，拒绝会打印 `{"refused": "<reason>"}`。

## 合并 stack

合并 stack 中的 pull request 会把从 stack 根直到它的每个打开的 pull request 在一次提交中落地。`origin pr merge --auto --downstack` 也会让它之下的每个打开的 pull request 进入 merge-when-ready。见[合并](https://cursor.com/docs/origin/cli/reference/pull-requests.md#merge)。

## 后续步骤

- [pull request 命令](https://cursor.com/docs/origin/cli/reference/pull-requests.md)
- [命令](https://cursor.com/docs/origin/cli/reference/commands.md)
- [Pull requests](https://cursor.com/docs/origin/pull-requests.md)


---

## 站点地图

[所有文档页面概览](/llms.txt)
