> 原文：https://cursor.com/docs/cloud-agent/self-hosted/computer-use

# Computer Use 与桌面共享

Computer Use 让 [自托管机器](https://cursor.com/docs/cloud-agent/self-hosted.md) worker 上的 Agent 能够点击、键入、截屏，并驱动带有 UI 的应用程序。当 worker 安装了 Chrome 或 Chromium 时，Agent 也可以驱动浏览器。它适用于 macOS 和 Linux worker，对 [My Machines](https://cursor.com/docs/cloud-agent/self-hosted/my-machines.md) 和 [Team Pools](https://cursor.com/docs/cloud-agent/self-hosted/pool.md) 都有效。桌面共享让获得授权的查看者从 Cursor 观看 Linux 上的 Agent 桌面，或取得其控制权。托管 Cloud Agents 上的产品行为，见[能力](https://cursor.com/docs/cloud-agent/capabilities.md)。

Computer Use 和桌面共享还很新。按照本指南操作之前，请安装或更新到最新的 Cursor CLI：运行 `agent update`，或按[安装 CLI](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#install-the-cli)中的步骤重新安装。

这两项功能都是显式选择加入的，服务器绝不会启用它们。Computer Use 在 macOS 和 Linux worker 上运行。桌面共享（`--share-desktop`）仅限 Linux。

## 启用 Computer Use

用 `--computer-use` 启动 worker。Worker 标志放在 `start` 之前：

```bash
agent worker --computer-use start
```

池 worker 使用同一个标志：

```bash
agent worker --pool gpu --computer-use start
```

接下来发生什么取决于操作系统。在 macOS 上，worker 通过一个名为 **Cursor Computer Use** 的辅助应用驱动已登录的桌面会话，你需要授予它两项 macOS 隐私权限。在 Linux 上，worker 使用一个 X11 显示，并且需要先安装桌面包。

在 Enterprise 团队上，管理员可以用[团队设置](https://cursor.com/dashboard/settings)里 **MCP Configuration** 下的 **Computer Use** 开关关闭 Computer Use。该开关默认打开。当它关闭时，或者当该团队的全部 MCP 服务器都被禁用时，你的 worker 上的 Agent 会在没有 Computer Use 的情况下启动，即使 worker 是用 `--computer-use` 运行的。

## macOS

在 macOS 上，Computer Use 通过 **Cursor Computer Use** 运行。这是一个由 CLI 替你管理的小型辅助应用。它在 worker 所运行的桌面会话中点击、键入并捕获屏幕。Worker 上不需要 Cursor 桌面应用。

- **由 CLI 安装。** 在一台 Mac 上第一次运行 `agent worker --computer-use start` 时，如果缺少 Cursor Computer Use，会从 `downloads.cursor.com` 下载它，并安装为 `~/.cursor/cursor-computer-use/Cursor Computer Use.app`。
- **一个应用身份。** 包标识符为 `co.anysphere.cursor-computer-use`，使用 Apple Team ID `DCNK4UB866` 签名。它与 **Cursor Agent Helper**（`co.anysphere.cursor.agent-helper`）是不同的应用，后者供 `--share-desktop` 使用。
- **需要已登录的桌面。** Worker 使用运行它的那个 macOS 用户的登录 GUI 会话。`--display` 和虚拟显示仅限 Linux。
- **需要两项权限。** macOS 要求为 Cursor Computer Use 授予 **Accessibility**（以便移动鼠标和键入）和 **Screen Recording**（以便截屏）。Worker 不会替你检查或请求这些权限。在你授予之前，Agent 的点击和截屏都会失败。

### 安装 Cursor Computer Use

### 以 Computer Use 启动 worker

以将要运行 worker 的用户身份登录这台 Mac，然后启动它：

```bash
agent worker --computer-use --name "build-mac-01" start
```

对于池 worker，加上 `--pool`：

```bash
agent worker --pool --computer-use start
```

首次启动时，如果缺少 Cursor Computer Use，会安装它，然后 macOS 会提示授予权限。

### 把 Accessibility 和 Screen Recording 授予 Cursor Computer Use

打开 **System Settings → Privacy & Security**。在 **Accessibility** 下打开 **Cursor Computer Use**。在 **Screen & System Audio Recording**（较旧的 macOS 版本中称为 **Screen Recording**）下打开 **Cursor Computer Use**。

把权限授予 **Cursor Computer Use**，而不是 Terminal、你的 shell 或 Cursor 应用。你已经授予那些应用的权限不会带过来。如果 macOS 在你授予 Screen Recording 之后要求你退出并重新打开该应用，请停止并重启 worker。

### 验证安装和权限

```bash
agent worker debug
```

该报告会确认 Cursor Computer Use 已安装。它不会报告权限状态，因此也要测试这一点：启动 worker，并从 [cursor.com/agents](https://cursor.com/agents) 发送一个任务，让 Agent 打开浏览器、访问一个页面并截屏。当两项权限都已就位时，截屏会出现在聊天中。

macOS 15（Sequoia）及更高版本会定期要求已登录用户重新确认那些直接捕获屏幕的应用的 Screen Recording。如果长时间运行的 worker 上截屏不再工作，请在 GUI 会话中寻找一条待处理的 “Allow For One Month” 提示并批准它，然后再次运行 `agent worker debug`。

### 用正确的方式授予权限

两项权限都由已登录用户授予某个特定应用。几条规则能让这套设置在重启、更新和集群之间保持有效：

- **授予 Cursor Computer Use。** macOS 把每项权限绑定到该应用的包标识符（`co.anysphere.cursor-computer-use`）和代码签名要求。把 Accessibility 或 Screen Recording 授予 Terminal、iTerm、`agent`、Cursor 或 Cursor Agent Helper，对 Computer Use 没有任何作用。
- **以 worker 的用户身份授予。** 在授予权限或制作快照之前，以运行 worker 的那个 macOS 账号登录。Screen Recording 的批准属于该用户账号。
- **保持桌面会话存活。** Worker 需要一个已登录的 GUI 会话。对于会在无人值守时重启的机器，为 worker 的用户打开自动登录，并在 **System Settings → Lock Screen** 和 **Energy** 中让显示器保持唤醒。
- **让 CLI 管理该应用。** 只通过 `agent worker --computer-use start` 安装和更新 Cursor Computer Use。不要复制、重新签名或重新构建该应用；签名身份一旦改变，macOS 会把它当作一个新应用，你的授权就不再适用。
- **变更之后重新测试。** 在 macOS 升级之后，或在一次替换了 Cursor Computer Use 的 CLI 更新之后，再次运行一个截屏任务。

不要用脚本绕过这些提示。编辑 macOS 隐私数据库，或禁用 System Integrity Protection，会破坏你的 Mac 所依赖的保护，并且不受支持。请改用 System Settings 或 MDM 配置文件。

### 使用你部署的应用包

如果 MDM 或镜像流程已经把 Cursor Computer Use 放到了机器上，让 worker 指向那个包，而不是让 CLI 下载一个。在 `agent worker` 进程的环境中设置 `CUA_SERVICE_APP`（例如在启动它的 launchd plist 中），值为该 `.app` 包的路径。Worker 会使用那个包，并跳过下载。

授权跟随该应用的代码签名身份和安装位置。如果你把 `CUA_SERVICE_APP` 指向另一个位置的包，或换入一个用不同身份签名的构建，macOS 会把它当作一个新应用，并再次要求 Accessibility 和 Screen Recording。

### 用 MDM 管理权限

如果你的 Mac 已加入 MDM，请为 Cursor Computer Use 制作一份 Privacy Preferences Policy Control（PPPC）配置文件。Cursor 目前不提供 `.mobileconfig`。Apple 限制了配置文件对每项权限能做的事：

| 权限 | 配置文件支持 | 做法 |
| --- | --- | --- |
| Accessibility | 在大多数 macOS 版本上，配置文件可以静默允许它。从 macOS 26.2 起，Apple 弃用了对 Accessibility 的静默允许。 | 在 `Accessibility` 服务下，为 `co.anysphere.cursor-computer-use` 设置 `Allowed`。在 macOS 26.2 及更高版本上，请计划让用户在 System Settings 中批准它。 |
| Screen Recording | 配置文件不能允许它。Apple 要求由人在 System Settings 中批准。 | 在 `ScreenCapture` 服务下，为 `co.anysphere.cursor-computer-use` 设置 `AllowStandardUserToSetSystemService`，这样标准（非管理员）用户无需管理员密码即可批准它。 |

每条 payload 都需要包标识符、Team ID（`DCNK4UB866`）和代码要求。从 CLI 所安装的应用中读取代码要求，以便它与你交付的构建一致：

```bash
codesign -d -r - ~/.cursor/cursor-computer-use/Cursor\ Computer\ Use.app
```

因为 Screen Recording 始终需要人工批准，请按每份镜像批准一次来计划，而不是按每台机器批准一次。见下一节。

### 准备一个集群：授予一次，然后制作镜像

权限会保留在机器上，因此在一台模板 Mac 上授予一次，证明它们有效，然后为它制作快照。从该镜像恢复的每个 worker 都会带着可用的 Computer Use 起来，只要该应用、它的路径和已登录用户保持不变。这个模式是：授予、验证、制作快照。

### 准备模板

以 worker 的用户身份登录。安装 CLI，并登录或设置池 API 密钥。为该用户启用自动登录，这样重启之后桌面会话仍然存在。

### 安装并授予

运行一次 `agent worker --computer-use start`，让 CLI 安装 Cursor Computer Use。把 Accessibility 和 Screen Recording 授予 Cursor Computer Use，或者推送你的 PPPC 配置文件并批准 Screen Recording。停止该 worker。

### 验证

```bash
agent worker debug
```

确认报告显示 Cursor Computer Use 已安装。然后启动 worker，并运行一个会截屏、且会在某个应用中点击的任务。只有当截屏返回、并且点击落在目标上时才继续；单靠 `agent worker debug` 并不能证明权限已经授予。

### 制作快照

从这个状态制作 VM 快照或磁盘镜像。为每个 worker 恢复它，并在 `launchd` 或你的进程管理器下用 `--computer-use` 启动 worker。

在以下情况重新授予并重新制作快照：

- 一次 CLI 更新用签名身份已改变的构建替换了 Cursor Computer Use。在你再次授予之前，截屏和点击都会失败。
- Worker 以另一个 macOS 用户身份运行，而不是授予权限的那个用户。
- 你把模板升级到一个新的 macOS 主版本。再次运行截屏测试，如果失败就再次授予。

## Linux

在 Linux 上，Computer Use 使用一个 X11 显示。Worker 会复用你已经在运行的显示，或创建自己的显示。

### 安装依赖

在每台 Linux worker 上安装桌面包。Cursor 不会替你安装系统包：

```bash
sudo apt-get install -y --no-install-recommends \
  dbus-x11 ffmpeg tigervnc-standalone-server \
  x11-utils x11-xserver-utils xdotool xfce4
```

`xdotool`、`ffmpeg` 和 X11 工具始终是必需的。当 worker 自己创建桌面时需要 `tigervnc-standalone-server` 和 `xfce4`：无头机器上用于 Computer Use 的受管理显示，以及用于桌面共享的隔离桌面。

安装 Chrome 或 Chromium（可选，推荐），以便在浏览器中使用 Computer Use。

对于[池](https://cursor.com/docs/cloud-agent/self-hosted/pool.md)，把这些包烘焙进你的 worker 镜像，这样每台机器起来时都已就绪。

### 选择 X11 显示

Worker 按以下顺序解析 Computer Use 所用的显示：

1. **`--display <display>`。** 要求一个已存在的 X11 显示，例如 `:0`。如果该显示不可达，worker 启动会失败，而不会回退。
2. **继承的 `DISPLAY`。** 没有 `--display` 时，worker 会复用其环境中一个可达的 `DISPLAY`。如果它不可达，worker 会回退到受管理的桌面。
3. **受管理的桌面。** 没有配置任何显示时，worker 会启动自己的 TigerVNC 桌面，并运行一个 Xfce 会话。这是无头机器上的默认方式，需要 `tigervnc-standalone-server` 和 `xfce4` 这两个包。

```bash
# Reuse a desktop you already run
agent worker --computer-use --display :0 start

# Let the worker create a managed desktop (headless machines)
agent worker --computer-use start
```

### 共享 Agent 桌面

桌面共享与 Computer Use 相互独立，并且仅限 Linux。要让获得授权的查看者从 Cursor 观看或控制 Agent 桌面，请加上 `--share-desktop`：

```bash
agent worker --computer-use --share-desktop start
```

`--share-desktop` 接受一个可选模式：`view`（只观看）或 `view_and_control`（查看者可以取得鼠标和键盘控制权，这是默认值）。共享使用的是 worker 创建的、隔离的 Agent 桌面，而不是机器自己的会话，并且需要 `tigervnc-standalone-server`。机器上一个失败时默认拒绝的输入过滤器负责强制控制权，剪贴板传输保持阻止。

像素只通过 worker 已有的出站连接离开机器。不会打开入站端口。

![Cursor 显示某个 worker 的共享桌面，其中有 Take control](/docs-static/images/cloud-agent/private-worker-desktop-share.png)

## 验证设置

运行预检报告：

```bash
agent worker debug
```

在 macOS 上，该报告显示 Cursor Computer Use 是否已安装。它不检查 Accessibility 或 Screen Recording，因此用一个真实任务来验证：让 Agent 截屏并点击某个东西。在 Linux 上，该报告会检查每一个必需的二进制文件、所配置的显示是否可达，以及 worker 能否回退到受管理的桌面。

### 为什么我的 Mac worker 上截屏或点击会失败？

运行 `agent worker debug`，确认 **Cursor Computer Use** 已安装。如果安装失败，检查该 worker 能否到达 `downloads.cursor.com`。如果已经安装，则是权限缺失：在 System Settings → Privacy & Security 中把 Accessibility 和 Screen Recording 授予 **Cursor Computer Use**，重启 worker，然后再次运行该任务。Worker 不会替你检查这些权限。

### 我把权限授予了 Terminal 或 Cursor Agent Helper。为什么不起作用？

macOS 把 Accessibility 和 Screen Recording 绑定到某个特定应用。Worker 通过 Cursor Computer Use（`co.anysphere.cursor-computer-use`）驱动桌面，因此需要权限的是这个应用，而不是启动 `agent` 的终端、Cursor 应用，或只处理 `--share-desktop` 的 Cursor Agent Helper（`co.anysphere.cursor.agent-helper`）。

### 我能否从 MDM 授予 Screen Recording，而无需用户点击任何东西？

不能。Apple 不允许配置文件允许 Screen Recording。配置文件可以设置 `AllowStandardUserToSetSystemService`，以便标准用户无需管理员即可批准它，你也可以在一台模板 Mac 上批准一次，并[为它制作快照](https://cursor.com/docs/cloud-agent/self-hosted/computer-use.md#prepare-a-fleet-grant-once-then-image)。

### Worker 在 macOS 上需要 Cursor 桌面应用吗？

不需要。CLI 会自行安装 Cursor Computer Use。Worker 通常运行在没有安装 Cursor 应用的 Mac 上。

## 后续步骤

- [My Machines](https://cursor.com/docs/cloud-agent/self-hosted/my-machines.md)：连接一台个人 Mac 或 Linux 机器。
- [Team Pools](https://cursor.com/docs/cloud-agent/self-hosted/pool.md)：共享 worker，以及完整的 `agent worker` 标志参考。
- [API 参考](https://cursor.com/docs/cloud-agent/api/endpoints.md#workers-and-pools)：worker、池、待处理请求队列和 worker token 的端点。
- [Cloud Agent 能力](https://cursor.com/docs/cloud-agent/capabilities.md)：托管 Cloud Agents 上的桌面和浏览器控制。

---

## 站点地图

[所有文档页面概览](/llms.txt)
