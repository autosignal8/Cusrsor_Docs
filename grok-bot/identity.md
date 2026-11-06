> 原文：https://cursor.com/docs/grok-bot/identity

# 配置身份与访问

本指南面向运行 Okta 或 Microsoft Entra ID 的管理员。它说明如何通过你的 Cursor 团队，让工程组织之外的人访问 Grok Bot，以及如何让成员从 Bot 的计算机登录由 IdP 预配的应用。更广的安全模型见 [Grok Bot 安全](https://cursor.com/docs/grok-bot/security.md)。

Grok Bot 使用你的 Cursor 账户，因此 Okta 或 Entra ID 中没有单独的 Grok Bot 应用。本指南中的一切都发生在你已经拥有的 Cursor 应用上。Cursor 单点登录是 SAML 2.0，可用于 Okta、Microsoft Entra、Google Workspace 和 OneLogin；本指南详述设备信任策略最常需要注意的那两个。

## 你将更改什么

你将做两处更改，它们的作用不同：

- **分配 Cursor 应用**，使工程组织之外的用户可以通过你的 Cursor 团队访问 Grok Bot。
- **添加一条身份验证规则**，使用户可以从 Bot 的计算机登录由 IdP 预配的应用。这台计算机运行 Linux，并且不运行设备信任代理。

只有第一处更改控制 Grok Bot 登录。第二处永远不会阻止它，并且它不适用于已连接应用的登录，因为连接应用不经过这台计算机。

## 开始之前

Grok Bot 访问要求用户是你的 Cursor 团队的成员。成员用 Cursor 账户登录，因此你现有的 [Cursor SSO](https://cursor.com/docs/account/teams/sso.md) 适用。开启自动预配后，用户在首次登录时加入团队；否则，先预配用户，然后让他们登录 Cursor 以获得席位，或从 [Cursor 仪表盘](https://cursor.com/dashboard)邀请他们。

**SCIM 仅限 Enterprise。** 如果你使用 [SCIM](https://cursor.com/docs/account/teams/scim.md)，预配是 SCIM 2.0，取消预配是自动的：在身份提供商中移除用户，就会把他们从 Cursor 中移除。

## 分配 Cursor 应用

用户只有拥有 Cursor 账户才能登录，而账户来自被分配到你现有的 Cursor SSO 应用，如果你使用 SCIM，再加上 SCIM 应用。如果 Cursor 只分配给工程部门，其他人会以 **User is not assigned to this application** 失败，或他们的邀请永远无法完成。不要创建第二个 Grok Bot 应用；放宽你已有的 Cursor 应用上的分配。

### Okta

1. 前往 **Admin Console** > **Applications** > **Applications**，打开你现有的 Cursor 应用。
2. 打开 **Assignments**，选择 **Assign** > **Assign to Groups**。要改为分配个人，选择 **Assign to People**。
3. 添加每一个应当获得 Grok Bot 的组，而不只是工程部门。
4. 如果你使用 SCIM，把同样的组分配到 Cursor SCIM 应用并推送它们。用户只有在被分配到 SCIM 应用之后才会出现在 Cursor 中。
5. 确认每个组也被分配到 SAML 应用。只有 SCIM 分配而没有 SSO 分配，仍然会阻止首次登录。

如果你在 Cursor 中使用组织级身份，在目录组同步之后把它映射到 Grok Bot 团队；见[组织](https://cursor.com/docs/enterprise/organizations.md)。应用分配和 Group Push 使用不同的组。

### Microsoft Entra ID

1. 前往 **Microsoft Entra admin center** > **Enterprise applications**，打开你现有的 Cursor 企业应用。
2. 打开 **Users and groups**，选择 **Add user/group**。
3. 添加每一个应当获得 Grok Bot 的组，而不只是工程部门。
4. 如果你使用 SCIM，把同样的组分配到 Cursor 预配应用。打开预配，并把范围设为已分配的用户和组。
5. 确认该分配授予的是 SSO，而不是仅报告访问。

基于组的分配需要 Entra ID P1 或 P2，并且不包含嵌套组。用户必须被显式分配，取消分配会阻止 SSO 登录，如果使用 SCIM，还会把他们从 Cursor 中移除。

分配生效的标志是：

- 原工程分配之外的用户可以打开 Grok Bot 并完成 Cursor SSO，而不会出现 **User is not assigned to this application**。
- 开启自动预配后，该用户在首次登录后出现在 Cursor 团队中，无需手工邀请。
- 关闭自动预配后，该用户在登录 Cursor 以获得席位之后，或在你从仪表盘邀请他们之后，位于团队中。

## 允许从计算机登录 IdP 应用

在托管计算机内部，成员通过浏览器中你自己的身份提供商登录应用程序，因此你的会话策略管理这些会话。这台计算机运行 Linux，并且不会加入 MDM，Okta FastPass 这类设备信任代理也不会在上面运行。任何要求 FastPass、已注册或受管设备、合规设备，或只有 FastPass 能满足的抗钓鱼因素的规则，都会在计算机浏览器中失败。

不要为整个公司关闭 FastPass 或设备合规。添加一条优先级更高、范围限定到这个未受管 Linux 会话的规则。此更改只覆盖 Bot 在计算机浏览器中打开的、由 IdP 预配的应用；Grok Bot 登录或已连接应用的登录并不需要它。

这些登录方式在计算机浏览器中可用：

- **密码加上能在远程浏览器中使用的第二因素**，例如 Okta Verify 推送或身份验证器应用。
- **存储在计算机上密码管理器中的通行密钥**，用 [Team Setup Scripts](https://cursor.com/docs/grok-bot/teams.md#team-setup-scripts) 安装。Team Setup Scripts 仅限 Enterprise。

对 Grok Bot 登录本身要求受管设备仍然有效。Grok Bot 使用你的 Cursor SSO，因此身份提供商中感知设备的登录策略会适用于它。该策略限制的是成员设备上的登录，不是托管计算机。

### Okta

在 Okta Identity Engine 中，这台计算机匹配 **Other Desktop** 设备平台；没有 Linux 复选框。对 Bot 在计算机浏览器中打开的每个由 IdP 预配的应用重复这些步骤。

1. 前往 **Admin Console** > **Security** > **Authentication Policies**，打开附加到该应用的策略。要找到该策略，打开 **Applications**，然后打开该应用，再打开 **Sign On**。
2. 在 FastPass、受管设备和拒绝兜底规则之上添加一条规则。给它起一个以后能找到的名字，例如 **Grok Bot computer (Linux)**。
3. 在 IF 条件中，把规则范围限定到一个组。如果你不希望该规则覆盖全公司，Cursor 已分配的组就可以。
4. 把 **Device platform** 设为 **Other Desktop**，把 **Device state** 设为 **Any**。不要要求 **Registered**、**Managed**，或依赖 FastPass 的设备保障策略。
5. 在 THEN 条件中，把访问设为 **Allowed after successful authentication**，并使用 **Password + Another factor**。不要要求抗钓鱼或硬件保护因素。
6. 保存规则，并确认它位于未受管设备拒绝兜底规则之上。

如果多个应用共享一条「受管设备上的 FastPass」策略，把 Linux 规则加到共享策略中并按组限定范围，或给这些应用各自的策略。在 Classic Engine 上，允许 **Other Desktop**，并且不要要求 **Device Trust = Trusted**。

### Microsoft Entra ID

Entra ID 没有 FastPass。会挡住计算机浏览器的授权是：合规设备、混合加入的设备、只有平台或受管通行密钥才能满足的抗钓鱼身份验证强度，以及已批准的客户端应用或应用保护策略。

1. 前往 **Microsoft Entra admin center** > **Protection** > **Conditional Access** > **Policies**。
2. 找出把这些授权应用到 Bot 所需 IdP 应用的每一条策略。
3. 保持这些策略开启。为 Linux 上的 Grok Bot 用户添加一条优先级更高的策略，或把他们排除出那些阻止性策略。
4. 在新策略中，把用户设为 Cursor 已分配的组，并把目标设为他们必须在计算机上打开的 IdP 应用。只有在你接受该范围时才选择 **All resources**。
5. 在 **Device platforms** 下，包含 **Linux**，并排除 **Windows** 和 **macOS**。
6. 把授权仅设为 **Require multifactor authentication**。
7. 先以 report-only 模式启动该策略，然后再打开它。
8. 在现有的合规设备策略上，排除该组或排除 Linux。

要在没有受管设备的情况下获得抗钓鱼能力，使用同步通行密钥能够满足的身份验证强度，并在强制执行之前从计算机测试登录。

更改生效的标志是：用户可以从 Bot 的计算机登录由 IdP 预配的应用，而不会出现 FastPass 或设备合规错误；已连接应用的登录与以前一样可用；笔记本电脑上的登录不变。

无论哪种方式，撤销都由你掌握：在身份提供商中撤销用户会结束他们在计算机内的应用会话，组织管理员也可以随时[终止该成员的计算机](https://cursor.com/docs/grok-bot/security.md#identity-and-sign-ins)。

## 限制

- Okta FastPass 不在 Linux 上运行，因此 Bot 的计算机无法满足 FastPass 或受管设备规则。
- 计算机默认不会加入 MDM。
- Entra ID 中基于组的分配需要 P1 或 P2，并且不包含嵌套组。

## 常见问题

### 我需要在 Okta 或 Entra ID 中单独建一个 Grok Bot 应用吗？

不需要。Grok Bot 登录使用 Cursor 账户，因此你现有的 Cursor 应用控制访问。放宽该应用上的分配，而不是新建一个。

### 设备信任更改会影响已连接应用的登录吗？

不会。连接应用不经过这台计算机，因此计算机浏览器的身份验证规则不适用于它。

### 这些更改会削弱员工笔记本电脑上的登录吗？

不会。把新规则或策略的范围限定到 Linux 平台和 Cursor 已分配的组，笔记本电脑登录仍保持你现有的要求。

### 如果我取消用户对 Cursor 应用的分配，他们会失去访问权限吗？

会，他们不能再通过 SSO 登录。使用 SCIM 时，取消分配还会自动把他们从 Cursor 中移除；不使用 SCIM 时，在仪表盘中把他们从团队中移除。

## 相关页面

- [Grok Bot 安全](https://cursor.com/docs/grok-bot/security.md)
- [面向 Teams 与 Enterprise 的 Grok Bot](https://cursor.com/docs/grok-bot/teams.md)
- [SSO](https://cursor.com/docs/account/teams/sso.md)
- [SCIM](https://cursor.com/docs/account/teams/scim.md)
- [组织](https://cursor.com/docs/enterprise/organizations.md)


---

## 站点地图

[所有文档页面概览](/llms.txt)
