> 原文：https://cursor.com/docs/cloud-agent/automations

# 自动化

Cursor 自动化在后台运行 [Cloud Agents](https://cursor.com/docs/cloud-agent.md)，可以按计划运行，也可以响应来自 GitHub、GitLab、Slack、webhook、Linear 等的事件。

自动化可以用来自动完成这些任务：[审查最近的 PR 提交以查找缺陷](/marketplace/automations/find-bugs)、[对漏洞进行深入审查](/marketplace/automations/find-vulnerabilities)、[在 Slack 中分拣缺陷](/marketplace/automations/fix-slack-bugs)，以及[按计划总结代码库的变更](/marketplace/automations/daily-digest)。

## 开始使用

在 [Agents Window](https://cursor.com/docs/agent/agents-window.md)、[cursor.com/automations](/automations)，通过本地 Agent 会话中的 `/automate` Skill，或从 [Cursor Marketplace](/marketplace/automations) 的模板创建新的自动化。

`/automate` Skill 让你用通俗的语言描述想要的工作流。Cursor 会为你配置自动化的触发器、说明和工具。

无论从哪条路径开始：

1. 选择一个触发器，例如每小时，或当 pull request 被打开时。
2. 编写带有自动化说明的提示词。
3. 选择 Agent 可以使用的可选工具，例如 Send to Slack、Comment on Pull Request，或来自 MCP 的工具。
4. 选择自动化需要一个仓库、多个仓库，还是完全不需要仓库。
5. 保存并激活自动化。

Automations 页面还包含三个由 Cursor 管理的 Agent：

- [Bugbot](https://cursor.com/docs/bugbot.md) 审查 pull request 中的缺陷和代码质量问题。
- [Security Agents](https://cursor.com/docs/security-agents.md) 审查 pull request 并扫描代码库中的漏洞。
- [PR Routing & Approval](https://cursor.com/docs/approval-agents.md) 把 pull request 路由给审查者，并可以批准低风险改动。

从 **From Cursor** 下的卡片打开它们。它们不会出现在 **Mine** 或 **Team** 自动化列表中。

## 计费

自动化会创建 Cloud Agents，并按 Cloud Agent 用量计费。详情见 [Cloud Agent 定价](https://cursor.com/docs/models-and-pricing.md#model-pricing)。

自动化使用每个模型所支持的最大上下文窗口，因为它们作为 Cloud Agents 运行。没有上下文窗口开关。

用量如何计费取决于[共享](https://cursor.com/docs/cloud-agent/automations.md#share)中的 **Run as**：

- **Me**：用量向你计费。其他成员看到的此选项是 **Creator**。
- **Service account**：用量向团队的用量池计费。该自动化以它自己的专用服务账号运行，因此不会使用任何成员的个人用量。

个人账号始终向你计费。它们没有 Share 菜单。

## 触发器

触发器决定自动化何时运行。一个自动化可以有多个触发器，并且在*任一*触发器触发时运行。

对于 Slack 或 cron 计划等某些触发器，Cursor 默认不使用仓库。如果你的自动化应当做出代码改动，请指定 Agent 应在哪个或哪些仓库中工作。对于源代码管理触发器，必须指定一个或多个仓库。

### 计划触发器

计划触发器按重复计划运行。可以从预设选项中选择，或输入 cron 表达式以精确控制。

计划触发器可能会延迟运行，但不会在所示时间之前开始。

### 源代码管理触发器

源代码管理触发器响应来自已连接 Git 提供方的 pull request 和推送事件：GitHub、GitLab 和 Bitbucket Cloud。把自动化连接到一个仓库或一个多仓库环境。

每个已连接的提供方都支持这些核心的 pull request 和推送触发器：

- **Draft opened**：创建草稿 pull request 时。
- **Pull request opened**：创建非草稿 PR 时，或草稿被标记为可以审查时。
- **Pull request pushed**：新的提交被推送到现有 PR 时。
- **Pull request merged**：PR 被合并时。
- **Push to branch**：提交被推送到 pull request 之外的特定分支时。
- **Comment added**：有人在 pull request 上留下顶层评论时。

GitHub 支持的触发器最多。GitLab 和 Bitbucket 支持上面的核心触发器，外加各自小节中列出的一些额外触发器。

#### GitHub 触发器

GitHub 是参考提供方，支持每一个源代码管理触发器。除核心触发器外，它还增加：

- **Pull request label changed**：特定标签或任意标签被添加到 pull request 或从中移除时。
- **Issue label changed**：标签被添加到非 PR 的 issue 或从中移除时。
- **CI completed**：GitHub check 在 pull request 或分支上完成时。
- **Issue comment**：有人在非 PR 的 issue 上发表评论时。
- **PR review comment**：有人在 pull request 的 diff 上留下行内评论时。
- **PR review submitted**：审查以批准、请求更改或评论的形式提交时。
- **Review thread updated**：pull request 上的审查线程被标记为已解决或未解决时。
- **Workflow run completed**：由 `push` 事件启动的 GitHub Actions 工作流运行结束时。由 `pull_request`、`workflow_dispatch` 或 `schedule` 启动的运行不匹配；pull request 检查请使用 **CI completed**。**Workflow** 筛选项匹配工作流文件名，例如 `deploy.yml`。

[Cursor Marketplace](/marketplace/automations) 包含用于[分拣失败的 GitHub Actions](/marketplace/automations/triage-github-workflow-failures)和[修复 pull request 审查评论](/marketplace/automations/autofix-pr-review-comments)的模板。

#### GitLab 触发器

除核心触发器外，GitLab 还增加：

- **Pull request label changed**：标签被添加到 merge request 或从中移除时。
- **Pull request approved**：merge request 被批准时。

#### Bitbucket 触发器

Bitbucket 支持仅覆盖 Bitbucket Cloud（`bitbucket.org`）。不支持 Bitbucket Server 和 Data Center。除核心触发器外，它还增加：

- **Pull request approved**：pull request 被批准时。

Bitbucket Cloud 没有 pull request 标签触发器，也没有行内审查评论触发器。

从 fork 打开的 PR 不会运行 pull request 触发器。这些运行会失败，并出现 “Fork pull requests not supported” 错误，因为该分支只存在于 fork 上，用仓库的权限运行外部代码并不安全。例外是 **Pull request merged** 触发器，它们仍会运行，因为它们从合并提交开始。要绕过这一点，请把分支推送到仓库本身，并从那里打开 PR。

### Slack 触发器

Slack 触发器响应来自 [Cursor Slack 集成](https://cursor.com/docs/integrations/slack.md)的事件。

目前，Slack 触发器只能看到公开的 Slack 频道。

- **New message in channel**：消息被发送到已连接的 Slack 频道时。如果没有消息筛选，该触发器只在频道的顶层消息上触发。如果也希望线程回复触发运行，请添加关键词或正则表达式筛选。
- **Emoji reaction**：有人用特定 emoji 回应一条 Slack 消息时。
- **Channel created**：工作区中创建了新的公开 Slack 频道时。

### Webhook 触发器

Webhook 触发器为你的自动化创建一个私有 HTTP 端点。向该端点 POST 即可启动一次运行。你可以用 webhook 把自动化连接到内部系统、CI 流水线、监控工具等。

要获取 webhook URL，必须先保存自动化，之后才会生成可调用的 webhook URL 和用于身份验证的 API 密钥。

### Linear 触发器

Linear 触发器响应来自 [Cursor Linear 集成](https://cursor.com/docs/integrations/linear.md)的事件。

- **Issue created**：创建新 issue 时。
- **Status changed**：issue 的状态变化时。在 **to** 之后选择一个或多个状态，即可仅在 issue 进入其中之一时运行，或保持 **Any status**。
- **End of cycle**：Linear 周期完成时。

### Sentry 触发器

当 Sentry 项目中发生错误和 issue 事件时，Sentry 触发器会运行。用它们自动调查错误、识别根因并提出修复。现成示例见[调查 Sentry issue](/marketplace/automations/investigate-sentry-issues)市场模板。

- **Issue created**：在 Sentry 中创建新 issue 时。
- **Issue updated**：现有 issue 发生变化时，例如状态或指派更新。
- **Any issue event**：匹配所有 issue 事件类型。

### PagerDuty 触发器

PagerDuty 触发器在事件发生时运行，可用于自动分拣甚至解决事件。只有团队管理员可以连接或断开团队的 PagerDuty 集成。

- **Incident triggered**：创建新事件时。
- **Incident acknowledged**：事件被确认时。
- **Incident resolved**：事件被解决时。
- **Any incident event**：匹配所有事件类型。

## 工具

Cursor 自动化可以启用工具，以获得围绕 GitHub、Slack、记忆、MCP 等的更丰富能力。自动化也包含与其他 Cloud Agents 相同的基础工具集。详情见 [Cloud Agent 能力](https://cursor.com/docs/cloud-agent/capabilities.md)。

当有人从 Web、Slack 或 GitHub 对一次自动化运行发送后续消息时，Agent 不会为该回复使用 Send to Slack 或 Comment on pull request 这类消息或评论工具。Cursor 会把回复发送到后续消息所在的位置。

### 创建 pull request

有仓库支持的自动化在按自动化提示词完成代码改动后，可以打开 pull request。每个自动化都默认启用此工具。

pull request 会针对源代码管理触发器所指定的仓库打开。对于其他触发器，它使用环境所指定的仓库。

### 在 pull request 上评论

在目标 pull request 上发表评论。支持顶层审查评论和行内代码评论。

如果启用批准，Agent 还可以批准、请求更改和驳回审查。否则，它只能发表评论。

### 请求审查者

在目标 pull request 上请求审查者。Agent 可以使用 `git`、记忆和其他工具来识别领域专家。

### 发送到 Slack

向 Slack 频道发送消息。你可以指定某个频道，或让 Agent 动态选择任意频道。

当你允许任意频道时，Cursor 还会附带 Agent 发现可用公开频道所需的读取权限。

请注意，Agent 会被授予对其可以发送消息的公开频道的读取权限。

要让 Agent 提及人员或用户组，请把 Slack 的提及 token 作为纯文本放进提示词，不要加反引号：人员用 `<@U123>`，用户组用 `<!subteam^S123|@handle>`。用户组 ID 以 `S` 开头。

### 读取 Slack 频道

让 Agent 以只读方式列出并阅读公开 Slack 频道中的消息。

当 Agent 在回复或打开 pull request 之前需要更多上下文时，使用此项。

### MCP 服务器

连接 [MCP（Model Context Protocol）](https://cursor.com/docs/mcp.md) 服务器，使 Agent 可以使用外部工具和数据源。

连接 MCP 服务器会让 Agent 访问该服务器暴露的每一个工具。只连接你信任、且具备自动化所需权限的服务器。

如果 MCP 服务器拒绝了自动化的凭证，运行会在没有该服务器工具的情况下继续，其页面会显示 **MCP authentication failed** 事件。在 Enterprise 上，[OpenTelemetry Export](https://cursor.com/docs/enterprise/opentelemetry-export/wire.md#cursorcloud_agentmcp_auth_error) 也会把它作为 `cursor.cloud_agent.mcp_auth_error` 日志发送。

### 记忆

记忆让 Agent 在同一次自动化的多次运行之间读取和写入持久笔记。用它来构建能够记住并随时间改进的 Agent。每条记忆存储为一个具名条目（默认为 `MEMORIES.md`），存在于 Agent 的工作文件系统之外。

记忆默认启用，但可以关闭。可以在工具配置界面中查看和编辑记忆。

Agent 可以在自动化运行期间删除过时的记忆文件。你也可以在工具配置界面中删除记忆文件。

记忆会跨运行保留。如果你的自动化处理不受信任的输入，应谨慎使用。输入可能导致误导性或恶意的记忆，无意中影响以后的自动化运行。

### 计算机操作

计算机操作让由自动化启动的 Cloud Agents 像开发者一样使用计算机。这意味着自动化可以操作浏览器、生成截图或录制，或使用你的内部服务。每个自动化都默认包含它。

要让计算机操作有效，请确保已为自动化配置开发环境。然后，当你希望 Agent 展示其工作时，可以在自动化说明中要求一次演示。例如，让 Agent 在更改面向用户的流程后附上一段简短的屏幕录制。

## 自动化设置

### 模型

你可以选择 Cloud Agent 为该自动化使用哪个模型。

### 仓库

选择自动化不需要仓库、需要一个仓库，还是需要多仓库环境。

仓库设置控制每次运行的代码库上下文：

- **No repository**：Agent 不克隆代码。用于只需要 Slack、MCP、webhook、Linear 或 PagerDuty 的工作流。它不能编辑代码或打开 pull request。
- **Single repository**：Agent 在一个仓库和一个分支中工作。当自动化应当在一个代码库中读取、审查或更改代码时使用。
- **Multi-repo environment**：Agent 在一个环境中的多个仓库上工作。当任务跨越多个代码库时使用。

对于 Slack 或 cron 计划等某些触发器，Cursor 默认不使用仓库。如果你的自动化应当做出代码改动，请指定 Agent 应在哪个或哪些仓库中工作。

对于源代码管理触发器，必须指定一个或多个仓库。

#### 单仓库自动化

默认情况下，自动化针对一个仓库和一个分支运行。当 Agent 应当在单个代码库中读取、审查或更改代码时，这是正确的选择。

源代码管理触发器从 pull request 推断仓库。对于其他触发器，请在自动化设置中选择仓库和分支。

#### 多仓库自动化

当自动化需要跨多个仓库工作时，使用多仓库环境。配置环境时选择多个仓库，或从 [Cloud Agents 仪表盘](https://cursor.com/dashboard/cloud-agents#environments)中选择一个现有环境。

在[自托管机器](https://cursor.com/docs/cloud-agent/self-hosted.md#environments-on-self-hosted-machines)上，自动化只使用该环境的仓库。用 `--clone-git-repos` 启动的任意仓库池 worker 会检出所有这些仓库。其他 worker 使用它们已有的检出。

## 共享

团队账号从自动化详情标题上的 **Share** 设置可见性和身份。个人账号没有此菜单。

### Run as

- **Me**：该自动化使用你的身份验证运行。用量向你计费。其他成员看到的此选项是 **Creator**。
- **Service account**：该自动化以它自己的专用服务账号运行。用量向团队的用量池计费。只有团队管理员可以选择此项。

### 访问

- **Private**：其他团队成员看不到该自动化。只有你可以管理它。团队管理员可以查看并禁用它。
- **Members can view**：团队成员可以查看该自动化。当它以你的身份运行时，由你管理。当它以服务账号运行时，由团队管理员管理。两种情况下管理员都可以禁用它。
- **Members can edit**：团队成员可以查看并编辑该自动化。

该菜单还包含 **Copy link**。

把 **Run as** 改为 **Service account** 会改变自动化使用的身份。它不再使用你的身份验证，而开始使用专属于该自动化的服务账号。如果自动化使用 webhook 触发器，请在更改后重新生成其 webhook API 密钥。如果它使用依赖个人 OAuth 凭证的 MCP 或其他集成，请改为为团队的服务账号配置这些凭证。只有团队管理员可以把自动化切换到服务账号。

### 自动化的服务账号

每个设为 **Run as: Service account** 的自动化都有自己的服务账号。无论是从自动化编辑器，还是通过 API 或 Terraform provider，Cursor 都会在该自动化第一次以该设置保存时创建它。

[Cursor Terraform provider](https://registry.terraform.io/providers/cursor/cursor/latest/docs) 把自动化作为 `cursor_platform_workflow` 资源来管理。使用团队服务账号的 API 密钥对其进行身份验证（provider 的 `token`，或环境变量 `CURSOR_TOKEN`），并让 `team_id` 保持未设置。工作流的 `scope` 设置其 Share 选项：`team` 以服务账号运行并由团队管理员管理，`team_editable` 以服务账号运行且成员可以编辑，`team_visible` 以其所有者身份运行且成员可以查看。

在 Enterprise 团队中，这些账号列在 Dashboard → Settings → API Keys → Service Accounts 下，名称类似 `automation-<id>`。名称中的 ID 和服务账号 ID（`sa_...`）都不是自动化的 ID。

一些较旧的自动化共用一个名为 `automations` 的团队服务账号。它们会在下一次保存时改为使用自己的服务账号。

以服务账号运行的自动化，如果该服务账号被归档或删除，就会停止运行。只有在确定没有自动化仍在使用时，才归档 `automation-` 服务账号。

## 身份

当自动化作用于外部服务时，它使用以下身份：

- GitHub 评论、审查批准和审查者请求以 `cursor` 的身份运行。
- 以你的身份运行的自动化，会用你的 GitHub 账号打开 pull request。
- 以服务账号运行的自动化，会以 `cursor` 的身份打开 pull request。
- Slack 消息以 Cursor 机器人的身份发送。

## 编写提示词

提示词定义 Agent 应当做什么。写法与你为一次 Cloud Agent 运行编写说明相同。

提示：

- 具体说明 Agent 应当检查、更改或产出什么。
- 引用你启用的操作：你可以 @ 提及工具，或非正式地提到它们的名称。
- 包含针对不同情况该怎么做的决策规则。
- 为 Agent 何时应当打开 pull request、发表评论或什么都不做设定质量标准。
- 描述你想要的输出格式。

## 相关

- [Agents Window](https://cursor.com/docs/agent/agents-window.md)
- [Cloud Agents 概览](https://cursor.com/docs/cloud-agent.md)
- [Cloud Agent 环境设置](https://cursor.com/docs/cloud-agent/setup.md)
- [Cloud Agent 定价](https://cursor.com/docs/models-and-pricing.md#model-pricing)
- [Skills](https://cursor.com/docs/skills.md)
- [GitHub 集成](https://cursor.com/docs/integrations/github.md)
- [GitLab 集成](https://cursor.com/docs/integrations/gitlab.md)
- [Slack 集成](https://cursor.com/docs/integrations/slack.md)
- [Microsoft Teams 集成](https://cursor.com/docs/integrations/microsoft-teams.md)
- [Linear 集成](https://cursor.com/docs/integrations/linear.md)
- [自动化帮助](https://cursor.com/help/ai-features/automations.md)

---

## 站点地图

[所有文档页面概览](/llms.txt)
