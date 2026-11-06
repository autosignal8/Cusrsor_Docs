> 原文：https://cursor.com/docs/origin/cli/reference/pull-requests

# pull request 命令

Origin 目前以早期 beta 发布。你可以创建仓库，用 git 推送和拉取，从 GitHub 镜像，浏览并搜索代码，打开并合并 pull request，并与你的 Cursor 团队共享。

每个 `origin pr` 子命令及其选项。CLI 的其余部分见[命令](https://cursor.com/docs/origin/cli/reference/commands.md)。pull request 在 Origin 中如何工作，见 [Pull requests](https://cursor.com/docs/origin/pull-requests.md)。

## 指定 pull request

pull request 命令先从 `ORIGIN_REPO` 推断仓库，然后从当前检出中的 `origin` git 远程推断。用 `org/name` 格式的 `-R, --repo` 覆盖它。

大多数子命令接受可选的 `[target]`：数字（`13` 或 `#13`）、Origin pull request URL，或分支名称。省略它时，CLI 使用你当前分支上打开的或草稿 pull request：

```bash
origin pr view 13        # pull request 13
origin pr view my-branch # pull request for my-branch
origin pr view           # pull request for the current branch
```

## 子命令

| 子命令 | 说明 | 用法 |
| ----------------------- | ------------------------------------------------------------------------------------ | ------------------------------- |
| `create` \| `new` | 针对 Origin 仓库打开 pull request | `origin pr create -t "Fix CI"` |
| `list` \| `ls` | 列出仓库中的 pull request | `origin pr list` |
| `status` | 显示仓库中与你相关的 pull request | `origin pr status` |
| `view [target]` | 显示 pull request，以及可选的 diff、检查、评论或 Agent | `origin pr view 13` |
| `diff [target]` | 查看 pull request 的 diff | `origin pr diff` |
| `checks [target]` | 显示 pull request 的 CI 状态 | `origin pr checks` |
| `requirements [target]` | 显示 pull request 在可以合并之前还需要什么 | `origin pr requirements` |
| `watch [target]` | 等待，直到 CI 稳定、pull request 合并、其 head 移动，或评论匹配 | `origin pr watch` |
| `checkout [target]` | fetch 并检出 pull request 分支 | `origin pr checkout 13` |
| `edit [target]` | 编辑标题、正文、基分支、标签或审查者 | `origin pr edit -t "New title"` |
| `ready [target]` | 把草稿标为可以审查 | `origin pr ready` |
| `reviewer` | 列出、请求并移除被请求的审查者 | `origin pr reviewer list` |
| `review [target]` | 添加审查 | `origin pr review --approve` |
| `comment [target]` | 添加、编辑或删除讨论评论 | `origin pr comment -b "LGTM"` |
| `thread` | 列出、查看、解决、重新打开并回复审查线程 | `origin pr thread list` |
| `merge [target]` | 合并 pull request，或合并栈中直到并包括它的部分 | `origin pr merge` |
| `close [target]` | 关闭 pull request | `origin pr close` |
| `reopen [target]` | 重新打开已关闭的 pull request | `origin pr reopen` |
| `refresh [target]` | 从当前 ref 快照一个新版本 | `origin pr refresh` |

推送到 head 分支会自行快照一个新版本。仅当 `origin pr view` 或 `origin pr checks` 仍报告先前的 head 提交时，才运行 `origin pr refresh`。当已解析的提交已经与最新版本匹配时，它什么也不做。

`origin pr review` 提交正式审查。`origin pr comment` 添加讨论评论。`origin pr view --comments` 列出这两者。

## 创建

| 选项 | 说明 |
| --------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| `-t, --title <title>` | pull request 的标题 |
| `-b, --body <body>` | pull request 的正文 |
| `--description <text>` | pull request 的描述 |
| `-F, --body-file <path>` | 从文件读取正文。用 `-` 表示 stdin |
| `-H, --head <branch>` | 持有你的提交的分支（默认：当前分支） |
| `-B, --base <branch>` | 你希望代码合并进去的分支（默认：`--stack-on` 父级的 head 分支，否则为仓库默认分支） |
| `--stack-on <target>` | 把新的 pull request 堆叠到已有的一个上，按编号或分支 |
| `-l, --label <name>` | 按名称添加标签。可重复，或用逗号分隔 |
| `-r, --reviewer <reviewer>` | 按邮箱、`org/team` slug，或 `origin pr reviewer list` 打印的 ID 请求审查。可重复，或用逗号分隔 |
| `--push` | 当 head 分支尚不在远程上时，不经提示就推送它 |
| `--remote <remote>` | head 分支尚不在远程上时要推送到的远程（默认：`origin`） |
| `--status <status>` | 初始状态：`draft` 或 `open`（默认：`open`） |
| `-d, --draft` | 创建为草稿。`--status draft` 的别名 |
| `-f, --fill` | 用提交信息作为标题和正文 |
| `--fill-first` | 用第一次提交的信息作为标题和正文 |

新的 pull request 会以可以审查的状态打开。当作者的 **Never open PRs as drafts** 设置为打开时，即使带有 `--draft`，Origin 也会以就绪状态打开它，并且输出会说明这一点。

当你从父级的 head 分出分支，从而其提交在你的分支中时，`--stack-on` 效果最好。父级移动时 Origin 不会 rebase 你的分支；请运行 `origin stack restack`。见 [stack 命令](https://cursor.com/docs/origin/cli/reference/stacks.md)。

## 列表与状态

| 命令 | 选项 | 说明 |
| -------- | ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `list` | `-s, --state <state>` | 按状态筛选：`open`、`closed`、`merged`、`draft` 或 `all`（默认：`open`） |
| `list` | `--mine` | 只显示你撰写的 pull request |
| `list` | `-B, --base <branch>` | 按基分支筛选 |
| `list` | `-H, --head <branch>` | 按 head 分支筛选 |
| `list` | `--file <path>` | 按已更改的路径筛选。可重复；末尾的 `/` 匹配文件夹前缀 |
| `list` | `--commit <sha>` | 查找一个完整提交 SHA 的 pull request：分支顶端、已推送的版本，或合并提交。不能与其他筛选或 `--limit` 组合 |
| `list` | `-L, --limit <number>` | 要获取的条目数量上限（默认：`30`） |
| `list` | `--json <fields>` | 输出 JSON，包含你列出的字段，例如 `number,title,status` |
| `status` | `-c, --conflict-status` | 显示每个 pull request 的合并冲突状态 |

## 查看、diff 和检查

| 命令 | 选项 | 说明 |
| -------- | --------------------------- | ------------------------------------------------------------------------------ |
| `view` | `--diff` | 显示所选版本收集到的 patch diff |
| `view` | `--checks` | 包含可合并性和 CI 摘要 |
| `view` | `-c, --comments` | 显示评论和审查，以及每个线程的解决状态 |
| `view` | `--agents` | 显示挂到该 pull request 的 Cloud Agents |
| `view` | `--name-only` | 与 `--diff` 一起使用时，只打印已更改的文件路径 |
| `view` | `--exclude <glob>` | 排除匹配 glob 的路径。可重复 |
| `view` | `-w, --web` | 在浏览器中打开 pull request |
| `view` | `--change-version <number>` | 要显示的版本（默认：最新） |
| `view` | `--json <fields>` | 输出 JSON，包含你列出的字段。单独的 `--json` 会列出字段 |
| `diff` | `--patch` | 以 patch 格式显示 diff |
| `diff` | `--name-only` | 只打印已更改的文件路径 |
| `diff` | `-e, --exclude <glob>` | 排除匹配 glob 的文件。可重复 |
| `diff` | `--change-version <number>` | 要 diff 的版本（默认：最新） |
| `checks` | `--watch` | 监视检查直到它们完成 |
| `checks` | `--name <name>` | 仅名称匹配的检查，精确匹配或带 `*` 和 `?` 的 glob。可重复 |
| `checks` | `--log <job>` | 打印已完成的 Origin CI 作业的完整日志，名称与检查列表中的相同 |
| `checks` | `--json <fields>` | 输出 JSON，包含你列出的字段 |

`list`、`status`、`view`、`diff`、`checks`、`requirements` 和 `watch` 也接受 `--color <always|never|auto>`（默认：`auto`）。

要把 `origin pr diff` 限制到某些路径，在 `--` 之后列出它们。每一项都是相对于仓库根的文件、目录或 glob：

```bash
origin pr diff 123 -- src/ '*.md'
```

当每一项检查都通过时，`origin pr checks` 以 `0` 退出；当某项检查失败或没有检查时以 `1` 退出；检查仍在等待时以 `8` 退出。`--watch` 每 10 秒轮询一次，直到检查完成。

`--diff`、`--checks`、`--comments` 和 `--agents` 只影响 `origin pr view` 的纯文本输出。对于 JSON，改为请求你想要的字段，例如 `files`、`mergeability`、`ciState`、`comments`、`threads` 或 `agents`。

## 要求与监视

`origin pr requirements` 列出 pull request 在可以合并之前还需要什么：必需的 gate 及其在 head 提交上的状态、批准、每条 owner 规则及其现状、Code Review Gate，以及其栈中的阻碍。`--json <fields>` 接受 `headSha`、`mergeability`、`gates`、`codeReviewGates`、`approvals` 和 `ownerRules`。

`origin pr watch` 通过实时流跟踪 pull request，并在条件满足时退出：

| 选项 | 说明 |
| --------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| `--until <condition>` | `checks`（默认）、`merged`、`head:<sha>` 或 `comment:<regex>`。`comment:` 也会匹配监视开始之前发布的评论 |
| `--timeout <seconds>` | 经过这么多秒后放弃 |
| `-f, --follow` | 持续流式输出，直到你按 Ctrl-C，而不是在 CI 稳定或 pull request 合并或关闭时退出 |
| `--json <fields>` | 打印一个对象，描述监视如何结束：`outcome`、`exitCode`、`status`、`headSha`、`checks`、`comment` |

使用默认的 `--until checks` 时，检查通过或 pull request 合并时 `origin pr watch` 以 `0` 退出，某项检查失败或它关闭时以 `1` 退出。其他条件在满足时以 `0` 退出，否则以 `1` 退出，例如超时，或 pull request 先关闭。

## 检出、编辑与生命周期

| 命令 | 选项 | 说明 |
| ---------- | --------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| `checkout` | `-b, --branch <name>` | 要使用的本地分支名称（默认：pull request 的 head ref） |
| `checkout` | `--detach` | 以游离 HEAD 检出 |
| `checkout` | `-f, --force` | 把已有本地分支重置为最新状态 |
| `checkout` | `--remote <remote>` | 要从中 fetch 的远程（默认：`origin`） |
| `checkout` | `--stack` | fetch 并快进 pull request 栈中的每个分支，然后检出这一个。从不在本地合并或 rebase。与 `origin stack get` 相同 |
| `edit` | `-t, --title <title>` | 设置新标题 |
| `edit` | `-b, --body <body>` | 设置新正文 |
| `edit` | `-F, --body-file <path>` | 从文件读取新正文。用 `-` 表示 stdin |
| `edit` | `-B, --base <branch>` | 更改基分支 |
| `edit` | `--stack-on <target>` | 把此 pull request 堆叠到另一个上，按编号或分支 |
| `edit` | `--clear-stack` | 移除此 pull request 的栈父级。不要在父级合并之后使用它，因为那会拆开栈 |
| `edit` | `--add-label <name>` | 按名称添加标签。可重复 |
| `edit` | `--remove-label <name>` | 按名称移除标签。可重复 |
| `edit` | `--add-reviewer <reviewer>` | 向用户（按邮箱）或团队（按 `org/team` slug）请求审查。可重复，或用逗号分隔 |
| `ready` | `--undo` | 把 pull request 转回草稿 |
| `close` | `-c, --comment <body>` | 留下关闭评论 |
| `reopen` | `-c, --comment <body>` | 添加重新打开的评论 |
| `reopen` | `--reset-base` | 当此 pull request 的提交已经在基分支上时，把基分支重置到这些提交之前，然后重新打开 |
| `reopen` | `-y, --yes` | 跳过 `--reset-base` 确认 |

标签必须已经存在于仓库上。用 [`origin label list`](https://cursor.com/docs/origin/cli/reference/commands.md#labels) 列出它们。

## 合并

| 选项 | 说明 |
| ------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------- |
| `-s, --squash` | 把提交压缩成基分支上的一次提交。当仓库不允许 merge commit 时为默认 |
| `-m, --merge` | 创建 merge commit。当仓库允许 merge commit 时为默认 |
| `-t, --subject <text>` | merge commit 的主题，按原样使用。仅用于单个 pull request 的合并 |
| `-b, --body <text>` | merge commit 的正文，按原样使用。仅用于单个 pull request 的合并 |
| `-F, --body-file <path>` | 从文件读取 merge commit 正文。用 `-` 表示 stdin |
| `--auto` | 一旦要求满足就合并，然后返回。打开 merge-when-ready |
| `--disable-auto` | 关闭此 pull request 的 merge-when-ready |
| `--downstack` | 与 `--auto` 一起，也为栈中此 pull request 之下的每个打开的 pull request 打开 merge-when-ready |
| `--expect-head <number>=<sha>` | 除非 pull request `<number>` 仍在 `<sha>`，否则拒绝合并。可重复，并覆盖该合并会落地的每个栈成员 |
| `--match-head-commit <sha>` | 除非此 pull request 的 head 仍为 `<sha>`，否则拒绝合并。针对目标的 `--expect-head` 的 `gh` 写法 |
| `--base-ok` | 即使基分支是另一个 pull request 的 head 分支也合并：一个二者并未堆叠其上的打开的 pull request，或一个关闭但未合并的 pull request |
| `--bypass-rules` | 即使基分支上的规则失败也合并，通过你的 ruleset 绕过。绝不会让你越过你不能绕过的规则 |

没有 `--auto` 时，合并立即发生。对于打开的或草稿 pull request，`origin pr view`、`origin pr status` 和 `origin pr requirements` 会打印一行 `Merge-when-ready:`。

在堆叠的 pull request 上，合并 `#N` 会把从栈根直到 `#N` 的每个打开的 pull request 一起落地，成为一次提交。Origin 会先检查每个成员，如果任一成员被阻止，则什么也不落地。要逐个落地，每次合并最低的打开的 pull request，然后运行 `origin stack sync` 和 `origin stack push --all`，把其余的移到 trunk 上。`--auto` 让一个 pull request 进入 merge-when-ready，一旦它成为其栈中最低的打开成员，Origin 就会落地它。`--auto --downstack` 也会让它之下的每个打开的 pull request 进入 merge-when-ready。见 [stack 命令](https://cursor.com/docs/origin/cli/reference/stacks.md)。

在仓库要求的地方，依赖你的 ruleset 绕过的合并会被拒绝，直到你传入 `--bypass-rules`。

## 审查者、审查和评论

| 子命令 | 说明 | 用法 |
| ----------------------------- | -------------------------------------------------------------------------------------- | ------------------------------------------------ |
| `reviewer list [target]` | 列出 pull request 上被请求的审查者 | `origin pr reviewer list` |
| `reviewer request [target]` | 向用户、团队或服务账号请求审查。会再次请求已有审查者 | `origin pr reviewer request -r dana@acme.com` |
| `reviewer remove [target]` | 移除被请求的审查者 | `origin pr reviewer remove -r acme/platform` |
| `comment edit <comment-id>` | 替换你所写评论的正文 | `origin pr comment edit <comment-id> -b "Fixed"` |
| `comment delete <comment-id>` | 删除评论 | `origin pr comment delete <comment-id>` |

`reviewer request` 和 `reviewer remove` 接受 `-r, --reviewer`（必填）：邮箱、`org/team` slug，或 `origin pr reviewer list` 打印的 ID（`user_…`、`grp_…`、`sa_…`）。可重复，或用逗号分隔。评论 ID 来自 `origin pr comment`、`origin pr view --comments` 或 `origin pr thread list -c`。

| 命令 | 选项 | 说明 |
| ---------------- | ------------------------------- | ---------------------------------------------------- |
| `review` | `-a, --approve` | 批准 pull request |
| `review` | `-r, --request-changes` | 请求更改 |
| `review` | `-c, --comment` | 把审查作为评论提交 |
| `review` | `-b, --body <body>` | 审查正文 |
| `review` | `-F, --body-file <path>` | 从文件读取审查正文。用 `-` 表示 stdin |
| `review` | `--change-version <number>` | 正在审查的版本（默认：最新） |
| `comment` | `-b, --body <body>` | 评论正文 |
| `comment` | `-F, --body-file <path>` | 从文件读取评论正文。用 `-` 表示 stdin |
| `comment` | `--edit-last` | 编辑你最新的讨论评论 |
| `comment` | `--create-if-none` | 与 `--edit-last` 一起，当你没有评论时添加一条 |
| `comment` | `--delete-last` | 删除你最新的讨论评论 |
| `comment` | `-y, --yes` | 与 `--delete-last` 一起，跳过确认提示 |
| `comment edit` | `-b, --body`, `-F, --body-file` | 新的评论正文 |
| `comment delete` | `-y, --yes` | 跳过确认提示 |

`reviewer list` 接受 `--json <fields>`：`kind`、`id`、`name`、`slug` 和 `requestedAt`。

## 审查线程

每条审查评论都属于一个有自身解决状态的线程。线程 ID 以 `cth_` 开头。

| 子命令 | 说明 | 用法 |
| ------------------------------------- | --------------------------------------- | ------------------------------------------- |
| `thread list [target]` | 列出线程及其解决状态 | `origin pr thread list --unresolved` |
| `thread view <thread-id> [target]` | 显示一个线程及其每条评论 | `origin pr thread view cth_123` |
| `thread resolve <thread-id> [target]` | 解决线程 | `origin pr thread resolve cth_123` |
| `thread reopen <thread-id> [target]` | 重新打开已解决的线程 | `origin pr thread reopen cth_123` |
| `thread reply <thread-id> [target]` | 向线程添加评论 | `origin pr thread reply cth_123 -b "Fixed"` |

| 命令 | 选项 | 说明 |
| -------------- | --------------------------- | -------------------------------------------------------------------- |
| `list` | `--unresolved` | 只显示未解决的线程 |
| `list` | `-c, --comments` | 打印每条评论正文，而不是开头摘录 |
| `list` | `--change-version <number>` | 仅针对此版本提交的线程（默认：每个版本） |
| `list`, `view` | `--json <fields>` | 输出 JSON，包含你列出的字段，例如 `id,resolved,path` |
| `reply` | `-b, --body <body>` | 回复正文 |
| `reply` | `-F, --body-file <path>` | 从文件读取回复正文。用 `-` 表示 stdin |

以「所有线程已解决」为门禁的脚本可以使用 `origin pr thread list --unresolved --json id | jq length`。

## 获取帮助

每个子命令都接受 `--help`：

```bash
origin pr --help
origin pr create --help
origin pr thread --help
```

## 后续步骤

- [命令](https://cursor.com/docs/origin/cli/reference/commands.md)
- [stack 命令](https://cursor.com/docs/origin/cli/reference/stacks.md)
- [Pull requests](https://cursor.com/docs/origin/pull-requests.md)
- [克隆、推送与拉取](https://cursor.com/docs/origin/git.md)


---

## 站点地图

[所有文档页面概览](/llms.txt)
