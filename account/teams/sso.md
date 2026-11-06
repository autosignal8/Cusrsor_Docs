> 原文：https://cursor.com/docs/account/teams/sso

# SSO

## 概述

SAML 2.0 SSO 在 Teams 和 Enterprise 方案上不另收费。使用你现有的身份提供商（IdP）来验证团队成员，而无需单独的 Cursor 账户。

本页介绍 Team Settings 中的团队级 SSO。在 Teams 方案上使用它，或在仍拥有自己的身份提供商的团队上使用。Enterprise 组织管理员在组织的 Settings 中配置 SSO。参见[组织](https://cursor.com/docs/enterprise/organizations.md#identity-model)和[身份与访问管理](https://cursor.com/docs/enterprise/identity-and-access-management.md#single-sign-on-sso-and-saml)。

## 前提条件

- Cursor Teams 方案，或仍使用团队级 SSO 的团队
- 对身份提供商的管理员访问权限（例如 Okta）
- 对 Cursor 团队的管理员访问权限

## 配置步骤

### 登录你的 Cursor 账户

使用团队管理员账户前往 [Single Sign-On (SSO) 设置](https://cursor.com/dashboard/team-settings#single-sign-on-sso)。

### 找到 SSO 配置

找到 “Single Sign-On (SSO)” 部分并展开它。

### 开始设置

在 “SSO-Provider Connection Settings” 旁点击 “Configure”，开始 SSO 设置并按照向导操作。

### 配置身份提供商

在你的身份提供商中（例如 Okta）：

- 创建新的 SAML 应用
- 使用 Cursor 的信息配置 SAML 设置
- 设置 Just-in-Time（JIT）预配

### 验证域名

在 “Domain Verification Settings” 旁点击 “Configure”，验证用户的域名。

## 查看 SSO 配置

团队管理员可以随时查看团队级 SSO 连接及其域名：

1. 使用团队管理员账户前往 [Single Sign-On (SSO) 设置](https://cursor.com/dashboard/team-settings#single-sign-on-sso)。
2. 在 “SSO-Provider Connection Settings” 旁点击 “Configure”，查看提供商连接详情。
3. 在 “Domain Verification Settings” 旁点击 “Configure”，查看或管理已验证的域名。

这些设置用于团队级 SSO。Enterprise 组织管理员在组织的 Settings 中查看组织级 SSO。参见[组织](https://cursor.com/docs/enterprise/organizations.md#identity-model)。

### 身份提供商设置指南

提供商专用的设置说明：

### 身份提供商指南

Okta、Azure AD、Google Workspace 等的设置说明。

## 其他设置

一旦域名验证和 SSO 提供商连接生效，该域名上的用户就必须使用 SSO 登录。没有单独的强制开关。

- 新用户通过 SSO 登录时会自动加入
- 通过身份提供商管理用户

## 多个域名

要在组织中处理多个域名：

1. 在 Cursor 中通过域名验证设置**分别验证每个域名**
2. 在身份提供商中**配置每个域名**
3. 每个域名都需要独立完成验证流程

## 故障排除

### 用户未被分配到此应用

如果 Microsoft Entra ID 返回 `AADSTS50105`，或其他身份提供商返回 **User is not assigned to this application**，Cursor 无法授予访问权限。身份提供商阻止了登录，因为该用户未被分配到 Cursor 企业应用。

请身份提供商中的管理员直接分配该用户，或将其作为已分配组的直接成员。嵌套组常常会失败：在许多提供商（包括 Microsoft Entra ID）上，仅仅属于某个已分配组之下的组是不够的。

分配保存后，等待几分钟让它生效，然后让用户再次登录。

面向成员的这一错误说明见 [SSO 与身份验证](https://cursor.com/help/security-and-privacy/sso.md#why-do-i-see-aadsts50105-or-user-is-not-assigned-to-this-application)。

### 其他问题

- 确认域名已在 Cursor 中验证
- 确保 SAML 属性映射正确
- 确认 SSO 连接处于活动状态且域名已验证
- 使身份提供商与 Cursor 中的名和姓一致
- 查看上面的提供商专用指南
- 如果问题仍然存在，访问 [SSO 帮助中心](https://cursor.com/help/security-and-privacy/sso.md)

---

## 站点地图

[所有文档页面概览](/llms.txt)
