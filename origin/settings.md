> 原文：https://cursor.com/docs/origin/settings

# 设置

Origin 目前以早期 beta 发布。你可以创建仓库，用 git 推送和拉取，从 GitHub 镜像，浏览并搜索代码，打开并合并 pull request，并与你的 Cursor 团队共享。

## 仓库设置

在 [cursor.com/codebase](https://cursor.com/codebase) 打开仓库并选择 **Settings** 标签页。这些设置适用于一个仓库。团队范围的 Origin 设置见[代码库设置](https://cursor.com/docs/origin/settings.md#codebase-settings)。

**Settings** 下的标签页取决于谁拥有该仓库。仓库可见性和 **Access** 标签页只出现在团队拥有的仓库上；个人仓库则在 **Collaborators** 下管理共享。

- 团队拥有的仓库：**General**、**Access**、**Rules and Protections** 和 **Apps**
- 个人仓库：**General**、**Collaborators**、**Apps**、**Rules and Protections** 和 **Advanced**

Access、Collaborators 以及 Rules and Protections 的 UI 正在重新设计；早期 beta 期间标签和布局可能会变化。

### General

#### 同步状态

对于[从 GitHub 镜像](https://cursor.com/docs/origin/mirror-github.md)的仓库，**Sync Status** 把 Origin 显示为镜像、把 GitHub 显示为来源，并带有指向源仓库的链接。在 Origin 上创建的仓库不显示同步状态。

#### 与 GitHub 分离

在 **Danger Zone** 下，**Detach from GitHub** 会停止与 GitHub 同步，并使 Origin 副本成为独立的 Origin 托管仓库：Origin 成为事实来源，推送到 Origin 远程不再流向 GitHub。你的 GitHub 仓库不受影响。

### Access

**Access** 只适用于团队拥有的仓库。个人账号拥有的仓库见 [Collaborators](https://cursor.com/docs/origin/settings.md#collaborators)。

用 **Access** 查看谁可以访问此仓库。

可见性在你[创建仓库](https://cursor.com/docs/origin/create-repository.md)时选择。**Internal** 仓库对 Cursor 团队中有权访问该代码库的任何人可见。**Private** 仓库仅对直接被授予访问权限，或通过代码库权限获得访问的成员可见；当仓库切换为 Private 时，进行该更改的人会自动保留管理员访问权限。

团队范围的 Origin 访问权限（谁可以启用 Origin、创建仓库或禁用该功能）在[代码库设置](https://cursor.com/docs/origin/settings.md#permissions)中管理。

![Origin 仓库 Settings 的 Access 标签页](/docs-static/images/origin/settings-permissions.png)

### Collaborators

**Collaborators** 只适用于个人账号拥有的仓库。团队拥有的仓库在 [Access](https://cursor.com/docs/origin/settings.md#access) 下管理访问权限。

在个人账号下创建的仓库始终为 **Private**，默认仅你可见。用 **Collaborators** 邀请特定用户并授予他们对此仓库的访问权限。

个人仓库没有按仓库的可见性控件；如果你需要 Internal 式的团队范围访问，请改为在团队代码库下创建该仓库。

### Rules and Protections

**Rules and Protections** 是你为仓库配置分支规则和合并保护的地方。早期 beta 期间可用控件可能会增加。

### Apps

仓库的 **Apps** 标签页显示为此仓库安装的 app。要安装或管理 app，选择 **Manage Apps**，它会打开代码库级别的 [Apps 设置](https://cursor.com/docs/origin/apps.md)。

## 代码库设置

代码库设置适用于 [cursor.com/codebase](https://cursor.com/codebase) 上你团队的 Origin 仓库，而不是单个仓库。从代码库主页打开代码库设置（与仓库的 **Settings** 标签页分开）。

### Permissions

团队级权限控制谁可以为你的代码库使用 Origin：谁可以访问你已认领的代码库名称下的仓库，以及 Origin 与你的 Cursor 团队成员资格如何关联。

- 团队管理员[认领代码库名称](https://cursor.com/docs/origin.md#enable-origin)并启用 Origin；非管理员可以从同一页面请求访问
- Origin 启用后，管理员可以创建仓库，并用 Permissions 向其他成员授予访问权限，包括创建仓库
- 管理员可以随时从仪表盘为团队禁用 Origin；使用旧版隐私模式的团队无法启用 Origin，因此若要访问，请先切换到 [Privacy Mode](https://cursor.com/help/security-and-privacy/privacy.md#how-do-i-enable-privacy-mode)

早期 beta 期间，Permissions UI 中的具体控件可能会变化。

### Notifications

在代码库设置中使用 **Notifications**，选择代码库仓库中的哪些 pull request 事件会让 `@Cursor` 给你发送 Slack 私信。如果尚未关联你的 [Slack](https://cursor.com/docs/integrations/slack.md) 账号，在 **Slack** 行连接它。然后打开你想要的事件：**Review requested**、**Pull request merged**、**Review submitted**、**Comment** 和 **Mention**。**Apps and bots** 包含来自 app 和 bot 的活动，**Ignore drafts** 会跳过草稿 pull request。每个开关在你更改时立即保存。

要从 Slack 打开这些设置，在私信的菜单中选择 **Manage notifications**。

**Scheduled personal reminders** 按重复计划给你发送 Slack 私信摘要，内容为未完成的审查请求。选择 **Add reminder**，然后选择 **Days**（**Weekdays**、**Every day** 或 **Custom**）、**Times**、**Time zone** 和 **Repositories**。打开 **All open pull requests**，以列出这些仓库中的每个打开的 pull request，而不只是你的审查请求。**Ignore drafts**、**Ignore approved pull requests** 和 **Labels** 会收窄该摘要。要停止提醒，在其行上选择 **Remove**，然后用 **Remove reminder** 确认。之后你可以再次添加。

**Channel subscriptions** 把代码库仓库的 pull request 活动发布到 Slack 频道。请先邀请 `@Cursor` 加入该频道。只有频道成员可以管理其订阅。选择 **Add subscription**，选择频道和仓库，然后挑选要发布的事件：**Pull requests**、**Reviews** 或 **Comments**。添加 **Labels**，仅当 pull request 拥有每一个标签时才发布。对于 private 或 internal 仓库，频道中的任何人，包括之后加入的人，都能看到其活动。

Cursor 不会把 private 或 internal 仓库的活动发布到通过 Slack Connect 与另一个组织共享的频道，包括有待处理邀请的频道。在共享频道中为这样的仓库保存订阅会失败，并提示 "This channel is shared with another organization, so it can't get updates from a private repository."（或 "an internal repository"）。已有订阅会保持保存状态，并在频道不再共享后重新开始发布。


---

## 站点地图

[所有文档页面概览](/llms.txt)
