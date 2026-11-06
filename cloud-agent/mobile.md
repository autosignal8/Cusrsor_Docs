> 原文：https://cursor.com/docs/cloud-agent/mobile

# Cursor for iOS

Cursor for iOS 是一款原生移动应用，用于控制[在云端](https://cursor.com/docs/cloud-agent.md)和你的本地计算机上运行的 Agent。从 iPhone 或 iPad 启动 Agent、实时跟进它们的工作，并审查和合并它们的 pull request。它与 [cursor.com/agents](https://cursor.com/agents) 和桌面端的 Agents Window 使用同一套后端，因此你在移动端启动的 Agent 会出现在你工作的每一个地方。

[https://apps.apple.com/app/cursor/id6767085653](https://apps.apple.com/app/cursor/id6767085653)

该应用在 iOS 26.0 或更高版本的 iPhone，以及 iPadOS 26.0 或更高版本的 iPad 上运行，界面语言为英语。
Android 已在计划中。

Cursor for iOS 可用于 Premium、Super、Ultra、Teams 和 Enterprise，以及旧版 Start、Pro、Pro+ 和 Ultra 方案。进一步了解[包含的内容](https://cursor.com/help/account-and-billing/pricing.md)。

## 开始使用

### 下载应用

在 iPhone 或 iPad 上[从 App Store 获取 Cursor](https://apps.apple.com/app/cursor/id6767085653)。

### 打开 Cursor 并登录

使用你的 Cursor 账号登录。如果你的组织要求 SSO，你将通过 SSO 登录。

### 选择仓库

选择你希望 Cursor 工作的仓库和分支。

### 开始指挥 Agent

发送一项任务，并在 Agent 工作时进行监督。

设置完成后，就让 Agent 开始工作。例如：

- 离开工位时修复一个缺陷，或响应一次事件。
- 在手机上审查并合并队友的 pull request。
- 启动一次重构或一个新功能，然后在 Agent 完成时回来查看。
- 让 Agent 调查失败的 CI 检查。

## 你可以做什么

你在手机上就能获得完整的 Cloud Agent 工作流，而不是一个精简版聊天框。你在网页上使用的同一批机器、模型和审查工具都会跟着你。

- **在云端机器上运行 Agent。** 为每次运行选择一个 worker：一台云端机器、一个 [Team Pool](https://cursor.com/docs/cloud-agent/self-hosted/pool.md)，或 [My Machines](https://cursor.com/docs/cloud-agent/self-hosted/my-machines.md) 中的一台。Agent 在完整的开发环境中工作，因此它们会安装依赖、运行测试并验证自己的改动。
- **使用任意模型。** 选择 Cloud Agents 可用的任意模型。每次运行都使用该模型所支持的最大上下文窗口。
- **让 Agent 长时间运行。** 开始一项任务，锁上手机，稍后再回来查看。无论你的设备是否保持连接，Agent 都会继续在云端工作。
- **实时跟进工作。** 在 Agent 编写代码时观看聊天流，向正在运行的 Agent 发送后续消息，并点按 Subagent 卡片以阅读其子对话记录。
- **审查并合并 pull request。** 阅读完整的 diff、提交、部署、批准、评论和检查。添加或更改审查者，然后让 Agent 解决审查评论或修复失败的检查。你也可以用 squash 合并、标记为就绪、更新分支、切换自动合并、发布或关闭。
- **使用你的命令和自动化。** 你的 slash commands、Skills 和自动化在移动端的工作方式与在本地、CLI 和网页上相同。
- **进入 Design Mode。** 附上照片、相机拍摄的图像或文件，然后在图像或前端组件上指向、点击和绘制，为 Agent 提供视觉指引。
- **使用你的语音。** 向 Agent 口述指令，通过实时转录进行免提编辑。
- **连接 MCP 工具。** 在启动时选择一次运行应使用的 [MCP](https://cursor.com/docs/mcp.md) 服务器，包括 Slack MCP，使 Agent 能够使用与网页上相同的外部工具。
- **保持了解进展。** 当 Agent 完成一个回合时收到推送通知，并使用锁屏和 Dynamic Island 上的 Live Activities 同时跟踪最多 8 个 Agent。

该应用采用缓存优先。它从本地数据读取，因此收件箱和对话会快速打开，然后在连接恢复后同步。

## 使用 iPad 布局

iPad 应用利用更大的屏幕，让 Agent 的更多工作保持可见：

- 在侧边栏中保留聊天，同时跟进多个 Agent。
- 在聊天旁边打开审查。
- 以全宽阅读文件 diff。
- 附上一张截图并点按某个位置以留下视觉指引，或用 Apple Pencil 在图像上绘制。

## 哪些内容在网页上

该应用专注于指挥和审查 Agent。它不是 IDE，也不是管理控制台。有几项内容仍在 [cursor.com/agents](https://cursor.com/agents) 或 [Cursor Dashboard](https://cursor.com/dashboard) 上：

- **编辑器、终端和文件浏览器。** 在移动端，你在 diff 视图中看到更改的文件，而不是完整的工作区。
- **密钥和环境。** 在网页上配置 [Cloud Agent 环境](https://cursor.com/docs/cloud-agent/setup.md)和密钥。移动端上的 Agent 使用已经设置好的内容。
- **MCP 服务器管理。** 在移动端为每次运行选择服务器；在网页上添加并管理它们。
- **源代码管理设置。** 从仪表盘连接或重新连接 [GitHub](https://cursor.com/docs/integrations/github.md) 和 [GitLab](https://cursor.com/docs/integrations/gitlab.md)。
- **自动化、Rules 和 Skills 配置。** 在网页上管理这些内容。Agent 会从仓库获取项目 Skills，以及你已同步的任何个人 Skills。
- **管理、计费和用量。** 仅限网页。

## 在设备之间移动

Agent 会跟随你跨越各个界面：

- **从桌面或网页到移动端。** 你在任何地方启动的 Agent 都会自动出现在移动端收件箱中。从本地 IDE 会话，先用 Move to Cloud 把工作推送到 Cloud Agent，或让它留在你的计算机上，并用 [Remote Control](https://cursor.com/docs/cloud-agent/mobile.md#remote-control) 从手机指挥它。
- **从移动端到桌面。** 你在移动端启动的任何 Agent 都会出现在桌面端的 Cloud Agents 面板和 [cursor.com/agents](https://cursor.com/agents) 中。打开它并继续。
- **从手机指挥你的计算机。** 用 [Remote Control](https://cursor.com/docs/cloud-agent/mobile.md#remote-control) 把正在你计算机上运行的会话交给云端，然后继续从手机指挥它。Agent 循环在云端运行，而终端命令、文件编辑和测试在你的计算机上运行。

在移动端启动的 Agent 会带有 `source: iosApp` 标记，以便你分辨它们来自哪里。

## Remote Control

Remote Control 让你把正在计算机上运行的 Agent 拿到手机上继续指挥。Agent 循环转移到云端，而其工具继续在你的机器上运行，因此它读取你的文件、运行你的测试，并使用与桌面上相同的本地设置。

Remote Control 及其设置仅在 [Agents Window](https://cursor.com/docs/agent/agents-window.md) 中可用。

### 开始之前

- **使用 Cursor 3.9.8 或更高版本。** Remote Control 要求你的计算机上的 Cursor 客户端为 3.9.8 或更高版本。更旧的客户端不会在 **Settings > Agents** 下显示 Remote Control 设置，也不会显示 `/remote-control` 命令。
- **使用受支持的账号。** Remote Control 可用于 Premium、Super、Ultra、Teams 和 Enterprise，以及旧版 Start、Pro、Pro+ 和 Ultra 方案，且用户需要拥有 Cloud Agents 访问权限。参见[包含的内容](https://cursor.com/help/account-and-billing/pricing.md)。
- **在 Cursor 中启用 Remote Control。** 在 Agents Window 中，交接会话之前，在 **Settings > Agents** 下开启它。
- **为你的团队启用它。** 在 Teams 和 Enterprise 方案上，管理员必须先从 [Cursor Dashboard → Cloud Agents → Self-Hosted](https://cursor.com/dashboard/cloud-agents#self-hosted) 启用 Remote Control，成员才能使用它。
- **允许云数据存储。** 当你的隐私设置禁用云数据存储时，Remote Control 不可用。
- **使用本地或 Remote SSH 工作区。** 两种工作区类型都受支持，并且项目不需要 Git remote。
- **让你的计算机保持可用。** 你的计算机必须保持唤醒并在线，因为工具调用在它上面运行。你可以在 **Settings > Agents** 下开启 **Keep this computer awake**，以便在计算机接通电源时防止休眠。

### 交接会话

### 运行 /remote-control

在你计算机上的 Agent 输入框中运行 `/remote-control`，然后发送下一条消息。Cursor 把会话交给你机器上的 worker，并使你可以从其他设备控制它。

### 在手机上打开 Agent

该会话会与你的其他桌面 Agent 一起出现在 Cursor 应用的收件箱中。点按进入，从你离开的地方继续。

### 继续指挥它

从手机发送后续消息、实时观看工作过程，并审查结果。你的计算机继续执行这项工作。

### 你的代码如何留在你的机器上

Remote Control 会自动管理你计算机上的 worker，不需要单独设置。Agent 循环在 Cursor 的云端运行，每一次工具调用（终端命令、文件编辑、测试和 git）都在你的计算机上运行。

- 你的仓库、密钥、凭证和构建缓存留在你的机器上。只有工具结果和模型所需的上下文会传到 Cursor。
- 只有你可以控制你的 Agent。Cursor 把每个会话绑定到你的账号和你的机器，并拒绝针对你不拥有的 Agent 的请求。
- 当你为该 Agent 启用 Remote Control 时，Cursor 会发送继续会话所需的对话状态和模型上下文。

当工具调用在你的计算机上运行时适用的信任边界，见[安全与网络](https://cursor.com/docs/cloud-agent/security-network.md)。

### 当前限制

- **你的计算机必须保持可用。** 当你的计算机处于休眠或离线时，工具调用无法运行。

### 团队控制

团队管理员和 Enterprise 管理员从 [Cursor Dashboard → Cloud Agents → Self-Hosted](https://cursor.com/dashboard/cloud-agents#self-hosted) 控制 Remote Control 访问。当管理员启用 Remote Control 时，Cursor 也会启用该团队的自托管 worker 访问。当它关闭时，成员无法启用 Remote Control，也无法把本地会话交给其他设备。

## 可用性

你可以在任何包含 Cloud Agents 的方案上使用该应用：Premium、Super、Ultra、Teams 和 Enterprise，以及旧版 Start、Pro、Pro+ 和 Ultra 方案。如果你的组织要求 SSO，你将先通过 SSO 登录。

有关 HIPAA BAA 的详情，包括符合条件的服务和实施要求，见 [HIPAA 业务伙伴协议](https://cursor.com/docs/enterprise/baa.md)。

Cursor for iOS 依赖 [Cloud Agents](https://cursor.com/docs/cloud-agent.md)，它们需要云数据存储才能运行。如果你使用的是 Privacy Mode (Legacy)，请在使用该应用之前切换到 Privacy Mode。在此之前，你将无法在移动端启动 Agent。我们绝不会用你的代码进行训练，并且只为运行 Agent 而保留代码。[进一步了解 Privacy Mode](https://www.cursor.com/privacy-overview)。

当你尝试使用 Cloud Agents 时，Cursor 会提示你选择加入 Privacy Mode。点按 **Update**，然后在对话框中确认 **Switch to Privacy Mode**。你的代码仍然不会用于训练，但之后你不能切换回 Legacy Privacy Mode。Cloud Agents 如何存储和保留数据，见[安全与网络](https://cursor.com/docs/cloud-agent/security-network.md)。

## 相关页面

- [Cloud Agents 概览](https://cursor.com/docs/cloud-agent.md)
- [Cloud Agent 能力](https://cursor.com/docs/cloud-agent/capabilities.md)
- [Cloud Agent 环境设置](https://cursor.com/docs/cloud-agent/setup.md)
- [安全与网络](https://cursor.com/docs/cloud-agent/security-network.md)

---

## 站点地图

[所有文档页面概览](/llms.txt)
