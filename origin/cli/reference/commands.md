> 原文：https://cursor.com/docs/origin/cli/reference/commands

# 命令

Origin 目前以早期 beta 发布。你可以创建仓库，用 git 推送和拉取，从 GitHub 镜像，浏览并搜索代码，打开并合并 pull request，并与你的 Cursor 团队共享。

除 pull request 和 stack 之外的每个 `origin` 命令及其选项；这两类有各自的页面：[pull request 命令](https://cursor.com/docs/origin/cli/reference/pull-requests.md)和 [stack 命令](https://cursor.com/docs/origin/cli/reference/stacks.md)。要在终端里看到相同信息，运行 `origin --help` 查看命令列表，或运行 `origin <group> --help` 查看一个组。要安装 CLI，见[安装 CLI](https://cursor.com/docs/origin/cli.md)。

Origin CLI（`origin`）与[CLI](https://cursor.com/docs/cli/overview.md)下记录的 Cursor Agent CLI（`agent`）是分开的。

## 全局选项

这些选项适用于任何命令：

| 选项 | 说明 |
| ------------ | --------------------------------- |
| `--version` | 显示已安装的版本 |
| `-h, --help` | 显示当前命令的帮助 |

CLI 从环境读取 API 端点和凭据，即 `CURSOR_ORIGIN_ENDPOINT`、`CURSOR_AUTH_TOKEN` 和 `CURSOR_API_KEY`。见[环境变量](https://cursor.com/docs/origin/cli/reference/commands.md#environment-variables)。

## 命令组

| 命令 | 说明 | 用法 |
| --------------------------------------------------------------------- | ----------------------------------------------------------------------------------- | ----------------------------------- |
| `auth` | 登录、登出，并配置 git 身份验证 | `origin auth login` |
| `repo` | 创建、查看、编辑、克隆、镜像和删除仓库，并管理其访问权限 | `origin repo create my-project` |
| [`pr`](https://cursor.com/docs/origin/cli/reference/pull-requests.md) | 创建、审查并合并 pull request | `origin pr create` |
| [`stack`](https://cursor.com/docs/origin/cli/reference/stacks.md) | 查看、restack 并推送 pull request 栈及其本地分支 | `origin stack restack` |
| `label` | 管理仓库定义的标签 | `origin label list` |
| `ruleset` | 管理 Origin ruleset（合并时和推送时）。别名：`rs` | `origin ruleset list` |
| `namespace` | 管理对 Origin 命名空间的访问。别名：`ns` | `origin namespace access list acme` |
| `app` | 管理 Origin app、其签名密钥和 webhook 投递 | `origin app list` |
| `ssh-key` | 管理登记到你的 Origin 账号的 SSH 密钥 | `origin ssh-key list` |
| `api <endpoint>` | 向 `api.cursor.com/v1/origin` 的 Origin REST API 发起经过身份验证的请求 | `origin api /repos/{owner}/{repo}` |
| `agent` | 为 AI Agent 打印用法指南，或安装一个 skill，把 Agent 指向该指南 | `origin agent prompt` |
| `completion` | 设置 bash 或 zsh 的 tab 补全 | `origin completion >> ~/.zshrc` |
| `update` | 把 `origin` 更新到你所在通道上的当前版本 | `origin update` |
| `config` | 管理配置，包括发布通道 | `origin config get-channel` |

## 指定仓库

大多数命令从当前检出中的 `origin` git 远程推断仓库。用 `org/name` 格式的 `-R, --repo` 覆盖它，或为整个 shell 设置 `ORIGIN_REPO`。`-R` 优先于 `ORIGIN_REPO`，`ORIGIN_REPO` 优先于 git 远程：

```bash
origin pr list -R acme/checkout
```

## 身份验证

管理你的会话和 git credential helper。

| 子命令 | 说明 | 用法 |
| ----------- | --------------------------------------------------------- | ----------------------- |
| `login` | 通过浏览器登录并配置 git | `origin auth login` |
| `setup-git` | 为 Origin 主机重新配置 git credential helper | `origin auth setup-git` |
| `status` | 显示当前身份验证方式和账号 | `origin auth status` |
| `token` | 打印 `origin` 随请求发送的 token | `origin auth token` |
| `logout` | 清除已存储的凭据 | `origin auth logout` |

| 命令 | 选项 | 说明 |
| ----------- | ----------------- | ----------------------------------------------------------------------------------- |
| `login` | `--api-key <key>` | 用 API key 登录，而不是浏览器流程 |
| `login` | `--local` | 在此仓库的 git config 中配置 credential helper，而不是全局 |
| `setup-git` | `--host <host>` | 要配置的 Origin git 主机（默认：从 `CURSOR_ORIGIN_ENDPOINT` 推导） |
| `setup-git` | `--global` | 应用到全局 git config（默认：`true`） |
| `setup-git` | `--local` | 应用到此仓库的 git config，而不是全局 |
| `status` | `--json <fields>` | 输出 JSON，包含你列出的字段，例如 `account,team,organization` |

CLI 使用它找到的第一份凭据：`CURSOR_AUTH_TOKEN`，然后是 `CURSOR_API_KEY`，然后是 `origin auth login` 存储的登录。`origin auth status` 显示正在使用哪一份。在 CI 中，设置 `CURSOR_AUTH_TOKEN` 或 `CURSOR_API_KEY`，而不是登录。

当设置了 `CURSOR_API_KEY` 且你省略 `--api-key` 时，`origin auth login` 会询问 `Log in with this API key instead of the browser? [y/N]`，并默认走浏览器流程。在非交互 shell 中，例如 CI，它会用该 key 登录并打印一条提示。

`origin auth login` 还会安装 git credential helper，因此针对 Origin 远程的 `git push` 和 `git pull` 无需进一步设置即可工作。

## 仓库

| 子命令 | 说明 | 用法 |
| ------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------- |
| `create <repo>` | 创建仓库 | `origin repo create acme/checkout` |
| `create-mirrored <github-repo>` | 创建 GitHub 仓库的 Cursor 镜像 | `origin repo create-mirrored acme/checkout` |
| `list` | 列出你的账号可以访问的仓库 | `origin repo list` |
| `view [repo]` | 显示仓库 | `origin repo view acme/checkout` |
| `edit [repo]` | 更改仓库设置 | `origin repo edit --visibility private` |
| `from-github [github-repo]` | 显示镜像某个 github.com 仓库的 Origin 仓库 | `origin repo from-github acme/checkout` |
| `verify-migration [repo]` | 比较 GitHub 仓库的设置与其 Origin 对应仓库 | `origin repo verify-migration acme/checkout` |
| `clone <repo> [directory]` | 使用已保存的登录通过 HTTPS 克隆 | `origin repo clone acme/checkout` |
| `delete <repo>` | 删除仓库 | `origin repo delete acme/checkout` |
| `access` | 管理谁可以访问仓库。见[仓库访问权限](https://cursor.com/docs/origin/cli/reference/commands.md#repository-access) | `origin repo access list` |
| `mirror` | 管理仓库的 GitHub 镜像。见[镜像](https://cursor.com/docs/origin/cli/reference/commands.md#mirrors) | `origin repo mirror status` |

传入 `org/name` 以指定仓库。不带斜杠的 `origin repo create <name>` 会在你账号的命名空间中创建仓库。

| 命令 | 选项 | 说明 |
| ------------------ | ------------------------------- | --------------------------------------------------------------------------------------------- |
| `create` | `--default-branch <branch>` | 新仓库的默认分支（服务器默认：`main`） |
| `create-mirrored` | `--namespace <namespace>` | 在其下创建镜像的命名空间（默认：你账号的命名空间） |
| `create-mirrored` | `--github-enterprise-id <uuid>` | GitHub Enterprise Server app UUID。从 github.com 镜像时省略它 |
| `list` | `--namespace <namespace>` | 只列出一个命名空间，而不是你能访问的每个命名空间 |
| `list` | `--json <fields>` | 输出 JSON，包含你列出的字段，例如 `org,name,mirrorStatus` |
| `view` | `--json <fields>` | 输出 JSON，包含你列出的字段，例如 `org,name,defaultBranch` |
| `edit` | `--default-branch <branch>` | 设置默认分支 |
| `edit` | `--visibility <visibility>` | 设置可见性：`internal` 或 `private` |
| `edit` | `--allow-merge-commit` | 允许 merge commit。`--no-allow-merge-commit` 禁止它们 |
| `edit` | `--allow-squash-merge` | 允许 squash merge。`--no-allow-squash-merge` 禁止它们 |
| `edit` | `--delete-branch-on-merge` | pull request 合并时删除 head 分支。`--no-delete-branch-on-merge` 保留它 |
| `edit` | `--json <fields>` | 输出 JSON，包含你列出的字段 |
| `from-github` | `--json <fields>` | 输出 JSON，包含你列出的字段，例如 `found,org,name,sourceOfTruth` |
| `verify-migration` | `--github-repo <owner/repo>` | GitHub 对应仓库（默认：从 Origin 仓库的镜像信息解析） |
| `verify-migration` | `--namespace <namespace>` | 验证命名空间中的每个仓库 |
| `verify-migration` | `--github-input <file>` | 从 JSON 文件读取已捕获的 GitHub 设置，而不是调用 `gh api`。用 `-` 表示 stdin |
| `verify-migration` | `--json <fields>` | 输出 JSON，包含你列出的字段 |
| `delete` | `-y, --yes` | 跳过确认提示。在非交互 shell 中为必填 |

`origin repo from-github` 接受 `owner/repo`、`HOST/OWNER/REPO` 或 URL。没有参数时，如果 `origin` 远程指向 github.com，它会读取该远程。

`origin repo view --json mirrorStatus` 告诉你哪个代码托管平台拥有该仓库。`no-mirror` 和 `outbound` 表示 Origin 拥有它。任何其他值表示 GitHub 拥有它。见[镜像 GitHub 仓库](https://cursor.com/docs/origin/mirror-github.md)。

## 仓库访问权限

`origin repo access` 管理谁可以访问仓库：拥有该仓库的团队的组、团队成员、组织组和外部协作者。

| 子命令 | 说明 | 用法 |
| ---------- | ----------------------------------------------------------------- | ---------------------------------------------------------- |
| `list` | 列出仓库上的每一项访问授权 | `origin repo access list` |
| `set` | 授予或更改角色 | `origin repo access set --user dana@acme.com --role Write` |
| `remove` | 移除直接授权 | `origin repo access remove --user dana@acme.com` |
| `policies` | 列出你可以授予的角色，即有效的 `--role` 值 | `origin repo access policies` |

`set` 和 `remove` 恰好接受一个主体：

| 选项 | 说明 |
| ------------------- | ---------------------------------------------------------------------------------------------- |
| `--team <group>` | 拥有该仓库的团队的 `admins` 或 `members` 组。在 `remove` 上，清除按仓库的覆盖 |
| `--user <user>` | 团队成员，用数字形式的 Cursor 用户 ID，或现有被授权人的邮箱 |
| `--group <group>` | 组织组，用 ID，或现有被授权人的显示名称 |
| `--external <user>` | 外部协作者，用数字形式的 Cursor 用户 ID，或现有协作者的邮箱 |

| 命令 | 选项 | 说明 |
| ------- | ----------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| `set` | `--role <role>` | 要授予的角色，用显示名称（`Read`、`Write`、`Admin`）或策略名称（`ORIGIN_REPO_READ`）。`none` 会移除该授权。必填 |
| `list` | `--search <text>` | 按名称或邮箱筛选授权和协作者 |
| 全部 | `--json <fields>` | 输出 JSON，包含你列出的字段 |

移除仓库授权不会影响命名空间级别的授权。要更改那些授权，使用 [`origin namespace access`](https://cursor.com/docs/origin/cli/reference/commands.md#namespaces)。

## 镜像

`origin repo mirror` 管理仓库的 [GitHub 镜像](https://cursor.com/docs/origin/mirror-github.md)。

| 子命令 | 说明 | 用法 |
| ---------- | ---------------------------------------------------------------------------- | ------------------------------------------- |
| `status` | 显示镜像状态和任何过渡作业 | `origin repo mirror status` |
| `sync` | 立即把一个 ref 从 GitHub fetch 到入站镜像 | `origin repo mirror sync --ref main --wait` |
| `detach` | 永久断开仓库与 GitHub 的连接，使其成为仅 Origin | `origin repo mirror detach` |

| 命令 | 选项 | 说明 |
| -------- | ----------------- | ------------------------------------------------------------------------------ |
| `status` | `--job <id>` | 显示指定的过渡作业，而不是当前活动的作业 |
| `status` | `--watch` | 轮询，直到过渡作业成功、失败并回滚，或被取代 |
| `sync` | `--ref <ref>` | 要同步的分支名称或完整 ref（默认：默认分支） |
| `sync` | `--wait` | 阻塞，直到该 ref 同步完成，或服务器的等待预算用尽 |
| `sync` | `--sha <sha>` | 要等待的完整提交 SHA。一旦它从该 ref 可达就返回 |
| `detach` | `-y, --yes` | 跳过确认提示 |
| 全部 | `--json <fields>` | 输出 JSON，包含你列出的字段 |

## Push local

在[从 GitHub 镜像](https://cursor.com/docs/origin/mirror-github.md)的仓库上，`origin push local` 会在 `https://origin.cursor.com/{owner}/{repo}.git/local` 设置 `origin-local` 远程，并把本地 `origin/*` 分支推送到那里。GitHub 不可用时用它继续工作，或存放任何你只想留在 Origin 副本上的其他 git 状态。它不会出现在 `origin --help` 中。第一次运行之后，该远程看起来像这样：

```text
origin-local    https://origin.cursor.com/{owner}/{repo}.git/local (fetch)
origin-local    https://origin.cursor.com/{owner}/{repo}.git/local (push)
```

其他分支仍用 `git push origin` 进入 GitHub。见[仅存在于 Origin 的分支](https://cursor.com/docs/origin/git.md#forge-local-branches-on-mirrored-repos)。

| 子命令 | 说明 | 用法 |
| ---------- | ------------------------------------------------------------- | ------------------- |
| `local` | 设置 `origin-local` 远程并推送 `origin/*` 分支 | `origin push local` |

| 选项 | 说明 |
| ----------------- | ---------------------------------------------------------------------------------------- |
| `--remote <name>` | 要创建或更新的 git 远程（默认：`origin-local`）。不能是 `origin` 或 `github` |
| `--dry-run` | 显示将要推送的内容，而不实际推送 |

## 标签

管理仓库定义的标签。用 `origin pr create --label` 或 `origin pr edit --add-label` 把它们加到 pull request。

| 子命令 | 说明 | 用法 |
| ---------------- | ------------------------------------------------ | ---------------------------------------------------------------------- |
| `list` | 列出仓库的标签 | `origin label list` |
| `create <name>` | 创建标签 | `origin label create bug -c d73a4a` |
| `edit <label>` | 更改标签的名称、颜色或描述 | `origin label edit bug -n defect` |
| `delete <name>` | 删除标签 | `origin label delete defect` |
| `import <input>` | 从 JSON 文件导入标签，或用 `-` 表示 stdin | `gh label list --json name,color,description \| origin label import -` |

| 命令 | 选项 | 说明 |
| -------- | -------------------------- | ------------------------------------------------------------------------ |
| `list` | `-S, --search <text>` | 搜索标签名称 |
| `list` | `-L, --limit <number>` | 要获取的标签数量上限（默认：`30`） |
| `create` | `-c, --color <hex>` | 六位十六进制颜色，可以带或不带前导 `#`（默认：随机） |
| `create` | `-d, --description <text>` | 标签描述 |
| `create` | `-f, --force` | 当标签已存在时更新颜色和描述 |
| `edit` | `-n, --name <name>` | 新名称 |
| `edit` | `-c, --color <hex>` | 新颜色 |
| `edit` | `-d, --description <text>` | 新描述 |
| `delete` | `-y, --yes` | 跳过确认提示 |
| `import` | `-f, --force` | 更新已存在的标签，而不是跳过它们 |
| `import` | `--dry-run` | 打印将要导入的内容，而不写入任何内容 |
| 全部 | `--json <fields>` | 输出 JSON，包含你列出的字段 |

`origin label import` 读取 `gh label list --json name,color,description` 的输出，因此你可以一步复制 GitHub 仓库的标签。

## Ruleset

管理仓库的合并时和推送时 [ruleset](https://cursor.com/docs/origin/settings.md#rules-and-protections)：

| 子命令 | 说明 | 用法 |
| --------------------- | ----------------------------------------------------------------------- | ------------------------------------------------- |
| `list` | 列出仓库的 ruleset | `origin ruleset list` |
| `view <ruleset-id>` | 按 ID 查看 ruleset，ID 由 `origin ruleset list` 打印。别名：`get` | `origin ruleset view <id>` |
| `create` | 用标志或 JSON spec 创建 ruleset | `origin ruleset create --input ruleset.json` |
| `update <ruleset-id>` | 用标志或 JSON spec 更新 ruleset | `origin ruleset update <id> --enforcement active` |
| `delete <ruleset-id>` | 删除 ruleset | `origin ruleset delete <id>` |
| `import` | 从 GitHub ruleset 或分支保护 JSON 创建 ruleset | `origin ruleset import --from-github rules.json` |

`create` 和 `update` 接受这些选项。每个可重复选项都会替换整个列表：

| 选项 | 说明 |
| -------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| `--name <name>` | ruleset 显示名称 |
| `--description <text>` | ruleset 描述 |
| `--enforcement <mode>` | `active`、`evaluate` 或 `disabled` |
| `--kind <kind>` | `merge_branch`、`push_branch`、`push_tag` 或 `push_repository` |
| `--included-ref <pattern>` | ruleset 适用的 ref 模式，例如 `~DEFAULT_BRANCH` 或 `refs/heads/release/*`。可重复 |
| `--excluded-ref <pattern>` | 要排除的 ref 模式。可重复 |
| `--rule <rule>` | 一条规则，格式为 `<rule_type>[=<json parameters>]`，例如 `'pull_request={"requiredApprovingReviewCount":1}'`。可重复 |
| `--bypass-actor <json>` | 一个绕过行动者，为包含 `bypassMode`、`actorKind` 和可选 `actorPayload` 的 JSON 对象。可重复 |
| `--input <file>` | JSON spec 文件，或用 `-` 表示 stdin。接受 `origin ruleset view --json` 的输出。标志会覆盖 spec 中的字段 |

| 命令 | 选项 | 说明 |
| -------- | ---------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| `import` | `--from-github <file>` | GitHub JSON：一个 ruleset、ruleset 数组，或经典分支保护。用 `--from-github=-` 表示 stdin。必填 |
| `import` | `--branch <pattern>` | 经典分支保护载荷所适用的分支或模式。该输入为必填 |
| `import` | `--enforcement <mode>` | 覆盖每个导入 ruleset 的 enforcement，例如先用 `evaluate` 试运行 |
| `import` | `--dry-run` | 打印将要创建的 ruleset，而不写入它们 |
| `delete` | `-y, --yes` | 跳过确认提示 |
| 全部 | `--json <fields>` | 输出 JSON，包含你列出的字段 |

要在仓库之间复制 ruleset，把 `origin ruleset view <id> --json name,enforcement,kind,includedRefNames,excludedRefNames,rules,bypassActors` 保存到文件，并把它传给 `origin ruleset create --input`，同时用 `-R` 指定目标仓库。

## 命名空间

`origin namespace access` 管理命名空间上的授权，即 `org/repo` 中的 `org`。`origin ns` 是 `origin namespace` 的别名。

| 子命令 | 说明 | 用法 |
| ---------------------- | ----------------------------------------------------------------- | -------------------------------------------------------------------- |
| `list <namespace>` | 列出团队组和单项授权 | `origin namespace access list acme` |
| `set <namespace>` | 向团队组、用户或组织组授予角色 | `origin namespace access set acme --user dana@acme.com --role Admin` |
| `remove <namespace>` | 移除授权。别名：`rm` | `origin namespace access remove acme --user dana@acme.com` |
| `policies <namespace>` | 列出你可以授予的角色，即有效的 `--role` 值 | `origin namespace access policies acme` |

`set` 和 `remove` 恰好接受下列之一：`--team <admins|members>`、`--user <user>`（Cursor 用户 ID，或已在该命名空间上获得授权的用户邮箱），或 `--group <id>`（组织组 ID，由 `list` 打印）。

| 命令 | 选项 | 说明 |
| --------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------- |
| `set` | `--role <role>` | 要授予的角色，用策略名称（`ORIGIN_NAMESPACE_ADMIN`）或显示名称（`Admin`）。必填 |
| `set`, `remove` | `--retain-self-admin` | 当该更改会移除最后一位 Codebase Admin 时，通过个人授权保留你为管理员，而不是失败 |
| `remove` | `-y, --yes` | 跳过确认提示 |
| `list` | `--search <text>` | 按名称或邮箱筛选单项授权 |
| 全部 | `--json <fields>` | 输出 JSON，包含你列出的字段 |

## Apps

`origin app` 管理 [Origin app](https://cursor.com/docs/api/origin.md)：它们的 webhook 订阅、签名密钥和 webhook 投递。

| 子命令 | 说明 | 用法 |
| ------------------------------------- | --------------------------------------------------------------- | ----------------------------------------------------------- |
| `list` | 列出命名空间发布的 app | `origin app list` |
| `view <app-id>` | 查看 app 的配置、统计和签名密钥 | `origin app view <app-id>` |
| `create` | 在你管理的命名空间中创建 app | `origin app create --name ci-bot --generate-key ci-bot.pem` |
| `edit <app-id>` | 编辑 app。你省略的选项会保持字段不变 | `origin app edit <app-id> --event pull_request.merged` |
| `key add <app-id> <key-file>` | 添加 Ed25519 公钥（PEM SPKI）。用 `-` 表示 stdin | `origin app key add <app-id> key.pub` |
| `key revoke <app-id> <kid>` | 按密钥 ID 撤销签名密钥，ID 由 `origin app view` 打印 | `origin app key revoke <app-id> <kid>` |
| `webhook deliveries` | 列出 webhook 投递及其结果 | `origin app webhook deliveries --no-delivered` |
| `webhook redeliver <delivery-ids...>` | 把投递加入队列以再次发送 | `origin app webhook redeliver <delivery-id>` |
| `webhook ping` | 发送已签名的测试 `ping` 事件并报告响应 | `origin app webhook ping` |

| 命令 | 选项 | 说明 |
| ---------------- | ------------------------- | -------------------------------------------------------------------------------------------------------------- |
| `list` | `--namespace <namespace>` | 要列出其 app 的命名空间（默认：从你的账号推断） |
| `list` | `-L, --limit <number>` | 要列出的 app 数量上限（默认：`100`） |
| `create` | `--name <name>` | app 显示名称。必填 |
| `create` | `--namespace <namespace>` | 拥有该 app 的命名空间（默认：从你的账号推断） |
| `create` | `--public-key <file>` | app 签名密钥的 Ed25519 公钥（PEM SPKI）。用 `-` 表示 stdin |
| `create` | `--generate-key <path>` | 生成 Ed25519 密钥对，把私钥（PKCS#8 PEM）写到此路径，并登记公钥 |
| `create`, `edit` | `--webhook-url <url>` | 接收 webhook 事件的 HTTPS URL。在 `edit` 上，`""` 会清除 webhook 配置 |
| `create`, `edit` | `--event <event>` | webhook 事件订阅，例如 `pull_request.created`。可重复。在 `edit` 上会替换列表 |
| `create`, `edit` | `--redirect-uri <uri>` | 允许的 OAuth 安装回调（绝对 HTTPS URI）。可重复。在 `edit` 上会替换列表 |
| `create`, `edit` | `--default-scope <scope>` | app 声明的默认 scope，例如 `repository:contents:read`。可重复。在 `edit` 上会替换列表 |
| `create`, `edit` | `--description <text>` | 简短的 app 描述。在 `edit` 上，`""` 会清除它 |
| `create`, `edit` | `--website-url <url>` | 发布者网站（绝对 HTTPS URL）。在 `edit` 上，`""` 会清除它 |
| `edit` | `--name <name>` | 新的显示名称 |
| `edit` | `--clear-events` | 移除每一个事件订阅 |
| `edit` | `--clear-redirect-uris` | 移除每一个安装回调 |
| `edit` | `--clear-default-scopes` | 移除每一个默认 scope |
| `webhook *` | `--app <app-id>` | 要作为其进行身份验证的 app。需要 `--signing-key` |
| `webhook *` | `--signing-key <file>` | 该 app 的私钥签名密钥（Ed25519 PKCS#8 PEM） |
| `deliveries` | `--delivered` | 仅列出接收方已确认的投递。`--no-delivered` 列出从未得到 2xx 响应的每一次投递 |
| `deliveries` | `--event-type <event>` | 仅此事件类型，例如 `pull_request.created` |
| `deliveries` | `--installation <id>` | 仅此 installation |
| `deliveries` | `-L, --limit <number>` | 要列出的投递数量上限（默认：`30`） |
| 除 `key` 外的全部 | `--json <fields>` | 输出 JSON，包含你列出的字段 |

重新投递是异步的。之后运行 `origin app webhook deliveries` 查看结果。

## SSH 密钥

管理你的 Origin 账号上的 SSH 公钥：

| 子命令 | 说明 | 用法 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------- |
| `add [key-file]` | 把 SSH 公钥添加到你的账号。`-t, --title` 为必填，并命名该密钥。省略文件或传入 `-` 以读取 stdin | `origin ssh-key add ~/.ssh/id_ed25519.pub -t work-laptop` |
| `list` | 列出你账号上的 SSH 公钥（`--json <fields>` 输出 JSON） | `origin ssh-key list` |
| `delete <id>` | 按 ID 删除密钥，ID 由 `origin ssh-key list` 打印。`-y, --yes` 跳过提示 | `origin ssh-key delete <id>` |

## API 请求

`origin api` 向 `api.cursor.com/v1/origin` 的 Origin REST API 发起经过身份验证的请求，用于任何没有一等命令的操作。它支持与 `gh api` 类似的请求标志：`-X, --method`、`-H, --header`、`-F, --field`、`-f, --raw-field` 和 `--input`。`--verbose` 把请求和响应头打印到 stderr，`--silent` 丢弃响应体。要筛选 JSON 响应，把它用管道传给 `jq`。`{owner}`、`{repo}` 和 `{branch}` 占位符会从 `-R, --repo`、环境变量 `ORIGIN_REPO` 或 `origin` git 远程展开。端点、身份验证和示例见 [Origin API](https://cursor.com/docs/api/origin.md) 文档。

## Agents

`origin agent prompt` 为 AI Agent 打印一份关于 `origin` 的 Markdown 指南，针对所安装的版本撰写。`origin agent prompt <topic>` 打印一个主题：`auth`、`prs`、`review`、`ci`、`stacking`、`json` 或 `reference`。`origin agent prompt reference pr` 列出一个组中的每个命令。

`origin agent setup` 会安装一个 skill，让你的编程 Agent 运行 `origin agent prompt`：

| 选项 | 说明 |
| ------------------- | ---------------------------------------------------------------------- |
| `--harness <agent>` | 要为其安装该 skill 的 Agent：`cursor`、`claude-code` 或 `codex` |
| `--global` | 安装到你的主目录，用于每个项目（默认：`true`） |
| `--project` | 改为安装到当前项目（`cursor` 和 `claude-code`） |
| `--print` | 打印该 skill 而不是写入它，以便为另一个 Agent 安装 |

## 更新

更新到你所在通道上的当前版本：

```bash
origin update
```

即使你已经在当前版本上，`--force` 也会重新安装。

CLI 默认从 `stable` 通道更新。用 `origin config set-channel <latest|stable>` 切换，并用 `origin config get-channel` 查看。

## Shell 补全

把补全脚本追加到你的 shell 配置：

```bash
origin completion >> ~/.zshrc
```

然后，取值为分支的标志，例如 `--head` 和 `--base`，会补全本地分支名称。

## 环境变量

| 变量 | 说明 |
| ------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `CURSOR_AUTH_TOKEN` | Cursor 身份验证 token，直接用作 bearer token。优先于其他每一份凭据 |
| `CURSOR_API_KEY` | API key，会交换为会话且从不存储。在未设置 `CURSOR_AUTH_TOKEN` 时使用。`origin auth login` 在交互 shell 中使用它之前会询问 |
| `CURSOR_ORIGIN_ENDPOINT` | API 服务器端点（默认：`https://api.origin.cursor.com`） |
| `ORIGIN_REPO` | 当你省略 `-R, --repo` 时的默认仓库，格式为 `org/name` |
| `NO_COLOR` | 关闭彩色输出 |

## 获取帮助

每个命令都接受 `--help`：

```bash
origin --help
origin repo --help
origin repo create --help
```

## 后续步骤

- [pull request 命令](https://cursor.com/docs/origin/cli/reference/pull-requests.md)
- [stack 命令](https://cursor.com/docs/origin/cli/reference/stacks.md)
- [安装 CLI](https://cursor.com/docs/origin/cli.md)
- [创建仓库](https://cursor.com/docs/origin/create-repository.md)
- [克隆、推送与拉取](https://cursor.com/docs/origin/git.md)


---

## 站点地图

[所有文档页面概览](/llms.txt)
