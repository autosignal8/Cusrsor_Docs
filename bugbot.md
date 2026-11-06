> 原文：https://cursor.com/docs/bugbot

# Bugbot

Bugbot 审查 pull request，并识别 bug、安全问题和代码质量问题。

在 [Automations](https://cursor.com/automations/from-cursor/bugbot) 中配置 Bugbot。

[Bugbot 在 PR 上留下评论](/docs-static/images/bugbot/bugbot-report-cropped.mp4)

## 工作原理

Bugbot 分析 PR 的 diff，并留下带有解释和修复建议的评论。它可以在每次 PR 更新时自动运行，也可以在被触发时手动运行。

- 在每次 PR 更新时运行**自动审查**
- **手动触发**：在任意 PR 上评论 `cursor review` 或 `bugbot run`。当机器人账号或 GitHub App 发布这些命令时，Bugbot 会忽略它们。
- **把已有 PR 评论用作上下文**：读取已连接的 PR 评论（顶层和行内），以避免重复建议，并在先前反馈的基础上继续
- **Fix in Cursor** 链接会在 Cursor 中直接打开问题
- **Fix in Web** 链接会在 [cursor.com/agents](https://cursor.com/agents) 中直接打开问题

### 限制

- **PR 大小**：Bugbot 会跳过超过默认审查大小限制的 PR。该限制按更改行数和 diff 字符数衡量，并会发布一条评论，请你拆分这次改动。被跳过的运行不计入审查用量。
- **生成文件**：Bugbot 会排除 `.gitattributes` 中标记为 `linguist-generated` 的文件。它们不计入 PR 大小限制。
- **机器人创建的 PR**：Bugbot 会按团队限制它对机器人账号所打开 PR 的审查次数。每一次审查都计数，包括对同一 PR 的重复审查。该上限是默认值，你的团队的上限可能不同。

## 设置

通过 Cursor 仪表盘连接仓库，即可开始使用 Bugbot。

- **Origin**：见 [Origin 页面](https://cursor.com/docs/origin.md)
- **GitHub**（包括 GitHub Enterprise Server）：见 [GitHub 集成页面](https://cursor.com/docs/integrations/github.md)
- **GitLab**（包括 GitLab Self-Hosted）：见 [GitLab 集成页面](https://cursor.com/docs/integrations/gitlab.md)
- **Bitbucket**（包括 Bitbucket Data Center）：见 [Bitbucket 集成页面](https://cursor.com/docs/integrations/bitbucket.md)
- **Azure DevOps**（Azure DevOps Services）：见 [Azure DevOps 集成页面](https://cursor.com/docs/integrations/azure-devops.md#set-up-bugbot)

连接之后，打开 [Automations 中的 Bugbot](https://cursor.com/automations/from-cursor/bugbot)，管理 Bugbot 审查哪些仓库。

在 GitHub 上，完成连接会为你授予访问权限的那些仓库打开 Bugbot。当连接方不是团队管理员，且连接的是 Enterprise 团队，或已经打开 Bugbot 的团队时，仓库保持关闭。在 GitLab、Bitbucket、Azure DevOps 和 Origin 上，需要按仓库打开 Bugbot。

在团队中，无法更改某次安装的设置的成员，在 Bugbot 处于关闭的仓库上会看到 **Request access**，而不是开关。发起请求后，会向团队管理员发送一封邮件，其中包含一键打开该仓库 Bugbot 的链接，按钮会变为 **Requested**。在管理员打开之前，该仓库保持关闭。

与 Security Reviewer 的对比，见 [Security Agents 的源代码管理支持](https://cursor.com/docs/security-agents.md#source-control-support)。

## CI 检查状态

Bugbot 会为每次审查运行发布一个状态。在 GitHub 上，它显示为名为 `Cursor Bugbot` 的检查。在 Bitbucket 上，它显示为键为 `cursor-bugbot` 的构建状态。在 Azure DevOps 上，它显示为上下文为 `cursor-bugbot/review` 的状态。该状态使用以下结论：

- `success`：Bugbot 没有发现问题，并且先前运行留下的 Bugbot 评论都已解决。
- `neutral`：Bugbot 发现了问题、运行被更新的提交取消，或 Bugbot 遇到内部错误。当 Bugbot 报告发现时，这是默认结论。
- `failure`：Bugbot 发现了问题，并且该检查被配置为在存在未解决问题时失败。

如果使用分支保护，请要求 Bugbot 检查或构建状态，以确保合并前 Bugbot 已经运行。仅要求该状态不会因发现而阻止合并，因为发现的默认结论是 `neutral`。如果组织可以使用 fail-on-unresolved-issues 行为，请启用它，使未解决的发现产生失败状态。Bugbot 不会发出 `skipped` 结论。

启用 Bugbot Autofix 后，GitHub 还可能显示一个单独的 `Cursor Bugbot Autofix` 检查。该检查只使用 `success` 或 `neutral`。

## 配置

### 个人

### 仓库设置

在安装列表中按仓库启用或禁用 Bugbot。Bugbot 只在你编写的 PR 上运行。

### 个人设置

- 以 **only when mentioned** 运行：通过评论 `cursor review` 或 `bugbot run`
- 每个 PR 以 **only once** 运行，跳过后续提交

在 [Bugbot](https://cursor.com/automations/from-cursor/bugbot) 的组织页面上，按组织打开或关闭学习。打开 **Organizations**，选择一个组织，并使用 **Automatically Learn Rules** 开关。[仓库规则](https://cursor.com/dashboard/bugbot/repository-rules)页面上的 **Manage learning** 会打开同一个开关。[Azure DevOps](https://cursor.com/docs/integrations/azure-devops.md#differences-from-other-providers) 没有这个开关。

### Team

### 仓库设置

Team 用户和管理员可以按仓库启用 Bugbot，为审查者配置允许列表和拒绝列表，并设置：

- 每个安装的每个 PR 以 **only once** 运行，跳过后续提交

对于已启用的私有仓库，无论贡献者是否属于团队，Bugbot 都会运行。在公开仓库上，Bugbot 的 **Team** 设置中的 **Public repositories** 开关决定自动审查是否覆盖团队以外的人以及机器人发起的 pull request。如果没有管理员更改过它，则在 October 22, 2026 之前创建的团队上为打开，自该日起创建的团队为关闭。该开关不影响以 `cursor review` 或 `bugbot run` 启动的手动审查。

### 个人设置

团队成员可以对自己的 PR 覆盖设置：

- 以 **only when mentioned** 运行：通过评论 `cursor review` 或 `bugbot run`
- 每个 PR 以 **only once** 运行，跳过后续提交
- 使用 **Enable reviews on draft PRs**，把草稿 pull request 纳入自动审查

### Enterprise

### 仓库设置

Enterprise 管理员可以按仓库启用 Bugbot，为审查者配置允许列表和拒绝列表，并设置：

- 每个安装的每个 PR 以 **only once** 运行，跳过后续提交

对于已启用的私有仓库，无论贡献者是否属于团队，Bugbot 都会运行。在公开仓库上，Bugbot 的 **Team** 设置中的 **Public repositories** 开关决定自动审查是否覆盖团队以外的人以及机器人发起的 pull request。如果没有管理员更改过它，则在 October 22, 2026 之前创建的团队上为打开，自该日起创建的团队为关闭。该开关不影响以 `cursor review` 或 `bugbot run` 启动的手动审查。

### 个人设置

Enterprise 用户可以对自己的 PR 覆盖设置：

- 以 **only when mentioned** 运行：通过评论 `cursor review` 或 `bugbot run`
- 每个 PR 以 **only once** 运行，跳过后续提交
- 使用 **Enable reviews on draft PRs**，把草稿 pull request 纳入自动审查

### 仓库配置文件

你也可以选用仓库级 YAML 文件来配置 Bugbot：

```yaml
# .cursor/config/bugbot.yaml
version: 1

triggers:
  drafts: false
  frequency: everyPush # everyPush | oncePerPr

review:
  effort: default # low | default | high | smart
  smartPrompt: ""
  incremental: true

prSummary:
  mode: comment # disabled | description | comment
  riskScore: true

autofix:
  mode: disabled # disabled | newBranch | existingBranch
```

所有字段都是可选的。如果文件不存在，Bugbot 使用与以往相同的行为。如果某个字段缺失或无效，Bugbot 会忽略该字段，并回退到优先级顺序中的下一项设置。Bugbot 会忽略大于 64 KB 的文件。

Bugbot 从 PR 的基分支（PR 要合并进去的分支）读取仓库根目录的 `.cursor/config/bugbot.yaml`。该文件必须已提交并推送到仓库，其设置只适用于该仓库。Bugbot 不会读取 PR 的 head 版本，因此一个 PR 不能改变 Bugbot 审查它自身的方式。使用代码审查和 `CODEOWNERS` 来控制谁可以更改此文件。

优先级从高到低：

- PR 作者对自己 PR 的个人覆盖。
- `.cursor/config/bugbot.yaml`。
- 仪表盘中的团队和组织设置。

`autofix.mode` 可以把仪表盘设置中的 Autofix 模式调低，但不能调高。例如，它可以把 **Commit to Existing Branch** 改为 `newBranch` 或 `disabled`，但在仪表盘关闭 Autofix 时不能把它打开。

## 分析

打开 [Automations 中的 Bugbot](https://cursor.com/automations/from-cursor/bugbot)，查看审查活动和结果。

## API

Enterprise 团队可以使用 Bugbot API 触发审查并获取每次审查的分析数据。在 [Cursor Dashboard → API Keys](https://cursor.com/dashboard/api) 创建 API 密钥，并使用 [Basic Authentication](https://cursor.com/docs/api.md#basic-authentication) 进行身份验证。

### 触发审查

POST

`/bugbot/review`

为 pull request 或 merge request 将一次 Bugbot 审查加入队列。请求在审查入队后即返回；审查异步运行。

需要具有 `admin:*` 范围的 API 密钥。该端点限制为每个团队每分钟 30 次请求。

将 `dryRun` 设为 `true`，即可运行完整分析流程，而不发布审查评论、行内评论、检查或其他 SCM 副作用。试运行审查仍会保存发现，并按普通审查计费。使用 `GET /analytics/team/bugbot-reviews` 获取它们。试运行请求另有每个团队每分钟 10 次的限制。

#### 请求体

`prUrl` string (required)

完整的 GitHub pull request 或 GitLab merge request URL。

`dryRun` boolean (optional)

为 `true` 时，运行分析并保存发现，而不向 SCM 提供方发布任何内容。默认值：`false`。

```bash
curl --request POST \
  --url https://api.cursor.com/bugbot/review \
  -u YOUR_API_KEY: \
  --header 'Content-Type: application/json' \
  --data '{
    "prUrl": "https://github.com/your-org/your-repo/pull/42"
  }'
```

```bash
curl --request POST \
  --url https://api.cursor.com/bugbot/review \
  -u YOUR_API_KEY: \
  --header 'Content-Type: application/json' \
  --data '{
    "prUrl": "https://github.com/your-org/your-repo/pull/42",
    "dryRun": true
  }'
```

**响应：**

```json
{
  "outcome": "success",
  "message": "Bugbot review queued",
  "request_id": "6e0d261c-86a2-4383-89f0-9162c1c10662",
  "dry_run": false
}
```

试运行响应使用 `"message": "Bugbot dry-run review queued"` 和 `"dry_run": true`。

保存 `request_id`，以便在分析端点中匹配已完成的审查。

如果 Bugbot 无法审查该 pull request，端点会返回 `400 Bad Request` 及原因：

```json
{
  "outcome": "error",
  "message": "Bugbot is disabled for this repository"
}
```

### 审查分析

GET

`/analytics/team/bugbot-reviews`

每个已完成的 Bugbot 审查返回一项，包括被审查的提交、发现数量、计费成本和每条发现的解决数据。

同时包含已发布的审查和试运行审查。已发布的发现由 `comment_id` 和 `resolution_status` 标识。试运行发现改为返回 `title`、`description` 和 `locations`，因为没有向 SCM 发布任何内容。

需要具有 `read:*` 范围的 API 密钥。

#### 查询参数

`startDate` string (optional)

分析范围的开始时间。默认值为 7 天前。见[日期格式](https://cursor.com/docs/account/teams/analytics-api.md#date-formats)。

`endDate` string (optional)

分析范围的结束时间。默认值为当前时间。见[日期格式](https://cursor.com/docs/account/teams/analytics-api.md#date-formats)。

`repo` string (optional)

仓库过滤条件，形式为 `host/owner/repo`。协议和 `.git` 后缀可选。

`prNumber` number (optional)

Pull request 或 merge request 编号。

`page` number (optional)

分页的页码。默认值：`1`。

`pageSize` number (optional)

每页的审查数量。默认值：`100`，最大：`250`。

`dryRun` boolean (optional)

仅过滤试运行（`true`）或已发布（`false`）的审查。

```bash
curl --get https://api.cursor.com/analytics/team/bugbot-reviews \
  -u YOUR_API_KEY: \
  --data-urlencode 'startDate=2026-06-01' \
  --data-urlencode 'endDate=2026-06-29' \
  --data-urlencode 'repo=github.com/your-org/your-repo' \
  --data-urlencode 'prNumber=42' \
  --data-urlencode 'page=1' \
  --data-urlencode 'pageSize=100'
```

```bash
curl --get https://api.cursor.com/analytics/team/bugbot-reviews \
  -u YOUR_API_KEY: \
  --data-urlencode 'dryRun=true' \
  --data-urlencode 'repo=github.com/your-org/your-repo' \
  --data-urlencode 'prNumber=42'
```

**响应（已发布的审查）：**

```json
{
  "data": [
    {
      "request_id": "6e0d261c-86a2-4383-89f0-9162c1c10662",
      "timestamp": "2026-06-29T19:42:18.000Z",
      "repo": "github.com/your-org/your-repo",
      "repo_node_id": "R_kgDOABCDEF",
      "pr_number": 42,
      "commit_sha": "9f3c2a1b7d8e4f5061728394a5b6c7d8e9f0a1b2",
      "bugs_found": 2,
      "cost_cents": 42.5,
      "dry_run": false,
      "publication_status": "posted",
      "bugs": [
        {
          "comment_id": "2147483999",
          "resolution_status": "resolved",
          "severity": "high"
        },
        {
          "comment_id": "2147484000",
          "resolution_status": "unresolved",
          "severity": "medium"
        }
      ]
    }
  ],
  "pagination": {
    "page": 1,
    "pageSize": 100,
    "totalItems": 1,
    "totalPages": 1,
    "hasNextPage": false,
    "hasPreviousPage": false
  },
  "params": {
    "metric": "bugbot-reviews",
    "teamId": 12345,
    "startDate": "2026-06-01",
    "endDate": "2026-06-29",
    "repo": "github.com/your-org/your-repo",
    "prNumber": 42,
    "page": 1,
    "pageSize": 100
  }
}
```

**响应（试运行审查）：**

```json
{
  "data": [
    {
      "request_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
      "timestamp": "2026-06-29T20:15:03.000Z",
      "repo": "github.com/your-org/your-repo",
      "repo_node_id": "R_kgDOABCDEF",
      "pr_number": 42,
      "commit_sha": "9f3c2a1b7d8e4f5061728394a5b6c7d8e9f0a1b2",
      "bugs_found": 1,
      "cost_cents": null,
      "dry_run": true,
      "publication_status": "dry_run",
      "bugs": [
        {
          "comment_id": null,
          "resolution_status": null,
          "severity": "medium",
          "title": "Unbounded retry loop",
          "description": "retry() recurses without a ceiling.",
          "locations": [
            { "file": "src/net.ts", "start_line": 5, "end_line": 9 }
          ]
        }
      ]
    }
  ],
  "pagination": {
    "page": 1,
    "pageSize": 100,
    "totalItems": 1,
    "totalPages": 1,
    "hasNextPage": false,
    "hasPreviousPage": false
  },
  "params": {
    "metric": "bugbot-reviews",
    "teamId": 12345,
    "startDate": "2026-06-01",
    "endDate": "2026-06-29",
    "repo": "github.com/your-org/your-repo",
    "prNumber": 42,
    "dryRun": true,
    "page": 1,
    "pageSize": 100
  }
}
```

`repo_node_id`、`pr_number`、`commit_sha`、`cost_cents`、`bugs[].comment_id`、`bugs[].resolution_status` 和 `bugs[].severity` 在不可用时可能为 `null`。当审查不单独计费时，`cost_cents` 为 `null`。对于试运行审查，`bugs[].title`、`bugs[].description` 和 `bugs[].locations` 承载发现内容。试运行发现的 `comment_id` 为 `null`、`resolution_status` 为 `null`，因为没有向 SCM 发布任何内容。

### 触发并取回一次审查

1. 调用 `POST /bugbot/review` 并传入 pull request URL。传入 `"dryRun": true` 即可分析而不向 SCM 发布。
2. 保存返回的 `request_id`。
3. 轮询 `GET /analytics/team/bugbot-reviews`，按 `repo` 和 `prNumber` 过滤。触发试运行审查时使用 `dryRun=true`。
4. 找到 `request_id` 与触发响应相匹配的那一项。

审查入队后，分析数据可能需要一小段时间才会可用。

## 增量审查

默认情况下，Bugbot 只审查自上次 Bugbot 审查以来的改动。在 [Bugbot Automations](https://cursor.com/automations/from-cursor/bugbot) 中关闭 **Incremental Review**，即可在每次推送时审查完整的 pull request diff。

![Bugbot Automations 中的 Incremental Review 设置](/docs-static/images/bugbot/incremental-review-setting.png)

## PR 摘要

Bugbot 会为它审查的每个 pull request 写一份摘要。[Bugbot Automations](https://cursor.com/automations/from-cursor/bugbot) 中的 **Post PR Summary** 控制摘要的去向：

- **In Description**（默认）：Bugbot 把摘要添加到 pull request 描述中，并在每次审查时更新。
- **As Comment**：Bugbot 把摘要作为评论发布，并在每次审查时更新该评论。
- **Off**：Bugbot 不发布摘要。

用个人设置 **PR Summaries** 为自己的 PR 打开或关闭摘要。选择 **On**、**Off** 或 **Use Installation Default**。

## 力度级别

力度级别控制 Bugbot 在审查期间花多少时间推理。更高的力度级别可以发现更多 bug，但每次审查可能更久，并占用更多用量。

可从以下力度级别中选择：

- **Low**：以成本为优化目标，质量接近 Default。审查更便宜，但耗时更长。
- **Default**：以效率和速度为优化目标。审查费用更低，但 Bugbot 可能发现的 bug 更少。
- **High**：花更多时间推理。审查更贵、耗时更长，但 Bugbot 可能发现更多 bug。
- **Smart**：让你描述 Bugbot 应在何时使用 Low、Default 或 High。Cursor 会根据你的说明动态设置力度级别。做选择时，Smart 会连同 PR 的标题、作者、仓库名和 diff（大型 PR 上的 diff 会被裁剪）一起读取你的说明。它不读取 PR 描述、标签或 `BUGBOT.md`。只有当你的说明要求使用 Low 时，Smart 才会使用 Low；当说明没有明确要求另一个级别时，则使用 Default。当 Smart 选择了 Default 或 High，并且审查发现了问题时，审查评论会注明它所使用的力度级别。

力度级别仅适用于按用量计费的 Bugbot 方案。

## 规则

用团队规则、仓库规则和项目中的 `.cursor/BUGBOT.md` 文件来引导审查。

### 团队规则

团队管理员可以在 [Bugbot Automations](https://cursor.com/automations/from-cursor/bugbot) 中创建适用于团队内所有仓库的规则。这些规则对每个已启用的仓库可用，便于执行全组织的标准。

当 Team Rules、仓库规则和项目规则文件同时适用时，Bugbot 会把它们合并为一个审查规则块。纳入顺序：Team Rules → 项目 .cursor/BUGBOT.md（包括嵌套文件）→ 已学习规则 → 手动规则。

### 规则限制

纳入审查时，每条规则会在 30,000 个字符处截断。从 `.cursor/BUGBOT.md` 文件链接的每个 `.md` 或 `.mdc` 文件都算作自己的一条规则，各自有 30,000 字符的限制，并且 Bugbot 对每个链接路径只加载一次。Bugbot 为一次审查纳入的规则合计上限为 100,000 个字符。如果超过该合计上限，部分规则可能会被省略。必需的团队规则优先于非必需规则。

### 查看某次审查使用了哪些规则

在 pull request 上评论 `bugbot run verbose=true` 或 `cursor review verbose=true`。Bugbot 会发布一张表，列出该次运行纳入的每条规则，并标出被截断或省略的规则。

### 仓库规则

#### 项目规则

创建 `.cursor/BUGBOT.md` 文件，为审查提供项目特定的上下文。Bugbot 始终包含根目录的 `.cursor/BUGBOT.md`，以及从更改文件向上遍历时找到的其他文件。

```bash
project/
  .cursor/BUGBOT.md          # Always included (project-wide rules)
  backend/
    .cursor/BUGBOT.md        # Included when reviewing backend files
    api/
      .cursor/BUGBOT.md      # Included when reviewing API files
  frontend/
    .cursor/BUGBOT.md        # Included when reviewing frontend files
```

Cursor 的[项目规则](https://cursor.com/docs/rules.md)（`.cursor/rules/` 中的 `*.mdc` 文件）不适用于 Bugbot 运行。

#### 已学习规则

在 [Bugbot](https://cursor.com/automations/from-cursor/bugbot) 的组织页面上，用 **Automatically Learn Rules** 开关按组织打开或关闭学习。打开 **Organizations** 并选择一个组织，或在[仓库规则](https://cursor.com/dashboard/bugbot/repository-rules)页面使用 **Manage learning**。在存在该开关的地方，手动规则只在学习打开时适用，因此关闭学习也会停止应用手动规则。[Azure DevOps](https://cursor.com/docs/integrations/azure-devops.md#differences-from-other-providers) 没有 **Automatically Learn Rules**。你手写的仓库规则在那里仍然适用。

规则会根据团队在 GitHub 上对该仓库的活动自动生成，也可以从仓库历史中手动回填。

pull request 被合并或关闭时，Bugbot 会从中学习。它不会在该 pull request 上发布任何内容，并且每次学习运行都像一次审查一样计入 Bugbot 用量。对于设为仅手动的仓库，学习不会运行；在个人账号上，当个人设置让 Bugbot 以 **only when mentioned** 运行时，学习也不会运行。关闭 **Automatically Learn Rules** 会为该组织停止学习。

你也可以在任意 PR 上评论 `@cursor remember [fact]`，当场教给 Bugbot 新规则。Bugbot 会把该事实保存为已学习规则，并应用到以后的审查。

随着 Cursor 对团队活动的了解增加，它会自动启用或禁用规则。

| 字段 | 说明 |
| :--------------- | :------------------------------------------------------------------------------------------------------------- |
| **Name** | 规则的简短标题。 |
| **Rule content** | Bugbot 应遵循的说明（即风格门禁、路径或审查预期）。 |
| **Scoped paths** | 可选的 glob 模式，例如 `src/components/**`。留空则将该规则应用到整个仓库。 |

#### 手动规则

在 [Bugbot 仓库规则](https://cursor.com/dashboard/bugbot/repository-rules)中，你可以为单个仓库创建手动规则。与已学习规则一样，手动规则只在该组织和该仓库启用了学习时适用。[Azure DevOps](https://cursor.com/docs/integrations/azure-devops.md#differences-from-other-providers) 没有该开关，因此你手写的仓库规则在那里仍然适用。

| 字段 | 说明 |
| :--------------- | :------------------------------------------------------------------------------------------------------------- |
| **Name** | 规则的简短标题。 |
| **Rule content** | Bugbot 应遵循的说明（即风格门禁、路径或审查预期）。 |
| **Scoped paths** | 可选的 glob 模式，例如 `src/components/**`。留空则将该规则应用到整个仓库。 |

#### 规则分析

Bugbot 规则上的 **Analytics** 展示它在真实 PR 上的表现：

| 指标 | 含义 |
| :------------------ | :--------------------------------------------------------- |
| **Issues found** | Bugbot 报告的、涉及此规则的发现数量。 |
| **PRs reviewed** | 出现这些发现的 pull request 数量。 |
| **Accepted issues** | 团队接受的发现数量。 |
| **Acceptance rate** | 被接受的发现所占百分比。 |

#### 示例

### 安全：标记任何 eval() 或 exec() 的使用

```text
If any changed file contains the string pattern /\beval\s*\(|\bexec\s*\(/i, then:
- Add a blocking Bug with title "Dangerous dynamic execution" and body:
  "Usage of eval/exec was found. Replace with safe alternatives or justify with a detailed comment and tests."
- Assign the Bug to the PR author.
- Apply label "security".
```

### 开源许可证：阻止导入不允许的许可证

```text
If the PR modifies dependency files (package.json, pnpm-lock.yaml, yarn.lock, requirements.txt, go.mod, Cargo.toml), then:
- Run the built-in License Scan.
- If any new or upgraded dependency has license in {GPL-2.0, GPL-3.0, AGPL-3.0}, then:
  - Add a blocking Bug titled "Disallowed license detected"
  - Include the offending package names, versions, and licenses in the Bug body
  - Apply labels "compliance" and "security"
```

### 语言标准：标记 React componentWillMount 的使用

```text
For files matching **/*.{js,jsx,ts,tsx} in React projects:
If a changed file contains /componentWillMount\s*\(/, then:
- Add a blocking Bug titled "Deprecated React lifecycle method"
- Body: "Replace componentWillMount with constructor or useEffect. See React docs."
- Suggest an autofix snippet that migrates side effects to useEffect.
```

### 标准：要求后端改动附带测试

```text
If the PR modifies files in {server/**, api/**, backend/**} and there are no changes in {**/*.test.*, **/__tests__/**, tests/**}, then:
- Add a blocking Bug titled "Missing tests for backend changes"
- Body: "This PR modifies backend code but includes no accompanying tests. Please add or update tests."
- Apply label "quality"
```

### 风格：不允许 TODO 注释

```text
If any changed file contains /(?:^|\s)(TODO|FIXME)(?:\s*:|\s+)/, then:
- Add a non-blocking Bug titled "TODO/FIXME comment found"
- Body: "Replace TODO/FIXME with a tracked issue reference, e.g., `TODO(#1234): ...`, or remove it."
- If the TODO already references an issue pattern /#\d+|[A-Z]+-\d+/, mark the Bug as resolved automatically.
```

## 在 Agent 中运行

在推送代码之前，使用 `/review-bugbot` 或 `/review` Skill，从 Agent 运行 Bugbot。

**审查哪份 diff：** 默认情况下，`/review-bugbot` 审查你的分支改动：相对于基分支的每一处改动，包括已提交和未提交的改动。如果希望反馈范围更窄，可以让它只审查未提交的改动。

**对照哪个分支：** `/review-bugbot` 会与你的默认基分支比较。当基分支不是默认分支（例如 `main`）时，告诉 Agent 要对照哪个分支，或让它根据上下文推断。

![从 Agent 输入框运行 /review-bugbot Skill](/docs-static/images/bugbot/review-bugbot-skill.png)

### 与 pull request 同步

`/review-bugbot` 的审查会与已连接 SCM（GitHub、GitLab 或 Bitbucket）上的 Bugbot 保持同步。

在底层，`/review-bugbot` 会存储所审查 diff 的 [patch ID](https://git-scm.com/docs/git-patch-id)。当你的 SCM 上的 Bugbot 看到具有相同 patch ID 的 diff 时，它会跳过审查，并留下评论说明已经审查过该 diff。

一个常见用法：先运行 `/review-bugbot`，再以同一份 diff 打开 pull request，Bugbot 会识别这次审查并跳过远端 PR 审查。

`/review` 和 `/review-bugbot` 在 Cursor 3.7+、[cursor.com/agents](https://cursor.com/agents) 以及 [Cursor CLI](https://cursor.com/docs/cli/overview.md) 中可用。

## Autofix

Bugbot Autofix 会自动启动一个 [Cloud Agent](https://cursor.com/docs/cloud-agent.md)，修复 PR 审查中发现的 bug。

### 工作原理

当 Bugbot 在 PR 审查中发现 bug 时，它可以自动：

1. 启动一个 Cloud Agent 来分析并修复报告的问题
2. 把修复推送到现有分支或新分支（取决于你的设置）
3. 在原始 PR 上发布一条包含结果的评论

![PR 上的 Bugbot Autofix 评论](/docs-static/images/bugbot/bugbot-autofix-comment.png)

### 配置

在 [Bugbot Automations](https://cursor.com/automations/from-cursor/bugbot) 中配置 Autofix 行为。

### 个人

个人用户可以在个人 Bugbot 设置中配置 Autofix 偏好：

- **Use Installation Default** — 遵循组织的设置
- **Off** — Autofix 已禁用；使用手动的 **Fix in Cursor** 或 **Fix in Web** 链接
- **Create New Branch**（推荐）— 把修复推送到新分支
- **Commit to Existing Branch** — 把修复推送到你的分支（每个 PR 最多 3 次尝试，以防止循环）

用户设置会在你自己的 PR 上覆盖团队默认值。

### Team

团队管理员可以为 GitHub 组织中的所有团队成员设置默认 Autofix 模式：

- **Off** — 默认禁用 Autofix
- **Create New Branch**（推荐）— 为团队成员把修复推送到新分支
- **Commit to Existing Branch** — 把修复直接推送到 PR 分支（每个 PR 最多 3 次尝试，以防止循环）

团队成员可以在个人设置中覆盖这些默认值。

可用的 Autofix 模式取决于仓库提供方：

| 提供方 | Create New Branch | Commit to Existing Branch |
| :-------------------------------------------------- | :---------------- | :------------------------ |
| GitHub、[Origin](https://cursor.com/docs/origin.md) | 是 | 是 |
| GitLab、Bitbucket | 否 | 是 |
| Azure DevOps | 否 | 否 |

安装设置只提供你的提供方支持的模式。如果个人设置选择了提供方不支持的模式，Bugbot 会跳过该 PR 的 Autofix 运行。

在 stacked PR 上，基分支不是仓库的默认分支时，**Commit to Existing Branch** 不会推送到你的分支。在 GitHub 和 Origin 上，Autofix 会改为在新分支上交付修复；在 PR 上评论 `@cursor push <sha>` 即可应用。在 GitLab 和 Bitbucket 上，Autofix 会跳过 stacked PR。

Autofix 使用你在 [Settings → Models](https://cursor.com/dashboard/settings) 中的 **Default agent model**。如果尚未设置个人模型偏好，Autofix 会回退到团队的默认模型（如果你在团队中），或系统默认模型。

### 要求

Autofix 需要：

- 有额外用量可用：在 The X Subscription 上有[预付额度](https://cursor.com/docs/models-and-pricing.md#top-ups-and-spending-caps)，或在旧版 Cursor 方案上启用了[按需用量](https://cursor.com/docs/account/pricing/legacy-cursor-plans.md#on-demand-usage-on-legacy-plans)
- 已启用存储（未处于 Legacy Privacy Mode）

### 计费

Autofix 使用 Cloud Agent 额度，并按你的方案费率计费。Cloud Agent 计费遵循你现有的[价格方案](https://cursor.com/docs/models-and-pricing.md)。

## MCP 支持

Bugbot 已与你的 [MCP 服务器](https://cursor.com/docs/mcp.md)集成，因此你的 AI 工具可以直接与 Bugbot 交互。使用 MCP 服务器提供额外工具，以引导 Bugbot 的审查过程。

要开始使用：

1. 按照 [MCP 文档](https://cursor.com/docs/mcp.md)中的说明设置 MCP 服务器。
2. 把工具添加到 [Automations 中的 Bugbot](https://cursor.com/automations/from-cursor/bugbot)。

MCP 支持仅在 Team 和 Enterprise 方案上可用。

## 管理配置 API

团队管理员可以使用 Bugbot Admin API 管理仓库，并控制哪些用户可以使用 Bugbot。用它来自动化仓库管理、在多个仓库上启用 Bugbot，或把用户开通与内部工具集成。

### 身份验证

所有端点都需要以 Bearer token 传递团队 Admin API Key：

```bash
Authorization: Bearer $API_KEY
```

创建 API 密钥：

1. 访问 [Cursor 仪表盘中的 API Keys](https://cursor.com/dashboard/api)
2. 点击 **New API Key**
3. 保存该 API 密钥

所有端点的速率限制为每个团队每分钟 60 次请求。

### 启用或禁用仓库

使用 `/bugbot/repo/update` 端点为仓库打开或关闭 Bugbot：

```bash
curl -X POST https://api.cursor.com/bugbot/repo/update \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "repoUrl": "https://github.com/your-org/your-repo",
    "enabled": true,
    "manualTriggerOnly": false
  }'
```

**参数：**

- `repoUrl`（string，必填）：仓库的完整 URL
- `enabled`（boolean，必填）：`true` 表示启用 Bugbot，`false` 表示禁用
- `manualTriggerOnly`（boolean，可选）：为 `true` 时，Bugbot 不会在此仓库的 PR 更新时自动运行。手动触发仍然有效，例如评论 `cursor review` 或 `bugbot run`。

由于缓存，Automations 界面可能需要片刻才能反映通过 API 所做的更改。API 响应显示的是数据库中的当前状态。

对于 Azure DevOps 仓库，请在 [Automations 中的 Bugbot](https://cursor.com/automations/from-cursor/bugbot) 里打开或关闭 Bugbot。对于 `dev.azure.com` 或 `*.visualstudio.com` 上的仓库 URL，此端点返回 `400`。

### 列出仓库

使用 `/bugbot/repos` 端点列出团队中所有仓库及其 Bugbot 设置：

```bash
curl https://api.cursor.com/bugbot/repos \
  -H "Authorization: Bearer $API_KEY"
```

响应包含每个仓库的启用状态、仅手动设置和时间戳。

### 管理用户访问权限

使用 `/bugbot/user/update` 端点控制哪些 GitHub、GitLab 或 Bitbucket 用户可以使用团队的 Bugbot 许可。Enterprise 用它把 Bugbot 开通与内部访问申请工具集成。

#### 前提条件

调用此端点之前，请在[团队 Bugbot 设置](https://cursor.com/automations/from-cursor/bugbot)中启用允许列表或阻止列表模式：

- **Allowlist mode ("Only...")**：只有列表中的用户可以使用 Bugbot
- **Blocklist mode ("Everyone but...")**：除列表中的用户外，所有用户都可以使用 Bugbot

如果两种模式都未启用，API 会返回错误。

#### 添加或移除用户

```bash
curl -X POST https://api.cursor.com/bugbot/user/update \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "username": "octocat",
    "allow": true
  }'
```

**参数：**

- `username`（string，必填）：GitHub、GitLab 或 Bitbucket 用户名（不区分大小写）
- `allow`（boolean，必填）：是否授予或撤销访问权限

`allow` 的行为取决于当前模式：

| 模式 | `allow: true` | `allow: false` |
| --------- | -------------------------------------------- | ------------------------------------------ |
| Allowlist | 把用户加入列表（可以使用 Bugbot） | 把用户从列表移除（不能使用 Bugbot） |
| Blocklist | 把用户从阻止列表移除（可以使用 Bugbot） | 把用户加入阻止列表（不能使用 Bugbot） |

**响应：**

```json
{
  "outcome": "success",
  "message": "Updated team-level allowlist for @octocat",
  "updatedTeamSettings": true,
  "updatedInstallations": 0
}
```

允许列表存储在团队级别，并适用于该团队拥有的所有 GitHub、GitLab 和 Bitbucket 安装。用户名会规范化为小写。

#### 示例：通过内部工具开通用户

把此 API 连接到内部访问申请门户。员工申请 Bugbot 访问权限时，门户调用 API 将其加入。员工离开或失去访问权限时，门户调用 API 将其移除。

**授予访问权限：**

```bash
curl -X POST https://api.cursor.com/bugbot/user/update \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"username": "employee-scm-username", "allow": true}'
```

**撤销访问权限：**

```bash
curl -X POST https://api.cursor.com/bugbot/user/update \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"username": "employee-scm-username", "allow": false}'
```

## 价格

Bugbot 使用按用量计费。

Bugbot 价格随 May 2026 价格更新而改变。背景见[公告博客](https://cursor.com/blog/may-2026-bugbot-changes)。如果仍在使用旧的按席位方案，见[旧版 Bugbot 价格](https://cursor.com/docs/bugbot/legacy-pricing.md)。

### 计费

每次审查只计费一次，计给为该仓库打开了 Bugbot 的团队或个人账号。PR 作者所属的团队不会改变计费对象。

### 个人

### 按用量计费

Bugbot 包括：

- 你的仓库中所有 PR 上的审查
- 对 Bugbot 规则的访问
- 设置 Bugbot 审查所用力度级别的能力

Bugbot 会先消耗包含的用量，然后从预付额度对额外审查计费，或在旧版 Cursor 方案上从按需支出计费。当前费率见[价格页面](https://cursor.com/pricing#bugbot)。

### 开始使用

通过账号设置订阅。

### Teams

### 按用量计费

Bugbot Teams 包括：

- 所有 PR 上的代码审查
- 分析与报告
- 设置 Bugbot 审查所用力度级别的能力
- 高级规则和设置

Bugbot Teams 从团队的预付额度计费，或在旧版 Teams 方案上从按需支出计费。当前费率见[价格页面](https://cursor.com/pricing#bugbot)。

### 开始使用

通过团队仪表盘订阅以启用计费。

## 故障排除

如果 Bugbot 没有工作：

1. **启用详细模式**：评论 `cursor review verbose=true` 或 `bugbot run verbose=true`，以获取详细日志、加载了哪些 Bugbot 规则，以及 request ID
2. **检查权限**，确认 Bugbot 拥有仓库访问权限
3. **验证安装**，确认仓库提供方集成已安装并启用

报告问题时，请附上详细模式中的 request ID。

## 常见问题

### Bugbot 会读取 PR 评论吗？

会。Bugbot 会读取已连接提供方的顶层和行内 pull request 评论，并在审查期间把它们作为上下文。这有助于避免重复建议，并让 Bugbot 在审查者先前反馈的基础上继续。

### 如何查看 Bugbot 使用了哪些规则？

在 pull request 上评论 `bugbot run verbose=true` 或 `cursor review verbose=true`。Bugbot 会发布一张表，列出该次运行纳入的每条规则，并标出被截断或省略的规则。如果某条规则缺失或被截断，见[规则限制](https://cursor.com/docs/bugbot.md#rule-limits)。

### Bugbot 是否符合 Privacy Mode？

是的。Bugbot 遵循与 Cursor 相同的隐私合规，并以与其他 Cursor 请求相同的方式处理数据。

### Bugbot 会读取凭证文件吗？

Bugbot 不会读取路径看起来像凭证文件的文件内容，例如 `.env`、`id_rsa` 或 `*.pem` 这类密钥文件、`credentials.json`、`*.tfvars`，以及 Terraform 状态。`.env.example` 这类模板会照常审查。其他文件照常审查，包括硬编码了密钥的源代码。

### 用完包含的 Bugbot 用量后会怎样？

用完包含的 Bugbot 用量后，额外的 Bugbot 审查会从预付额度计费，或在旧版 Cursor 方案上从按需支出计费。

### 如何让 Bugbot 访问自托管的源代码管理实例？

见各自集成页面上的设置和网络指南：

- [GitHub Enterprise Server](https://cursor.com/docs/integrations/github.md#setup)
- [GitLab Self-Hosted](https://cursor.com/docs/integrations/gitlab.md#setup)
- [Bitbucket Data Center](https://cursor.com/docs/integrations/bitbucket.md#setup)

## 相关

- [Bugbot 帮助](https://cursor.com/help/ai-features/bugbot.md)

---

## 站点地图

[所有文档页面概览](/llms.txt)
