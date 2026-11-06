> 原文：https://cursor.com/docs/grok-bot/get-started

# 开始使用 Grok Bot

本指南带你从安装走到完成的第一项任务。你将设置桌面应用，创建一个职责清晰的 Bot，并审查它的工作。更短的帮助中心清单见[入门](https://cursor.com/help/grok-bot/onboarding.md)。

Grok Bot 的访问权限包含在每一项付费的个人 Cursor 方案和 Cursor Teams 方案中，也可以通过个人 SuperGrok 关联获得，或使用自助 Grok Business 方案上的席位。见[方案与计费](https://cursor.com/help/grok-bot/plans.md)。Grok Bot 需要云端数据存储，因此使用 Privacy Mode (Legacy) 的账户会在开始前被提示切换到 Privacy Mode。

### 安装桌面应用

从 [Cursor 仪表盘](https://cursor.com/dashboard/bot)的 Grok Bot 页面获取安装程序：**Download Grok Bot** 这一设置行会安装桌面应用，或复制一个你可以分享的链接。如果你没有仪表盘访问权限，请向管理员索取下载链接。应用可在 macOS（Apple silicon 和 Intel）、Windows（x64 和 Arm64）以及 Linux（x64 和 Arm64）上运行，并会自动检查更新。在手机上，从 App Store 或 Google Play 安装 Grok Bot；见[在手机上使用 Grok Bot](https://cursor.com/docs/grok-bot/get-started.md#use-grok-bot-on-your-phone)。

### 用 Cursor 账户登录

在欢迎屏幕上选择 **Sign in**，并在浏览器中完成身份验证。Grok Bot 使用你的 Cursor 账户，没有单独的 Grok Bot 登录。如果你的组织使用单点登录，按平时的公司流程登录；你现有的 Cursor SSO 配置会生效。登录问题见[登录 Grok Bot](https://cursor.com/help/grok-bot/sign-in.md)。

### 认识你的第一个 Bot

登录后，点过一段简短介绍。然后 Grok Bot 会设置你的第一个 Bot，名为 **Grok Bot**，并打开与它的聊天。

要添加更多 Bot，选择侧边栏中的 **+** 按钮，然后选择 **Create a Bot**，为 Bot 命名，并选择 **Create**。在 Cursor 团队中，分享一个 Bot 可以让队友与它聊天；在 **+** 下选择 **Browse shared Bots**，可以找到分享给你的 Bot；见[共享 Bot](https://cursor.com/help/grok-bot/shared-bots.md)。在第一条消息里给每个 Bot 一项主要职责，并让它记住自己应该如何工作。要重命名，点击聊天顶部的名称以打开详情。职责聚焦的 Bot 会积累比什么都做的助手更有用的上下文。例如：

> 你的职责是产品性能。用我们的可观测性工具调查产品性能问题。保留链接和截图，把证据和假设分开，并先讲影响最大的问题。绝不更改生产设置。以后的工作请记住这一点。

### 交给它第一项任务

一条有力的请求会说明结果、重要的来源、约束、交付物，以及 Bot 应在哪里停下来等你。想在五分钟内得到第一次结果，可以附上一份文档并试试：

> 用五个要点总结这份文档。在单独的一节中列出每一个日期、决策和未决问题，并为每一项注明所在章节。不要修改源文件。

### 登录它需要的工具

当 Bot 遇到需要身份验证的应用时，它会使用保存在 [Vault](https://cursor.com/help/grok-bot/secrets.md#what-is-vault) 中的登录、已连接的密码管理器，或通过 Secure Form 向你索取。Bot 永远看不到密码，已登录的会话会保留在你的计算机上，供以后的任务使用。对于单点登录、通行密钥，或表单无法处理的其他步骤，它会把计算机交给你：点击 **Open computer**，完成该步骤，然后交还控制权。对于受支持的服务，你也可以改为[连接一个应用](https://cursor.com/docs/grok-bot/work.md#connect-apps)。

### 审查结果并让它固定下来

要求修改，并明确说出希望长期保留的偏好，例如你希望重复使用的报告格式。当流程稳定后，把它保存为 [Skill 或例程](https://cursor.com/docs/grok-bot/work.md#skills-and-routines)。

在让 Bot 更改外部系统之前，阅读[批准与 Auto Review](https://cursor.com/docs/grok-bot/security.md#approvals-and-auto-review)。它涵盖批准提示、背后的审查模型，以及如何让凭证远离聊天。

## 在手机上使用 Grok Bot

iPhone 应用（iOS 18 或更高版本）和 Android 应用（Android 9 或更高版本）连接到与桌面相同的 Bot、对话、例程和云端计算机。从 App Store 或 Google Play 安装 Grok Bot，并用同一个 Cursor 账户登录；来自你的方案或 SuperGrok 关联的访问权限会一并带上，无需再次购买。在手机上，你可以给 Bot 发消息、观看并接管计算机、批准操作，以及暂停或恢复例程。

有些流程仍留在桌面：通过演示教授工作流、测试例程，以及更新计算机。设置细节见 [手机上的 Grok Bot](https://cursor.com/help/grok-bot/mobile.md)；如果你的账户不包含访问权限，见[从移动应用订阅](https://cursor.com/help/grok-bot/mobile-purchase.md)。

## 如果有地方不对

大多数设置问题在你完全退出并重新打开应用后就会消失。关于计算机、例程、已连接应用和启动的分症状帮助，见帮助中心的 [Grok Bot 操作指南](https://cursor.com/help/grok-bot/how-to.md#how-do-i-troubleshoot-grok-bot-computer-routine-connector-and-startup-problems)，或[联系支持](https://cursor.com/help/grok-bot/get-help.md)。

## 相关页面

- [使用 Grok Bot](https://cursor.com/docs/grok-bot/work.md)
- [Grok Bot 使用场景](https://cursor.com/docs/grok-bot/use-cases.md)
- [将 Grok Bot 部署到你的组织](https://cursor.com/docs/grok-bot/deployment.md)
- [方案与计费](https://cursor.com/help/grok-bot/plans.md)


---

## 站点地图

[所有文档页面概览](/llms.txt)
