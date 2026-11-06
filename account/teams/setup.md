> 原文：https://cursor.com/docs/account/teams/setup

# 入门

## 面向团队的 Cursor

Cursor 适用于个人和团队。Teams 方案为组织提供这些工具：SSO、团队管理、访问控制和使用情况分析。

## 创建团队

按以下步骤创建团队：

### 设置 Teams 方案

要创建 Team，按以下步骤操作：

1. **新用户**：访问 [cursor.com/team/new-team](https://cursor.com/team/new-team) 创建新账户和团队
2. **现有用户**：前往你的[仪表板](https://cursor.com/docs/account/teams/dashboard.md)，点击 “Upgrade to Teams”

### 填写 Team 详情

选择 Team 名称和计费周期

### 邀请成员

邀请团队成员。用户数按比例计费——你只需为用户作为成员的时间付费。

你可以选择启用域名匹配，这样邮箱域名已经验证且匹配的队友无需邀请即可加入团队。在[团队设置](https://cursor.com/dashboard/settings#domain-join)中配置。

### 启用 SSO（可选）

启用 [SSO](https://cursor.com/docs/account/teams/sso.md)，以便提高安全性并自动完成加入流程。

## 常见问题

### 我的团队使用 ZScaler / 代理 / VPN，Cursor 能用吗？

Cursor 默认使用 HTTP/2。一些代理和 VPN 会阻止它。

前往 `Cursor Settings` > `Network`，然后把 `HTTP Compatibility Mode` 设为 `HTTP/1.1`。

### 如何为公司购买许可证？

Cursor 按活跃用户计费，而不是按席位。你可以随时添加或移除用户——新成员按其剩余时间按比例收费。如果被移除的用户使用过任何额度，其席位会一直被占用到计费周期结束。

你的续订日期保持不变。

### 我自己不用 Cursor 时，如何建立团队？

把你自己设为 [Unpaid Admin](https://cursor.com/docs/account/teams/members.md)，即可在没有许可证的情况下进行管理。

Teams 至少需要一名付费成员。你可以先完成设置、邀请一名成员，然后在计费前更改自己的角色。

### 如何把 Cursor 加入公司的 MDM？

所有平台的下载链接见 [cursor.com/downloads](https://cursor.com/downloads)。

MDM 说明：

- [Omnissa Workspace ONE](https://docs.omnissa.com/bundle/MobileApplicationManagementVSaaS/page/DeployInternalApplications.html)（原 VMware）
- [Microsoft Intune（Windows）](https://learn.microsoft.com/en-us/mem/intune-service/apps/apps-win32-app-management)
- [Microsoft Intune（Mac）](https://learn.microsoft.com/en-us/mem/intune-service/apps/lob-apps-macos-dmg)
- [Kandji MDM](https://support.kandji.io/kb/custom-apps-overview)

### 我可以同时属于多个团队吗？

在 Teams 方案上，一个 Cursor 账户一次只能属于一个团队。加入另一个团队之前，先离开当前团队。在 Enterprise Organization 中，你可以属于同一组织内的多个团队，并在每个团队中拥有不同角色。参见[组织](https://cursor.com/docs/enterprise/organizations.md)。

---

## 站点地图

[所有文档页面概览](/llms.txt)
