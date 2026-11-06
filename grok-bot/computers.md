> 原文：https://cursor.com/docs/grok-bot/computers

# 管理 Grok Bot 计算机

团队中的每个成员都有一台托管计算机，他们运行的每个 Bot 都在上面工作。[Cursor 仪表盘 Grok Bot 页面](https://cursor.com/dashboard/bot)上 **Security & Compliance** 下的 **Manage Member Computers and Data**，让管理员一次重建或终止许多成员的计算机、[连同数据一起删除计算机](https://cursor.com/docs/grok-bot/computers.md#delete-vms-and-data)，或[删除成员的全部 Grok Bot 数据](https://cursor.com/docs/grok-bot/computers.md#delete-all-grok-bot-data)。**Data Retention** 下的 **Retention Period for Computers** 会替你[删除不活跃的计算机](https://cursor.com/docs/grok-bot/computers.md#delete-inactive-computers-automatically)。其余管理员控制见[面向 Teams 与 Enterprise 的 Grok Bot](https://cursor.com/docs/grok-bot/teams.md)。

**Manage Member Computers and Data 仅限 Enterprise。** 组织管理员和 Enterprise 团队管理员可以使用它。成员永远看不到这项控制。

## 重建或终止

两种操作都保留成员的持久磁盘，因此他们已同步的 Bot、文件和登录会在下一台计算机上回来。两种操作都会移除成员自行安装的应用和软件包。Team Setup Scripts 安装的内容会在新计算机上回来。

| 操作 | 会发生什么 | 何时使用 |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Recreate** | 在最新镜像上构建一台替换计算机，并运行 [Team Setup Scripts](https://cursor.com/docs/grok-bot/private-networks.md)。当前计算机在替换机就绪之前保持可用，然后 Grok Bot 切换过去。正在回合中的 Bot 会在安全点暂停，并在新计算机上继续。如果它来不及暂停，该成员的重建不会开始，并显示为失败。 | 一次向许多成员推出新镜像或更新后的 Team Setup 清单。[网络策略](https://cursor.com/docs/grok-bot/security.md#network-policy)更改无需重建即可到达计算机。 |
| **Terminate** | 删除成员的计算机，无论正在运行还是已休眠。正在进行的工作会停止。计算机不会自行重启；成员的下一条消息会在同一块持久磁盘上启动一台新的。 | 结束成员的当前工作，或清理一台卡住的计算机。终止不会移除访问权限；见[常见问题](https://cursor.com/docs/grok-bot/computers.md#faq)。 |

## 删除 VM 和数据

**Delete VMs and Data** 会删除成员的计算机及其持久磁盘。他们的 Bot 和对话会保留，下一条消息会启动一台空计算机。

- **每次操作最多 1,000 名成员。**
- **无法撤销。** 每次操作都会记录在[审计日志](https://cursor.com/dashboard/audit-log)中。

## 删除全部 Grok Bot 数据

**Delete All Grok Bot Data** 会永久删除成员在其所属每个团队中的 Bot、对话、记忆、例程和计算机。他们的 Cursor 账户会保留。

- **每次操作最多 100 名成员。**
- **无法撤销。** 每次操作都会记录在[审计日志](https://cursor.com/dashboard/audit-log)中。

## 运行一项操作

### 打开 Manage Member Computers and Data

前往 [Cursor 仪表盘中的 Grok Bot](https://cursor.com/dashboard/bot)，在 **Security & Compliance** 下找到 **Manage Member Computers and Data**，并选择 **Manage**。

### 选择成员

按姓名或电子邮件搜索，然后勾选你想要的成员。**Select all** 会选中当前结果中的所有人。

### 选择操作

打开 **Manage selected**，选择 **Recreate VMs** 或 **Terminate VMs**。确认屏幕会重述该操作以及它影响的成员数量。同一菜单还提供[Delete VMs and Data](https://cursor.com/docs/grok-bot/computers.md#delete-vms-and-data)和[Delete All Grok Bot Data](https://cursor.com/docs/grok-bot/computers.md#delete-all-grok-bot-data)。只有在你希望成员从空白开始时才使用其中之一。

### 确认

选择 **Recreate VMs** 或 **Terminate VMs** 开始。没有计算机的成员会被跳过。

### 查看结果

进度卡片显示排队、运行中、完成、跳过和失败的数量，每个成员的行显示其状态。操作结束后，选择 **Done** 以清除结果并开始下一次。

## 操作如何运行

- **它在 Cursor 这一侧运行。** 关闭对话框或浏览器不会停止它。在同一浏览器中重新打开 **Manage**，即可接回进度。
- **大型操作需要一段时间。** 成员分批处理，每次重建都会等到替换计算机就绪才计为完成。
- **每个团队一次一项操作。** 在你对已完成的结果选择 **Done** 之前，操作按钮保持禁用。
- **Retry Start 绝不会重复工作。** 当仪表盘无法确认操作已开始时使用它。

## 被跳过和失败的成员

完成的操作会为你选择的每个成员列出一个结果。

| 结果 | 含义 | 怎么做 |
| ---------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Skipped**，No VM | 该成员没有正在运行或已休眠的计算机。 | 无需处理。 |
| **Skipped**，Action could not be completed | 操作排队期间，该成员离开了团队，或其账户发生了变化。 | 无需处理。 |
| **Failed**，Another recreation is in progress | 该成员的计算机已在被重建，由一次更新或另一位管理员发起。 | 等它完成，然后为该成员再运行一次。 |
| **Failed**，VM scan incomplete | Cursor 无法确认该成员计算机的状态，因此没有做任何更改。 | 过几分钟再运行该操作。 |
| **Failed**，Another deletion is in progress | 该成员的 Grok Bot 数据已在被删除。 | 等它完成。 |
| **Failed**，Grok Bot data could not be deleted | 删除没有完成。 | 再运行该操作。如果失败两次，[联系支持](https://cursor.com/help/grok-bot/get-help.md)。 |
| **Failed**，Action could not be completed | 重建或终止没有完成。对于重建，该成员计算机上的 Bot 可能来不及暂停，成员会保留当前计算机。 | 在可以的情况下等他们的 Bot 空闲后，为受影响的成员再运行该操作。如果失败两次，请带上该成员的电子邮件和操作时间[联系支持](https://cursor.com/help/grok-bot/get-help.md)。 |

## 通过 API 运行操作

要从脚本跨许多团队推出，用 [Organization API](https://cursor.com/docs/account/organizations/organization-admin-api.md#grok-bot-computers) 启动同样的操作。你发送一个团队、一份成员列表和一个操作，然后轮询该操作，获取计数和每个成员的结果。

API 和仪表盘共享每个团队一次一项操作的限制。从任一处启动的操作会挡住另一处，直到它完成。

## 自动删除不活跃的计算机

休眠的计算机会一直留着，直到有人删除它，即使成员已经离开或停止使用 Grok Bot。同一仪表盘页面上 **Data Retention** 下的 **Retention Period for Computers** 会替你做这项清理。当在你选择的期限内没有人给该成员的任何 Bot 发过消息时，Cursor 会在该计算机下次休眠时删除它及其持久磁盘。来自应用或 Slack 的消息都计入，无论是成员还是队友发送的。例程以及 Bot 自行开始的其他工作不计入。发送一条消息会重新开始计数。

该设置默认关闭。选择 30、60、90、180 或 365 天，或 30 到 365 天的自定义期限。打开它或缩短期限时，会先打开确认。

- **删除方式与 [Delete VMs and Data](https://cursor.com/docs/grok-bot/computers.md#delete-vms-and-data) 相同。** 成员的 Bot 和对话会保留。他们的文件、登录和已安装应用会永久消失，下一条消息会启动一台空计算机。
- **没有警告。** 成员在计算机被删除之前不会收到通知。
- **最短的期限生效。** 一台计算机跨越成员所属的每个团队，因此它遵循其任一 Enterprise 团队所设的最短期限。

每次删除都会记录在[审计日志](https://cursor.com/dashboard/audit-log)中。关闭该设置会停止以后的删除。

## 成员看到什么

重建期间，桌面应用会显示 **Updating Grok Bot's Computer**，直到切换完成。正在工作的 Bot 会在安全点暂停，并在新计算机上继续。计算机被重建时，其中的登录会话可能会掉线，因此成员会在你的身份提供商策略下重新登录公司工具。

终止之后，成员的下一条消息会在其持久磁盘上启动一台新计算机。重新连接可能需要几分钟。已同步的 Bot、文件和登录会回来；正在运行的回合会丢失。

## 常见问题

### 我可以从另一个团队重建成员的计算机吗？

可以。从该成员所属的任何一个团队选择他们。一台计算机服务于该成员所在的每个团队，因此从一个团队重建或终止它，会在所有地方影响同一台计算机。

### 终止会阻止成员使用 Grok Bot 吗？

不会。终止计算机会结束成员的当前工作。他们的下一条消息会在同一块持久磁盘上启动一台新计算机，带有他们的 Bot、文件和登录。要移除访问权限，把成员从团队中移除，或用 **Manage Group Access** 关闭他们的访问，并在身份提供商中撤销他们的会话。见 [Enable Grok Bot](https://cursor.com/docs/grok-bot/teams.md#enable-grok-bot)。

### 自动删除会打断仍在工作的 Bot 吗？

不会。Cursor 只删除处于休眠的计算机。醒着的计算机，因为 Bot 正在工作或成员正在使用 Grok Bot，绝不会被该设置删除，即使该成员的 Bot 在整个期限内都没有收到消息。

## 相关页面

- [面向 Teams 与 Enterprise 的 Grok Bot](https://cursor.com/docs/grok-bot/teams.md)
- [Grok Bot 安全](https://cursor.com/docs/grok-bot/security.md)
- [连接到私有网络](https://cursor.com/docs/grok-bot/private-networks.md)
- [Grok Bot 计算机](https://cursor.com/help/grok-bot/computer-recovery.md)


---

## 站点地图

[所有文档页面概览](/llms.txt)
