> 原文：https://cursor.com/docs/account/teams/scim

# SCIM

## 概述

SCIM 2.0 预配通过身份提供商自动管理团队成员和目录组。它在已启用 SSO 的 Enterprise 方案上可用，[联系销售](https://cursor.com/contact-sales?source=docs-scim)以获取访问权限。

本页介绍单个团队的 SCIM。如果你在一个[组织](https://cursor.com/docs/enterprise/organizations.md)下运行多个已关联的团队，也可以在组织级别同步目录组，并通过[组织组](https://cursor.com/docs/enterprise/organization-groups.md)在团队之间使用它们。

## 前提条件

- Cursor Enterprise 方案
- 先配置 SSO。**SCIM 需要一个活动的 SSO 连接**
- 对身份提供商的管理员访问权限（Okta、Azure AD 等）
- 对 Cursor 团队的管理员访问权限

## 它如何工作

### 用户预配

当用户在身份提供商中被分配到 SCIM 应用时，会自动加入 Cursor。取消分配时，他们会被移除。更改会实时同步。

### 目录组

目录组及其成员关系从身份提供商同步。在身份提供商中管理组和用户；Cursor 以只读方式显示这些信息。在团队内，目录组控制支出限额和策略，包括 [Grok Bot 组设置](https://cursor.com/docs/grok-bot/teams.md#group-settings)。

要从目录驱动团队成员关系和角色，把目录组同步到一个[组织组](https://cursor.com/docs/enterprise/organization-groups.md#set-up-scim-synced-groups)，再把该组映射到一个团队。参见[用组来驱动团队](https://cursor.com/docs/enterprise/organization-groups.md#use-groups-to-power-teams)。

### 支出管理

为每个目录组设置不同的人均支出限额。目录组限额优先于团队级限额。属于多个组的用户获得适用限额中的最高值。

## 设置

### 确保已配置 SSO

SCIM 要求先设置 SSO。如果尚未配置 SSO，请先遵循 [SSO 设置指南](https://cursor.com/docs/account/teams/sso.md)。

### 打开 Active Directory Management

使用管理员账户前往 [cursor.com/dashboard/members?subtab=active-directory](https://www.cursor.com/dashboard/members?subtab=active-directory)，或进入仪表板设置，选择 “Members & Groups” 选项卡，然后选择 “Directory Groups” 子选项卡。

### 开始 SCIM 设置

SSO 验证之后，你会看到逐步 SCIM 设置的链接。点击它开始配置向导。

### 在身份提供商中配置 SCIM

在身份提供商中创建或配置 SCIM 应用，使用 Cursor 提供的 SCIM 端点和令牌，启用用户预配和推送组预配，然后测试连接。

### 配置支出限额（可选）

回到 Cursor 的 Active Directory Management 页面，查看已同步的目录组，为特定组设置人均支出限额，并查看哪些限额适用于属于多个组的用户。

### 身份提供商设置

提供商专用的设置说明：

### 身份提供商指南

Okta、Azure AD、Google Workspace 等的设置说明。

## 管理用户和组

通过 SCIM 预配的用户和组在身份提供商中管理。Cursor 以只读方式显示该目录数据。在 SCIM 之前已经在团队中的用户，仍可以从 Members 仪表板移除。

### 用户管理

- 在身份提供商中把用户分配到 SCIM 应用来添加用户
- 从 SCIM 应用取消分配来移除用户
- 用户资料更改（姓名、电子邮件）会从身份提供商自动同步

### 组管理

- 目录组从身份提供商自动同步
- 组成员关系的更改会实时反映
- 用组来组织用户并设置不同的支出限额

### 支出限额

- 为每个目录组设置不同的人均限额
- 用户继承其各组中最高的支出限额
- 组限额覆盖默认的全团队人均限额

## 常见问题

### 为什么仪表板中没有显示 SCIM 管理？

在设置 SCIM 之前，请确保 SSO 已正确配置并且可用。SCIM 需要活动的 SSO 连接才能工作。

### 为什么用户没有同步？

确认用户已在身份提供商中被分配到 SCIM 应用。用户必须被明确分配才会出现在 Cursor 中。

### 为什么组没有出现？

检查身份提供商的 SCIM 设置中是否启用了推送组预配。组同步必须与用户同步分开配置。

### 为什么支出限额没有生效？

确认用户在身份提供商中已被正确分配到预期的组。组成员关系决定适用哪些支出限额。

### 我可以直接在 Cursor 中管理 SCIM 用户和组吗？

通过 SCIM 预配的用户和组在 Cursor 中是只读的。在身份提供商中添加、移除或更新它们。在 SCIM 之前已经在团队中的用户，仍可以从 Members 仪表板移除。

### 更改同步有多快？

在身份提供商中所做的更改会实时同步到 Cursor。大规模批量操作可能会有短暂延迟。

### 我可以从身份提供商同步用户角色吗？

角色在 Cursor 中配置。要从目录设置角色，把目录组同步到一个[组织组](https://cursor.com/docs/enterprise/organization-groups.md#set-up-scim-synced-groups)，并把该组以某个角色映射到团队。该组的成员会在团队上获得该角色。参见[从映射设置团队角色](https://cursor.com/docs/enterprise/organization-groups.md#set-team-roles-from-mappings)。

映射上的角色也控制仪表板编辑。当组以某个角色连接到团队时，同步会管理该团队上的角色，管理员不能再在仪表板中更改它们。只有在选择组时没有选择角色的情况下，角色才仍可在仪表板中编辑。

### 为什么 Members 仪表板上有不在已预配组中的用户？

设置团队级 SCIM 时，现有用户不会自动从 Cursor 移除。从 Members 仪表板移除他们，或用 SCIM 同步一次，然后从身份提供商取消预配。

### 为什么已同步组中的用户与 Cursor Members 仪表板上的用户不一致？

用户账户完成预配后，在他们第一次登录之前不会出现在 Cursor Members 仪表板上。

### SCIM 在 Enterprise 方案上可用

联系我们的团队以请求访问权限。

---

## 站点地图

[所有文档页面概览](/llms.txt)
