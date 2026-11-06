> 原文：https://cursor.com/docs/cloud-agent/capabilities

# 能力

## 计算机操作

每个 Cloud Agent 都在自己的隔离虚拟机中运行，并配备完整的桌面环境。Agent 可以使用鼠标和键盘控制桌面和浏览器，从而像人类开发者一样与它们构建的软件交互。

这意味着 Agent 可以启动开发服务器、在浏览器中打开应用、点选 UI 流程，并在推送 PR 之前验证改动是否有效。更多内容见[公告博客文章](/blog/agent-computer-use)。

在[自托管机器](https://cursor.com/docs/cloud-agent/self-hosted.md)上，用 `--computer-use` 启动 worker，即可让 Agent 驱动该机器的桌面。macOS worker 使用 Cursor Computer Use 助手应用；Linux worker 使用 X11 显示。参见[计算机操作与桌面共享](https://cursor.com/docs/cloud-agent/self-hosted/computer-use.md)。

## 演示与产物

Agent 会创建截图、视频和日志引用等产物来演示它们的工作。这些产物会附加到 PR 上，因此你可以快速验证改动，而无需在本地检出该分支。

### GitHub 中的产物

你可以选择让 Cloud Agents 把产物直接嵌入 GitHub pull request 描述，方法是在 [Cloud Agents 仪表盘](https://cursor.com/dashboard/cloud-agents#my-pull-requests)中启用 **Allow posting artifacts to GitHub** 设置。

GitHub 的[图像代理](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/about-anonymized-urls)要求公开 URL，因此 PR 描述中的产物使用很长、无法猜测的 URL，无需身份验证即可查看。作为背景，直到 [May 2023](https://github.blog/changelog/2023-05-08-more-secure-private-attachments)，GitHub 对所有 issue 和 PR 附件都使用公开 URL。

## 远程桌面控制

你可以接管 Agent 的远程桌面，与 Agent 正在构建的软件交互。随时把控制权交还给 Agent，让它继续工作。

Cloud Agents 运行在远程虚拟机中，可以用你的仓库、依赖、工具和设置脚本完整配置。这样你就可以直接在 Agent 的虚拟机中测试改动，而无需在本地机器上检出该分支。

## MCP 工具

Cloud Agents 可以使用为你的团队配置的 [MCP（Model Context Protocol）](https://cursor.com/docs/mcp.md) 服务器。这使 Agent 在运行期间可以访问数据库、API 和第三方服务等外部工具与数据源。

通过 [cursor.com/agents](https://cursor.com/agents) 中的 MCP 下拉菜单添加并启用个人 MCP 服务器。团队管理员在 **Dashboard -> Plugins & MCPs** 下配置共享服务器。

管理员可以把共享的团队 MCP 服务器链接到[默认团队市场](https://cursor.com/docs/plugins.md#migrate-existing-team-mcps)。链接后，这些服务器对 Cloud Agents 保持可用，队友也可以在 Agent Window、IDE 和 CLI 中安装并配置它们。

对于需要 OAuth 的 MCP 服务器，Cloud Agents 支持 OAuth。OAuth 按用户生效，包括在团队级别共享的 MCP 服务器。

### 自定义 MCP 服务器

你可以使用 **HTTP** 或 **stdio** 传输添加自定义 MCP 服务器。不支持 SSE 和 `mcp-remote`。

MCP 配置会静态加密。敏感字段会被隐去，保存后任何用户都无法再读回：

- **`env`**：stdio 服务器的环境变量
- **`headers`**：HTTP 服务器的请求头
- **`CLIENT_SECRET`**：HTTP 服务器的 OAuth 客户端密钥

### HTTP 与 stdio

- **HTTP（推荐）**：服务器配置从不会出现在 Cloud Agent 的虚拟机环境中。Agent 无法访问刷新 token、请求头或其他凭证。工具调用通过后端代理。
- **Stdio**：服务器在 Cloud Agent 的虚拟机内运行，因此 Agent 可以访问服务器的配置和环境变量。这与 stdio MCP 在 Cursor IDE 中的工作方式类似。

Stdio 服务器依赖虚拟机环境来执行。在 Cloud Agent 启动之前，我们无法验证 stdio 服务器能否成功运行。我们建议尽可能使用 HTTP MCP，如果你使用 stdio 服务器，请正确配置你的[环境设置](https://cursor.com/docs/cloud-agent/setup.md)。

### Cursor Cloud MCP

Cursor Cloud MCP 是 Cloud Agent 运行期间可用的内置诊断服务器。它让 Agent 检查当前运行、浏览同一环境中的相关运行，并获取对话记录、diff 元数据、环境详情、运行事件和设置日志，而无需手动收集链接和文件。

团队管理员可以在 [team settings](https://cursor.com/dashboard/settings) 的 **MCP Configuration** 中为团队禁用 Cursor Cloud MCP。有关 MCP 管理控制的更多内容，见[团队仪表盘](https://cursor.com/docs/account/teams/dashboard.md#integrations)。

#### 访问与权限

Cloud Agent 对话可以包含提示词、代码、工具输出和密钥。所有工具都会在每次请求上执行访问检查。

| 角色 | 你可以访问的内容 |
| :--------- | :------------------------------------------------------------------------------------------------------------------------------------------------- |
| 团队管理员 | 列出并获取团队中 Cloud Agent 运行的详情（包括对话记录），限于他们已经可以访问的仓库和环境 |
| 非管理员 | 仅限你自己的运行和对话记录。你不能通过此 MCP 查看其他团队成员的聊天 |

即使在共享环境中列出运行，非管理员也只能看到自己启动或拥有的 Agent。服务账号遵循它们所运行的用户或团队上下文的相同规则。

#### 你可以检查什么

| 类别 | 示例 |
| :------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 当前运行 | 运行 ID、URL、仓库、分支、模型、所有者、生命周期状态，以及运行的启动位置（Cursor、Slack、GitHub、API 等） |
| 事件 | 运行仪表盘上显示的设置、pull request、产物和 MCP 身份验证结果。当前运行使用 `get-events`，其他运行使用带 `include_events` 的 `batch-fetch-details`。事件种类见工具一节。 |
| 相关运行 | 同一环境中的其他 Cloud Agents；如果没有附加已保存的环境，则为同一仓库上的其他 Cloud Agents |
| 环境 | 环境版本、完整环境配置、仪表盘 URL，以及生效的出站网络策略 |
| 对话记录 | 完整的用户与 Agent 对话，包括可用时的工具调用 |
| Diff 元数据 | Agent 是否更改了代码、更改了多少，以及是否打开了 PR |
| 设置日志 | 环境设置和镜像构建步骤的原始日志 |

#### 工具

根据你的 MCP 客户端，工具名称可能包含服务器前缀（例如 `cursor-cloud-run-info`）。底层工具包括：

| 工具 | 用途 |
| :---------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `run-info` | 获取当前运行的身份、元数据和 URL。从这里开始。 |
| `environment-info` | 获取当前运行的环境版本、配置、仪表盘 URL、生效的出站策略，以及该运行所启动自的[构建](https://cursor.com/docs/cloud-agent/builds.md)。 |
| `get-events` | 列出当前运行的仪表盘事件，从最早的开始。 |
| `rename-chat` | 重命名当前运行的聊天。新标题最多 200 个字符，显示在 Agent 列表和对话标题中。 |
| `list-cloud-agents` | 浏览你在此环境中可见的 Cloud Agent 运行。可按来源、状态、日期、代码改动、PR 创建和归档状态筛选。 |
| `batch-fetch-details` | 获取特定运行 ID（`bcId`）的详情。可选地通过 `include_events` 包含对话记录、diff 元数据、设置日志、环境信息和运行事件（每次运行写入 `events.json`；每批最多 50 次运行）。 |
| `get-automation` | 根据 ID 获取自动化的详情，例如名称和所有者。 |
| `list-environment-builds` | 列出当前环境最近的[构建](https://cursor.com/docs/cloud-agent/builds.md)，并查看它们的状态。 |
| `environment-build-logs` | 下载某次构建的安装和设置日志。 |
| `trigger-environment-build` | 用当前配置，或提议的 install 与 start 命令，运行一次测试构建。 |
| `propose-environment-json` | 在保存环境之前，展示 install 和 start 命令供你审查。 |
| `take-environment-snapshot` | 在 Agent 验证其环境设置之后，为机器创建快照。 |
| `check-environment-snapshot` | 检查环境快照是否就绪。 |
| `request-environment-setup-actions` | 请求会阻塞环境设置的用户操作，例如添加密钥。 |

来自 `get-events`，以及带 `include_events` 的 `batch-fetch-details` 的仪表盘事件 `kind` 值如下：

| `kind` | 含义 |
| :------------------- | :------------------------------------------------------------------------------- |
| `setup_started` | 环境设置已开始。 |
| `setup_completed` | 环境设置已完成。 |
| `setup_failed` | 环境设置失败。 |
| `pr_created` | pull request 已打开。 |
| `pr_creation_failed` | pull request 创建失败。 |
| `artifact_created` | 演练产物已上传。 |
| `mcp_auth_error` | MCP 服务器身份验证失败；其工具被跳过，运行继续。 |

典型的诊断流程是 `run-info` → `get-events` → `environment-info` → `list-cloud-agents` → `batch-fetch-details`（当你需要其他运行的仪表盘事件时，设置 `include_events`）。

## 订阅

Agent 的任务很少在最后一次提交时结束。CI 必须通过。审查者会留下评论。队友需要在 Slack 中回答一个问题。订阅让 Cloud Agent 等待这些事件，并在它们发生时继续工作，而无需你再次发送提示词。

Agent 订阅一个事件源，结束当前回合，并在匹配的事件到达时醒来。事件作为后续消息进入同一对话，因此 Agent 带着完整上下文继续：

- 打开一个 PR，然后回应审查评论和 CI 失败
- 在 Slack 中提出一个问题，并在有人回复后继续
- 用计时器回头查看一个长时间运行的作业

要订阅，请在提示词中描述等待。例如，“打开一个 PR 并保持 CI 通过”或“在 #releases 中询问并等待批准”。你也可以调用内置的 `/subscribe` Skill，它的用法相同：告诉它要观察什么，Agent 会选择正确的订阅。

Agent 可以订阅来自这些集成的事件：

| 集成 | 事件 |
| :---------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| GitHub | 针对一个 PR 的 pull request 活动（评论、审查和生命周期变化），以及分支上的 CI 结果。使用 [GitHub 集成](https://cursor.com/docs/integrations/github.md)。 |
| Slack | 线程中的回复和频道中的消息。使用 [Slack 集成](https://cursor.com/docs/integrations/slack.md)。 |
| Linear | 创建的 issue 或状态发生变化的 issue，以及 issue 上的新评论。使用 [Linear 集成](https://cursor.com/docs/integrations/linear.md)。 |
| 计时器 | 一个时间点：延迟后的一次性提醒，或重复的 cron 计划。重复循环也可以作为内置的 [`/loop`](https://cursor.com/docs/skills.md#built-in-cursor-skills) Skill 使用。 |

### 订阅如何工作

- 订阅属于单个 Agent 对话。事件会作为后续消息唤醒该 Agent。
- 突发事件会合并。短时间内到达的多个事件可以只唤醒 Agent 一次，Agent 在行动前会重新阅读来源（PR、线程或 issue）。
- 一个订阅最多持续 180 天。等待结束时，Agent 也会自行取消订阅。
- 订阅需要一个由人拥有的 Agent。由[服务账号](https://cursor.com/docs/account/enterprise/service-accounts.md)拥有的 Agent，包括设为 [Run as: Service account](https://cursor.com/docs/cloud-agent/automations.md#run-as) 的自动化，不会获得 `/subscribe` Skill 或订阅工具。

### GitHub CI 订阅

CI 订阅会等到该提交上的每一项检查都完成，然后交付一个覆盖整个提交的结果：成功，或失败并附上失败检查的名称。

有些检查会长时间处于待处理状态，例如在等待某人批准时。单个待处理的检查会挡住整个结果，Agent 会继续等待。

用 GitHub 的 [`action_required`](https://docs.github.com/en/rest/checks/runs#create-a-check-run) 结论结束这些检查，而不是让它们保持待处理。检查会完成，仍然需要操作，并且当它是必需检查时仍然阻止合并，因此 CI 订阅会交付结果，同时合并仍受保护。

## 修复 CI 失败

Cloud Agents 会自动尝试修复它们所创建 PR 中的 CI 失败。目前仅支持 GitHub Actions。

在以下情况下，Cloud Agents 会跳过自动 CI 跟进：

- 你已向该分支推送了新的提交；Cloud Agents 不会对人类的提交自动修复 CI 失败。
- 你已向该 Agent 发送了后续消息。
- 同一项检查在该 PR 的基础提交上已经失败。
- 该 PR 已经有 10 次 CI 失败跟进。

要在你所有的个人 Cloud Agents 上禁用此功能，请前往 [Cursor Dashboard → Cloud Agents → My Settings](https://cursor.com/dashboard/cloud-agents)，并关闭 “Automatically fix CI Failures” 选项。

要在某个特定的 Cloud Agent PR 上禁用此功能，可以在该 PR 上评论 `@cursor autofix off`。要重新启用，请评论 `@cursor autofix on`。

如果你希望 Cloud Agents 修复你自己的 PR 中的 CI 失败，可以像平常一样在评论中提及 Cursor 来请求它们。例如，`@cursor please fix the CI failures`，或 `@cursor fix the CI lint check failure`。

自动修复 CI 失败适用于 Teams 和 Enterprise 方案。在其他方案上，请让 Cloud Agent 监控并修复该 PR 上的 CI 失败。

## OIDC 身份 token

Cursor 托管的 Cloud Agent 虚拟机可以从本地套接字签发短期 OIDC JWT。Agent 用它们来代入云角色或调用内部 API，而无需存储长期密钥。参见 [OIDC token](https://cursor.com/docs/cloud-agent/identity.md)。

## Agent 元数据

同一个套接字也提供 [Agent 元数据](https://cursor.com/docs/cloud-agent/metadata.md)。Agent、Hooks 和脚本可以以纯文本读取 Agent id、所有者、当前回合和工作区。

---

## 站点地图

[所有文档页面概览](/llms.txt)
