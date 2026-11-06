> 原文：https://cursor.com/docs/grok-bot/settings

# 设置与通知

Grok Bot 设置涵盖应用范围和桌面本地的行为。窗格为 **General**、**Computer**、**Usage & Billing** 和 **Updates**。每个 Bot 的名称、图片和标签位于其对话详情中。从账户菜单打开设置，或使用 Cmd+,。

设置分区取决于你的账户和推出进度，因此下面的某些选项可能不会出现。

## General

- **Account.** 登录或退出 Grok Bot 使用的 Cursor 账户。账户菜单中还有 **Get Grok Bot for mobile**，**Support** > **About** 显示已安装的版本。
- **Appearance.** Follow System、Light 或 Dark。
- **Bot.** [例程](https://cursor.com/help/grok-bot/routines.md#how-do-schedules-and-time-zones-work)用于计划的时区，以及你的 Auto-review 开关和 **Custom Rules**，加上管理员要求的任何团队规则。Grok Bot 管理模型选择，因此没有模型选择器。

Auto-review 规则决定哪些操作会停下来等你批准。当管理员为团队强制执行 Auto-review 时，同一张表还会显示锁定的团队规则，并带有 `Required by your admin. You can't edit or delete this rule.` 成员可以在此之上添加自己的规则，但它们只会让行为更严格；规则冲突时 **Ask first** 优先。如果管理员关闭强制执行，你只会看到并使用自己的规则。你的个人规则保存在账户中，因此同一套规则会在每台桌面和 iPhone 上生效。更改之前阅读[批准与 Auto Review](https://cursor.com/docs/grok-bot/security.md#approvals-and-auto-review)。

## Computer

**Computer** 有两个分区。**Your computer** 存放已登录你账户的桌面的设置。**Bot's computer** 存放你的 Bot 共用的云端计算机的更新和重置控制；见[更新](https://cursor.com/docs/grok-bot/settings.md#updates)。

### 在你的桌面上运行命令

**Execution on this computer** 控制 Bot 是否可以在桌面上运行命令。**Your computer** 下列出的每台桌面都有自己的设置。在某台桌面被列出之前，页面显示的是 **Execution on Local Computer**。默认是每次命令都需批准，并且该设置只适用于那台桌面。更改之前阅读[批准与 Auto Review](https://cursor.com/docs/grok-bot/security.md#approvals-and-auto-review)。

### 通过你的桌面路由流量

在 **Your computer** 下，打开 **Route traffic through this computer**，把你的 Grok Bot 计算机的 Web 流量经由当前桌面发送。该路由适用于新连接。目的地看到的是你桌面的 IP 地址。解析到私有地址或 VPN 地址的站点会被阻止，除非已为你的组织允许，并且该设置会显示本次会话中阻止了多少个。

该设置适用于一台桌面。如果 Enterprise 管理员关闭 **Allow Local Egress Routing**，开关会关闭并锁定，并显示 `Disabled by your admin. You can't turn this on.` 任何活动路由会在五分钟内停止。你的选择会被保留，如果管理员重新允许本地出口，它会再次生效。

私有网络选项及其取舍见[连接到私有网络](https://cursor.com/docs/grok-bot/private-networks.md)。

## 已连接的应用

应用不在设置里。在侧边栏打开 **Connect apps** 以查找应用和打包的 Skill。选择标题旁边的已连接数量，以管理已安装的应用和私有 Skill。已安装的应用可能仍需要浏览器身份验证，并且可以启用或禁用单个应用工具。在 Teams 方案和 Enterprise 方案上，管理员可能会限制成员可以使用哪些应用。见[连接应用](https://cursor.com/help/grok-bot/connect-apps.md)。

## 用量与计费

**Usage & Billing** 为符合条件的账户显示每周包含的用量和按需用量，账户菜单也可以一眼看到每周用量。关于方案、每周用量和按需花费如何运作，见[方案与计费](https://cursor.com/help/grok-bot/plans.md)。

## Team Setup Scripts

**Team Setup Scripts** 仅限 Enterprise。管理员在仪表盘的 Grok Bot 页面管理这些脚本，它们会在每个成员的计算机上运行。成员在设置中看不到它们。见[面向 Teams 与 Enterprise 的 Grok Bot](https://cursor.com/docs/grok-bot/teams.md#admin-controls)。

## Updates

Grok Bot 应用和 Grok Bot 的计算机是分开更新的：

- **Updates** 窗格中的 **Check for Updates** 和 **Restart to Update** 会更新桌面应用。
- 同一窗格中的 **Automatic Updates** 默认开启。开启后，已下载的更新会在你离开时安装并重启应用：屏幕已锁定或屏保已开启，计算机已空闲至少 10 分钟，并且 Grok Bot 不忙。关闭它，则只有在你选择 **Restart to Update** 时才安装。Linux 的 `.deb` 和 `.rpm` 安装不会显示它。
- **Settings** > **Computer** 中 **Bot's computer** 下的 **Update Bot's Computer** 会把云端计算机移到最新版本。软件更新只会刷新 Grok Bot 的软件，并保留已安装的应用。计算机更新会重建计算机，保留文件和登录，但移除已安装的应用和软件包。**Update Bot's Computer** 下方的文字会说明等待的是哪一种。
- 同一区域中的 **Reset Bot's Computer** 是万不得已的手段，它根据你上次保存的数据重建计算机，与 Recover 相同；如果在保存之前无法访问计算机，非常近期的改动不会回来。

破坏性最小的恢复顺序见[使用 Grok Bot](https://cursor.com/docs/grok-bot/work.md#update-recover-or-reset)。

## 每个 Bot 的设置

点击聊天顶部的 Bot 名称（**View conversation details**）以打开详情。点击名称或标签进行编辑，点击 Bot 的图片以更换头像。这些只属于该 Bot。个人 Bot 没有描述；要改变 Bot 做什么，在聊天中告诉它。见[更改 Bot 的名称、图片和职责](https://cursor.com/help/grok-bot/edit-bot.md)。

## 需要关注的状态与通知

侧边栏会区分需要关注的 Bot（问题、批准或交接）、未读活动（新结果）和工作状态。打开对话会把当前活动标为已读，Bot 菜单也可以手动把对话标为已读或未读。

为一个 Bot 打开 **Notifications**，即可在该 Bot 完成或正在等你时收到系统或移动通知。在手机上，开关位于 Bot 的资料中。在桌面上，让 Bot 打开或关闭它的通知。群聊没有同样的按 Bot 通知开关。当 Grok Bot 处于焦点时，通知会被抑制；侧边栏和 Dock 徽章仍会显示未读活动。在 iPhone 上，设备权限和 Bot 的通知设置都必须允许该通知，并且推送正在逐步推出。

## 应用内错误

错误出现在输入框上方的 **Notifications** 下。有些通知包含供支持使用的 **Copy request ID**；复制完整 ID 并分享给[支持](https://cursor.com/help/grok-bot/get-help.md)。清除通知只会移除这条通知，不会移除底层操作或历史。

## 相关页面

- [使用 Grok Bot](https://cursor.com/docs/grok-bot/work.md)
- [方案与计费](https://cursor.com/help/grok-bot/plans.md)
- [面向 Teams 与 Enterprise 的 Grok Bot](https://cursor.com/docs/grok-bot/teams.md)
- [Grok Bot 安全](https://cursor.com/docs/grok-bot/security.md)
- [删除你的 Grok Bot 账户](https://cursor.com/help/grok-bot/delete-account.md)


---

## 站点地图

[所有文档页面概览](/llms.txt)
