> 原文：https://cursor.com/docs/grok-bot/security

# Grok Bot 安全

用这些控制和部署细节决定是否允许 Grok Bot 进入，并限制 Bot 可以访问、更改和保留什么。推出步骤和仪表盘设置列表位于[面向 Teams 与 Enterprise 的 Grok Bot](https://cursor.com/docs/grok-bot/teams.md)。常见审查问题位于 [Grok Bot 安全常见问题](https://cursor.com/docs/grok-bot/security-faq.md)。

Grok Bot 仪表盘上**仅限 Enterprise** 的有：组织范围的启用开关、**Network Controls**、**Team Setup Scripts**、**Team Secrets**、**Action Recording**、**Allow Local Egress Routing**、**Allow Sudo on Bot Computers**、带团队规则的 **Enforce Auto-review**、**Inference Hooks**，以及面向组织管理员的计算机管理。审计日志、OpenTelemetry Export、MCP 允许列表和 SCIM 也仅限 Enterprise。自助 Teams 看不到这些设置。完整列表位于[管理员控制](https://cursor.com/docs/grok-bot/teams.md#admin-controls)。

## 架构

Grok Bot 桌面和移动应用把每个请求发送到 Cursor 的服务器。从那里，工作在成员的托管计算机上运行，提示词发往模型提供商，工具调用通过已连接应用到达第三方服务。

每条路径上都有一项控制。每一项都链接到本页的对应章节。

## 网络策略

**Network Controls 仅限 Enterprise。** 管理员在 Cursor 仪表盘 [Grok Bot 页面](https://cursor.com/dashboard/bot)的 **Network** 下、于 **Grok Bot Network Access** 中设置 Grok Bot 网络策略。它控制团队计算机可以到达哪些目的地。自助 Teams 看不到这个面板，也不能设置目的地允许列表。没有策略的团队默认为允许全部。

| 模式 | 效果 |
| ----------------------------- | ------------------------------------------------------------------ |
| **Allow All Network Access** | 所有目的地。没有策略的团队得到这一项。 |
| **Defaults + Team Allowlist** | Cursor 的默认目的地加上你的列表 |
| **Team Allowlist Only** | 只有你的列表，加上计算机运转所需的目的地 |

- **目的地**涵盖 Web 域名，以及带端口的、用于原始连接的 IP 范围，条目数量没有上限。
- **目录组。仅限 Enterprise，在 Network Controls 之内。** 组可以设置自己的网络策略，它会替换其成员的团队策略，锁定后团队策略对所有人生效。
- **该策略与 Cloud Agent 网络设置是分开的。** 正在运行的计算机大约在一分钟内自动应用更改。休眠的计算机在下次唤醒时应用更改。你不需要重建计算机。
- **限制出口会限制数据可以发往哪里。** 要对照你自己的数据防泄漏策略检查到达模型的内容，使用 [inference hooks](https://cursor.com/docs/grok-bot/security.md#inference-hooks)。

阻止一个应用并不会阻止该服务的网站。连接器策略和网络策略是独立的层，要关闭两条路径需要两项控制。

### MCP 服务器与网络策略

网络策略管理团队计算机把流量发往哪里。已连接应用（MCP 服务器）不同：Bot 从 Cursor 的服务器到达远程 MCP 服务器，因此允许列表模式本身并不覆盖它们。要让它们受同一列表约束，打开 **Apply to MCP servers**。该开关位于 **Grok Bot Network Access** 中 **Allowed destinations**（旧设置页面上为 Allowed Destinations）之下、**Lock for all groups**（Lock for All Groups）之上，并且只在选中 **Default, plus the allowlist**（Defaults + Team Allowlist）或 **Allowlist only**（Team Allowlist Only）时出现。

- **关闭，这是每个团队的默认值。** 无论网络策略如何，Bot 都可以连接到连接器策略所允许的任何 MCP 服务器。
- **开启。** 远程 MCP 服务器还必须匹配 **Allowed destinations** 中的一个条目。不匹配的服务器会在 Bot 尝试使用它时被拒绝，成员会看到要请管理员添加哪个目的地。在计算机上运行的命令类服务器不受影响；网络策略已经适用于它们。

在依赖它之前先打开该开关：允许列表模式本身并不限制 MCP 服务器。该开关是团队设置。目录组会继承它，不能关闭，锁定的团队策略包含它。保存它不会重启计算机；它适用于下一次 MCP 连接。它与 Team Settings 中 **MCP Configuration** 下的设置是分开的，那些设置管理编辑器中的 Cursor；改其中一个不会改另一个。

**内置连接器也会被检查。** 该开关覆盖 Bot 使用的每个已连接应用，包括 Cursor 在 **Connect apps** 下为你运营的那些，例如 GitHub、X、Origin、Google Workspace 和 Microsoft 365。已连接应用按其发送请求的主机检查，而不是按连接器列表中的名称，因此在打开开关之前把这些主机加入 **Allowed destinations**。主机并未全部被允许的连接器，会在 Bot 下次使用它时被拒绝，并显示消息 `MCP server "<host>" refused: it is not on your team's Grok Bot network allowlist (Grok Bot settings > Network > Allowed destinations). A team admin can add it.`

| 已连接应用 | 要允许的主机 |
| ---------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| GitHub | `api.github.com` |
| X | `api.x.com` |
| Origin | `api.origin.cursor.com` |
| Gmail | `gmail.googleapis.com`；如果连接器是在 Google 的托管地址添加的，还包括 `gmailmcp.googleapis.com` |
| Google Calendar | `www.googleapis.com`；如果是在 Google 的托管地址添加的，还包括 `calendarmcp.googleapis.com` |
| Google Drive | `www.googleapis.com`、`sheets.googleapis.com`；如果是在 Google 的托管地址添加的，还包括 `drivemcp.googleapis.com` |
| Google Docs | `docs.googleapis.com` |
| Google Sheets | `sheets.googleapis.com` |
| Google Slides | `slides.googleapis.com`、`slidesmcp.googleapis.com` |
| Google Ads | `googleads.googleapis.com` |
| Outlook、Outlook Calendar、OneDrive、Microsoft Teams | `graph.microsoft.com` |
| Stripe Link | `api.link.com` |
| Tailscale | `api.tailscale.com`、`login.tailscale.com` |
| QuickBooks Online | `ah-api.merge.dev`、`quickbooks.api.intuit.com`（sandbox 公司使用 `sandbox-quickbooks.api.intuit.com`）、`oauth.platform.intuit.com`、`developer.api.intuit.com` |
| Workday | `ah-api.merge.dev`，加上连接器设置中填入的你的 Workday 租户的登录主机和 REST API 主机 |
| WhatsApp Business | `ah-api.merge.dev`、`graph.facebook.com` |
| Zoho CRM、Zoho Books | `ah-api.merge.dev`，加上你的 Zoho 数据中心的账户主机和 API 主机，例如美国使用 `accounts.zoho.com` 和 `www.zohoapis.com`（`.eu`、`.in`、`.com.au`、`.jp`、`.sa`、`.uk`、`.ae`、`.sg`，加拿大则使用 `accounts.zohocloud.ca` 和 `www.zohoapis.ca`） |
| Zoho Desk | `ah-api.merge.dev`，加上你的数据中心的账户主机及其 Desk 主机，例如 `accounts.zoho.com` 和 `desk.zoho.com` |
| Microsoft Power BI | `api.powerbi.com` |
| Databricks Genie | 你的 Databricks 工作区主机，由团队管理员在插件设置中填入，例如 `dbc-a1b2c3d4-e5f6.cloud.databricks.com` |
| Google BigQuery | `bigquery.googleapis.com` |
| Asana | `mcp.asana.com` |
| Vanta | 登录时选择的区域对应的主机：`mcp.vanta.com`（美国）、`mcp.eu.vanta.com`（欧洲）或 `mcp.aus.vanta.com`（澳大利亚） |
| Shop Pay | `mcp.shop.com` |

Team Marketplace 中的其他连接器按其服务器地址中的主机检查，该地址显示在仪表盘的 [Plugins & MCPs 页面](https://cursor.com/dashboard/plugins)上；允许该主机。

## 静态出口 IP

托管计算机默认通过共享的静态出口 IP 地址访问互联网。这些范围在 Grok Bot 客户之间共享，没有按客户专用的 IP，因此把这些范围视为标识 Grok Bot 流量，而不是只标识你的团队。当前范围可从你的客户团队获取，产品控制是目的地允许列表，而不是源 IP 编辑器。

如果成员设备位于 Zscaler 或其他会检查 TLS 的网关之后，请允许 Cursor 的域名，并在每个配置文件上把它们排除在检查之外，包括离网。见[配置检查 TLS 的代理](https://cursor.com/docs/grok-bot/proxies.md)。

成员可以把流量经由自己的桌面路由，以使用其 IP 地址；在该路由上，解析到私有地址或 VPN 地址的站点仍会被阻止，除非已为你的组织允许。**Team Setup Scripts** 仅限 Enterprise。这些团队还可以在每台团队计算机上安装自己的网络客户端。两条路径都与共享出口范围分开。见[连接到私有网络](https://cursor.com/docs/grok-bot/private-networks.md)。

## 批准与 Auto Review

批准把有实质后果的操作留在成员的控制之下。最强的边界就在请求本身里，因此让成员说明 Bot 可以改什么、必须在哪里停下：

> 核对活动数据，并起草建议的预算变更。不要更改活动，也不要给代理商发消息。在展示当前值、建议值和预期影响之后请求批准。

当某个操作需要批准时，对话会显示提议的操作及其输入。**Allow once** 让 Bot 继续该操作，**Always allow** 可以保存一条匹配规则，**Deny** 会停止该操作；在成员再发送一条消息之前，Bot 不会再试。iPhone 上的控件相同。一次批准控制的是提议的操作，不是已经完成的工作，任何人都不应当批准自己无法辨认目标或效果的操作。

Auto Review 是这些提示背后的审查层：一个独立的审查模型，在有风险的 Bot 操作运行之前评估它们，覆盖 shell 命令、应用调用、计算机操作、自动化写入（对例程和事件触发器的更改），以及 Cloud Agent 和 subagent 启动这类委派。它可以让操作继续、要求批准，或拒绝它。

- **团队管理员可以强制执行 Auto-review。仅限 Enterprise。** 开关位于 Cursor 仪表盘的 Grok Bot 页面。开启后，成员不能关闭 Auto-review，除非他们所属的组用 **Don't enforce for this group** 解除锁定；见[组设置](https://cursor.com/docs/grok-bot/teams.md#group-settings)。
- **管理员可以添加团队 Auto-review 规则。仅限 Enterprise。** 这些规则也位于 Grok Bot 页面。它们适用于每个成员的 Bot，在成员设置表中显示为锁定的行，并且在管理员添加、编辑或删除规则时自动保存。如果管理员关闭强制执行，团队规则就不再应用，成员回到只用自己的规则。
- **成员可以添加个人规则**，位于 **Settings** > **General** > **Auto-review** 的 **Custom Rules** 中。**Ask first** 规则总会拦住匹配的操作，**Allow automatically** 规则只在审查者没有发现其他需要拦住的理由时才让匹配的操作继续。成员可以在团队规则之上添加个人规则，但它们只会让行为更严格；规则冲突时 **Ask first** 优先。规则要保持狭窄，并绑定到已知操作，例如「发送任何外部电子邮件之前先询问」，或「在 `/workspace/reports` 中运行 `git status` 时自动允许」。避免「允许浏览器中的一切」这类宽泛规则。个人规则保存在成员的账户中，因此从每台桌面和移动应用都适用。
- **它不会审查每一个副作用。** 记忆写入和大多数设置更改就是例子。把它当作对明确边界和最小权限的补充，与不依赖模型判断的控制一起使用：逐项批准、网络策略，以及按用户隔离。

## Inference hooks

Inference hooks 把每个 Grok Bot 模型请求签名后发送到你的组织自己运行、或通过安全供应商使用的 AI 安全服务器。你的服务器在模型运行之前允许或拒绝该请求，因此你自己的策略会检查提示词以及 Grok Bot 收集的内容，例如网页和应用结果。新配置从 Shadow mode 开始，它把拒绝记录到审计日志，但不阻止任何东西。

Inference hooks 检查模型输入。打开 **Validate Tool Calls** 后，它们也会在工具调用运行之前检查工具调用，但不检查操作开始之后做了什么。保留网络策略和 Auto Review，以控制 Bot 做什么。设置、模式、请求格式和审计事件位于 [Grok Bot inference hooks](https://cursor.com/docs/grok-bot/inference-hooks.md)。

Inference hooks 处于 beta，所有 Enterprise 团队都可用。

## 身份与登录

成员用 Cursor 账户登录 Grok Bot，因此你现有的 Cursor SSO 配置适用。SAML 2.0 单点登录可用于 Okta、Microsoft Entra、Google Workspace 和 OneLogin，并且可以要求所有成员使用 SSO（这会阻止密码登录）。**SCIM 仅限 Enterprise：** SCIM 2.0 预配和取消预配。Okta 和 Entra ID 的分步配置，包括应用分配和计算机浏览器的登录规则，见[配置身份与访问](https://cursor.com/docs/grok-bot/identity.md)。

在托管计算机内部，成员通过浏览器中你自己的身份提供商登录应用程序，相当于登记一台新笔记本电脑。你的会话策略管理这些会话，在身份提供商中撤销用户会结束它们。

Bot 没有自己的身份或凭证：

- **Bot 以已登录的成员身份行事。** Bot 永远不能持有比它所属的人更多的访问权限，每个操作都可以归到一个具名成员，并且在你的身份提供商之外没有单独的机器身份需要预配、轮换或审计。团队管理的连接器是唯一的例外：它们可以使用团队或服务账户凭证。
- **连接器 token 留在 Cursor 的后端。** Bot 调用工具时不会收到 OAuth token，token 也从不存储在计算机上。
- **凭证不进入模型。** 要登录时，Bot 使用成员保存在 Vault 中的登录、已连接的密码管理器，或成员在 Secure Form 中输入的值。这些值按引用填入页面：模型永远不会收到已保存的密码、卡号或安全码，也不会收到成员在 Secure Form 中键入的内容。Bot 可以看到哪些登录已保存，以及成员已保存的联系信息。对于单点登录、通行密钥，以及表单无法处理的其他步骤，Bot 会把计算机交给成员。对于 API 密钥或其他机密，安全机密卡片会掩码该值，并把它排除在记录之外、远离模型。保存在 Vault 中的卡在结账时填入，无需每次单独批准，因此希望批准每一笔付款的成员不应当保存该卡。Vault 在共享 Bot 上不可用。密码和一次性验证码绝不属于普通聊天。见[安全存储机密](https://cursor.com/help/grok-bot/secrets.md)。

要结束成员的当前工作，组织管理员从仪表盘终止该成员的计算机；见[管理 Grok Bot 计算机](https://cursor.com/docs/grok-bot/computers.md)。该控制仅限 Enterprise。它会停止正在运行的 Bot，保留持久磁盘，成员的下一次会话会启动一台新计算机。它不会移除访问权限。要移除访问权限，把成员从团队中移除，或为他们的组关闭 Grok Bot，并在身份提供商中撤销他们的会话。应用会话只保留在成员的计算机上。

当某个项目或登录不应再可用时，成员直接清理：暂停或删除相关例程，在计算机上退出网站，移除已连接应用并在来源服务中撤销其授权，以及从 `/workspace` 移除敏感文件。删除 Bot 不会移除计算机文件或浏览器会话。

## 日志与审计

审计日志和 Action Recording 使用分开的管道。审计日志覆盖管理和安全事件，包括 Grok Bot 控制面操作。Action Recording 捕获 Bot 操作，配置后 OpenTelemetry Export 会把这些事件发送到你的收集器，并带上标签 `cursor.surface=grok_bot`。

- **审计日志。仅限 Enterprise。** 它们覆盖管理员、安全和身份验证事件，加上 Grok Bot 控制面事件：Bot 创建、成员访问更改、Team Setup 清单、MCP 身份验证、Slack 账户关联，以及例程。在仪表盘中按应用筛选，或流式传输到你的 SIEM。自助 Teams 没有这份日志。
- **Action Recording。仅限 Enterprise。** 它是 Grok Bot 页面上的一项设置，默认关闭。团队启用后，Cursor 会把 Bot 操作，包括已清除机密的 shell 命令，记录在内部存储中，保留 90 天。Action Recording 事件不会出现在 Audit Log 页面上。要在你自己的收集器中接收经过清理的事件，配置 [OpenTelemetry Export](https://cursor.com/docs/enterprise/opentelemetry-export.md)，它也仅限 Enterprise。Privacy Mode (Legacy) 会强制关闭记录。

## 端点工具

Grok Bot 不提供面向客户的内置遥测或 EDR 源。这些计算机是 Cursor 运营的基础设施，Cursor 会监控其运行健康和滥用，该遥测有意排除客户数据。**Team Setup Scripts** 仅限 Enterprise。这些管理员可以在每台团队计算机上安装自己的工具。

## 数据保留与删除

每个成员的计算机在会话之间把本地文件、浏览器会话，以及浏览器中保存的任何内容保留在持久磁盘上。连接器 token 从不存储在计算机上。

- **空闲计算机会自动休眠。** 休眠不是删除。
- **镜像更新会保留文件。** 位于过时系统镜像上的计算机会在新镜像上重建，并保留成员文件。
- **成员重置会保留已保存的数据。** 成员可以从桌面应用重置自己的计算机。与 Recover 一样，Reset 根据上次保存的数据重建计算机，如果无法访问计算机以保存，非常近期的改动可能会丢失。见 [Grok Bot 计算机](https://cursor.com/help/grok-bot/computer-recovery.md)。
- **删除遵循 DPA。** 根据[数据处理协议](https://cursor.com/terms/dpa)，服务结束后，数据会在书面指示后的 30 天内删除或返还。
- **备份每天运行。** Cursor 的生产控制面有每日加密备份，并复制到单独的恢复设施。
- **Enterprise 管理员可以设置保留期限。** 不活跃的 Bot、不活跃的计算机和旧消息可以自动删除。见[数据保留](https://cursor.com/docs/grok-bot/teams.md#data-retention)。

客户管理的、针对单台计算机的时间点恢复目前不可用。

## 数据驻留

Grok Bot 计算机运行在美国。Grok Bot 也包含在 Cursor 仅限美国的数据驻留计划中。启用后，Grok Bot 的推理、数据处理和存储都留在美国。联系你的客户团队以加入。支持的模型、排除项和价格见[美国数据驻留](https://cursor.com/docs/enterprise/privacy-and-data-governance.md#us-data-residency)。

## 模型与数据

Grok Bot 管理模型选择。没有面向客户的模型选择器，服务组合会随时间变化，不保证固定的供应商集合。每个请求发往第一方 xAI 模型，或[子处理者列表](https://trust.cursor.com/subprocessors)上某个提供商的第三方模型。用量和花费视图把这项活动记录为 Grok Bot 用量，而不是按模型记录。见[哪些 AI 模型驱动 Grok Bot？](https://cursor.com/help/grok-bot/models.md)

- **团队模型允许列表不管辖 Grok Bot。** 无论你的[模型访问控制](https://cursor.com/docs/enterprise/model-and-integration-management.md#model-access-control)设置如何，Grok Bot 都使用 xAI 第一方模型，并且可能使用第三方模型。有模型允许列表的团队在启用 Grok Bot 时会看到这一确认。如果你的合同限制子处理者，请联系客户团队。
- **Privacy Mode 适用。** 成员在你的团队中时，团队的隐私模式管理他们；启用 Privacy Mode 后，客户数据不会用于训练。
- **零数据保留遵循 Cursor 现有的提供商协议。** 模型提供商不保留提示词或输出，Grok Bot 不另加单独的控制。提供商可能会运行滥用和安全分类器，被标记的数据可能会被存储以供调查。

## 本地执行

Bot 可以通过桌面应用在成员自己的机器上行事：运行命令、读取文件，以及在云端计算机和本地机器之间移动文件。这与托管计算机中的工作是分开的，有自己的控制，并且与管理托管计算机内部工作的 Auto Review 不同。

默认是每次命令都需批准，批准卡片会显示确切的命令。成员在 **Settings** > **Computer** > **Your computer** 的 **Execution on this computer**（在桌面被列出之前为 **Execution on Local Computer**）下选择策略：**Ask every time**、**Always allow** 或 **Never allow**。除非 Bot 有具体理由要处理本地文件，否则建议 **Never allow**。管理员可以用 Grok Bot 页面上的 [Execution on Local Computer](https://cursor.com/docs/grok-bot/teams.md#execution-on-local-computer) 为整个团队封顶该策略；当成员自己的设置更严格时，仍然适用。

## 托管

Grok Bot 只在 Cursor 托管的云端计算机上运行。本地部署、在你自己的边界内部署，以及自带镜像部署，目前都不支持。托管计算机默认使用共享静态出口。成员可以把流量经由自己的桌面路由，Enterprise 团队可以用 **Team Setup Scripts** 安装网络客户端。见[连接到私有网络](https://cursor.com/docs/grok-bot/private-networks.md)。

## 提示词注入

Bot 从外部世界读取的内容，例如网页、应用结果和命令输出，可能会试图引导它。Grok Bot 分层防御：强制执行开启时，Auto Review 会对照成员的请求检查 Bot 操作；其下是不依赖任何模型判断的控制，包括网络策略、逐项批准和按用户隔离。外部内容在呈现给模型时会被标记为不受信任的数据。有了 [inference hooks](https://cursor.com/docs/grok-bot/security.md#inference-hooks)，你自己的安全服务器也可以在下一次模型调用之前筛查这些内容。这些控制会降低恶意内容的风险，但不会消除它，这也是把有实质后果的操作留在批准之后的另一个原因。

## 认证

Cursor 背后的公司 Anysphere 持有由 Schellman 颁发的 ISO/IEC 27001 和 ISO/IEC 42001 认证，Grok Bot 包含在当前的 ISO 范围内。ISO/IEC 27001 认证的是信息安全管理体系，即整个组织的安全计划。ISO/IEC 42001 认证的是 AI 管理体系，即 Cursor 如何治理它构建和运营的 AI。证书和报告可在 [trust.cursor.com](https://trust.cursor.com) 获取。

## 相关页面

- [Grok Bot 安全常见问题](https://cursor.com/docs/grok-bot/security-faq.md)
- [面向 Teams 与 Enterprise 的 Grok Bot](https://cursor.com/docs/grok-bot/teams.md)
- [Grok Bot inference hooks](https://cursor.com/docs/grok-bot/inference-hooks.md)
- [配置身份与访问](https://cursor.com/docs/grok-bot/identity.md)
- [连接到私有网络](https://cursor.com/docs/grok-bot/private-networks.md)
- [配置检查 TLS 的代理](https://cursor.com/docs/grok-bot/proxies.md)
- [隐私与数据治理](https://cursor.com/docs/enterprise/privacy-and-data-governance.md)

### 与你的客户团队一起审查 Grok Bot

联系我们的团队，了解出口地址范围、数据驻留承诺，以及安全审查支持。


---

## 站点地图

[所有文档页面概览](/llms.txt)
