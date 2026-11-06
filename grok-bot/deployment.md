> 原文：https://cursor.com/docs/grok-bot/deployment

# 将 Grok Bot 部署到你的组织

IT 团队用它已经用于其他软件的工具，在成员设备上安装 Grok Bot 桌面应用：下载带版本的安装程序，推送它，并决定设备何时移到新版本。打开 Grok Bot 和限定访问范围的仪表盘控制位于[面向 Teams 与 Enterprise 的 Grok Bot](https://cursor.com/docs/grok-bot/teams.md)，部署 Cursor 编辑器见[部署模式](https://cursor.com/docs/enterprise/deployment-patterns.md)。

## 你部署什么

| 组件 | 运行位置 | 更新者 |
| --------------- | ------------------------------------------------------------------------------- | --------------------------------------------------------------------- |
| 桌面应用 | macOS（Apple silicon 和 Intel）、Windows（x64 和 Arm64）、Linux（x64 和 Arm64） | 应用本身，或 Linux `.deb` 和 `.rpm` 安装所用的包管理器 |
| iPhone 应用 | iOS 18 或更高版本 | App Store |
| Android 应用 | Android 9 或更高版本 | Google Play |
| 托管计算机 | Cursor 的云端，每位成员一台 | Cursor |

这些应用是用于聊天、审查和批准的客户端。Bot 在成员的托管计算机上工作。你的设备管理工具能到达成员设备上的应用，到此为止：托管计算机在 Cursor 的云端运行 Linux，并且不会加入 MDM。要在托管计算机上放入你自己的工具，Enterprise 团队使用 [Team Setup Scripts](https://cursor.com/docs/grok-bot/private-networks.md#install-a-networking-client-with-team-setup)。

## 获取安装程序

- **Cursor 仪表盘。** [Grok Bot 页面](https://cursor.com/dashboard/bot)上的 **Download Grok Bot** 设置行会安装应用，或复制一个你可以分享给成员的链接。
- **下载页面。** [cursor.com/download/bot](https://cursor.com/download/bot) 列出每个平台和格式的当前安装程序。
- **发布源。** 对于脚本化打包，从下面的源读取当前版本，并下载它指名的文件。

| 平台 | 架构 | 格式 |
| -------- | -------------------- | ------------------------ |
| macOS | Apple silicon、Intel | `.dmg` |
| Windows | x64、Arm64 | Setup `.exe` |
| Linux | x64、Arm64 | `.deb`、`.rpm`、AppImage |

### 从源读取当前版本

每个平台在 `https://api2.cursor.sh/updates/api/download/stable/{platform}/sand` 有一个 JSON 源：

| `{platform}` | 安装程序 |
| ------------------ | -------------------- |
| `darwin-arm64` | macOS，Apple silicon |
| `darwin-x64` | macOS，Intel |
| `win32-x64-user` | Windows x64 |
| `win32-arm64-user` | Windows Arm64 |
| `linux-x64` | Linux x64 |
| `linux-arm64` | Linux Arm64 |

```bash
curl -s https://api2.cursor.sh/updates/api/download/stable/darwin-arm64/sand
```

你需要的字段：

| 字段 | 值 |
| ------------------ | -------------------------------------------------------------------------- |
| `version` | 当前版本 |
| `commitSha` | 该版本所来自的构建。Linux 下载 URL 包含它。 |
| `downloadUrl` | 安装程序：macOS 上为 `.dmg`，Windows 上为 Setup `.exe`，Linux 上为 AppImage |
| `debUrl`、`rpmUrl` | 仅 Linux。`.deb` 和 `.rpm` 软件包。 |

响应还可能带有其他字段。安装桌面应用不需要它们。

### 构建带版本的 URL

macOS 和 Windows 安装程序为每个版本保留一个带版本的 URL。把 `{version}` 替换为你想要的版本：

| 安装程序 | URL |
| -------------------- | ------------------------------------------------------------------------------------------------ |
| macOS，Apple silicon | `https://downloads.cursor.com/grokbot/stable/darwin-arm64/{version}/Grok_Bot_{version}.dmg` |
| macOS，Intel | `https://downloads.cursor.com/grokbot/stable/darwin-x64/{version}/Grok_Bot_{version}_x64.dmg` |
| Windows x64 | `https://downloads.cursor.com/grokbot/stable/win32-x64/{version}/Grok_Bot_{version}_Setup.exe` |
| Windows Arm64 | `https://downloads.cursor.com/grokbot/stable/win32-arm64/{version}/Grok_Bot_{version}_Setup.exe` |

Linux 软件包位于该版本的 `commitSha` 之下，在 `https://downloads.cursor.com/grokbot/stable/{commitSha}/linux/x64/` 或 `.../linux/arm64/`。从 Linux 源中的 `downloadUrl`、`debUrl` 和 `rpmUrl` 复制确切的文件 URL。

## 用设备管理工具推送桌面应用

- **macOS。** 用你现在运行的 Mac 管理工具部署 `.dmg`，例如 Jamf Pro、Kandji 或 Microsoft Intune。
- **Windows。** 用 Intune、Configuration Manager 或其他软件分发工具部署 Setup `.exe`。用 `Grok_Bot_{version}_Setup.exe /S` 静默安装。安装程序按用户安装，装到成员配置文件下的 `%LOCALAPPDATA%\Programs`，因此在用户上下文中部署，而不是作为 SYSTEM。在推送之前，先在一台试点设备上测试安装。
- **Linux。** 用你的配置管理工具安装 `.deb` 或 `.rpm`，例如 Ansible、Puppet 或 Chef。受管机群优先使用这些软件包：软件包安装会按你的计划通过包管理器更新。见 [Linux 软件包更新](https://cursor.com/docs/grok-bot/deployment.md#linux-package-updates)。
- **iPhone。** 成员在 iOS 18 或更高版本上从 [App Store](https://apps.apple.com/app/grok-bot/id6794501026) 安装 Grok Bot。
- **Android。** 成员在 Android 9 或更高版本上从 [Google Play](https://play.google.com/store/apps/details?id=ai.x.grok.bot) 安装 Grok Bot。要推送到受管设备，从 Intune 或其他 Android 设备管理工具在受管 Google Play 中批准它。

成员通过你平时的 SSO 流程用 Cursor 账户登录。Grok Bot 没有单独的登录。要在 Okta 或 Entra ID 中分配 Cursor 应用，见[分配 Cursor 应用](https://cursor.com/docs/grok-bot/identity.md#assign-the-cursor-app)。

## 选择版本

- **固定你测试过的版本。** 下载该版本的安装程序，并把它保存在你自己的软件仓库或 MDM 软件包库中。从你的副本部署，这样每台新设备都得到同一构建。Cursor 不公布旧安装程序可供下载多久，固定的构建只在 Cursor 停止支持它之前有效。见[桌面更新如何工作](https://cursor.com/docs/grok-bot/deployment.md#how-desktop-updates-work)。
- **保存完整的 Linux URL。** Linux 软件包 URL 包含该版本的 `commitSha`，因此把 URL 和版本一起记录下来。
- **检查设备运行的是什么。** 在应用中打开账户菜单，选择 **Support**，然后选择 **About**。对话框显示 **Version**，**Copy version info** 会复制版本、发布轨道和操作系统。

### 桌面更新如何工作

| 安装 | 更新如何到达 |
| -------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| macOS `.dmg`、Windows Setup `.exe`、Linux AppImage | 应用会自动检查更新，并向成员提供每个新版本。[设置](https://cursor.com/docs/grok-bot/settings.md#updates)的 **Updates** 分区中的 **Restart to Update** 会安装它。开启 **Automatic Updates**（默认）后，应用还会在成员离开时安装已下载的更新。 |
| Linux `.deb` 或 `.rpm` | 应用不会安装更新。新版本通过你的包管理器到达。 |

对于 macOS、Windows 和 AppImage 安装，没有任何设备策略、安装程序选项或仪表盘设置可以关闭桌面应用更新。成员可以关闭 **Automatic Updates**，使更新等待 **Restart to Update**，但应用仍会检查并提供新版本。固定版本决定你安装哪个版本；之后应用仍会提供更新的版本。

旧构建会停止工作。当 Cursor 停止支持某个构建时，应用会用 **Update required** 盖住整个窗口，直到成员更新。当构建低于 Cursor 的最低版本，或超过最长使用期限且有更新的版本可用时，该构建会失去支持。这适用于每一次安装，包括 Linux `.deb` 和 `.rpm` 软件包。Cursor 远程设置这两个限制，并且不公布它们，因此请规划一个定期节奏，把设备移到当前版本。

### Linux 软件包更新

安装 `.deb` 会在 `/etc/apt/sources.list.d/grok-bot.sources` 添加 Cursor 的已签名 apt 仓库。安装 `.rpm` 会在 `/etc/yum.repos.d/grok-bot.repo` 添加一个已签名仓库。之后，设备在运行软件包升级时就会取得新的 Grok Bot 版本。

要控制 Linux 设备何时移动：

- **冻结软件包。** 使用包管理器的冻结，例如 `apt-mark hold grok-bot`，准备升级时再解除。
- **关闭仓库。** 在 `grok-bot.sources` 中设置 `Enabled: no`，或在 `grok-bot.repo` 中设置 `enabled=0`。软件包在每次安装和升级时都会重写这些文件，并保留你的选择。然后用你测试过的软件包文件自行发布新版本。

无论哪种方式，都要在已安装版本失去支持之前升级。被冻结的软件包仍会看到 **Update required** 屏幕。

## 更新托管计算机

Cursor 维护托管计算机的镜像，因此没有可供你固定或提供的镜像。计算机以下列三种方式之一移到更新的镜像：

| 触发 | 会发生什么 | 谁启动 |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------ |
| 新镜像 | Cursor 在后台把空闲计算机重建到新镜像上，逐步进行，没有固定计划。在此之前，计算机停留在当前镜像。成员文件会保留，Team Setup 会再次运行。 | Cursor，在后台 |
| **Update Bot's Computer** | 在最新镜像上重建该成员的计算机。把它安排到以后的时间还不是每个账户都能用。 | 成员，从桌面应用的 **Settings** > **Computer** > **Bot's computer** |
| **Recreate VMs** | 在最新镜像和 Team Setup Scripts 上重建所选成员的计算机 | Enterprise 上的管理员，从 [Manage Member Computers and Data](https://cursor.com/docs/grok-bot/computers.md) |

正在运行的计算机大约每天一次采用 Team Setup 清单的更改。要更快应用更改，请重建计算机。在 Enterprise 上，[审计日志](https://cursor.com/docs/enterprise/compliance-and-monitoring.md#audit-logs)把计算机更新、重置、重建和终止记录为 `grok_bot_vm` 事件，把跨许多成员的操作记录为 `grok_bot_vm_bulk`。

## 打通网络路径

设备从 `cursor.com` 和 `downloads.cursor.com` 下载安装程序和桌面更新，应用连接到 Cursor 的 API，以及成员托管计算机在[允许这些域名模式](https://cursor.com/docs/grok-bot/proxies.md#allow-these-domain-patterns)中的域名。全部允许它们，包括嵌套的 `*.*.cursorvm.com` 模式，并在推送应用之前把它们排除在 TLS 检查之外。

## 推出清单

### 离开 Privacy Mode (Legacy)

Privacy Mode (Legacy) 会完全阻止 Grok Bot。见[推出之前](https://cursor.com/docs/grok-bot/teams.md#before-you-roll-out)。

### 放行你的网络

在网关上允许 Cursor 的域名，并把它们排除在 TLS 检查之外。见[配置检查 TLS 的代理](https://cursor.com/docs/grok-bot/proxies.md)。

### 在身份提供商中分配 Cursor 应用

Grok Bot 登录使用你的 Cursor SSO。见[分配 Cursor 应用](https://cursor.com/docs/grok-bot/identity.md#assign-the-cursor-app)。

### 打开 Grok Bot 并选择谁获得它

在 Enterprise 上，管理员从仪表盘打开 Grok Bot，并用 **Manage Group Access** 限定范围。在 Teams 上，每个成员已经有访问权限。见[为团队启用 Grok Bot](https://cursor.com/docs/grok-bot/teams.md#enabling-grok-bot-for-your-team)。

### 应用推荐配置

在成员开始之前，设置[推荐配置](https://cursor.com/docs/grok-bot/teams.md#recommended-configuration)中的管理员控制。

### 试点安装程序

在每个平台的几台设备上安装你选择的版本，登录，并确认应用能连接到托管计算机。

### 推送到机群

把测试过的安装程序部署到所有者有访问权限的每台设备。

### 规划更新

选择一个把设备移到新版本的节奏。对于 Linux，决定设备是从 Cursor 的仓库升级，还是从你发布的软件包升级。

## 常见问题

### 我可以关闭桌面应用的自动更新吗？

在 macOS、Windows 和 AppImage 安装上，不可以。应用会检查更新并向成员提供。每个成员可以关闭 **Automatic Updates**，使更新只在他们选择 **Restart to Update** 时安装，但没有管理员控制。Linux `.deb` 和 `.rpm` 安装不会自行更新，因此你用包管理器控制它们。见[桌面更新如何工作](https://cursor.com/docs/grok-bot/deployment.md#how-desktop-updates-work)。

### 有 MSI 或 PKG 安装程序吗？

已发布的安装程序是 macOS 的 `.dmg`、Windows 的 Setup `.exe`，以及 Linux 的 `.deb`、`.rpm` 或 AppImage。如果管理工具要求特定格式，把 `.dmg` 或 `.exe` 包装成它的软件包格式。

### 我的设备管理工具能到达托管计算机吗？

不能。托管计算机在 Cursor 的云端运行 Linux，并且不会加入 MDM。Enterprise 团队用 [Team Setup Scripts](https://cursor.com/docs/grok-bot/private-networks.md) 在上面安装自己的工具。如果身份提供商要求受管设备，见[允许从计算机登录 IdP 应用](https://cursor.com/docs/grok-bot/identity.md#allow-sign-in-to-idp-apps-from-the-computer)。

### 我可以固定托管计算机的镜像吗？

不能。Cursor 维护镜像，并在后台把计算机移到新镜像。Enterprise 管理员可以在自己选择的时间从 [Manage Member Computers and Data](https://cursor.com/docs/grok-bot/computers.md) 重建计算机。

### 成员可以不经 IT 安装 Grok Bot 吗？

可以，除非你的设备策略阻止。成员可以从管理员分享的仪表盘链接，或从 [cursor.com/download/bot](https://cursor.com/download/bot) 下载应用。访问权限仍然取决于你的方案，在 Enterprise 上还取决于 **Manage Group Access**。

## 相关页面

- [面向 Teams 与 Enterprise 的 Grok Bot](https://cursor.com/docs/grok-bot/teams.md)
- [配置身份与访问](https://cursor.com/docs/grok-bot/identity.md)
- [配置检查 TLS 的代理](https://cursor.com/docs/grok-bot/proxies.md)
- [管理 Grok Bot 计算机](https://cursor.com/docs/grok-bot/computers.md)
- [设置与通知](https://cursor.com/docs/grok-bot/settings.md#updates)
- [部署模式](https://cursor.com/docs/enterprise/deployment-patterns.md)

### 规划你的 Grok Bot 推出

联系我们的团队，了解 Enterprise 启用、部署规划，以及安全审查支持。


---

## 站点地图

[所有文档页面概览](/llms.txt)
