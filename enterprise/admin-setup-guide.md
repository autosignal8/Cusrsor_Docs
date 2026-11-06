> 原文：https://cursor.com/docs/enterprise/admin-setup-guide

# 管理员设置指南

Cursor 在设计时考虑了企业推出。在 [Cursor 管理仪表板](https://cursor.com/dashboard)上，你可以找到管理组织内 Cursor 使用所需的一切。

Cursor 的可配置程度很高。建议通读完整的 [Enterprise 文档](https://cursor.com/docs/enterprise.md)，以便全面了解如何治理和保护团队对 Cursor 的使用。

本页简要归纳了我们建议你在 Cursor 中设置组织时采取的关键操作。

## 了解 Cursor 的安全流程与认证

- [查看 Trust Center](https://trust.cursor.com/)，了解审计、认证、政策和子处理方
- 阅读 [Cursor 的安全简报](https://cursor.com/security)

## 设置身份与访问

1. 使用 Okta、Entra 或任何 SAML 2.0 提供商完成 [SSO](https://cursor.com/docs/account/teams/sso.md)
2. 可选：启用 [SCIM](https://cursor.com/docs/account/teams/scim.md) 以自动预配
3. 如果需要在单个 Team 之外进一步组织，可选地[配置 Organizations、Teams 和 Groups](https://cursor.com/docs/enterprise/organizations.md)。建议咨询你的 Cursor 客户团队，以获得适合你需求的组织设置指导。

## 连接 Git 平台

在任何人能从仓库启动 Cloud Agent 之前，Cursor 账户管理员需要为该账户连接源代码管理。设置 [GitHub（Cloud 与 Enterprise Server）](https://cursor.com/docs/integrations/github.md)、[GitLab（Cloud 与 Self-Hosted）](https://cursor.com/docs/integrations/gitlab.md)、[Bitbucket Cloud](https://cursor.com/docs/integrations/bitbucket.md) 或 [Azure DevOps](https://cursor.com/docs/integrations/azure-devops.md)。

## 部署与监控

1. 部署 Cursor，并为受管设备和扩展配置 [MDM 策略](https://cursor.com/docs/enterprise/deployment-patterns.md#mdm-configuration)
2. [通过 MDM 配置 AllowedTeamIDs](https://cursor.com/docs/enterprise/identity-and-access-management.md#allowed-team-ids)，以限制在公司设备上使用个人 Cursor 账户
3. 使用[仪表板](https://cursor.com/docs/account/teams/dashboard.md)监控用量并设置警报
4. 如需更深入的安全检查，遵循[安全与隐私加固](https://cursor.com/docs/enterprise/security-hardening.md)

## 为 Team 启用建议的功能

- 启用[浏览器自动化](https://cursor.com/docs/agent/tools/browser.md#enabling-browser-for-enterprise)，以便 Agent 使用内置浏览器测试
- 对于 JetBrains IDE（IntelliJ 等），安装 [通过 ACP 使用的 Cursor Agent](https://cursor.com/docs/integrations/jetbrains.md)。它包含在 Cursor 中，不需要单独的 JetBrains AI 许可证

## 相关

- [Enterprise 概述](https://cursor.com/docs/enterprise.md)
- [身份与访问管理](https://cursor.com/docs/enterprise/identity-and-access-management.md)
- [部署模式](https://cursor.com/docs/enterprise/deployment-patterns.md)
- [安全与隐私加固](https://cursor.com/docs/enterprise/security-hardening.md)

---

## 站点地图

[所有文档页面概览](/llms.txt)
