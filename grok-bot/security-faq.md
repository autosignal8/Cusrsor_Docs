> 原文：https://cursor.com/docs/grok-bot/security-faq

# Grok Bot 安全常见问题

这些是安全审查最常问的问题。每个答案背后的控制位于 [Grok Bot 安全](https://cursor.com/docs/grok-bot/security.md)。仪表盘设置和推出步骤位于[面向 Teams 与 Enterprise 的 Grok Bot](https://cursor.com/docs/grok-bot/teams.md)。

## 隔离与访问

### 用户之间如何隔离？

每个用户获得一台专用的 Firecracker microVM，有自己的内核、内存和虚拟设备。一个用户无法触及另一个用户的计算机。在同一个用户内部，每个 Bot 都共享这台计算机。把计算机上的登录或文件视为该用户运行的每个 Bot 都可以使用。当一项工作负载需要自己的计算机和凭证集时，给它一个单独的 Cursor 用户。队友的[共享 Bot](https://cursor.com/help/grok-bot/shared-bots.md)在该用户自己与它的聊天中，通常在该用户的计算机上工作。

### Bot 可以访问什么？

只有成员或团队授予的账户和已连接应用。Bot 没有自己的身份，也不能持有比已登录成员更多的访问权限。连接器 token 留在 Cursor 的后端。保存在 Vault 中的登录和卡，以及在 Secure Form 中输入的值，会在模型看不到它们的情况下填入。表单无法处理的步骤，例如单点登录或通行密钥，会交给成员。见[身份与登录](https://cursor.com/docs/grok-bot/security.md#identity-and-sign-ins)。

### 如果我们阻止一个应用，Bot 还能在浏览器中到达该服务吗？

能。阻止一个应用并不会阻止该服务的网站。连接器策略和[网络策略](https://cursor.com/docs/grok-bot/security.md#network-policy)是独立的层。要关闭网站这条路径，需要 **Network Controls**，且仅限 Enterprise。

### 管理员可以把连接器推送给成员吗？

不能。连接器策略允许或阻止服务器。向成员预配连接器（无论是强制还是默认开启）不可用。

## 网络与出口

### 我们可以限制计算机能到达哪些目的地吗？

**Network Controls 仅限 Enterprise。** 管理员从仪表盘 Grok Bot 页面上的该面板设置 Grok Bot 网络策略。自助 Teams 看不到它，也不能设置目的地允许列表。没有策略的团队默认为允许全部。见[网络策略](https://cursor.com/docs/grok-bot/security.md#network-policy)。

### 管理员控制是按组生效，还是只在组织范围生效？

目录组范围是 **Network Controls** 的一部分，且仅限 Enterprise。组可以设置自己的策略，锁定后团队策略对所有人生效。组织范围的启用开关也仅限 Enterprise，并适用于整个组织。Cloud Agents、Team Rules 和公开模板分享在 Teams 和 Enterprise 上适用于整个团队。Enforce Auto-review 和 Auto-review 规则仅限 Enterprise，也适用于整个团队。Allow Local Egress Routing 仅限 Enterprise，并适用于整个团队。在 Enterprise 上，组的 Grok Bot 标签页可以为该组成员放宽 Cloud Agents、agent email、本地执行、本地出口、Auto-review、规则和设置脚本，绝不会收紧它们。见[组设置](https://cursor.com/docs/grok-bot/teams.md#group-settings)。

### 为什么有些网站会阻止 Bot？

有些服务会标记数据中心 IP 地址。出口范围是共享的静态范围，可从你的客户团队获取；在合适的地方把它们加入你自己服务的允许列表。

### 有专用出口 IP 吗？

没有。计算机使用共享的静态出口 IP 地址。没有按客户专用的 IP。当前范围可从你的客户团队获取。

### 流量可以经过我们自己的网络吗？

可以。成员可以把 Grok Bot 计算机的 Web 流量经由自己的桌面路由，使用其网络和 IP 地址。在该路由上，解析到私有地址或 VPN 地址的站点会被阻止，除非已为你的组织允许。Enterprise 团队还可以通过 **Team Setup Scripts** 在每台托管计算机上安装网络客户端。见[连接到私有网络](https://cursor.com/docs/grok-bot/private-networks.md)。

### 从我们的网络进行计算机设置时，Grok Bot 会挂起。为什么？

Zscaler 这类会检查 TLS 的网关放行了 `api2.cursor.sh`，却阻止或检查了计算机嵌套的 `cursorvm.com` 主机名。聊天可能继续工作，也可能不工作；无论哪种，计算机链路都会失败。允许 `*.cursorvm.com` 和 `*.*.cursorvm.com`，把它们排除在检查之外，并把更改也应用到离网配置文件。见[配置检查 TLS 的代理](https://cursor.com/docs/grok-bot/proxies.md)。

## 批准、日志与数据

### Auto Review 检查什么？

强制执行开启时，Auto Review 会评估 shell 命令、应用调用、计算机操作、自动化写入，以及 Cloud Agent 和 subagent 启动这类委派。它可以让操作继续、要求批准，或拒绝它。在 Enterprise 上，团队管理员可以从 Grok Bot 页面强制执行 Auto-review，并添加每个成员都会继承的团队 Auto-review 规则。成员可以在此之上添加更严格的个人规则，规则冲突时 **Ask first** 优先。如果管理员关闭强制执行，成员回到只用自己的规则。它不会审查每一个副作用，例如记忆写入和大多数设置更改。

### 我们自己的安全工具可以检查 Grok Bot 发给模型的内容吗？

可以，使用 inference hooks，这是一项 Enterprise beta。Cursor 把每个 Grok Bot 模型请求签名后发送到你的组织自己运行、或通过安全供应商使用的 AI 安全服务器，你的服务器在模型运行之前允许或拒绝它。Hooks 检查模型输入。在开启了工具调用检查的团队上，它们也可以在工具调用运行之前检查工具调用，但不检查操作开始之后做了什么。任何 Enterprise 团队的团队管理员都可以从仪表盘设置它们。见 [Grok Bot inference hooks](https://cursor.com/docs/grok-bot/inference-hooks.md)。

### 我能看到 Bot 代表我的团队做了什么吗？

花费和用量在仪表盘用量页面上，按产品拆分。**审计日志仅限 Enterprise。** 它们覆盖管理员、安全和身份验证事件，加上 Grok Bot 控制面事件，例如 Bot 创建、访问更改、Team Setup 和例程，可按应用筛选，并可以流式传输到你的 SIEM。**Action Recording 仅限 Enterprise**，并且是一项单独的设置，默认关闭。启用后，它会在内部记录 Bot 操作。**OpenTelemetry Export 仅限 Enterprise。** 配置它，即可在你自己的收集器中接收这些事件，并带上标签 `cursor.surface=grok_bot`。它们不会出现在 Audit Log 页面上。

### 我可以限制 Grok Bot 使用哪些模型吗？

不可以。团队模型允许列表不管辖 Grok Bot。无论该设置如何，它都使用 xAI 第一方模型，并且可能使用[子处理者列表](https://trust.cursor.com/subprocessors)上提供商的第三方模型。有模型允许列表的团队在启用 Grok Bot 时会看到这一确认。Grok Bot 管理模型选择，没有面向客户的模型选择器。如果你的合同限制子处理者，请联系客户团队。见[哪些 AI 模型驱动 Grok Bot？](https://cursor.com/help/grok-bot/models.md)

### Grok Bot 计算机在哪里运行？

在美国。Grok Bot 也包含在 Cursor 的[仅限美国的数据驻留](https://cursor.com/docs/enterprise/privacy-and-data-governance.md#us-data-residency)计划中，该计划为已加入的客户把推理、数据处理和存储留在美国。联系你的客户团队以加入。见[数据驻留](https://cursor.com/docs/grok-bot/security.md#data-residency)。

### 我们可以在本地或用自己的镜像运行 Grok Bot 吗？

不可以。Grok Bot 只在 Cursor 托管的云端计算机上运行。本地部署、在你自己的边界内部署，以及自带镜像部署，目前都不支持。

### 我们可以在团队计算机上安装自己的安全工具吗？

**Team Setup Scripts** 仅限 Enterprise。这些管理员可以在每台团队计算机上安装自己的工具。Grok Bot 不提供面向客户的内置 EDR 源。

### 为什么成员必须重新登录公司工具？

计算机被重建时，其中的登录会话可能会掉线，例如在镜像更新之后，或管理员重建它时。会话依托你的身份提供商，因此你的会话策略也会适用。

### 管理员终止计算机时，数据会怎样？

计算机管理仅限 Enterprise。组织管理员和 Enterprise 团队管理员可以终止成员的计算机。持久磁盘会保留，成员的下一次会话会在该磁盘上启动一台新计算机。终止计算机与仅限 Enterprise 的启用开关是分开的。

### 哪些认证适用于 Grok Bot？

Anysphere 持有由 Schellman 颁发的 ISO/IEC 27001 和 ISO/IEC 42001 认证，Grok Bot 包含在当前的 ISO 范围内。证书和报告可在 [trust.cursor.com](https://trust.cursor.com) 获取。

## 相关页面

- [Grok Bot 安全](https://cursor.com/docs/grok-bot/security.md)
- [面向 Teams 与 Enterprise 的 Grok Bot](https://cursor.com/docs/grok-bot/teams.md)
- [Grok Bot inference hooks](https://cursor.com/docs/grok-bot/inference-hooks.md)
- [配置身份与访问](https://cursor.com/docs/grok-bot/identity.md)
- [连接到私有网络](https://cursor.com/docs/grok-bot/private-networks.md)
- [隐私与数据治理](https://cursor.com/docs/enterprise/privacy-and-data-governance.md)


---

## 站点地图

[所有文档页面概览](/llms.txt)
