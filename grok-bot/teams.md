> 原文：https://cursor.com/docs/grok-bot/teams

# 面向 Teams 与 Enterprise 的 Grok Bot

Grok Bot 为团队中的每个人提供用于日常工作的常驻 Bot：研究、运营、文档、浏览和自动化。本页面向正在推出 Grok Bot 的团队和组织管理员。安全审查人员应从 [Grok Bot 安全](https://cursor.com/docs/grok-bot/security.md) 和 [Grok Bot 安全常见问题](https://cursor.com/docs/grok-bot/security-faq.md) 开始。想了解批准模型的成员也应从那里开始。

## 可用性

| 方案 | 访问权限 |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------- |
| Individuals | 包含在每一项付费 Cursor 方案中，也可以通过个人 SuperGrok 关联获得，或使用自助 Grok Business 方案上的席位 |
| Teams | 已包含；每个成员都有访问权限，用量遵循该席位的额度 |
| Enterprise | 从[你的仪表盘](https://cursor.com/dashboard/bot)为团队启用 Grok Bot |

这张表是产品访问权限，不是管理员控制列表。Grok Bot 包含在 Teams 中。若干设置**仅限 Enterprise**，自助 Teams 不会看到它们。[管理员控制](https://cursor.com/docs/grok-bot/teams.md#admin-controls)会标明每一项。

[方案与计费](https://cursor.com/help/grok-bot/plans.md)是方案和用量的权威对照。

## 为团队启用 Grok Bot

在 Teams 方案上，Grok Bot 默认启用，每个成员无需任何管理员操作即有访问权限。对于处于 [Privacy Mode (Legacy)](https://cursor.com/docs/grok-bot/teams.md#before-you-roll-out) 或[旧版按请求计费方案](https://cursor.com/docs/models-and-pricing.md#legacy-request-based-pricing)的团队，它保持关闭。没有可以把它关掉的开关。

在 Enterprise 方案上，管理员从 [Cursor 仪表盘中的 Grok Bot](https://cursor.com/dashboard/bot) 打开它。启用后，你可以把访问权限给所有团队成员，或用 “Manage Group Access” 限制到特定[组](https://cursor.com/dashboard/members?subtab=groups)。关闭 Grok Bot 会阻止每个成员，但不会删除他们的计算机。

任一方案的管理员都可以在 Grok Bot 页面用 “Invite Team” 邀请成员。团队中已有的 Cursor 用户会收到一封带下载链接的电子邮件。新用户会通过电子邮件或邀请链接收到加入你的 Cursor 团队的邀请，其中也会指向 Grok Bot。通过 SCIM 管理成员资格的团队只会看到现有用户这一选项。

要自行在受管设备上安装桌面应用并规划更新，见[将 Grok Bot 部署到你的组织](https://cursor.com/docs/grok-bot/deployment.md)。

## 推出之前

- **离开 Privacy Mode (Legacy)。** 该设置会完全阻止 Grok Bot，启用前会提示你更改它。在 Team Settings 中检查隐私设置。
- **为共享出口地址做计划。** 如果你的公司按源 IP 限制服务，见[静态出口 IP](https://cursor.com/docs/grok-bot/security.md#static-egress-ips)。
- **放行你的网关。** 如果成员设备位于 Zscaler 或其他会检查 TLS 的代理之后，请允许 Cursor 的域名，包括嵌套的 `*.*.cursorvm.com` 模式，并在成员连接之前把它们排除在检查之外。见[配置检查 TLS 的代理](https://cursor.com/docs/grok-bot/proxies.md)。
- **决定成员如何从计算机登录公司工具。** 见[身份与登录](https://cursor.com/docs/grok-bot/security.md#identity-and-sign-ins)。
- **审查 Grok Bot 继承的策略**：每个方案上的 Team Rules 和连接器策略，以及 Enterprise 上的团队 Auto-review 规则和 MCP 允许列表。

## 架构

### Grok Bot 如何构建

Grok Bot 是一个计算机操作 Agent，可以操作应用程序、浏览器和开发环境。它运行在 Cursor 的云端，每个用户的工作在一台专用云端计算机上执行。桌面和移动应用是用于聊天、审查和批准的瘦客户端。

安全模型基于四条原则：

- **按用户隔离。** 每个用户的工作运行在专用的 Firecracker microVM 中，这是一种在硬件层面与其他用户分离的微型虚拟机。
- **默认无访问权限。** Bot 只能使用用户或团队授予它的账户和已连接应用。
- **人工批准关卡。** 敏感操作需要用户批准，由一个名为 Auto Review 的独立审查模型评估。
- **管理控制。** 团队管理员可以设置 Team Rules、Cloud Agent 委派、模板分享，以及本地执行上限。Network Controls、Team Setup Scripts、Team Secrets、Allow Local Egress Routing、Allow Sudo on Bot Computers、Action Recording、Enforce Auto-review、Auto-review 规则，以及组织范围的启用开关，仅限 Enterprise。

这些部分是这样配合的：

1. **本地机器。** 聊天、审查和批准发生在成员的设备上。工作在托管计算机中运行。可选的[本地执行](https://cursor.com/docs/grok-bot/security.md#local-execution)默认需要每次命令批准，并且可以关闭。
2. **环境。** 每个用户一台持久的 Firecracker microVM。该用户运行的每个 Bot 都共享这台计算机。管理员从 [Cursor 仪表盘](https://cursor.com/dashboard/bot)的 Grok Bot 页面管理 Grok Bot。Team Rules、Cloud Agent 委派、公开模板分享和 Execution on Local Computer 可供团队管理员使用。该页面上**仅限 Enterprise** 的有：组织范围的启用开关、Network Controls、Team Setup Scripts、Team Secrets、Allow Local Egress Routing、Allow Sudo on Bot Computers、Action Recording、Enforce Auto-review、Auto-review 规则，以及计算机管理。成员永远看不到这个页面。
3. **Bot。** 在托管计算机内使用 shell、浏览器和计算机操作。Bot 默认没有任何访问权限，只使用成员登录进去的账户行事。它从成员的 Vault、已连接的密码管理器或 Secure Form 填入登录和卡，而看不到这些值，并把单点登录和通行密钥等步骤交给成员。
4. **已连接的应用。** 你的团队的 Cursor MCP（Model Context Protocol）策略完全适用，允许或阻止每个连接器。OAuth token 留在 Cursor 的连接器后端，Bot 调用工具时不会收到它们。
5. **Cloud Agents。** Grok Bot 可以把编码任务委派给独立的计算机，并受你现有的 [Cloud Agent](https://cursor.com/docs/cloud-agent.md) 控制。管理员可以禁用拉起。
6. **模型与数据。** Grok Bot 管理模型选择。启用 Privacy Mode 后，客户数据不会用于训练；Cursor 在其服务器上强制执行这一点，当无法验证该设置时，系统默认不用于训练。

### 用户如何被隔离

每个用户获得一台具有硬件级分离的专用计算机，一个用户无法触及另一个用户的计算机。每台计算机都是一台 Firecracker microVM，有自己的内核、内存和虚拟设备。

在同一个用户内部，边界不同：该用户的所有 Bot 共享一台计算机，Bot 隔离的是个性和工作区，不是计算资源。把计算机上的登录或文件视为该用户运行的每个 Bot 都可以使用；让浏览器退出 Bot 不再需要的账户，并在工作完成时移除敏感的临时文件。当一项工作负载需要自己的计算机和凭证集时，给它一个单独的 Cursor 用户。

[共享 Bot](https://cursor.com/help/grok-bot/shared-bots.md) 是「每个用户一个 Bot」的例外。一个 Bot 要么是个人的，要么是共享的：成员把一个云端托管的 Bot 分享给团队中的所有人，或只分享给某些人和组，这些成员就可以与它聊天。每个成员的聊天都是私密的，所有者和该 Bot 的 Manager 也看不到。在成员自己的聊天或 Slack 私信中，Bot 通常在该成员的计算机上工作，在询问后使用该成员已连接的账户，并使用该成员的 Grok Bot 用量。在 Slack 频道、讨论串、群组私信和群聊中，共享 Bot 使用它自己的一台计算机。所有者或 Manager 添加到共享 Bot 的机密、应用和文件，在每个队友的聊天中都可用。

## 管理员控制

大多数 Grok Bot 设置位于 [Cursor 仪表盘](https://cursor.com/dashboard/bot)的 Grok Bot 页面，只有管理员能看到。少数位于 Team Settings、你的 Team Marketplace 或身份提供商中。若干控制仅在 [Enterprise 方案](https://cursor.com/docs/enterprise.md)上可用；其余在 Teams 和 Enterprise 上都可用。Enterprise 团队还可以从一个组的 Grok Bot 标签页，为同一批成员放宽其中若干控制；见[组设置](https://cursor.com/docs/grok-bot/teams.md#group-settings)。

### 访问与身份

谁可以使用 Grok Bot，以及成员如何被预配。

#### Enable Grok Bot

Grok Bot 页面上的组织范围开关，旁边是 **Manage Group Access**。打开它会弹出一个设置对话框，涵盖隐私模式、计费和模型可用性，在 Cloud Agents 关闭时提供打开它们的选项，并请你接受 Grok Bot Terms。关闭它会阻止每个成员，但不会删除他们的计算机。见[为团队启用 Grok Bot](https://cursor.com/docs/grok-bot/teams.md#enabling-grok-bot-for-your-team)。

*在 [Enterprise 方案](https://cursor.com/docs/enterprise.md)上可用。*

#### SCIM

通过身份提供商进行 SCIM 2.0 预配和取消预配。成员用 Cursor 账户登录 Grok Bot，因此你现有的 [SCIM](https://cursor.com/docs/account/teams/scim.md) 和 SSO 设置会一并沿用。Okta 和 Entra ID 的步骤，包括应用分配和计算机浏览器的登录规则，见[配置身份与访问](https://cursor.com/docs/grok-bot/identity.md)。

*在 [Enterprise 方案](https://cursor.com/docs/enterprise.md)上可用。*

#### 团队图标

Grok Bot 为整个团队显示的图像，例如成员把 Bot 分享给所有人时。管理员从 Team Settings 的 Team Profile 分区上传、更改或移除它。使用 PNG、JPEG 或 WebP，最大 2 MB。

### Agent 能力

Bot 可以代表成员做什么。每项控制适用于每个成员的 Bot。

#### Cloud Agents

允许或阻止 Bot 把编码任务委派给 Cursor [Cloud Agents](https://cursor.com/docs/cloud-agent.md)。开关在 Grok Bot 页面上，适用于整个团队，并且默认开启。委派的工作在独立的计算机上运行，受你现有的 Cloud Agent 控制。当团队不需要委派时把它关闭。

#### 公开模板分享

控制成员是否可以把 Bot 模板发布到团队之外。关闭后分享仅限团队内，Cursor 在其服务器上强制执行该策略，包括对已经公开的模板。Enterprise 团队一开始公开分享是关闭的；其他团队一开始是允许的。共享模板包含什么，见[分享 Bot](https://cursor.com/docs/grok-bot/work.md#share-a-bot)。

#### Bot sharing

控制谁可以把 Bot 分享给队友。在 Grok Bot 页面的 **Sharing** 下找到 **Bot sharing**，选择 **Everyone** 或 **Admins only**。**Everyone** 是默认值。选择 **Admins only** 后，成员会看到 **Only team admins can share Bots**，并且他们的 **Share** 控件被禁用；Cursor 在其服务器上强制执行这一点。已经分享的 Bot 仍保持分享，管理员在 **Manage Bots** 中设置的访问权限仍然适用。见[谁可以分享 Bot？](https://cursor.com/help/grok-bot/shared-bots.md#who-can-share-bots)

#### Allow Shared Logins

让共享 Bot 的 Manager 把自己的应用登录作为共享连接器分享出去。在 Grok Bot 页面的 **Sharing** 下找到 **Allow Shared Logins**。默认关闭。开启后，每个使用该 Bot 的人都通过该 Manager 的账户行事，包括 Bot 所做的更改。关闭后，Manager 会看到 **Your admin has turned off sharing logins with Bots**。见[共享连接器如何工作？](https://cursor.com/help/grok-bot/shared-bots.md#how-do-shared-connectors-work)

#### Slack Workspace Access

让你的 Slack 工作区中不在团队里的成员，无需关联 Cursor 账户即可在 Slack 中使用共享 Bot。它适用于分享给你的组织的 Bot，或者当该团队是组织中唯一的团队、或不属于某个组织时，适用于分享给该团队的 Bot。他们的回复使用团队的用量。绝不包括 Slack 访客和 Slack Connect 频道。在 Grok Bot 页面的 **Sharing** 下找到 **Slack Workspace Access**。默认开启。在组织中，组织管理员为每个团队设置它。该设置会在向你的团队推出时出现。见[谁可以在 Slack 中与共享 Bot 交谈？](https://cursor.com/help/grok-bot/shared-bots.md#who-can-talk-to-a-shared-bot-in-slack)

#### Manage Bots

列出团队中的每个 Bot。在 Grok Bot 页面的 **Shared Bots** 下打开 **Manage Bots**，并按分享或所有者筛选列表。**Access** 列显示谁可以使用每个[共享 Bot](https://cursor.com/help/grok-bot/shared-bots.md)，例如整个团队、某些人或组，或仅其所有者。点击它可以更改谁有访问权限。在 Bot 的 **Auto-add** 列中，选择 **All team** 或一个或多个组，把该 Bot 加到这些成员的侧边栏，或清除它。自动添加只适用于有访问权限的组。以这种方式获得 Bot 的成员不能把它从侧边栏隐藏。管理员可以在这里删除任何 Bot。删除 Bot 会为每个使用它的人移除它及其 Slack 应用，并且无法撤销。组在 Members & Groups 页面管理。

#### Page sharing

控制成员如何在团队内分享 [Pages](https://cursor.com/help/grok-bot/pages.md)。在 Grok Bot 页面的 **Sharing** 下找到 **Page sharing**，选择 **Anyone on the team**、**Specific teammates** 或 **Off**。**Anyone on the team** 是默认值。收紧之后，已经分享的 Pages 仍保持分享。该控件会在 Pages 向你的团队推出时出现。

#### Agent Email

让成员领取一个 `mail.grokbot.com` 地址，他们的 Bot 可以用它读取和发送邮件。开关在 Grok Bot 页面的 **Bot Capabilities** 下，默认关闭；在开启之前，成员没有电子邮件工具。当成员在聊天中要求发一封邮件时，一旦 Auto-review 允许或成员批准，它就会发送。Bot 自行撰写的邮件，包括来自例程的邮件，会作为草稿等待，直到成员按下 **Send**。新闻通讯和首次联系邮件包含退订链接，收到的邮件只会通过成员设置的例程采取行动。关闭它会移除 Bot 的电子邮件工具并停止电子邮件例程；现有地址仍会继续收信。见[给你的 Bot 一个电子邮件地址](https://cursor.com/help/grok-bot/agent-email.md)。

#### 连接器策略

Grok Bot 继承你的团队的 Cursor 连接器策略。没有单独的 Grok Bot 连接器列表，成员在 Grok Bot 的 **Connect apps** 下找到允许的连接器。从仪表盘 [Plugins & MCPs 页面](https://cursor.com/dashboard/plugins)上的 Team Marketplace 设置成员可以使用哪些服务器，而不是在 Grok Bot 页面上设置。MCP 允许列表仅限 Enterprise；见 [MCP 服务器信任管理](https://cursor.com/docs/enterprise/model-and-integration-management.md#mcp-server-trust-management)。任何被允许的连接器都可供成员运行的每个 Bot 使用，被阻止的连接器会显示为 **Disabled by team admin**。向成员推送连接器（无论是强制还是默认开启）不可用。如果你的团队使用允许列表网络策略，并希望远程 MCP 服务器也受同一列表约束，请在 **Network** 下打开 **Apply to MCP servers**；它默认关闭。见 [MCP 服务器与网络策略](https://cursor.com/docs/grok-bot/security.md#mcp-servers-and-the-network-policy)。

#### Execution on Local Computer

限制 Bot 通过桌面应用在成员自己的机器上可以做什么：打开文件和运行任务。在 Grok Bot 页面选择 **Always allow**、**Ask every time** 或 **Never allow**。**Always allow** 是默认值，把选择留给每个成员，而成员自己的设置默认在每个任务前询问。**Ask every time** 让每个本地任务都请求批准，**Never allow** 为整个团队关闭本地执行。当成员自己的设置比团队更严格时，仍然适用。除非 Bot 有具体理由要在成员机器上工作，否则选择 **Never allow**。见[本地执行](https://cursor.com/docs/grok-bot/security.md#local-execution)。

#### Allow Local Egress Routing

让成员把 Grok Bot 的 Web 流量经由自己的计算机路由。关闭后，Grok Bot 中的该选项会被禁用。开关在 Grok Bot 页面的 **Bot Computers** 下，默认开启。关闭它会在五分钟内停止活动路由。重新打开会恢复每个成员先前的选择。见[通过你的桌面路由流量](https://cursor.com/docs/grok-bot/settings.md#route-traffic-through-your-desktop)。

*在 [Enterprise 方案](https://cursor.com/docs/enterprise.md)上可用。*

### 规则与批准

Bot 遵循的指导，以及会拦住操作和模型请求的审查层。

#### Team Rules

每个成员的 Bot 都会遵循的规则。从 Grok Bot 页面添加，并把每条规则的范围设为 Cursor、Grok Bot 或两者。应用到 Grok Bot 的规则始终是必需的，因此成员不能关闭它们。保持简短、少量，例如「绝不把公司数据移到个人账户」。规则指导 Bot；对于批准行为，使用 [Auto-review 规则](https://cursor.com/docs/grok-bot/teams.md#auto-review-rules)。对于只属于同一批成员的指导，在[组设置](https://cursor.com/docs/grok-bot/teams.md#group-settings)下添加 Group Rules。

#### Enforce Auto-review

阻止成员关闭 Auto-review。开关在 Grok Bot 页面上，默认关闭。开启后，Bot 在运行有风险的操作之前总会检查，并在需要时请求批准。这包括在没人能回答批准的聊天中的共享 Bot，否则它们会在没有 Auto-review 的情况下运行。在依赖团队 Auto-review 规则之前先打开它。组可以用 **Don't enforce for this group** 为自己的成员解除锁定；见[组设置](https://cursor.com/docs/grok-bot/teams.md#group-settings)。

*在 [Enterprise 方案](https://cursor.com/docs/enterprise.md)上可用。*

#### Auto-review rules

适用于每个成员的 Bot、叠在他们自己的规则之上的团队范围 “Ask first” 和 “Allow automatically” 规则。在 Grok Bot 页面用 “Configure Rules” 添加；更改会自动保存。成员在 “Settings” > “General” > “Auto-review” 下看到团队规则，但不能编辑或删除它们，规则冲突时 “Ask first” 优先。关闭强制执行后，就不再应用团队规则。见[批准与 Auto Review](https://cursor.com/docs/grok-bot/security.md#approvals-and-auto-review)。

*在 [Enterprise 方案](https://cursor.com/docs/enterprise.md)上可用。*

#### Inference Hooks

把每个 Grok Bot 模型请求签名后发送到你的组织自己运行、或通过安全供应商使用的 AI 安全服务器。你的服务器在模型运行之前允许或拒绝该请求。从 Grok Bot 页面的 **Security & Compliance** 打开 [Inference Hooks](https://cursor.com/dashboard/bot/inference-hooks)。新配置从 Shadow mode 开始，它把拒绝记录到审计日志，但不阻止成员。Inference hooks 处于 beta，所有 Enterprise 团队都可用。见 [Grok Bot inference hooks](https://cursor.com/docs/grok-bot/inference-hooks.md)。

*在 [Enterprise 方案](https://cursor.com/docs/enterprise.md)上可用。*

### 计算机与网络

团队计算机如何设置、它们可以到达哪里，以及你如何重建或终止它们。

#### Network Controls

限制团队计算机可以到达哪些目的地。在 Grok Bot 页面的 **Network** 下找到它，名称为 **Grok Bot Network Access**。选择 **Allow All Network Access**、**Defaults + Team Allowlist** 或 **Team Allowlist Only**，并为允许列表模式列出域名和 IP 范围。目录组可以有自己的策略，锁定后团队策略适用于所有人。没有策略的团队默认为允许全部。模式以及新策略如何到达正在运行的计算机，见[网络策略](https://cursor.com/docs/grok-bot/security.md#network-policy)。

在允许列表模式下，目的地列表下方会出现 **Apply to MCP servers** 开关。它默认关闭。开启后，每个已连接应用，包括 GitHub、X 和 Origin 等内置连接器，都必须按其发送请求的主机匹配一个允许的目的地，Bot 连接到不匹配的应用会被拒绝。命令类服务器不受影响。每个内置连接器需要的主机，见 [MCP 服务器与网络策略](https://cursor.com/docs/grok-bot/security.md#mcp-servers-and-the-network-policy)。

*在 [Enterprise 方案](https://cursor.com/docs/enterprise.md)上可用。*

#### Team Setup Scripts

在每台团队计算机上运行的安装脚本清单，使同样的工具到处都有。在 Grok Bot 页面的 **Bot Computers** 下找到 **Team Setup Scripts**。不要把机密值放进设置脚本；当脚本需要凭证时，把它存为 [Team Secret](https://cursor.com/docs/grok-bot/teams.md#team-secrets)，并从环境中读取。成员在应用中看不到这些脚本。清单如何运行，以及如何安装能到达私有服务的网络客户端，见[连接到私有网络](https://cursor.com/docs/grok-bot/private-networks.md)。要只在同一批成员的计算机上运行额外脚本，在[组设置](https://cursor.com/docs/grok-bot/teams.md#group-settings)下添加。

*在 [Enterprise 方案](https://cursor.com/docs/enterprise.md)上可用。*

#### Team Secrets

Team Setup 脚本作为环境变量读取的团队范围值，因此脚本可以持有许可证密钥、身份验证密钥或服务 token，而该值不会出现在清单中。在 Grok Bot 页面的 **Team Secrets** 下添加：每个机密是一个环境变量名和一个值。Cursor 加密存储该值，保存之后仪表盘再也不会显示它。团队管理员，包括未付费的管理员，可以添加、替换或删除它们。

它们如何到达计算机：

- 你的 Team Setup 清单中的每个 Setup Script 和 Check Script，以及组的 [Setup Scripts](https://cursor.com/docs/grok-bot/teams.md#group-settings)，都会带着团队机密在其环境中运行。按名称引用机密，例如 `"$MY_LICENSE_KEY"`。
- 机密只存在于脚本的进程环境中。脚本结束后，除非脚本把它写到了某处，该值就消失了，因此不要在计算机上存放你不希望该成员运行的每个 Bot 都能触及的内容。
- 在记录脚本输出之前会检查其中的机密值，匹配项会被涂黑。
- 只有当计算机的 Team Setup 恰好来自一个团队时，它才会收到机密。如果成员的计算机应用了来自两个或更多团队的设置，则不会从其中任何一个获得机密，因为不同团队的脚本共享同一个计算机用户。
- 更改，包括轮换后的值，会在脚本下次运行时生效：计算机启动时、定期刷新时，或你重建它时。见[向现有计算机推出](https://cursor.com/docs/grok-bot/private-networks.md#roll-out-to-existing-computers)。

限制：每个团队最多 100 个机密，每个值最多 32 KB，合计最多 96 KB。名称必须是有效的环境变量名，计算机运行时保留的名称，例如 `PATH`、`HOME`，以及任何以 `SAND_` 或 `LD_` 开头的名称，在保存时会被拒绝。Team Secrets 用于你控制的脚本。它们不会让某个值直接可供 Bot 使用，并且与[成员在 Bot 上存储的机密](https://cursor.com/help/grok-bot/secrets.md)是分开的。它们不能通过 Admin API 使用。

*在 [Enterprise 方案](https://cursor.com/docs/enterprise.md)上可用。*

#### Allow Sudo on Bot Computers

让 Bot 在自己的计算机上以 root 运行命令。开关在 Grok Bot 页面的 **Bot Computers** 下，默认开启。现有计算机在重启后会采用更改。

*在 [Enterprise 方案](https://cursor.com/docs/enterprise.md)上可用。*

#### Manage Member Computers and Data

让管理员一次重建、终止或永久删除许多成员的计算机，或删除他们的全部 Grok Bot 数据，并为每个成员给出结果。在 Grok Bot 页面的 **Security & Compliance** 下找到它。Recreate 会把成员移到最新镜像和 Team Setup Scripts，同时保留他们的 Bot、文件和登录。Terminate 会结束成员的当前工作并保留持久磁盘；成员的下一次会话会在该磁盘上启动一台新计算机。Delete 会移除计算机及其存储的文件，成员的下一次会话会启动一台空计算机。要自动删除不活跃的计算机，使用[计算机保留期限](https://cursor.com/docs/grok-bot/teams.md#data-retention)。这些操作都不会移除访问权限：要做到这一点，把成员从团队中移除，或为他们的组关闭 Grok Bot，并在身份提供商中撤销他们的会话。见[管理 Grok Bot 计算机](https://cursor.com/docs/grok-bot/computers.md)。

*在 [Enterprise 方案](https://cursor.com/docs/enterprise.md)上可用。*

### 数据保留

一旦超过你选择的期限，就自动删除 Grok Bot 数据。在 Grok Bot 页面的 **Data Retention** 下找到这些设置。每一项默认关闭。

| 设置 | 删除的内容 | 期限 |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- |
| **Retention Period for Bots** | 在该期限内没有人发过消息的 Bot，连同它们的对话、记忆、Skill 和例程。Bot 的所有者会在删除前 30 天收到电子邮件，在该窗口内发一条消息即可保留该 Bot。 | 60 到 365 天 |
| **Retention Period for Computers** | 一旦在该期限内没有人给该成员的任何 Bot 发过消息，就删除该成员的计算机及其文件。见[自动删除不活跃的计算机](https://cursor.com/docs/grok-bot/computers.md#delete-inactive-computers-automatically)。 | 30 到 365 天 |
| **Retention Period for Messages** | 每天删除早于该期限的 Bot 消息和附件。不会通知任何人。 | 30 到 365 天 |

打开某项设置或缩短其期限时，会先请你确认。已删除的数据无法恢复。每一次更改和删除都会记录在[审计日志](https://cursor.com/dashboard/audit-log)中。

*在 [Enterprise 方案](https://cursor.com/docs/enterprise.md)上可用。*

### 日志与审计

记录什么，以及记录到哪里。

#### Action Recording

记录 Bot 做了什么：每一次工具调用及其结束方式，允许或拒绝它的决定（Auto-review、一个 Hook，或该成员），连接器（MCP）工具调用，shell 命令，浏览器导航，计算机操作会话，文件传输，Bot 发送的消息，例程运行，护栏介入，它读取的 Skill，以及交给 Subagents 或 Cloud Agents 的工作。事件是元数据，在存储或导出之前会经过清理。Shell 命令会清除机密；浏览器导航把每个页面保留为 `scheme://host/path` 并带上标题，但去掉查询字符串和凭证；计算机操作会话记录操作次数、截图次数和会话时长，不含截图、点击或键入的文本；其他每个事件携带 id、结果和时长，从不携带工具参数、文件路径或消息内容。开关在 Grok Bot 页面上，默认关闭。记录的事件不会出现在 Audit Log 页面上。要在你自己的收集器中接收它们，配置 [OpenTelemetry Export](https://cursor.com/docs/enterprise/opentelemetry-export.md)，它会交付每个带有 `cursor.surface=grok_bot` 标签的事件；[传输格式参考](https://cursor.com/docs/enterprise/opentelemetry-export/wire.md#shared-grok_bot-attributes)列出每个事件和属性。保留细节见[日志与审计](https://cursor.com/docs/grok-bot/security.md#logging-and-audit)。

*在 [Enterprise 方案](https://cursor.com/docs/enterprise.md)上可用。*

#### 审计日志

管理员、安全和身份验证事件，加上 Grok Bot 控制面事件：Bot 创建、成员访问更改、Team Setup 清单、MCP 身份验证、Slack 账户关联，以及例程。每一行都标出采取行动的应用，因此你可以把日志筛选到 Grok Bot。在[审计日志页面](https://cursor.com/dashboard/audit-log)查看，或流式传输到你的 SIEM；见[审计日志](https://cursor.com/docs/enterprise/compliance-and-monitoring.md#audit-logs)。对于 Bot 采取的操作，使用 [Action Recording](https://cursor.com/docs/grok-bot/teams.md#action-recording)。

*在 [Enterprise 方案](https://cursor.com/docs/enterprise.md)上可用。*

#### OpenTelemetry Export

把 Cursor 用量指标和日志，包括已记录的 Grok Bot 操作，流式传输到你运行的收集器。它是接收 Action Recording 事件的客户路径。在 **Team Settings** > **OpenTelemetry Export** 下配置。端点要求和事件 schema 见 [OpenTelemetry Export](https://cursor.com/docs/enterprise/opentelemetry-export.md)。

*在 [Enterprise 方案](https://cursor.com/docs/enterprise.md)上可用。*

### 组设置

上面的控制设定团队范围的基线。要为同一批成员放宽该基线，从 [Members > Groups](https://cursor.com/dashboard/members?subtab=groups) 打开你的团队拥有的一个组，并使用它的 **Grok Bot** 标签页。除网络策略外，组设置只会放宽：组可以授予其成员比团队所允许的更多权限，绝不会更少，并且属于多个组的成员会得到最宽松的结果。这与[组模型访问](https://cursor.com/docs/enterprise/model-and-integration-management.md#how-team-and-group-model-access-combine)是同一条规则。组控件保持为团队的值时不会改变任何东西，因此在 Grok Bot 页面收紧控制，再按组放宽。

该标签页有四个分区：

- **Bot Capabilities.** 当团队把它们关闭或设得更严格时，为组成员允许 [Cloud Agents](https://cursor.com/docs/grok-bot/teams.md#cloud-agents)、[Agent Email](https://cursor.com/docs/grok-bot/teams.md#agent-email) 或 [Allow Local Egress Routing](https://cursor.com/docs/grok-bot/teams.md#allow-local-egress-routing)，或提高 [Execution on Local Computer](https://cursor.com/docs/grok-bot/teams.md#execution-on-local-computer) 的上限。对于 Auto-review，**Don't enforce for this group** 会解除团队的 [Enforce Auto-review](https://cursor.com/docs/grok-bot/teams.md#enforce-auto-review) 锁定，使组成员可以关闭 Auto-review；组的 Auto-review 规则与团队的 [Auto-review 规则](https://cursor.com/docs/grok-bot/teams.md#auto-review-rules)合并，规则冲突时 “Ask first” 优先。
- **Network.** 组自己的网络策略，如 [Network Controls](https://cursor.com/docs/grok-bot/teams.md#network-controls) 下所述。它会替换组成员的团队策略，无论更松还是更紧，属于多个组的成员会得到最严格的一个。锁定的团队策略适用于所有人。**Apply to MCP servers** 只在团队的 Grok Bot 页面上设置；组会继承它，不能关闭。
- **Group Rules.** 该组 Bot 的规则，与 [Team Rules](https://cursor.com/docs/grok-bot/teams.md#team-rules)合并。成员不能关闭它们。
- **Setup Scripts.** 与 [Team Setup Scripts](https://cursor.com/docs/grok-bot/teams.md#team-setup-scripts)一起在组成员计算机上运行的清单。它们使用相同的结构、以相同的方式运行，同样的禁止机密规则适用，并且读取与团队清单相同的 [Team Secrets](https://cursor.com/docs/grok-bot/teams.md#team-secrets)；没有组级别的机密。见 [Team Setup 如何运行你的脚本](https://cursor.com/docs/grok-bot/private-networks.md#how-team-setup-runs-your-scripts)。

组设置适用于你的团队拥有的组，无论你是手工管理成员资格，还是通过 [SCIM](https://cursor.com/docs/account/teams/scim.md#directory-groups) 同步。它们与 [Organization Groups](https://cursor.com/docs/enterprise/organization-groups.md) 是分开的。要控制谁可以使用 Grok Bot，请改用 Grok Bot 页面上的 **Manage Group Access**；见[为团队启用 Grok Bot](https://cursor.com/docs/grok-bot/teams.md#enabling-grok-bot-for-your-team)。

*在 [Enterprise 方案](https://cursor.com/docs/enterprise.md)上可用。*

## Admin API

通过 [Admin API](https://cursor.com/docs/account/teams/admin-api.md#grok-bot) 启用 Grok Bot，并管理能力、Enforce Auto-Review、组访问、网络策略、团队规则和设置脚本。[Team Secrets](https://cursor.com/docs/grok-bot/teams.md#team-secrets) 只能从仪表盘管理。

要从脚本重建、终止或删除成员的计算机，或删除他们的全部 Grok Bot 数据，使用 [Organization API](https://cursor.com/docs/account/organizations/organization-admin-api.md#grok-bot-computers)。它需要一个具有 `admin:*` 范围的 Organization API 密钥，因为一台计算机服务于成员所属的每个团队。

## 安全

安全模型、网络策略、批准与 Auto Review、身份、日志、数据处理和认证位于 [Grok Bot 安全](https://cursor.com/docs/grok-bot/security.md)。常见审查问题位于 [Grok Bot 安全常见问题](https://cursor.com/docs/grok-bot/security-faq.md)。要让你自己的 AI 安全服务器检查每个模型请求，见 [Grok Bot inference hooks](https://cursor.com/docs/grok-bot/inference-hooks.md)。

## 推荐配置

对于安全敏感的部署，这是推荐的基线。

对于管理员：

1. **配置 Network Controls。仅限 Enterprise。** 没有策略的团队默认为允许全部。自助 Teams 不能设置此项。如果你选择允许列表模式，并希望它也覆盖已连接应用，把连接器使用的主机（例如 `api.github.com`、`api.x.com`、`api.origin.cursor.com`）加入列表，然后打开 **Apply to MCP servers**；在你这样做之前它是关闭的。见 [Grok Bot 安全](https://cursor.com/docs/grok-bot/security.md#network-policy)。
2. **在启用 Grok Bot 之前，于 Teams Marketplace 中审计连接器策略。** 任何被允许的连接器都可供成员运行的每个 Bot 使用。
3. **在依赖团队规则之前打开 Enforce Auto-review。仅限 Enterprise。** 成员可以在此之上添加更严格的个人规则，规则冲突时 “Ask first” 优先。
4. **把 Execution on Local Computer 设为 Never allow**，除非 Bot 需要在成员机器上行事。默认把选择留给每个成员。
5. **禁用 Cloud Agent 拉起**，如果你不需要委派。
6. **保持公开模板分享关闭**，除非成员应当把 Bot 模板发布到团队之外。
7. **把 Bot sharing 设为 Admins only**，如果共享 Bot 在队友可以使用之前应当经过审查。共享 Bot 会把所有者的应用、机密和文件带入每个队友的聊天。
8. **保持 Allow Shared Logins 关闭**，除非共享 Bot 应当让每个使用者都通过某一个 Manager 的账户行事。
9. **添加团队 Auto-review 规则。仅限 Enterprise。** 覆盖应当始终先询问、或可以自动进行的操作。生产部署、外部电子邮件、付款和接受法律条款是很好的 “Ask first” 例子。自动规则要保持狭窄。
10. **通过身份提供商把登录限制在受管设备上。** Grok Bot 登录使用你的 SSO，因此感知设备的登录策略会适用于它。这限制的是登录，不是托管计算机本身。

对于成员：

1. **绝不要把凭证粘贴到聊天中。** 掩码的机密请求是受支持的路径。
2. **对于触及账户、金钱或共享资源的操作，优先选择 Allow once，而不是 Always allow。**
3. **让 Bot 的浏览器登录范围与任务相称的账户**，并退出它不再需要的账户。在来源系统支持的地方使用范围限定的服务账户。
4. **新角色从只读任务和草稿输出开始**，并把发送、发布、购买、删除和生产变更留在批准之后。
5. **定期审查已连接应用和活动例程**，并在来源系统变化时暂停例程。

## 常见问题

### 我可以为团队打开或关闭 Grok Bot 吗？

组织范围的 **Enable Grok Bot** 开关仅限 Enterprise。它位于 Cursor 仪表盘的 Grok Bot 页面。自助 Teams 没有这个开关。禁用会阻止成员，但不会删除他们的计算机。

### 我可以通过 Admin API 管理 Grok Bot 吗？

可以。使用 [Admin API](https://cursor.com/docs/account/teams/admin-api.md#grok-bot)。

### 我可以设置 Grok Bot 的花费上限吗？

单独的 Grok Bot 花费上限目前不可用。账户级的按需控制仍然适用，按产品的拆分在仪表盘用量页面上。

### 为什么成员看到某个应用显示为 Disabled by team admin？

你的团队的连接器策略阻止了该服务器。在 **Teams Marketplace** 中启用它，如果你使用 MCP 允许列表，把它的服务器 URL 加进去，并让该成员重启应用。如果一个已允许的应用对普通成员仍然因供应商侧的权限错误而失败，请检查提供商的要求；有些供应商把他们的 MCP 端点限制给自己的管理员。见[连接应用](https://cursor.com/help/grok-bot/connect-apps.md)。

### 成员如何请求访问？

成员可以从应用发送请求。在管理员尚未完成设置的池化 Enterprise 团队上，成员看到的是一条团队设置消息。下一步是由管理员使用仅限 Enterprise 的启用开关。自助 Teams 不使用该开关。

### 我能看到团队用 Grok Bot 做哪类工作吗？

可以，在所有 Enterprise 团队上。分析仪表盘的 Conversation Insights 页面有一个 **Grok Bot** 来源，按 Type of Work 和 Level of Automation 对 Bot 对话分组。见 [Grok Bot Conversation Insights](https://cursor.com/docs/account/teams/analytics.md#grok-bot-conversation-insights)。

隔离、出口、批准、日志和数据处理问题位于 [Grok Bot 安全常见问题](https://cursor.com/docs/grok-bot/security-faq.md)。

## 相关页面

- [将 Grok Bot 部署到你的组织](https://cursor.com/docs/grok-bot/deployment.md)
- [Grok Bot 安全](https://cursor.com/docs/grok-bot/security.md)
- [Grok Bot 安全常见问题](https://cursor.com/docs/grok-bot/security-faq.md)
- [Grok Bot inference hooks](https://cursor.com/docs/grok-bot/inference-hooks.md)
- [配置身份与访问](https://cursor.com/docs/grok-bot/identity.md)
- [连接到私有网络](https://cursor.com/docs/grok-bot/private-networks.md)
- [配置检查 TLS 的代理](https://cursor.com/docs/grok-bot/proxies.md)
- [管理 Grok Bot 计算机](https://cursor.com/docs/grok-bot/computers.md)
- [使用 Grok Bot](https://cursor.com/docs/grok-bot/work.md)
- [共享 Bot](https://cursor.com/help/grok-bot/shared-bots.md)
- [Admin API](https://cursor.com/docs/account/teams/admin-api.md#grok-bot)
- [Grok Bot Conversation Insights](https://cursor.com/docs/account/teams/analytics.md#grok-bot-conversation-insights)
- [方案与计费](https://cursor.com/help/grok-bot/plans.md)
- [隐私与数据治理](https://cursor.com/docs/enterprise/privacy-and-data-governance.md)

### 与你的客户团队一起推出 Grok Bot

联系我们的团队，了解 Enterprise 方案启用、出口地址范围、数据驻留承诺，以及安全审查支持。


---

## 站点地图

[所有文档页面概览](/llms.txt)
