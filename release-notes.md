> 原文：https://cursor.com/docs/release-notes

# 发行说明

每条 Cursor 产品都放在同一条时间线上，最新的在前，每个产品最多列出 10 个近期发行。按产品筛选，打开某个产品的页面查看完整历史，或通过 [RSS 源](https://cursor.com/docs/release-notes/rss.xml) 跟进。

## 1.16.0

### 语音通话

- **语音通话会显示在锁定屏幕上，并让聊天保持聚焦。** 当 iOS 无法在系统通话界面显示语音通话时，iOS 版 Cursor 会把它作为 Live Activity 显示在锁定屏幕和灵动岛上，你可以在那里静音或挂断。通话期间，聊天会隐藏你的转写语音，以及里面什么都没有的 Worked 分区，因此你只会看到 Agent 的回复、问题、计划和编辑。通话卡片出现在通话开始的对话里，而不是固定在底部。

### 账户与账单

- **在最高方案用完包含用量时可以 Manage Plan。** 如果你用的是最高个人方案，并且用完了包含用量，Included Usage Reached 消息现在会带一个 Manage Plan 按钮。

### iPhone Duo

- **侧边栏和聊天在 iPhone Duo 的折痕处分开。** 在 iOS 27.1 或更高版本上，当 iPhone Duo 在横屏下半开时，Agents 侧边栏会在铰链处结束，侧边栏和聊天各自落在屏幕的一半上，而不是跨过折痕。如果任一侧会太窄，就保持原来的布局。

### 修复

- **iPhone 和 iPad 上的布局与视觉打磨。** Composer 和 iPad 侧边栏的账户行落在底部安全区内，并且彼此对齐，主屏幕指示条上方没有多余空隙。工具栏图标与原生工具栏符号大小一致，拖动手柄只在可调整大小的面板上显示，Access Disabled 屏幕在 iPad 上保持可读宽度。
- **对话与仓库选择器。** 应用在 iOS 27 上加载对话或个人资料屏幕时不再崩溃。正在进行的对话里，单独的 Thinking 步骤结束后会更新为显示它花了多久，仓库选择器不再列出或预选未发布的 Start from scratch 草稿。

## 3.27

### Projects

- **Project 分栏会记住焦点，并在重新加载后恢复。** 在 [Project](https://cursor.com/docs/agent/projects.md) 的分栏里，你聚焦的那一栏在分栏回来或窗口重新加载后仍保持聚焦，编辑器面板会跟着你聚焦的那一栏，包括从 Project 外部拖进来的聊天。你在另一个 Project 的分栏里打开的聊天，在重新加载或 Back/Forward 之后会在那个分栏里重新打开，Project 自己的聊天标签会固定在其标签条的最前面。
- **新 Project 的开始更安静。** 新 [Project](https://cursor.com/docs/agent/projects.md) 还在设置时，对话现在只显示一行 “Setting up project”，直到 Agent 的第一段输出，而不是在环境设置步骤之间来回重排。新 Project 开始时应用面板是关闭的，第一项任务出现后，Project 应用会自己打开。
- **在 List 和 Board 视图里跟踪 Project 的任务。** 从 3.26 起，[Project](https://cursor.com/docs/agent/projects.md) 的笔记是协调者持续更新的任务跟踪器，每项任务都有状态、ID、Agents 和 pull request，以 List 或 Board 显示。Project 标签固定在右侧面板的最前面，其标题可以在 List、Board 和 Library 之间切换。Project 的文件同步时，标签会显示加载状态；同步失败则显示错误，而不是看起来是空的。
- **在 Project 笔记里筛选任务。** 在 [Project](https://cursor.com/docs/agent/projects.md) 的 Notes 标签里，查找旁边的 Filter 按钮可以按 Status、PR、Tags 或 Owner 缩小任务 List 或 Board。生效的筛选显示为可编辑的芯片，你可以在 “is” 和 “is not” 之间切换、移除或清除；在查找栏里输入也会按文本筛选任务。
- **带多个 PR 的任务在 Project 笔记里仍然可读。** 在 [Project](https://cursor.com/docs/agent/projects.md) 的任务 List 里，如果某项任务的 PR 芯片会挤占标题，它会显示一颗 “N PRs” 药丸，并为每个 PR 的状态配一个图标。点击药丸可打开该任务的 pull request 菜单。
- **在项目选择器里更快找到仓库、文件夹和环境。** 项目选择器打开时落在搜索框上，你的仓库、文件夹和云环境在同一个列表里，搜索结果会显示所有者。粘贴克隆 URL 即可从选择器克隆，或用页脚克隆仓库、从零开始，或创建新仓库。
- **不再意外停止 Project 的协调者。** 当 [Project](https://cursor.com/docs/agent/projects.md) 的协调者正在工作时，聊天输入框保持 Send 按钮，而不会换成 Stop，这样你可以继续发消息，而不会误切断一个长时间运行的协调者。
- **Project worker 留在分配给它的任务上。** 当 [Project](https://cursor.com/docs/agent/projects.md) worker 执行协调者下达的计划时，它处理自己的任务，而不会表现得像 Project 的协调者。

### 聊天与 Composer

- **Project 消息在聊天里收成一行。** 当 [Project](https://cursor.com/docs/agent/projects.md) 向 Agent 发消息时，它们现在收成居中的一行，例如 “Messages from Hare, Tortoise”，而不会占满对话。点击某个 Agent 的名字，会在那条消息处打开 Project 的聊天；展开 “Update from” 条目可以阅读更新。
- **提示词附件的修复。** 已发送消息里附上的图片和视频会标上文件名，文件名非常长的图片也能附上，而不会保存失败。在 Windows 或 Linux 上添加 HEIC 或 HEIF 图片时，现在会说明这些图片需要 macOS；较慢的 HEIC 转换会得到更多时间，以及清楚的超时消息。没有本地文件的粘贴文档会附到 Cloud Agent 提示词上；无法附加的视频或文档会显示警告，而不是直接消失。
- **排队消息和操作按钮更可靠。** 会发送消息的操作按钮，例如 git 操作，现在遵循你的排队或引导行为，并保留你已输入的草稿和附加上下文。对刚刚编辑过的排队消息点 Send Now，会从任意 Composer 发送你保存的编辑；如果保存失败，会显示错误，而不会发送过期文本。来自其他 Agent 的已送达消息不再留在队列里。
- **新聊天不再沿用 Project 的环境。** 在选中 [Project](https://cursor.com/docs/agent/projects.md) 聊天时启动新 Agent，新聊天不再在该 Project 的环境里运行。当新 Agent 的默认值设为 Last Used 时，New Chat 使用你在 Project 之外最后用过的聊天的环境，包括在 Project 加载完成之前打开过 Project worker 的情况。
- **聊天气泡随聊天宽度增长。** 你的消息和 Agent 的回复不再被固定在一个最大宽度上，因此把 Chat Width 设置调宽后，气泡会给长文本、表格和代码更多空间。

### 导航与布局

- **标签在切换 Agent 和重启后仍停在你离开的位置。** Agents Window 里的标签在你隐藏面板、切换 Agent 或重启之后，会回到你离开时的位置。文件会在相同的 PDF 页和缩放、图片缩放和平移、CSV 滚动，以及代码滚动和光标处重新打开；Changes 标签保留其范围、分支和选中的 commit；Canvas 保留 Rendered 或 Source 视图；展开的文件夹、打开的查找栏，以及 Agent 打开的标签（如 Agent Changes 和 Desktop）也会回来。
- **侧边栏记住你折叠或展开的分区。** 折叠 Pinned 分区，或 Repositories、Workspaces、Chats 标题，现在会在重新加载和重启后保持，用 More 展开的分区继续显示同样的行数。你展开的仓库或工作区分区在重新加载后仍保持打开，即使其中的 Agent 处于空闲，直到其中一个有新活动。开始新聊天也会展开已折叠的 Repositories 或 Workspaces 标题，让聊天可见。
- **Edit > Undo 和 Redo 现在作用于你正在处理的内容。** 菜单栏里的 Undo 和 Redo 会作用到你打开菜单之前拥有焦点的提示词、编辑器、终端或浏览器。只有在没有可编辑内容拥有焦点时，它们才会撤销窗口操作，例如归档一个 Agent。因此先在提示词里打字、再从侧边栏归档，Undo 不再发给提示词。

### 文件、改动与 pull request

- **pull request 标签上的合并操作更清楚。** 当 pull request 标签上的一次合并会落地一叠多个 pull request 时，合并按钮会显示将要落地的数量。悬停 Remove from Queue 会显示该 pull request 在合并队列中的位置。
- **在 PRs 托盘里看堆叠的 pull request 如何彼此叠加上去。** [Project](https://cursor.com/docs/agent/projects.md) 里的 PRs 托盘把堆叠的 pull request 放在一条轨道上，这样你能看到每个 PR 如何建立在它下面那个之上。分叉的栈从父线分出去，托盘的栈视图与 PR 标签里的栈导航一致。

### 自定义与设置

- **隐藏菜单栏图标旁边的未读计数。** 在 macOS 上，菜单栏菜单新增 Show Count 选项。关掉它，菜单栏图标仍在，旁边不再有未读 Agent 数字。它默认开启，并在重启后保持。

### 浏览器

- **在浏览器里预览 iPhone Duo 页面，并旋转任意设备。** 浏览器的响应式设计模式增加了 iPhone Duo (Closed) 和 iPhone Duo (Open) 设备预设。Rotate 对每个预设都有效，包括 Laptop 和 Desktop，切换设备时旋转也会带过去。

### 修复

- **侧边栏键盘导航的修复。** 方向键、Home 和 End 现在会经过侧边栏里每一个可到达的行和按钮，跳过隐藏分组和仅悬停可见的按钮，并且不再在每一行上打开预览卡片。当你固定、取消固定、移动、归档或折叠 Agent，或按下 More 时，键盘焦点保持不动。屏幕阅读器也不再把仓库标题读成可用键盘重排。
- **加载 Agent 和 Project worker 的修复。** 从链接或按 id 打开一个已结束的云 Subagent 时，不再因为先前被卸载而失败。从侧边栏隐藏的 Agent 现在会加载它们的磁贴，而不是无限等待；已经结束的 worker 也不会再让 Project 显示为正在运行。
- **记录稿和提示词样式的修复。** 正在等待 Agent 取走的引导消息和 Send now 中断，不再显示成灰色。记录稿里的时间标记现在显示为两条线之间的标签，并与消息气泡对齐。
- **语音通话的修复。** Agents Window 里的语音通话，在你正在交谈的 Agent 已关闭、已卸载或尚未加载其对话时，不再报错。
- **Agent 可靠性的修复。** 在 Windows 上，Hooks 现在收到干净的 UTF-8 载荷，屏幕阅读器会在聊天里读出 Restore Checkpoint 控件。
- **Projects 的修复。** 打开新建的 [Project](https://cursor.com/docs/agent/projects.md) 不再短暂无法加载其任务和文件；打开父对话已被卸载的 Project 会显示该对话，而不是错误屏幕；你返回的 Project 标签会恢复布局和滚动位置。显示 Project 外部聊天的 Project 分栏，在另一个视图短暂占用面板时不再折叠；隐藏标签里的长任务列表不再崩溃。协调者在由通知开始的回合上保留其指令，未发布的 Start from Scratch 草稿不再出现在项目选择器的 Recents 里。
- **浏览器 Annotate 模式的修复。** 在 Annotate 模式里编辑页面文字时按 Cmd/Ctrl+L，会把编辑加到聊天输入框，而不是排队。选中的元素再次在笔记里显示为内联提及。
- **Subagent 行的修复。** Agent 对话里的 Subagent 行，在同一次调用的迟到更新到达时，不再变成另一个工具的空行。
- **应用密钥卡片的修复。** 当某个密钥的值已保存，但应用仍在使用另一个所有者的同名密钥时，卡片现在会写 “Saved, not used here”，而不是笼统的错误。你回答 Expand secret scope 提示之后，卡片不再显示过时的错误。如果你在应用密钥请求上选择 Not now，卡片在重新加载后以及该次运行显示的其他地方都保持折叠，直到你重新打开它，或 Agent 再次询问。
- **Automations 仓库选择器的修复。** 仓库选择器对大型 Bitbucket Cloud 账户不再无限加载。如果加载超过 25 秒，它会显示到目前为止找到的仓库。

## 3.27

### 编辑器与应用

- **Reduce Motion 现在覆盖 Cursor 自己的 UI，拖放叠加层会直接到位。** 打开 `workbench.reduceMotion` 后，即使操作系统偏好是关闭的，Cursor 的旋转指示、加载条、菜单和对话框也会脉冲或淡入淡出，而不是旋转或缩放。把编辑器或终端拖去分栏时，拖放叠加层会直接跳到每个目标，而不是滑动。
- **在 Markdown 预览里展开、缩放并复制 Mermaid 图。** Markdown 预览里的 Mermaid 图现在与聊天里的图有相同的控件。在更大的视图里打开一张图来缩放，或复制其源码。
- **Cursor 更快打开第一个窗口。** Cursor 现在等到第一个窗口打开后才加载错误报告，因此这项工作不再拖慢冷启动。

### Agent 与聊天

- **Agent 不再弄乱非 UTF-8 和非 ASCII 文本。** Agent 终端输出不再弄乱被拆到不同输出块里的非 ASCII 字符；当文件含有一些无效字符时，文件读取和编辑会保留原始字节。代码库索引现在把 Latin-1、Windows 代码页和旧式 CJK 文件当作文本读取，因此语义搜索能在其中找到匹配。
- **Hooks 可以知道是哪个 Agent 派生了 Subagent。** [Hook](https://cursor.com/docs/hooks.md) 脚本现在会在 `subagentStart` 和 `subagentStop` 上收到 `parent_agent_id`，即派生该 Subagent 的 Agent 的对话 ID，因此脚本不必再从更早的事件重建 Agent 树。

### 修复

- **聊天里 Agent 链接的修复。** 聊天里指向 Cloud Agent 的链接，在其详情加载后就会显示 Agent 的名字，而不必再等一次无关的更新。
- **MCP 的修复。** 当 [MCP 服务器](https://cursor.com/docs/mcp.md) 请求输入时，Agent 聊天里的表单现在会滚动，使 Submit 和 Cancel 按钮位于提示框上方，并且按钮可以可靠点击。OAuth 刷新 token 已过期的 MCP 登录，现在会请你重新连接，而不是一次次失败。
- **Review Changes 里更清楚的 Add to Chat 按钮。** Review Changes 文件列表里的 Add to Chat 按钮现在使用带加号的聊天气泡图标。
- **Linux 软件包更新通知的修复。** 从 deb 或 rpm 软件包安装的 Linux 上，当 apt 或 dnf 没有更新可安装时，Cursor 不再显示更新通知。Cursor 现在会在通知你之前检查其软件包仓库，并且每个新版本只显示一次通知，无论你打开多少窗口或重启多少次。

## v2026.10.22

### Cloud Agent 环境

- **用 `agent env deploy --env` 更新现有环境。** 传入 `--env <id>`，使用 `agent env list` 显示的公开 ID，把环境文件重新部署到现有 Cloud Agent 环境上。环境保留其名称和仓库，因此 `--repo` 变为可选。如果 `--name`、`--repo` 或 `--scm-repo-node-id` 与已存储的环境不匹配，命令会停止，并给出说明不匹配之处的错误。

### 自托管 worker

- **自托管 worker 在你选择的团队下运行。** `agent worker start` 和 `agent worker debug` 现在在你于 `agent login` 时选择的团队，或用 `agent team switch` 选择的团队下注册并检查 worker，而不是你账户的默认团队。如果该团队不允许你使用 Cloud Agents，worker 会退出，消息中会写出团队名称，并建议 `agent team switch`。
- **自托管 worker 的修复。** 共享一台机器的 worker，在另一个 worker 正在运行其较旧构建时，不再无法启动编辑器服务器；该服务器下载期间，worker 会立刻应答编辑器连接，而不是挂起。worker 无法服务的连接现在会立即失败，机器繁忙时后端连接不再掉线，会话的第一回合不再错过启动它的人的 Skills。

### Subagents

- &#x20;**自定义 Subagent 在其模型无法运行时不再失败。** 当自定义 Subagent 被固定到一个被团队管理员设置阻止、需要 Max Mode，或不可用的模型时，它现在改在父 Agent 的模型上运行，而不会报错。Subagent 的结果会注明它实际运行的模型，因此 Agent 知道被固定的模型被跳过了。参见[配置的模型不会被使用的情况](https://cursor.com/docs/subagents.md#when-the-configured-model-wont-be-used)。
- **Composer 2.5 Subagent 按你选择的速度运行和计费。** 设为 Composer 2.5 的 Subagent 现在按标准速度运行和计费，而不是快速档。Composer 2.5 Fast 上的 Subagent 以快速速度运行，不会因 “Unknown model ID” 错误而失败。
- **Subagents 的修复。** 当 Agent 等待一个已失败或已停止的 Subagent 时，它现在会看到失败，而不会把该 Subagent 当成已完成。

### Rules

- **Agent 编辑文件时，嵌套的 `AGENTS.md` 规则现在会生效。** 当 Agent 在子目录里编辑或创建文件时，它现在会看到该目录的 [`AGENTS.md`](https://cursor.com/docs/rules.md#agentsmd) 和嵌套的始终应用规则，即使它此前从未在那里读过文件。每条规则在每次对话中只显示一次，按 glob 限定范围的规则和根规则行为与以前相同。

### 修复

- **后台摘要的修复。** 较长的 Agent 会话现在保留在后台计算的摘要，而不会丢弃后再次摘要。
- **工具调用显示的修复。** Edit、shell 和 task 工具调用在 Agent 仍在编写它们时就会显示为已开始。
- **Agent 图像生成和网页抓取的修复。** 用中文、日文、泰文及类似文字书写的图像生成提示词，不再因过短而被拒绝。当截断落在 emoji 内部时，网页抓取不再在长页面上失败，视频生成现在会说明它为什么读不了输入图像。
- **Auto-review 的修复。** 对 MCP 工具调用的 Auto-review，现在会在服务器把工具的输入 schema 作为 JSON 字符串发送时看到它。
- **MCP 服务器的修复。** 单个无效工具，或没有 type 的工具 schema，不再隐藏 MCP 服务器其余的工具；列出工具失败的服务器现在会向 Agent 报告错误。
- **Windows 的修复。** Agent 在 PowerShell 中运行的 shell 命令现在使用 UTF-8，因此非 ASCII 输出不再显得乱码。暂存的更新不再被崩溃或被杀死进程留下的标记拦住，后台更新检查不再闪出控制台窗口。

## 1.15.0

### 语音通话

- **语音通话在网络掉线后会重连。** 在 [Cursor for iOS](https://cursor.com/docs/cloud-agent/mobile.md) 中，失去连接的语音通话会自行重连，并继续同一段对话，而不是挂断。线路中断期间 Agent 完成的工作，会在通话恢复后传过来。
- **你安静下来时，语音通话会挂断。** 当你大约 10 分钟没有出声，且 Agent 没有正在进行的工作时，语音通话会自行结束，因此被遗忘的通话不会一直开着。它像正常挂断一样结束，不是错误，正在播放的回复绝不会被切断。

### Projects

- **Project 回复会流入，协调者的工作放在 Worked 分区里。** 在 Project 的聊天里，协调者的回复在写出时就会出现，其思考和工具工作会折进回复前带时长的 Worked 分区。从历史打开的聊天显示相同视图，已完成的回合不再在最终回复下方留下多余的 Worked 行。
- **看出 Project 正在等你。** 当 Project 的协调者或其某个 worker 停在一个问题或 MCP 登录上时，其收件箱行会显示 Input Needed，Project 聊天里的 Agents 药丸会读作带计数的 Input Needed。等待中的 worker 在 Agents 面板里显示 Input Needed，而不是 Working，因此你不必再打开每个 worker 去找问题。
- **正在工作的 Project 出现在 Working 磁贴里。** 如果团队管理员已打开 Projects，iPhone 和 iPad 上收件箱的 Working 磁贴会计入协调者或 worker 正在运行的 Project，Working 列表对每个只显示一次，并带工作指示。已归档的 Project 离开 Projects 分区，只出现在状态筛选包含 Archived 的列表里。

### Pull request

- **重新堆叠 Origin 的堆叠 pull request。** 当栈里的 Cursor Origin pull request 落后于其基线时，审查卡片显示 Restack，而不是 Update Branch，因为 Origin 不允许对堆叠的 pull request 使用后者。点它即可重新堆叠整个栈，与网页上的 pull request 页面相同。如果重新堆叠遇到冲突，你会看到 Origin 的消息。
- **等待审查者的检查显示为 Action Required。** 当 pull request 唯一失败的检查是在等某人批准，例如代码所有者审查时，状态卡片、检查列表和 Agent 列表会显示黄色的 Action Required 状态，而不是失败的检查，并且对这些检查隐藏 Fix with Agent。如果混有真正的 CI 失败，检查仍显示为失败。
- **更快、更稳的 Pull Requests 列表。** Pull Requests 面板会立刻根据最近加载的数据显示行数以及打开或已合并状态，然后在 Agent 有很多 pull request 时更快刷新。细节填入时行保持原位，Open、Draft、Merged 和 Closed 筛选始终显示，筛选没有匹配时会出现一条消息。
- **pull request 链接在 Agent 对话里显示其状态。** Agent 对话里的 pull request 链接显示为带 GitHub 或 Cursor 图标的紧凑数字芯片，与桌面和 Web 一致。当 pull request 属于该 Agent 时，图标会显示它是打开、草稿、已合并还是已关闭，VoiceOver 会读出该状态。自定义链接文字会保留，复制链接仍给你原始文本。
- **表格、图片和可折叠分区在 pull request 描述里渲染。** pull request 描述和 Agent 消息里的 HTML 和 Markdown 表格，例如前后截图网格，会渲染成真正的表格，单元格里有图片和视频，而不是原始文本。附在 pull request 描述和评论上的截图会加载，检查的 Details 面板显示可折叠分区和换行，而不是原始 HTML。
- **Review 会自动跟上新 commit。** 在 Review 屏幕上打开 pull request 时，更改的文件和行数会在新 commit 后大约 30 秒内更新，无需下拉刷新或重新打开应用。更新加载时，现有文件行留在屏幕上。

### Agents

- **从 Cloud Agent 链接打开文件。** 在 Cloud Agent 的记录稿里点文件链接，会打开该 Agent 的聊天，并在链接的行上显示文件，从 Agent 的机器实时读取。指向 Agent Stores 中文件的链接也能打开；文件缺失、不是文本或加载失败时，你会得到清楚的消息。

### Composer

- **从你自己的机器上的仓库启动 Agent。** Composer 的仓库选择器列出你在线的个人机器上已检出的仓库，即使它们没有通过源代码管理连接。选一个，Agent 就在那台机器上运行。这些仓库不提供 Cloud，因此 Cloud 选项显示 “Not available for this repository.”
- **听写可在 CarPlay 和蓝牙上工作。** Composer 听写从 CarPlay 和蓝牙免提麦克风录音。如果音频路由中途改变，例如汽车连上时，录音会继续，而不会停止。

### 收件箱

- **下拉刷新 Inbox。** 在 iPhone 或 iPad 上的 Inbox 下拉，即可重新加载你的 Agent 及其未合并 pull request 的状态，而不必等下一次更新或重新打开应用。

### iPhone Duo

- **对 iPhone Duo 的更好支持。** 在 iPhone Duo 展开的内屏上，Cursor for iOS 会随设备旋转，并在应用打开时展开手机后保持正立。模型选择器、搜索和 pull request 审查这类面板把关闭按钮留在顶栏，Inbox 的个人资料按钮移进侧边栏。

### 账户与账单

- &#x20;**团队账户的方案管理更清楚。** 在团队账户上点 Manage Plan，会告诉你谁管理你的方案：团队成员会被要求联系团队管理员，企业管理员会被指向其账户团队。自助服务的团队管理员在个人资料里得到一行 Manage team subscription，在 App Store 地区允许的情况下，它会在 cursor.com 上打开团队订阅。如果你属于多个团队，应用会打开你的付费团队，而不是 Free 团队，除非你自己选了 Free 团队。

### 修复

- **语音通话。** 语音通话在听写之后立刻开始时不再无声，挂断后不再显示失败消息，也不会把问候语盖过你的第一句话。静音会结束你的回合，以便助手回复，实时记录稿不再重复你的回合。麦克风无法使用时，应用会说明它是被阻止、不可用，还是权限提示没有得到回答。
- **聊天历史与后续消息。** 长聊天里更早的消息会在你向上滚动时加载，而不是打开时一次全部加载，实时回复在历史加载时继续流入。后续消息在你点 Send 的那一刻就显示，重连后不再消失或重复，重新打开聊天时未回答的问题会重新出现。空闲的 Project 留在 Projects 分区，从 Inbox 里的 Project 返回时，Back 总会回到 Inbox。
- **应用内购买与付费墙。** 付费墙不再卡在空白屏幕或旋转指示上；它会显示带 Try again 选项的错误。如果你已付款的购买因为面板关闭而没有发放，再次购买会恢复它，而不会收两次费，“product not found” 错误也更少发生。没有月度支出上限、却遇到用量封锁的账户，会转到 Web 仪表板，而不是走进 Contact Sales 的死路。
- **性能与稳定性。** 当你有数千个仓库时，Inbox 和 Composer 不再挂起，带很多 pull request 的聊天打开得更快。应用在 iOS 26 上启动时不再崩溃，播放视频时旋转设备也不再崩溃，Game of Life 动画背景再次渲染。
- **Composer、面板和 pull request 操作。** 点 Fix Checks with Agent 之后，Composer 不再落到键盘后面；回复一段很长的文本选择之后，也不再画到工具栏上面。Search、Checks 和 Pull Requests 面板有不透明的标题，行不再透出来，Live Activity 计时器在狭窄的灵动岛里会隐藏，而不是被截断。记录稿里的 Agent 和 pull request 链接在你正在使用的应用里打开，失败的 pull request 操作会说明出了什么问题。

## 3.26

### Projects

- **搜索、排序并浏览 Project 的 Library 文件。** 在 [Project](https://cursor.com/docs/agent/projects.md) 的 Library 里，All Files 现在是带 Name、Kind、Modified 和 Size 列的表格。你可以搜索文件，按种类、文件夹或修改日期筛选，按任意列排序，把列表限制为 Project 或 User 文件，或切换到按文件夹分组的预览卡片网格，你的选择会按每个 Project 保存。Recents 现在会在 Agent 写入文件后数秒内更新，你可以用键盘在 Library 里移动。
- **从 @ 菜单提及 Project 的任务。** 在 [Project](https://cursor.com/docs/agent/projects.md) 聊天里，@ 菜单现在有一个 Tasks 文件夹，按状态列出 Project 的任务，顺序与右侧面板的任务列表相同。输入任务 ID（或只输入其数字）或标题中的词来找到它，选中后会加上带状态图标的任务芯片。对 Project 成员的提及现在显示为普通的 @Name 芯片，协调者的 @ 菜单不再提供它无法使用的 Branch 行。
- **Project 协调者的第一句回复更快。** 当你给 [Project](https://cursor.com/docs/agent/projects.md) 发消息时，其协调者现在会立刻用一句短回答，或一句快速确认及其第一步来回复，然后才开始读文件或启动 worker。
- **选择云 Project 可以使用哪些机器。** 在云 [Project](https://cursor.com/docs/agent/projects.md) 的聊天里，聊天输入框下方的 Run on 菜单现在列出你的机器和池，因此你可以搜索并勾选协调者可以不经询问就放置 worker 的那些。
- **把本地文件夹加到 Project 允许的机器。** 在云 [Project](https://cursor.com/docs/agent/projects.md) 的 Allowed machines 菜单里，Machines 子菜单现在有一行 Add folder on This Mac。选一个文件夹，Agents Window 会为它启动 worker 并允许它用于该 Project，显示 Starting 和 Allowing 进度，失败时显示错误消息。
- **Project 任务有更好的键盘和屏幕阅读器支持。** 在 Project 的任务 List 和 Board 里，每项任务的标题现在就是打开它的控件，因此 Tab 能干净地在任务之间移动，聚焦的 Board 卡片会完全滚进视野。屏幕阅读器现在会读出每项任务的状态、状态按钮所改变的任务，以及按状态计的 PR 数量，例如 “3 PRs: 2 open, 1 merged”。你改变任务状态时焦点也会跟着任务，在 Project 笔记里关闭查找时会回到你原来的位置。

### 聊天与 Composer

- **语音通话说出 Agent 自己的更新。** 在语音通话中，Agent 现在自己选择说什么：工作时给出简短进度，完成时给出简短的口头回答，它启动的 worker 回报时给出快速更新，而不是由通话把聊天回复大声读出来。线路掉线时，通话也会显示重连状态且时钟继续走，立刻确认任务，并在任务 20 秒内无法到达 Agent 时说出来。
- **修复了模式斜杠命令丢掉你在其后输入的提示词。** 输入模式命令再跟上提示词，例如 `/ask explain this file` 或 `/plan fix the tests`，然后按 Enter，不再清空输入并且什么都不发送。Agents Window 现在会切换到该模式并发送你的提示词。
- **你发给 Project 的长消息用 Show more 折叠。** 在 [Project](https://cursor.com/docs/agent/projects.md) 聊天里，你发送的长消息现在最多显示 30 行，带淡出和展开全文的 Show more 按钮，而不是一个小滚动框。Show less 会再次折叠它。
- **在 Markdown 文件里右键链接，得到与 Agent 回复相同的操作。** 在 Markdown 文件里右键链接，现在显示与 Agent 回复中的链接相同的菜单，带 Open、复制和文件操作。文件链接相对于文档打开，指向同一文件中标题的链接只提供 Copy。

### 导航与布局

- **分栏保留自己的控件，并在重新加载后仍在。** 当你把对话分成栏时，每一栏现在有自己的 “…” 聊天操作菜单，以及它所显示聊天的 Project 成员，磁贴的标题栏被收成只剩操作菜单和显示面板按钮。分栏、你聚焦过的那一栏，以及每个磁贴选中的 Agent，现在会在重新加载后回来，即使对话加载较晚或从侧边栏隐藏。Project 自己的聊天标签固定在其窗格前面，拖放、窄磁贴、键盘焦点和在窗格之间循环也更可靠。
- **用 Cmd+Z 撤销归档，并在归档仍有进程在跑的 Agent 之前得到警告。** 归档一个 Agent 或一组之后，在没有文本框拥有焦点时按 Cmd+Z（Windows 和 Linux 上为 Ctrl+Z），或使用 Edit > Undo 把它带回来，再用 Cmd+Shift+Z 再次归档。归档仍有后台进程在运行的 Agent 现在会先请求确认，包括对侧边栏分组使用 Archive All。
- **把文件拖到侧边栏里的 Agent 上。** 从桌面把文件拖到侧边栏里的 Agent 或 Project 上，即可打开该聊天，并把文件附到其提示词上。拖动时该行会高亮，让你看到文件会落到哪里。

### 文件、改动与 pull request

- **在 PR 标签里更容易审查 pull request。** PR 标签现在会记住你在切换 Agent 或重启时是在 Changes、Description、Commits、Checks 还是 Reviews，在 diff 行上 Cmd+点击（Windows 和 Linux 上为 Ctrl+点击）会在 GitHub 或 Origin 上打开该行。生成的文件以折叠开始并带 Generated 徽章，当 Origin 规则阻止合并时 Merge 按钮保持禁用，绕过规则的合并现在显示在时间线里。提示词和消息里的 pull request 芯片显示 PR 的当前状态，例如已合并或已关闭。

### Cloud Agents

- &#x20;**打开自托管 worker 上 Agent 的实时工作区。** 在 Linux [自托管 worker](https://cursor.com/docs/cloud-agent/self-hosted/private-workers.md) 上运行的 Cloud Agent，现在像 Cursor 托管的 Cloud Agent 一样，在 Agents Window 里显示 Changes 面板、文件树、终端和转发的端口。用 `agent worker --connect-workspace start` 启动 worker 来打开它。如果 worker 重启或离线，worker 回来后工作区会重连，过时的 worker 会显示一条消息请你更新它。
- **自托管 worker 上 Cloud Agent 的状态更清楚。** 当 Cloud Agent 的自托管 worker 断开时，编辑器面板显示 Worker offline，worker 回来后会自动重连。
- **把环境设置指向仓库里的任意 environment.json。** [环境设置](https://cursor.com/docs/cloud-agent/setup.md) 对话框不再等待检测 `.cursor/environment.json`。在 environment.json 位置下，它从 Start from scratch 开始，并列出设置中的每个仓库。选一个仓库来加载现有文件，并输入其路径，路径一开始会填成 `.cursor/environment.json`。

### 浏览器与 Design Mode

- **在 Design Mode 里编辑你批注的元素的 CSS。** 当你在 [Design Mode](https://cursor.com/docs/agent/design-mode.md) 里对单个元素留下笔记时，可以打开 CSS 检查器来筛选、编辑并实时预览其属性，然后把这些编辑连同笔记发给 Agent。工作时可以把检查器固定在页面一角。离开 Design Mode 也会立刻把页面自己的光标还回去。
- **更简单的内置浏览器工具栏。** 内置浏览器不再有书签栏、收藏和历史侧边栏，或 Cmd+D 书签快捷键。你的浏览历史仍可从地址栏建议和新标签菜单获得。

### 账户

- &#x20;**达到每周上限时切换模型。** 当你在额度不覆盖的模型上达到每周用量上限时，通知现在可以在 Buy Credits 旁边提供 Switch Model 按钮，以便转到已覆盖的模型。在 Enterprise 方案上，Plan & Usage 设置里的 Personal Usage 图表也会立刻从你上次保存的数据打开，并且在你属于多个团队时显示正确的支出类型。

### 无障碍

- **更多无障碍和减弱动态效果的修复。** 屏幕阅读器现在把聊天记录稿读作 “Chat transcript” 区域，并在分支菜单、项目选择器、字体选择器和自动化触发器选择器加载时把它们读作忙碌。打开减弱动态效果后，对话框在原地淡入，而不是缩放或滑动。

### 修复

- **更稳的 Agent 记录稿。** 重新打开最近的 Agent 会恢复其记录稿布局，而不会在行重新测量时跳动。Cloud Agent 重连后，你断开期间写出的输出现在会出现在对话里。
- **Subagent 托盘的修复。** 托盘行和面包屑在长聊天里保留任务名和状态，行不再卡在一个不断失败的加载后面，共享的 Project 不再显示为自己的 Subagent。被跟踪的任务芯片显示 Agent 的真实名字，而不是 “Agent”，打开已完成的云 Subagent 会可靠地加载它。
- **提示框和聊天切换的修复。** 切换聊天会把光标留在每个提示词里你离开的位置，Composer 会滑入到位，而不是跳动。全屏提示词现在显示附上的视频，在后续消息不可用时（例如 worktree 设置失败之后）保持可见但只读，锁定的提示词不再接受拖入或粘贴的文件。退出登录现在会清除记住的对话，创建 Agent 失败或取消后会回到你的草稿。
- **侧边栏的修复。** 本地聊天从侧边栏打开得更快，窗口在启动时只加载你最近的聊天。折叠的 Pinned 分区，以及你重新展开的过期分区，现在会在重新加载后保持那样，侧边栏行在动画期间不再显示过期标签。没有仓库的 Cloud Agent 在工具提示里不再显示两次 “Cloud”，只剩下已完成 worker 时 Project 不再显示为正在运行。
- **面板、菜单和项目选择器的修复。** Changes 视图现在正确命名嵌套仓库，并在你切换仓库时刷新 diff。打开不在 Agent 机器上的文件会显示清楚的消息，而不是原始错误，在 Files 树里按 Cmd+A 会选中树行，Chat 操作菜单的选择会把焦点留在它们打开的对话框里。项目选择器在搜索时保持多仓库 Save 按钮可见，并且在仓库列表非常大时仍保持响应。
- **云 Project 的修复。** 在云 Project 里，子 Agent 的转发端口菜单显示该子 Agent 的端口，协调者只在它真正给你发消息时播放完成提示音。

## 3.26

### Agent 与聊天

- **因扩展宿主崩溃而停止的 Agent 可以恢复。** 如果运行本地 Agent 的扩展宿主在运行中途退出，例如内存耗尽之后，聊天现在会显示 “Extension host stopped unexpectedly” 错误，并带 Resume 选项，而不是看起来像你停止了它。受影响的聊天会被标为未读；如果 Cursor 没有焦点，一条系统通知会覆盖所有已停止的 Agent。
- **修复了编辑更早的消息时 Skills 不出现。** 在 Agent 聊天里编辑更早的消息时，输入 / 现在会打开 Skill 和命令选择器，而不是空列表，因此你可以在重新提交之前加上一个 Skill。当你的 Skills、命令或 Subagents 改变时，列表会更新。
- **聊天里更干净的 @、# 和 + 菜单。** 空的 @ 菜单不再列出 Branch 或 Browser（输入才能找到它们），没有匹配到 pull request 的 # 查询现在会隐藏菜单，和 / 与 @ 一样。+ 菜单的文件项现在叫 Attach，聊天一有消息，Summarize 就会出现在 / 菜单里，模式选择器这类菜单里的工具提示不再盖住菜单项。
- **在 Mercurial 仓库里用 Bugbot 审查改动。** 从编辑器用 Bugbot 审查改动现在可在 Mercurial 检出中工作，文件路径从 Mercurial 根解析，即使它位于 git 仓库内部，或你打开的是子文件夹。

### 自定义与设置

- &#x20;**当 permissions.json 管理允许列表时，终端批准上的 Always Run 现在会保留。** 当你自己的 `~/.cursor/permissions.json` 设置终端允许列表时，在 Agent 的终端批准上选择 Always Run，现在会把命令加到该文件的 `terminalAllowlist`，因此同一条命令不再每次都提示。当团队的管理员设置控制允许列表时，批准卡片不再提供 Always Run，因为那个选择会被忽略。参见 [permissions](https://cursor.com/docs/reference/permissions.md)。
- **MCP 服务器状态更清楚，连接混淆更少。** 在 Customize 里，仍在重连的 [MCP 服务器](https://cursor.com/docs/mcp.md) 显示 “Connecting…”，而不是 “0 tools” 或登录警告，你的 Cursor 账户已经有的本地服务器会提供 “Use the account version”。服务器连接期间编辑项目的 mcp.json，现在会使用新配置，带 OAuth 的本地 MCP 登录现在给你最多 15 分钟在浏览器里完成。
- **Agent 不再收到重复的项目指令。** 当 `CLAUDE.md` 是 `AGENTS.md` 的符号链接、硬链接或完全相同的副本时，Agent 现在只收到这些指令一次，而不是两次。在 Windows 上，即使路径只在盘符大小写上不同，[规则](https://cursor.com/docs/rules.md)、嵌套的 `AGENTS.md` 文件和 Skills 现在也会生效。

### 修复

- **Agent 可靠性的修复。** 含有很大数字的 Agent 聊天现在能正确保存。超时的 Grep 和 Glob 搜索会保留到目前为止找到的匹配，笔记本编辑会保留 Agent 没有碰过的字段，在 Windows 上 Hooks 能正确接收非 ASCII 文本。
- **Agent 聊天和提示词的修复。** 聊天提示词里附上的图片，如果提示词在保存期间关闭，不再丢失，连按两次发送不再发出重复请求。重新打开聊天后，来自 Agent 的问题不再被跳过，在移到单独窗口的聊天里打字不再颠倒或丢掉字符，聊天文件列表里的 Undo 现在会在 Windows 上还原文件。
- **编辑器、终端和源代码管理的修复。** 你已经在里面打过字的终端，在扩展环境变量改变时不再被重置，打开终端后立刻关闭不再留下游离的 shell，zsh shell 集成现在在第一个终端上就会设置好。搜索结果再次把同一文件的命中归在一个条目下，Find All References 之后现在会出现 References 视图，部分克隆的远程和仅推送的远程现在会被识别。在 macOS 上，安装时卡住的更新现在会被检测并重试。
- **内置浏览器的修复。** 在内置浏览器里打开的 PDF 现在会显示页面，而不是空查看器。Agent 的浏览器操作在标签打开后或在窗口之间移动后不再立刻失败，页面导航离开或浏览器关闭时，它们会立刻停止，而不是挂起。

## 1.0.40

- &#x20;**选择 SDK Agent 在哪个团队下运行。** 如果你的登录属于多个团队，在 `Agent.create(...)` 选项里传入 `teamId`，即可让 Agent 在该团队下运行。同一选项也适用于 `Agent.list`、`Agent.get`、归档和删除调用，以及 `Cursor.me`、`Cursor.models.list` 和 `Cursor.repositories.list`。它不会被保存，因此要在 `Agent.resume` 和后续调用上再次传入。仅 TypeScript。
- **拒绝工具调用的 Claude Code Hooks 现在会在本地 Agent 里阻止它。** 修复了一个 bug：来自 Claude Code `settings.json` 的 `PreToolUse` Hook 在 `hookSpecificOutput` 下返回了 `permissionDecision: "deny"`，但本地 SDK Agent 仍然运行了该工具调用。本地 Agent 现在把该[嵌套响应](https://cursor.com/docs/reference/third-party-hooks.md#response-format-compatibility)视为拒绝。
- **本地 Agent 存储打不开时，本地运行仍保留其输出。** 如果本地数据库在运行中途打不开，已经流入文本的回合现在会以该文本作为结果完成，而不是以错误结束。运行在内存中继续，流会发出一条 `status` 消息，警告进程退出后该运行的 Checkpoint 可能不可用。

## 1.0.39

- **Python 运行会报告失败原因。** 当运行在开始后失败时，Python SDK 里的 `run.wait()` 现在会在 `RunResult` 上返回 `error_code` 和 `error_message`，流结果事件也带同样的字段。以前 Python 只能看到一个错误状态。
- **本地 MCP 服务器和记录稿的修复。** 在本地运行的单次发送上传入 `mcpServers`，不再丢掉从 `settingSources` 加载的 MCP 服务器。本地 Agent 在主目录缺失或只读时也会继续写记录稿，使用已设置的 `CURSOR_DATA_DIR`，否则使用私有临时目录。

## Oct 19, 2026 当周

### Web

- **合并队列变更的 Slack 私信。** 在个人 [Notifications 设置](https://cursor.com/docs/origin/settings.md#notifications) 里打开新的 **Merge queue** 开关，即可在你的 pull request 被加入或移出合并队列时收到私信。如果队列把它逐出，私信会说明原因，例如哪些检查失败了。如果你为别人的 pull request 打开了准备好就合并，它进入队列时你也会收到私信。
- **看出是哪些检查把 pull request 移出了合并队列。** 当合并队列因为检查失败而移出 pull request 时，Slack 通知会列出最多 10 个失败的必需检查，并链接到每次运行；pull request 时间线里的 Removed from merge queue 行会显示队列所测试的 commit 的检查状态。检查详情只对能读取仓库检查的人显示；其他人仍会收到移出通知。
- **合并队列不再卡在它无法构建的 pull request 上。** 如果 Origin 反复无法创建 CI 用来测试 pull request 的合并 commit 或队列分支，合并队列现在会移出该 pull request，而不是无限重试。移出消息会写出最后的错误，并请你在修好后重新入队。
- **取消 Origin CI 运行和作业。** 如果你对仓库有写权限，可以从其 CI 页面取消正在运行的 CI 运行，或单个未完成的作业。确认会列出等待你所取消作业的那些作业，它们也会显示为已取消，停止的作业大约在 10 秒内结束。作业的检查会显示是谁取消了它。
- **用作业和运行时间线看出 CI 时间花在哪里。** Origin CI 作业页面现在显示队列等待、槽位占用和每个阶段的 Timeline，包括拆开的检出；点击一个阶段只看它的日志，失败的作业会打开失败阶段，并带退出码和第一行错误。运行页面增加 Timeline 标签，在同一时钟上显示每个作业的等待和阶段，以及该运行相对仓库上限的槽位使用，并把作业分到 Failed、Running、Waiting 和 Passed 下，用 Next failure 按钮逐步查看失败。
- **容量不足时，Origin CI 作业会排队，而不是失败。** Origin CI 作业现在会在其仓库的作业队列里等待空闲槽位，集群满时会等待并重试，而不是立刻失败。作业页面把等待中的作业显示为 Queued 并带队列位置，或显示为 Waiting for cluster capacity 并带已等待时长，已完成的作业会显示排队了多久。运行页面上新的 All jobs in this repository 标签列出排队中、运行中和已完成的作业，你可以按状态、分支或 pull request，以及运行 ID 筛选。
- &#x20;**请应用在被阻止的 pull request 上破例或授予所有权豁免。** 当 pull request 被阻止时，其合并选项现在会列出可以破例或授予所有权豁免的应用。你可以向其中一个发送请求，在待处理时查看它，并取消它。应用通过创建豁免来批准请求。详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- &#x20;**所有权豁免现在会持续到新的推送，并显示为已豁免。** 在 pull request 上授予的所有权豁免，在你推送新 commit 之后仍然有效，因此不必再次授予。在合并框里，豁免所覆盖的代码所有者组和所有者策略现在显示为 “Waived · exemption”，并带授予者，而不是等待或消失。Origin API 里的审查要求和可合并性响应也包含豁免的原因和授予者。
- **合并一个以另一个未合并 pull request 的分支为目标的 pull request。** 当 pull request 的基线是另一个未合并 pull request 的分支，且它没有堆叠在其上时，你现在可以从 pull request 页面把它合并进该分支，而不再被阻止。合并框显示一行 Stack onto，因此你可以一键把它堆叠到另一个 pull request 上。合并队列和自动合并仍会等到另一个 pull request 合并。
- **更新堆叠的 pull request 不再把已移除的代码带回来。** 父 pull request 被 squash 合并之后，对其子 pull request 使用 Update branch，或应用合并冲突解决，现在会在一次合并可能重新引入子 pull request 所移除的改动时停下来，并请你重新堆叠到基线分支。当已知重新堆叠会在该 pull request 处或更低处因冲突停下时，pull request 页面也会隐藏 Restack 和 Update branch；合并不受影响。
- **commit 行会记上共同作者，以及 Agent commit 背后的人。** pull request 上的 commit 现在会在 commit 作者之后列出 Co-authored-by 尾注里点名的人。Cursor Agent 为你做的 commit 显示你的头像，并带 Cursor “via” 徽章，而不是通用的 Cursor Agent 头像。
- **pull request 页面上审查者组和机器人的修复。** pull request 上被请求的审查者组现在会显示其成员及其审查状态，即使该组不在你的审查者列表里。在 pull request 的 Activity 里，发给机器人的审查请求显示机器人的名字，而不是空白审查者；发给后来被移除的组的请求显示组名，而不是原始 ID。
- **绝不以草稿打开 pull request。** Origin 设置里 Pull requests 下的新设置 **Never open PRs as drafts**，会让你或你的 Agent 打开的每个 pull request 都以可审查状态开始，即使请求的是草稿。它适用于你的所有 pull request，无论你从 Web、Origin API 还是 Cloud Agent 打开它们。
- **推送现在会在加入密钥时警告你。** 当你推送到 Origin 仓库时，Origin 会扫描推送的改动，查找 API 密钥和 token 这类密钥。如果找到一个，`git push` 会打印警告，写出 commit、文件和行，并给出移除步骤。匹配到的密钥本身绝不会被打印或记录。
- &#x20;**审计日志记录更多 Origin 变更。** 你团队的审计日志现在记录仓库规则集的创建、更新和删除，仓库和 pull request 上的标签变更，merge-when-ready 的打开或关闭，仓库删除，审查驳回，以及命名空间入站 IP 允许列表的变更。每条记录包含操作成员的公开用户 ID，它也会作为新的 User ID 列出现在 CSV 导出里，应用安装记录会列出该安装持有的权限。仓库创建或删除后有时被丢掉的仓库事件现在也会被记录。
- &#x20;**规则集设置里绕过用户的名字更清楚。** 在仓库的 [Settings > Rules and Protections](https://cursor.com/docs/origin/settings.md#rules-and-protections) 里，规则集绕过列表上的人不再显示为裸用户 ID。你没有权限看到的人读作 “Hidden user”，已移除的账户读作 “Deleted user”，没有名字或邮箱的账户读作 “Unnamed user”。悬停一行可看到原因和用户 ID。
- &#x20;**Cloud Agent 可在有入站 IP 允许列表的团队上工作。** 即使你的团队有入站 IP 允许列表，Cursor Cloud Agent 现在也可以克隆、推送、使用 Origin MCP，并打开它们创建的原始文件、tarball 和克隆链接。豁免只覆盖你自己团队的仓库。并发的允许列表编辑现在一次应用一个，因此两个管理员同时编辑不再会互相锁在外面，或超过条目上限。
- **从 Cloud Agent 的评论跳到它的运行。** Cloud Agent 在私有 Origin pull request 上发布的评论、讨论和审查，现在以 Open in Web 按钮结束，打开写下它们的 Agent 运行。公开仓库上的评论不包含该链接。
- **提及的 Slack 通知，格式更干净。** 在 [Notifications 设置](https://cursor.com/docs/origin/settings.md#notifications) 里，你现在可以在有人提及你时打开 Slack 私信，加粗书写或标点之后的提及现在也算。Slack 里的 pull request 和评论正文现在把链接、代码、标题、列表和折叠分区显示为可读文本，而不是原始 HTML 标签。当你把 Slack 频道订阅到仓库时，确认会告诉你测试消息是已发送，还是因为频道与另一家公司共享而被跳过。

### API

- **通过 Origin API 列出触及某个文件或目录的 commit。** 通过 Origin API、Origin SDK 或 Origin MCP 服务器的 `list_commits` 工具列出 commit 时，现在接受 `path`，因此你只得到改变该文件或目录的 commit，如同 `git log -- <path>`。包含 `..` 的路径会被拒绝。参见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **在 Origin API 里按合并 commit 或合并时间查找 pull request。** 你现在可以列出作为特定 commit 落地的 pull request，一次最多 100 个，或列出在某个日期范围内合并的那些，因此更容易把 commit 映射回 pull request，或制作发行报告。同样的筛选也适用于 Origin MCP 服务器的 pull request 列表工具。详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **通过 Origin API 请求、列出和移除机器人审查者。** Origin API 里被请求的审查者现在在单个 `reviewers` 列表中返回，其中包含你团队的服务账户，以及用户和组，你可以按种类和 ID 请求或移除其中任何一个。审查者 webhook 现在会对服务账户审查者触发，而不是跳过它们，Origin SDK 支持新字段。较旧的 `users` 和 `groups` 字段已弃用，但仍被接受；参见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **Origin MCP 工具可以列出 ref 之间更改的文件，并从 pull request 获取 Agent 截图。** `compare_commits` [MCP 工具](https://cursor.com/docs/api/origin/mcp.md) 有一个新的 `files` 视图，分页列出两个 ref 之间每个不同的文件，并带逐文件补丁。`summary` 仍是默认。`get_pull_request_attachment` 也可以从工件分享链接或 Agent 的工件页面，获取 pull request 里链接的 Cloud Agent 截图和录制，供能查看该 Agent 的用户使用。
- &#x20;**应用和服务账户可以通过 Origin API 做更多事。** 安装在整个命名空间上的应用现在可以被授予 Create repositories 权限，并通过 Origin API 创建仓库。服务账户和应用安装 token，包括以成员身份行事的 token，现在可以通过签名下载链接下载仓库 tarball，而不会失败。列出命名空间现在也会返回其他团队与你共享的命名空间。详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **检查运行拒绝过大的名称和链接，可合并性会写出代码所有者。** 当你通过 Origin API 发布检查运行时，超过 255 个字符的检查或套件名称，或超过 8,192 个字符的详情 URL，现在会返回无效参数错误，而不会被接受。当你向可合并性端点询问所有者规则时，它现在也会返回拥有用户的名字和头像，因此你可以显示谁拥有这些文件，而不是原始用户 ID。参见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。

### 修复

- **代码所有者审查的修复。** 代码所有者审查请求不再被意外移除，非常大的栈里的 pull request 现在也会得到它们。在 pull request 页面上，由 `@login` 点名的所有者现在会正确显示其批准，在 `.agentic-owners` 目录里编辑规则提示词现在会刷新合并就绪状态。Code Review Gate 检查不再卡在进行中，推送之后立刻报告门禁结果不再因头部过期而失败。
- **可合并性与合并冲突的修复。** 仓库正在压缩时，可合并性检查不再失败。冲突数量非常大的 pull request 会把冲突文件列表标为不完整，合并结果遵循仓库当前的 `.gitattributes` 规则，目录重命名冲突现在被报告为它们自己的一种冲突。
- **pull request 页面的修复。** 合并详情或作者改变时，pull request 页面现在会实时更新，网络恢复后立刻重连。超过 10 MiB 的文件显示 “Too large to count”，而不是 +0 -0。
- **Origin CI 的修复。** 运行器集群短暂停止响应，或运行器启动缓慢时，CI 作业不再失败；它们会等待并重试，直到作业的截止时间。重试的运行现在从已保存的进度恢复，`origin.ci.ts` 编译后没有任何作业的运行，不再取消同一并发组里更早的运行。
- **Slack 通知的修复。** Slack 和电子邮件通知不再到达两次，时钟回拨时审查提醒不再发送两次，不需要审查请求的提醒不再列出你自己的 pull request。当许多频道关注一个仓库时，已订阅的频道不再错过事件，也不再为从未标为可审查的草稿收到关闭或合并帖子。Slack 提醒和频道设置在保存或移除失败后现在会刷新。
- **合并队列的修复。** 从 pull request 列表把 pull request 转为草稿或标为就绪时，现在会显示 Origin 拒绝该变更的原因，例如 pull request 正在合并队列中。Merge Queue 标签、页面标题和设置分区现在标签一致。
- **Git 操作的修复。** 作者行格式错误或非 UTF-8 编码的 commit 现在会按 `git log` 显示它们的方式加载和显示，中断的写入之后更改默认分支不再失败。
- **GitHub 镜像的修复。** 在 Origin 里做的分支更新、合并和删除现在会可靠地到达 GitHub 镜像，当 GitHub 拒绝隐藏 ref，或分支在此期间被删除时，推送不再失败。从镜像仓库获取 pull request ref 或新 commit，在镜像追上期间不再超时或停滞。
- **pull request 搜索的修复。** 无法解析的 `app/<slug>` 限定符现在会建议使用应用的 `app_…` id，来自仓库受限 token、针对它无法读取的仓库的搜索会被拒绝，且不计入你的搜索速率限制。

## Oct 19, 2026 当周

### Agents

- &#x20;**Agent 知道 Subagent 何时回退到了不同的模型。** 当自定义 [Subagent](https://cursor.com/docs/subagents.md) 被设为团队管理员设置所阻止、需要 Max Mode，或无效的模型时，父 Agent 现在会得知该 Subagent 改在父模型上运行。继承父模型的 Subagent，在父 Agent 没有使用 Fast 时，也不再在不允许 Fast 档的团队上被阻止。
- &#x20;**团队阻止视频审查模型时，computer use 保持开启。** 修复了一个问题：在团队模型设置里阻止 Gemini 3.1 Pro 会关掉团队上每个 Cloud Agent 的 computer use，即使 computer use 模型是允许的。现在只有视频审查不可用，Agent 继续用 computer use 测试。
- **损坏的 Skills 会在运行中被标出。** 当某个 Skill 的 SKILL.md 有无效的 frontmatter、没有描述，或无法读取时，Cloud Agent 运行现在会显示警告，写出该 Skill 以及如何修复。以前 Agent 会悄悄跳过该 Skill，或不太可能选中它。
- **只读文件的回合之后，后续消息更快。** 当一个回合只读取或搜索文件，或发送消息时，下一回合现在会更早开始。因为什么都没改变，Agent 不再等待并不需要的工作区快照。

### Pull request

- **pull request 默认以可审查状态打开。** Cloud Agents，包括 Project worker，现在打开的 pull request 是可审查的，而不是草稿，除非你或任务要求草稿。代码库设置的 Pull requests 分区里新的 Never open PRs as drafts 开关，会让你和你的 Agent 打开的每个新 pull request 都可审查，即使请求的是草稿。
- **Agent 在私有 Origin pull request 上的评论链回运行。** 当 Cloud Agent 在私有 Origin pull request 上发布评论、回复或审查时，评论现在以 Open in Web 按钮结束，打开该 Agent 运行，因此审查者可以直接跳到 Agent 的工作。

### Projects

- **Project 协调者保持任务跟踪器准确。** 在使用任务跟踪器的 Project 里，协调者现在会把 worker 报告的每个 pull request 链接到正确的任务，把 worker 从已完成的任务上解开，并在其他工作已经交付 Queued 或父任务后关闭它们。worker 现在向其协调者报告状态。
- **Project 跟踪器里的父任务现在跟随其子任务。** 在 [Project](https://cursor.com/docs/agent/projects.md) 的任务跟踪器里，自己没有 pull request 或 Agent 的父任务，现在取其最活跃子任务的状态（先是 In Progress，然后 For Review，然后 Queued），并在每个子任务都完成后移到完成。状态在其子任务的每次变更时更新，因此 List 和 Board 视图不再显示一个落在子任务后面的父任务。
- **Project worker 可以查看任务跟踪器。** 在跟踪任务的 [Project](https://cursor.com/docs/agent/projects.md) 里，worker 和子协调者现在可以读取 Project 的任务跟踪器，查看其他任务的状态，但不能更改它。仍忙于后续消息的 worker，或属于队友的 worker，在回合结束时也不再从已关闭的任务上被解开。
- **Project worker 得到你可以稍后找到的短主题名。** 当 Project 协调者启动 worker 时，它现在按工作内容为每个命名，例如 “Webhook retry backoff”，而不是角色、动作或 PR 编号。只从 PR 或 issue 编号启动的 worker，会在其第一份报告说明工作内容后被重命名。

### 环境

- **从环境的仪表板页面管理其密钥。** Cloud Agents 仪表板里每个环境的页面现在有一个 [Secrets 分区](https://cursor.com/docs/cloud-agent/setup.md#environment-scoped-secrets)，你可以查看该环境的密钥并添加新的，除非其配置被锁定。
- &#x20;**管理员可以为 Cloud Agents 关闭个人密钥。** 团队管理员可以在 Cloud Agents [安全设置](https://cursor.com/docs/cloud-agent/settings.md#security-settings) 里打开 **Disable personal secrets**。之后成员不能添加或编辑个人密钥，他们已经添加的密钥也不再交给 Cloud Agents。成员仍可以删除现有的个人密钥。
- &#x20;**没有仓库的 Agent 在团队共享的默认环境上运行。** 当团队成员启动 Cloud Agent 时没有选择仓库或环境，Agent 现在在团队的默认无仓库环境上运行，而不是个人的隐藏环境。没有人可以编辑、重命名、重建、删除团队默认环境，或向它添加环境密钥，但个人和团队密钥仍适用于它。没有仓库的未命名个人环境会被锁定，直到你给它一个名字。
- **Cloud Agent 创建的新环境现在使用仓库的 `environment.json`。** 当 Cloud Agent 在还没有环境的单个仓库上启动时，它现在用仓库已提交的 [`.cursor/environment.json`](https://cursor.com/docs/cloud-agent/setup.md#configuration-in-code-with-environmentjson) 启动，而不是忽略它。新环境会继续跟踪该文件，因此后来的 Agent 和构建也会使用它。
- **在特定环境构建上启动的 Agent 使用该构建的配置。** 在特定 [环境构建](https://cursor.com/docs/cloud-agent/builds.md) 上启动的 Cloud Agent 和 Subagent，现在用为该构建记录的 `environment.json` 和设置启动，而不是仓库的当前文件或父 Agent 更新的环境版本。这使 Agent 的设置与它启动所依据的构建保持一致。
- **从 Builds 分区用 Agent 验证环境构建。** Cloud Agents 仪表板上环境的 Builds 分区，现在在 Trigger build 旁边有 Verify with Agent 链接。它在新标签里打开一个新 Agent，准备检查构建是否可用。当你可以触发构建且团队允许手动构建时，该链接会出现。
- **设置环境时可以选择与你共享的 Origin 仓库。** 当你创建 Cloud Agents 环境或编辑其仓库时，仓库选择器现在也会在你自己团队的仓库之后，列出你被授予访问权限的其他团队命名空间中的 Origin 仓库。这让你可以从仪表板构建跨命名空间的多仓库环境，而不只通过 API。
- &#x20;**更紧、更可靠的受限网络访问。** 网络出站受限的 Cloud Agents 不再默认到达每个 `*.cloudfront.net` 主机；只有拉取公共容器镜像所需的 CloudFront 主机保持允许，因此请把环境需要的任何其他 CloudFront 域名加到你的[网络允许列表](https://cursor.com/docs/cloud-agent/security-network.md#network-access)。托管在 Cursor 上的仓库在受限出站下现在克隆得更快，Agent 运行期间更改网络设置，现在会应用该 Agent 当前的环境允许列表，而不是过期的那个。
- **默认环境不再信任两个已被攻破的根证书。** Cloud Agents 的默认环境现在不再信任 emSign ECC Root CA - G3 和 AAA Certificate Services 根，因为它们之下的中间证书私钥已暴露。系统证书存储已更新，在默认镜像上 Java 信任存储会据此重建，因此经这些根签名的连接会失败，而不会被信任。

### Self-Hosted Machines

- **打开 Self-Hosted Machines 上 Agent 的实时工作区。** 在 Self-Hosted Machines 上运行的 Agent 现在可以从 Cursor 桌面应用打开，带上它们的文件、改动、终端和转发的端口。启动 Linux 机器时打开[实时工作区](https://cursor.com/docs/cloud-agent/self-hosted.md#live-workspace)。如果机器重启，打开 Agent 会重连到它。
- &#x20;**把自托管运行送到团队默认池。** 团队管理员可以在 Cloud Agents 仪表板的 Self-Hosted 分区选择 [**Default pool**](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#default-pool)，没有点名池或机器的自托管运行会去那里，而不是只按仓库路由。如果默认池服务于该运行的仓库，它会接过该运行。如果不是，另一个服务于该仓库的团队池会接过它，任意仓库的默认池接过其余的一切。仓库访问检查仍然适用。

### Web

- **Web 上的新 Agent 记录稿。** cursor.com/agents 上的 Cloud Agent 对话、pull request Agent 面板和侧边聊天，现在使用与 Agents Window 相同的记录稿。新 Agent 里的第一条消息会立刻出现，包括图片，在代码库页面的 Agent 侧边栏里，鼠标滚轮以及 Page Up 和 Page Down 会滚动记录稿。
- **pull request 页面上的 Fix with agent 会到正确的 Agent。** 在 cursor.com 的 pull request 页面上，Fix with agent 及类似操作现在会跟进打开该 pull request 的 Agent，或你在其上最近活跃的 Agent，而不是最新的那个 Agent。
- **Agent 页面在 Web 上更早显示其详情。** 当你直接打开 Cloud Agent 的链接时，其标题、状态和起始提示词会更早出现。

### 集成

- **Linear 会话链接到 Agent 及其 PR，PR 会关闭其 Linear issue。** [Linear](https://cursor.com/docs/integrations/linear.md) 里的 Cloud Agent 会话现在显示 Open in Web、Open in Cursor 和 Pull Request 链接，最终回复是不再出现两次的纯文本摘要。从 Linear issue 上启动的运行所产生的 pull request，现在以一行 `Fixes <ISSUE-ID>` 结束，因此 Linear 会链接该 PR，并在合并时关闭 issue。

### MCP

- **需要额外权限的 MCP 工具现在会请你授予。** 当 MCP 服务器因为你的登录缺少服务器点名的权限而拒绝 Cloud Agent 的工具调用时，Agent 会显示一张登录卡片，写出该工具及其所需权限，而不是失败。如果再次登录无法授予，Agent 会告诉你需要管理员批准。你以这种方式授予的权限会在下次登录时保留。
- **Gmail 和 Google Calendar 工具的修复。** 当 Gmail 对请求限速时，Agent 现在会按 Google 的要求短暂等待并重试该调用，而不是让后来的 Gmail 调用在冷却期内被阻止。当 Google Calendar 调用因为连接缺少权限而失败时，Agent 现在会告诉你重新连接账户，而不是显示原始权限错误。
- **连错账户时，Google Sheets 调用会恢复。** 如果 Google Sheets 工具调用因为正在使用的账户打不开电子表格而失败，Agent 现在会看到你其他已连接的 Sheets 账户，并可以用有访问权限的那个重试。

### 修复

- **Self-Hosted Machines 的修复。** 当 Self-Hosted Machine 在运行中途掉线时，Cloud Agents 现在会重连到它，或移到池里另一台空闲机器，而不是丢失该运行。
- **MCP 连接器的修复。** Slack、Google Sheets、Airtable 和 Jira 工具调用里常见的参数错误会被自动修复，登录团队所有的 MCP 服务器不再与你自己对同一 URL 的条目冲突。
- **环境构建的修复。** 环境有很多仓库时，Trigger build 对话框现在会滚动，使其按钮仍可到达。
- &#x20;**Cloud Agents 仪表板错误的修复。** Cloud Agents 页面上的团队偏好不再要求付费、Enterprise 或已合并的团队升级。预付余额用完时，错误现在会点名它，而不是指向按需用量设置。
- **Agent 存储的修复。** Project 成员可以打开指向 Project 存储的链接，在自己机器上运行的 Subagent 在 Checkpoint 或已完成回合之后仍能访问其 Project 存储。来自 Cloud Agent 机器的存储流量不再计入你自己的速率限制，Agent Stores 页面在加载失败时会显示服务器的错误消息。
- **Agent 处理 Origin pull request 方式的修复。** 当 Agent 检查 Origin pull request 上的 CI 时，仍在运行但已经失败的检查现在会被标出，因此 Agent 可以开始修复它们，而不必等待。当列表被截断时，合并冲突通知会说 “at least N” 条冲突路径，合并托管在别处的 pull request 现在会把 Agent 指向该主机的工具。Slack 线程 Agent 的 Subagent 所做的 commit 现在带同样的共同作者行，Agent 不再提交共同作者 git hook 创建的游离文件。
- **应用发布的修复。** 当 Agent 在应用文件夹之外提交，或其机器已关闭时，确认发布不再因过期而失败；机器现在会唤醒，确认会自动重试。新应用按其文件夹或仓库命名，而不是通用名称，首次发布不再等待缺失的密钥值。发布之后，Agent 会写出应用来自的仓库和文件夹并链接到它，不再说密钥变更正在发布对话框里等待。
- **Pages 的修复。** 已发布的页面在你重新发布、取消发布、重命名或更改地址后，会在下次加载时显示更新。代码块或评论提到 `import` 时，页面不再编译失败，图片在加载时用模糊预览保持形状。在团队共享受限的团队线程里创建页面，现在会显示你管理员的共享策略，而不是笼统的错误。

## Oct 19, 2026 当周

### 自动化

#### 触发器

- **Security Reviewer 在 GitHub 标签变更时运行。** 带 [Pull request label changed 触发器](https://cursor.com/docs/cloud-agent/automations.md#github-triggers) 的 Security Reviewer 自动化，现在会在匹配的标签被加到 GitHub pull request 或从其上移除时运行。以前它们从不运行。要把修复应用到现有自动化，打开它并保存。

#### 自动化运行

- &#x20;**自动化在有入站 IP 允许列表的团队上启动。** 有入站 IP 允许列表的团队上的个人自动化，不再因 “IP address is not on this team's inbound IP allowlist” 错误而失败。定时运行、webhook 投递和 CI 触发正常启动，Cursor 仍会检查你的登录和仓库访问。手动运行和 API 密钥 webhook 调用仍必须来自允许的 IP 地址。
- **运行和 webhook 的失败消息更清楚。** 当[由 webhook 触发](https://cursor.com/docs/cloud-agent/automations.md#webhook-triggers)的自动化无法启动时，webhook 响应给出与运行历史相同的失败标题和原因，而不是笼统的错误。被限速的运行会说明该限制可以适用于整个 Cursor，并建议短等之后再试。Agent 无法启动的运行会告诉你重试，如果持续发生则联系支持。
- **定时自动化在设定时间之后最多 5 分钟启动。** 每次[定时运行](https://cursor.com/docs/cloud-agent/automations.md#scheduled-triggers)最多可以晚于其间隔的五分之一启动，上限为 5 分钟。这适用于每个日程，不只是每小时和整点的。每 15 分钟运行的日程最多可以晚 3 分钟启动，每日日程最多晚 5 分钟。日程运行的频率不变。

#### 集成

- **连接 Linear 或关联 Jira 时的错误更清楚。** 如果你不是团队管理员，从 [Linear 触发器](https://cursor.com/docs/cloud-agent/automations.md#linear-triggers) 连接 Linear 会告诉你只有团队管理员可以连接它，而不是显示笼统的失败。关联你的 Jira 账户时，会说明你不在连接到该 Jira 站点的 Cursor 团队上，或该站点尚未连接到 Cursor 团队。

#### 修复

- **仓库选择器的修复。** 自动化编辑器里的仓库选择器把部分列表标为 Recent，在加载更多仓库时显示旋转指示，并在搜索失败时清楚说明。
- **PR Approver 和 pull request 创建的修复。** PR Approver 自动化在 `.cursor/approval-policies/` 里找到[路由策略](https://cursor.com/docs/approval-agents.md#routing-policies)，包括 `ROUTING.md`，以及 `.cursor/approvals/` 里针对特定审查者的标准。当 Cursor 找不到仓库时，打开 GitHub pull request 的自动化不再静默失败。Agent 会得到清楚的错误。

### 安全与批准 Agent

#### 安全 Agent

- **对更早发现的修复检测更准确。** 当你推送到 PR 时，[Security Reviewer](https://cursor.com/docs/security-agents.md) 现在会识别在被标记文件之外做出的修复，例如 PR 里别处调用方中的新检查，并关闭该发现。它只在修复出现在你的 PR 所更改的行里时才关闭发现，并在发布结果之前重新检查每个更早的发现。
- **模型列表短暂不可用时，安全审查不再在启动时失败。** 如果模型列表被限速或暂时不可用，审查会按退避重试，遵守服务器要求的等待，然后才放弃。

#### 批准 Agent

- **修复了因缺少规则评级而卡住的批准。** 修复了一个问题：[PR Routing & Approval](https://cursor.com/docs/approval-agents.md) 可能因为匹配的规则或其全局覆盖副本从未得到风险评级，而扣住 PR 的批准。规则及其覆盖副本现在共享一个评级，基于该规则所管辖的文件。

## Oct 19, 2026 当周

### 团队设置

- &#x20;**选择 Bugbot 是否自动审查公开仓库上的外部贡献者。** 管理员可以关闭对公开仓库上来自机器人和团队外人员的 pull request 的自动审查。使用 Bugbot [Team settings](https://cursor.com/docs/bugbot.md#configuration) 里的 **Public repositories** 设置。手动审查、私有仓库，以及来自团队成员的 pull request 与以前一样。现有团队会继续审查这些 pull request，直到管理员更改该设置，新团队开始时它是关闭的。
- &#x20;**修复了 Bugbot 跳过某些 Enterprise 团队的审查。** 对仪表板显示 Bugbot 已启用的 Enterprise 团队，Bugbot 不再以 “Bugbot plan is disabled for this team” 跳过 pull request 审查。在 Teams 方案上，Bugbot 仍遵循团队的 Bugbot 设置。

### 审查与检查

- &#x20;**团队达到机器人 pull request 的每日上限时，Bugbot 检查不再保持待处理。** 当团队对机器人账户打开的 pull request 达到每日审查上限时，Bugbot 会立刻完成其[检查](https://cursor.com/docs/bugbot.md#ci-check-statuses)并显示上限消息。在 GitHub、Bitbucket 和 Azure DevOps 上检查失败，因此必需的 Bugbot 检查会阻止合并，直到 Bugbot 审查该 pull request。在 GitLab 上它以中性完成。
- **Bugbot 会说明它何时无法推送 Autofix 改动。** 当你请 Bugbot 推送 [Autofix](https://cursor.com/docs/bugbot.md#autofix) 改动而推送失败时，Bugbot 会在 pull request 上回复原因，而不是保持沉默。回复会写出原因，例如合并冲突、分支保护、缺少权限、镜像仓库或合并队列锁。

### 修复

- **GitLab webhook 的修复。** GitLab 在反复出错后不再禁用 Bugbot webhook。Bugbot 会跳过没有 head commit 到达的合并请求事件，而不是在它们上面失败。当 Bugbot 拒绝一次 webhook 投递时，GitLab 的近期事件会显示如何修复：在项目的 Settings > Webhooks 里删除残留的 webhook，如果审查停止了，就为该项目关闭再打开 Bugbot。

## v2026.10.17

### Hooks 与 Subagents

- **工具后 Hooks 现在会为 Subagent 触发。** 匹配 `Task` 工具的 [`postToolUse`](https://cursor.com/docs/hooks.md#posttooluse) 和 `postToolUseFailure` Hooks，现在会在 Subagent 完成或失败时运行。以前只有 `preToolUse` 会为 Subagent 触发。Hook 返回的任何 `additional_context` 现在都会到达主 Agent，针对 `Task` 的 Claude Code `PostToolUse` 匹配器也能工作。
- **Subagent 里的 Hooks 得到正确的记录稿。** Subagent 记录稿现在保存在父对话的文件夹内，位于 `agent-transcripts/<parent>/subagents/` 下。在 Subagent 内部运行的 Hooks 现在会把该文件作为 [`transcript_path`](https://cursor.com/docs/hooks.md#common-schema) 和 `CURSOR_TRANSCRIPT_PATH` 收到。

### 自托管 worker

- **健康检查在关闭过程中跟踪自托管 worker。** 在以 `--management-addr` 启动的 worker 上，[`/readyz`](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#monitoring) 现在在 worker 关闭期间继续应答，返回 503 并带 `draining: true`，以便编排器停止向它发送工作。如果几次重试后仍无法绑定管理地址，worker 现在会无法启动，而不是在没有健康检查的情况下运行。池 worker 在丢弃过期认领后也会退出，以便你的主机替换它们。
- **自托管 worker 的修复。** 运行 `agent worker` 的 worker，在本地重构建把机器卡住几秒时不再掉线，短暂被提供给另一个 Agent 时也不再在会话中途退出。关闭终端或失去 SSH 会话现在会干净地关闭 worker，正在关闭的 worker 会拒绝新运行，而不是接受一个它无法完成的。

### Agent

- **修复了未回答的批准被报告为已取消。** 如果在工具调用仍在等待你批准时发送新消息，Agent 不再告诉你是你中断或取消了它。在暂停之前已经完成的工具调用，在 Agent 恢复时也不再运行第二次。

### Rules

- **Windows 上规则匹配的修复。** 即使工作区路径和规则路径只在盘符大小写上不同，嵌套的 `AGENTS.md` 文件、[规则](https://cursor.com/docs/rules.md) 和 Skills 现在也会在 Windows 上生效。

## 1.0.38

- **修复了本地 Agent 在只在 max mode 下运行的模型上失败。** 用需要 max mode 的模型变体创建的本地 Agent，例如 500k 上下文的选择，现在会自动发送 max mode，而不会因无效参数失败，并在后来的发送和恢复中保持它。`Cursor.models.list()` 用 `isMaxMode` 标记这些变体，`ModelSelection` 接受可选的 `maxMode`。
- **computer use 工具调用出现在运行事件里。** 当 Agent 使用 computer use 时，工具调用事件现在带有类型化的 `computerUse` 调用，而不是丢掉它。`args.actions` 列出每个动作（`click`、`type`、`scroll`、`key`、`drag`、`screenshot` 等），`result` 报告动作计数、时长、屏幕稳定时间，以及可选的截图路径或错误消息。
- **`zod` 现在是对等依赖。** `@cursor/sdk` 现在使用你自己安装的 `zod`（3.x 线中的 3.25 或更高，或 4.x），因此你的 schema 和 SDK 的共享一份副本。导出的 schema 用 `zod/v3` API 构建，两个受支持的版本都包含它。如果项目还没有 `zod`，请把它加到依赖里。
- **启动较慢的 stdio MCP 服务器可以花更长时间连接。** 本地 Agent 现在遵守 MCP 配置里 stdio 服务器上的 `startupTimeoutSec`，因此启动慢的服务器不再在固定的 60 秒连接限制上失败。你可以设到最多 5 分钟；没有该设置的服务器仍默认为 60 秒。
- **短暂的服务器中断不再使一次性 SDK 请求失败。** 当服务器报告一次可以安全重试的临时中断时，SDK 会在最多 1 秒的短随机延迟之后重发一次性请求一次，而不是立刻失败。Agent 运行这类流式调用绝不会被重放。
- **后台 Subagent 在启动它们的工具调用之后继续运行。** 当本地 Agent 在后台启动 Subagent 时，Subagent 不再在启动它的工具调用返回后立刻被取消。
- **修复了沙箱运行因不可执行的沙箱助手而失败。** SDK 的平台包现在会以执行权限安装沙箱助手。
- **修复了带命名云环境的 Python 云启动。** 从 Python SDK 用命名云环境、且没有仓库来启动 Cloud Agent，不再验证失败。

## v2026.10.15

### Agent 工具

- **Grep 和 Glob 搜索工作区之外的文件夹。** 当 Agent 要求搜索你打开的工作区之外的文件夹时，Grep 和 Glob 现在会搜索该文件夹。以前它们会悄悄搜索工作区，并从错误的地方返回结果。`.cursorignore` 和 Read 拒绝规则仍适用于工作区之外的文件，Glob 现在会在结果被截断时说出来。

### Cloud Agents

- **`agent env` 命令可用于你的个人环境和公开 ID。** `agent env get <repoUrl>` 现在显示你为该仓库保存的[个人环境](https://cursor.com/docs/cloud-agent/setup.md#environment-resolution-order)，每个都带其 `environment.json`，`agent env list` 首先显示每个环境的公开 ID，JSON 输出里也是如此。`agent env delete` 接受该公开 ID，并在你使用旧的数字 ID 时说明该传入什么。`agent env get` 不再接受 `--ref`。

### 自托管 worker

- **给自托管 worker 上较慢的 MCP 服务器更多时间。** 使用可重复的 `agent worker --mcp-tool-timeout server=seconds` 或 `server/tool=seconds` 选项，来提高或降低 stdio [MCP](https://cursor.com/docs/cloud-agent/self-hosted/my-machines.md#mcp-servers) 工具调用可以运行多久，而不是固定的 60 秒。worker 也遵守 stdio MCP 服务器的 `startupTimeoutSec`，因此启动慢的服务器不再在 90 秒后被切断。
- **Windows worker 会说明何时缺少 Git Bash。** 在 Windows 上，`agent worker start` 现在会退出，并说明 worker 需要 Git for Windows，包括 Git Bash，以及去哪里安装，而不是稍后失败。同一条消息会追加到 `%LOCALAPPDATA%\cursor-agent\worker-startup.log`，`agent worker --help` 会列出该要求。

### 修复

- **Agent 搜索、读取、编辑和 shell 工具的修复。** 超时的搜索现在保留部分结果，无效的正则或空模式会得到清楚的错误，而不是匹配一切。偏移很大的读取不再结束会话，编辑不再只静默更改几个重叠匹配中的第一个。shell 输出不再把 stdout 和 stderr 行混在一起，等待后台命令会跟踪它，而不是睡满整个超时。
- **Subagents 的修复。** 当 Subagent 失败时，Agent 现在会看到 Subagent 的 ID 和失败原因，而不是笼统的错误。被要求使用带多余 `cursor-` 前缀或不存在的 `-fast` 后缀的模型名的 Subagent，现在会运行所请求的模型，而不会报错或切换到 Composer。恢复失败的 Subagent 会保留其原始类型和模型。
- **长会话、内存和摘要的修复。** 长会话使用更少内存：非常长的待办列表，以及在后台摘要期间结束的回合，不再使 Agent 膨胀，如果摘要无法在上下文限制附近启动，回合也不再失败。摘要之后的上下文用量更准确，过大的网页搜索结果和失控的工具调用流不再溢出上下文，停止 Agent 会保留已经返回的工具结果。
- **自托管 worker 的修复。** Agent 存储打开缓慢时，worker 不再使 Agent 认领超时，启动它的进程退出后，它们仍会识别正在运行的 Cursor 服务器。同一台机器上另一个 worker 在下载期间安装相同的服务器构建时，worker 也不再失败。
- **Windows 上 Hooks 和 shell 命令的修复。** 失败或指向找不到的命令的 Hooks，现在被报告为失败，而不是静默成功。Agent 在 Windows 上也不再尝试对 shell 命令使用 tmux。
- **文件建议、已归档 Agent 和本地地址的修复。** 在 macOS 上从主文件夹运行 CLI，在构建 `@` 文件建议时不再对 Desktop、Documents 或 Library 这类受保护文件夹触发权限提示。给已归档的 Agent 发消息现在会清楚说明，而不是显示笼统的未找到错误，提到 “archived” 的无关错误不再被误报。`[::1]` 这类 IPv6 主机现在被识别为环回，完整的链路本地范围被视为私有。

## 3.25

### 浏览器与 Design Mode

- **在 Design Mode 里更像浏览器 DevTools 那样编辑 CSS。** Design Mode 笔记里的 CSS 检查器现在像浏览器 DevTools 一样工作。用上、下方向键步进一个值（按住 Shift 则步长为 10），拖动数字来擦洗它，或输入像 `8` 这样的纯数字得到 `8px`。颜色和长度行会建议页面自己的 CSS 变量，显示的值现在来自浏览器的真实级联，包括嵌套规则、采纳的样式表和跨源样式表。
- **在 Design Mode 里用方向键选择父元素。** 在 [Design Mode](https://cursor.com/docs/agent/design-mode.md) 里悬停时，按 Up 把高亮移到父元素，按 Down 返回，然后点击选中它。提示条显示该快捷键，选中文本元素周围的选择环现在略微离开文字，而不是贴着字母。
- **开发服务器恢复后，浏览器标签会自己重新加载。** 指向已停止响应的本地端口的浏览器标签，现在会在终端命令或 Agent 的后台 shell 再次在该端口上提供服务时重新加载，因此你不必在重启开发服务器后手动刷新。新的 Reload Browser Tab 命令（Cmd/Ctrl+R）会重新加载活动标签。

### 导航与布局

- **更干净、更快的命令面板。** Cmd+K 面板现在提供 Select Model、Archive 和 New Side Chat。粘贴 pull request URL 或输入 pull request 编号，即可直接跳到该 pull request。行适合单行显示，命令旁边显示的快捷键与实际运行它们的一致，当你有很多 Agent 时，面板打开和搜索更快。即使面板开着，Ctrl+Tab 现在也会打开最近 Agent 切换器，它使用与面板相同的外观。
- **Back 会重新打开你刚刚关闭的标签。** 在 Agents Window 里关闭标签之后，例如文件、终端、浏览器、Changes、Output 或侧边聊天，按 Back 会把它带回原来的位置，导航历史保持完整。Cmd+Shift+T 会聚焦 Back 已经恢复的标签，而不是打开一个重复的。在聊天弹出窗口里按下的鼠标侧键，也不再导航主窗口。

### 文件、改动与 pull request

- **在文件标签里播放音频文件，并滚动完整的 PDF。** 在文件标签里打开 .mp3、.wav、.ogg、.aac 或 .opus 这类音频文件，现在会显示内置播放器，而不是二进制占位，切换离开时它会暂停。PDF 现在连续滚动每一页，工具栏显示当前页，并带在页面之间跳转的按钮，缩放会保持你的位置。CSV 预览的行号列也不再让滚过的单元格透出来。

### Cloud Agents

- **Self-Hosted Machines 上的 Agent 在侧边栏里按机器分组。** 侧边栏现在把在 [Self-Hosted Machines](https://cursor.com/docs/cloud-agent/self-hosted/pool.md) 上运行的 Agent 按每台机器或每个池分成一个分区，即使 worker 还没接过运行，也标上机器名。Agent 在刷新、分叉和其他窗口之间保持你选择的机器；对于无法提供实时改动的 worker，Changes 标签会说明原因，而不是一直转。
- **把文件夹加到 Project 允许的机器。** 在 Project 的 Allowed machines 菜单里，Machines 列表现在以针对这台计算机的 Add folder 行结束。选一个文件夹，Agents Window 会为它启动 worker 并允许它用于该 Project，显示 Starting，然后是 Allowing，直到完成。
- **Cloud Agent 的转发端口更可靠。** 在编辑器面板的转发端口菜单里，无法到达的 Cloud Agent 端口现在会显示红点和错误，例如超时的连接，直到连接成功。打开第二个云工作区不再破坏第一个的端口转发，关闭 Auto-Forward Ports 现在会关闭已自动转发的端口，并防止它们再次被转发。
- **修复了云环境配置里错误的 Dockerfile 路径错误。** 编辑 `environment.json` 现在会对照云构建所使用的路径检查 `build.dockerfile` 和 `build.context`，这些路径从仓库的 `.cursor` 文件夹解析。虚假的 Dockerfile 缺失错误消失了，缺失的构建上下文文件夹会被标出，设置环境的 Agent 会写出能正确构建的路径。

### 自定义与设置

- &#x20;**AWS Bedrock 上的 Claude Opus 5.5 和 Sonnet 5.5。** 通过 [AWS Bedrock](https://cursor.com/docs/customizing/aws-bedrock.md) 路由模型的团队，现在可以在 Agents Window 的模型设置里选择 Claude Opus 5.5 和 Claude Sonnet 5.5。

### 隐私与安全

- **git 和 GitHub 命令里对分支名的更安全处理。** 修复了一个问题：包含 shell 字符的分支名或 commit ref 可能在 pull request 查找、CI 重跑或分支 diff 期间运行意外命令。不安全的名称现在会跳过 GitHub CLI 查找，diff 会回退到与 origin/main 比较。

### Agent 与聊天

- **把记录稿里的链接和媒体加到聊天。** 右键对话里的 pull request、Agent、项目任务、Canvas、文件、文件夹或行范围链接，选择 Add to Chat 把它作为芯片插入，或其他 URL 的纯链接。图片、视频和文本工件卡片也在 Download、Copy Image 或 Copy Text 旁边提供 Add to Chat，网页搜索结果、终端引用和思考块现在打开相同的链接菜单。
- **加到聊天的链接变成紧凑芯片。** 对 Agent 回复里的 GitHub issue、commit、Linear issue 或网页链接使用 Add to Chat，现在会插入带图标的短标签芯片，而不是原始 URL。完整 URL 仍会发给 Agent，并在悬停时显示，点击芯片会打开链接。
- **提示词里的内联代码格式。** 你在提示词里输入或粘贴在单反引号之间的文本现在显示为内联代码，并连同反引号一起发送。转换之后立刻按 Backspace，即可恢复纯反引号。
- **从 `#` 菜单按编号查找任意 pull request。** 在提示框的 `#` 菜单里，输入不在列表中的 pull request 编号现在会查找并添加它，搜索时显示加载状态，没有匹配时显示 “No matching pull requests”。你片刻前在 Origin 上打开的 pull request 也会在数秒内出现在菜单里。
- **Agent 工作时的实时状态更清楚。** 当 Subagent 需要你的批准或回答时，主对话的状态行现在会说 “Waiting for approval” 或 “Waiting for response”，并显示它在问什么。每个完成的回合显示 Agent 工作了多久，回合结束时记录稿不再跳动，另一个 Agent 发送的消息会写出发送它的 Agent。
- **Cmd/Ctrl+F 在 Canvas 内可用。** 在 Canvas 里按 Cmd/Ctrl+F 会打开其查找栏，如果已经打开则把焦点放回栏中，而不丢失当前匹配。

### 修复

- **Annotate 的修复。** Escape 现在会退出 Annotate（或关闭模型警告），而不会折叠应用面板或丢掉你的笔记，听写会进入聚焦的笔记。元素和区域截图在缩放的标签上框住正确区域，滚动笔记或 CSS 检查器不再滚动页面，图钉悬停卡片不再挡住选择其下的元素。笔记在调整大小时不再抖动，离开后批注光标不再滞留，起草评论时点击已保存的笔记会先问是否丢弃草稿，然后再打开它。
- **在 Agent 之间切换和键盘焦点的修复。** 回到 Subagent 不再清除 Forward 历史，Cmd-N 会可靠地把光标放进 Agent 的提示词，而不会在打字中途跳走。快捷键不再到达隐藏的 Agent、全屏 Apps 后面隐藏的聊天，或从聊天弹出窗口到达主窗口。启动 New Agent 之后立刻打开的文件和 pull request 现在保持打开。
- **提示词上下托盘的修复。** 新 Agent 屏幕上的通知现在一次显示一个，而不移动 Composer，在阻挡性托盘上按 Escape 不再打开 Browser 窗格。短 Agent 磁贴里的托盘留在磁贴内部，而不会与标签重叠，你正在其中打字的托盘被盖住时焦点回到提示词，你打开或关闭的托盘在切换 Agent 时保持那样。
- **文件和编辑器面板的修复。** 从命令行打开尚不存在的文件路径，现在会打开一个保存到该路径的新编辑器，而不是一个加载失败的标签。在 macOS 上，当工作区包含 Desktop、Documents 和 Downloads 这类受保护文件夹时，后台文件监视和扫描不再进入它们，因此你不再收到访问提示。工作区连接时，编辑器面板显示一个小旋转指示：悬停查看状态，或点击查看远程连接输出。
- **窗口外观的修复。** 在 macOS 上，在浅色和深色主题之间切换时，窗口的半透明背景色调现在会更新。对话旁边折叠的应用轨道在你滚动记录稿时不再变淡。
- **中断通知的修复。** 侧边栏状态卡片现在显示来自 Cursor 状态页面的真实事件名称和最新更新，并在事件解决时说出来。Run on 选择器里的 Cloud 选项只反映 Cloud Agents 事件，没有额外的警告文本。
- **Cloud Desktop 标签的修复。** Cloud Desktop 标签更高效地绘制屏幕更新，并在选中另一个标签时停止流式传输。当你切换回来时，它会用新连接可靠重连，而不是一个可能已过期的连接。
- **语音通话的修复。** 与 Cloud Agent 的语音通话记录稿现在在通话进行时同步，因此其他设备会在通话中途显示回合。另一台设备上的通话卡片不再闪出虚高的时长，其他 Agent 里的返回条在重命名后显示被呼叫 Agent 的当前标题。语音正在启动时关闭窗口不再使语音服务崩溃。
- **Agent 记录稿的修复。** 从另一台设备发送的消息不再在记录稿里重新挂载或落在错误位置。断开期间写出的 Cloud Agent 输出在重连后出现，而不会丢失。
- **提示框菜单的修复。** 许多命令匹配时 / 菜单不再迟滞，选择 /add-plugin 之后打字不再冻结输入。方向键现在在 @ 和 + 菜单里环绕，在打开的菜单内右键不再打开第二个菜单，很小的附加图片现在以可读大小预览。
- **@ 提及菜单的修复。** 迟到的结果不再移动你已高亮的行，结果加载时行不再闪烁，慢的来源仍会出现。对 Cloud Agent，菜单会立刻显示 Agent 最后已知的文件，文件列表加载后自行刷新，并且只建议 Agent 机器上有的文件夹。
- **多文件夹工作区里 File Blame 的修复。** 当工作区有多个文件夹时，File Blame 现在从拥有该文件的仓库读取历史，而不是总是使用第一个。
- **审查改动的修复。** 打开很大的 GitHub pull request 进行审查不再冻结窗口。在触及很多或很大文件的长聊天上审查 Agent 的改动，使用更少内存并保持响应。
- **被阻止的 pull request 合并的修复。** diff 标题里的合并按钮现在会说明合并为何被阻止：分支必须重新堆叠到其基线时显示 “Needs restack”，冲突检查仍在进行时显示 “Checking for conflicts…”。

## 3.25

### Cloud Agents

- **Self-Hosted Machines 上的 Cloud Agent 留在你选择的机器上。** 在特定 Self-Hosted Machine 上启动的 Cloud Agent，在 Agent 列表刷新以及你分叉它们之后仍保持该机器，因此后续消息在同一台机器上运行。
- **Cloud Agent 和远程窗口的转发端口更可靠。** 得不到应答的 Cloud Agent 转发端口连接现在在 20 秒后关闭，而不是挂起并阻塞其他端口，打开第二个云工作区不再破坏第一个的端口转发。关闭 `remote.autoForwardPorts` 后，重新打开远程窗口时不再恢复曾自动转发的端口。
- **修复了 Cloud Agent 环境文件里错误的 Dockerfile 路径错误。** 编辑 `.cursor/environment.json` 现在会对照云构建所使用的相同路径检查 `build.dockerfile` 和 `build.context`，这些路径从仓库的 `.cursor` 文件夹解析。虚假的 “Dockerfile does not exist” 错误消失了，缺失的构建上下文文件夹现在会被标出，悬停文本会说明路径规则。参见 [cloud agent setup](https://cursor.com/docs/cloud-agent/setup.md)。

### 自定义与设置

- **切换团队后 Agent Store 同步继续工作。** 只要你仍是创建该存储的团队的成员，更改活动团队时，本地 Agent Store 不再停止同步。当同步被短暂拒绝时，Cursor 现在会等待建议的间隔再重试。
- **Skill 警告和更清楚的 Skill 菜单。** Customize > Skills 现在会在名称、描述或正文超过 Agent Skills 限制的 Skill 上显示警告图标；悬停阅读要修复的内容，或点击复制。同名 Skill 在 / 菜单里显示为单独的行，而不是折成一个，打开窗口后 Skill 会更早出现在那里，已发送消息里的 /skill 提及保持高亮。
- **Delete Old Chats 回收磁盘空间。** 运行 **Developer: Delete Old Chats** 之后，Cursor 会提供 **Rewrite Now**，把本地数据库里腾出的空间还给磁盘。清理也会移除已删除聊天留下的消息数据，而不冻结编辑器，已删除的聊天，包括从 Cloud Agent 移到本地的，不再重新出现在聊天列表里。
- **YAML 键在 Cursor Dark 主题里更突出。** 在 Cursor Dark、Cursor Dark High Contrast 和 Cursor Dark Colorblind 里，YAML 键现在有自己的颜色，而不是与值相同。布尔值和 `null` 现在使用常规文本颜色，因此 `paused: false` 不再看起来像字符串。
- **View in Cursor 打开你正在看的插件。** 在插件的市场页面上点击 View in Cursor，现在会在 Customize 里打开该插件的页面，而不是一般的插件设置。

### Agent 与聊天

- **新安装默认引导正在运行的 Agent。** 在全新安装上，Agent 运行期间发送的消息，包括从队列手动发送的，现在会引导它，而不是排队或中断，Steer 在 Settings 里列在第一位。现有安装保持当前行为。
- **把聊天里的文件链接加回 Composer。** 右键 Agent 回复里的文件链接，选择 Add to Chat，把该文件附到你当前的消息。文件和链接菜单现在也在思考块和你自己消息里的文件芯片上工作，点击已发送消息里的聊天提及会打开该聊天。
- **Agent 提示词里的内联代码格式。** 你在 Agent 输入里输入或粘贴在单反引号之间的文本现在显示为内联代码，并连同反引号一起发送。转换之后立刻按 Backspace，即可恢复纯反引号。
- **Agent 聊天里更清楚的实时状态。** 当 Subagent 需要你的批准或回答时，主聊天的状态行现在会说 “Waiting for approval” 或 “Waiting for response”，而不是 “Waiting for subagent”。回复完成时记录稿也不再跳动，另一个 Agent 发送的消息会写出发送它的 Agent。
- **后台 shell 在回合之间继续运行，归档聊天会停止它们。** Agent 启动的开发服务器和其他后台 shell，在回合结束以及聊天的工作区文件夹或 worktree 改变后继续运行，因此它们仍会在下一回合应答。归档聊天会停止它们，而不是让它们继续运行。
- **更干净、更紧凑的 Agents 侧边栏。** Agents 侧边栏里的 Agent 行现在只占一行，因此一眼能放下更多 Agent，它们可以显示每个 Agent 上次更新的时间。新的 Search 行会在原地打开聊天历史选择器，取代内联搜索框，行的键盘快捷键只在悬停时出现。

### 隐私与安全

- &#x20;**Subagent 尊重你的 Private Inference 模型选择。** 使用 Private Inference 时，Subagent 不再在你于 Settings > Models 里关掉的模型上运行。固定到已关闭模型的自定义 Subagent 改在父 Agent 的模型上运行。
- &#x20;**git diff、链接和仓库阻止列表的安全加固。** 查看 diff、blame 或历史不再运行仓库配置的外部 diff 或 textconv 命令，因此打开不受信任的仓库不能执行代码。链接保护里的通配受信任域名不再匹配形似的主机，列出目录的 Agent shell 命令现在尊重管理员的仓库阻止列表，包括 `**` glob。

### 修复

- **无障碍修复。** 聊天里的图片可以用 Tab 到达，并用 Enter 或 Space 打开，屏幕阅读器现在会按页面标题命名 Agent 状态图标、计划的 Build 选项菜单和内置浏览器。在 Cursor Settings 里，Partial Accepts 工具提示显示实际的键绑定，Open 和 Invite 按钮有名称，Tab 会在 Remove 之前到达模型的开关。
- **更顺畅的 Cloud Desktop 查看器。** Cloud Agent 的 Cloud Desktop 查看器更高效地解码屏幕更新，使用更少 CPU。
- **Agent 聊天滚动和渲染的修复。** 长 Agent 聊天在你滚动时不再跳动，加载很大的 Cloud Agent 对话不再阻塞编辑器。流入的消息留在同一个气泡里，用户消息不再显示内部自动化标签，代码块、列表和公式不再在消息之间增加额外空隙。
- **Agent 工具的修复。** 当模型向 Shell、Grep 或 Read 发送超出范围的值时，Agent 会话不再结束，长待办列表不再使 Agent 崩溃。Glob 现在能找到空文件和二进制文件，编辑不再只更改几个重叠匹配中的第一个，网页搜索和模式切换调用会在请求批准之前出现在聊天里。
- **Agent 聊天输入菜单的修复。** 许多命令匹配时 / 菜单不再迟滞，选择 /add-plugin 之后打字不再冻结输入。方向键现在在 @ 和 + 菜单里环绕，关闭 + 菜单会把焦点还给 + 按钮，图片预览里的小图片会被放大以便可读。
- **聊天里 @ 提及菜单的修复。** 迟到的结果不再移动你已高亮的行，结果加载时行不再闪烁，慢的来源仍会出现。对 Cloud Agent，包括编辑已发送消息时，菜单会立刻显示最后已知的文件，文件列表加载后刷新一次，并且只建议 Agent 机器上有的文件夹。
- **Review Changes 的修复。** 在 Review Changes 里打开很大的 GitHub pull request 不再冻结编辑器。Review Changes 在触及很多或很大文件的长 Agent 聊天上也使用更少内存并保持响应。
- **加载本地媒体文件的修复。** 本地视频和音频文件现在在编辑器里正确播放和定位。磁盘上任何位置的 AVIF、ICO、WebM 和 M4V 文件现在会在预览中加载，符号链接的主目录或扩展文件夹下的文件不再被拒绝。
- **从预览保存 Markdown 文件的修复。** 从 Markdown 预览编辑器保存 Markdown 文件而不编辑它，不再重写其格式。在预览里做的编辑不再移除文件末尾的换行，也不再在尾随的列表、代码块、表格或标题之后加空行。
- **模型设置的修复。** Settings 里的 Explore Subagent Model 帮助不再同时显示两个工具提示，更改选择时模型选择器不再横向移位。

## Oct 12, 2026 当周

### Web

- &#x20;**用规则集按分支限制合并方法。** 在仓库的 [Settings > Rules and Protections](https://cursor.com/docs/origin/settings.md#rules-and-protections) 里，规则集对话框有一个新的 **Restrict merge methods** 选项，把匹配分支上的 pull request 限制为合并 commit、squash 合并，或两者。合并按钮只提供目标分支允许的方法。当没有方法可以落地时，pull request 会显示一个阻止项，写出分支、涉及的规则集以及如何修复，而不是在合并时才失败。
- **选择自动合并如何落地你的 pull request。** 当你在 [pull request](https://cursor.com/docs/origin/pull-requests.md#pull-request-page) 上启用自动合并时，现在可以选择 squash 或合并 commit，你编辑的 commit 标题和消息会在落地时使用。按钮显示你的选择，例如 “Enable auto-merge (squash)”，自动合并打开后，合并弹出框会显示所选方法和 commit。
- &#x20;**用最大文件大小规则阻止大文件。** 在仓库的 [Settings > Rules and Protections](https://cursor.com/docs/origin/settings.md#rules-and-protections) 里，你现在可以创建带 **Maximum file size** 限制的 **Repository** 规则集，上限为 100 MB。Origin 会拒绝任何添加超过该限制的文件的推送，并写出过大的文件。规则集列表按限制摘要每条规则，例如 “Files up to 5 MB”。该规则也可通过 Origin API 和 Go SDK 使用；详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- &#x20;**合并前要求特定组的批准。** 在仓库的 [Settings > Rules and Protections](https://cursor.com/docs/origin/settings.md#rules-and-protections) 里，规则集现在可以要求特定组的批准，每组有自己的最少批准成员数，从 **Add group** 菜单选择。因此被阻止的 pull request 会显示一个点名该组的阻止项，写出它还需要多少批准、已经有多少，并建议向该组请求审查。组行显示组的名称、类型和成员数，无法命名的组读作 “Deleted group” 或 “Group outside this organization.”
- &#x20;**在规则集里选择代码所有者如何审查 pull request。** 在 [Settings > Rules and Protections](https://cursor.com/docs/origin/settings.md#rules-and-protections) 里，规则集编辑器现在有带 Off、Classic 和 Agentic 选项的 **Code owner review** 设置。它取代 “Require review from code owners” 开关。Classic 要求文件所有者对它们拥有的文件给出一次批准。Agentic 仍强制 CODEOWNERS，但当 Origin 的 Agent 在 pull request 上审查所有权时，其决定取代批准计数。当变更被阻止时，Origin 会显示哪些所有者需要批准，以及针对哪些文件。
- &#x20;**应用可以成为代码所有者。** 你可以在 `ORIGIN_CODEOWNERS` 文件里用 `app_<id>` 把应用命名为代码所有者，来自该应用的批准满足它拥有的路径，前提是该应用安装在仓库的命名空间上。在 pull request 页面上，应用所有者显示其名称和头像。未安装的应用以淡化显示，并带 “Not installed” 提示，codeowners 预览会说明它们为什么不能批准。
- &#x20;**保存规则集不再覆盖别人的更改。** 在 [Settings > Rules and Protections](https://cursor.com/docs/origin/settings.md#rules-and-protections) 里，如果有人在你打开规则集之后更改了它，保存现在会先请你重新加载。描述可以跨多行，被规则集阻止的推送会写出该规则集。活动规则集现在始终被强制执行，即使在有很多已禁用规则集的仓库里也是如此。
- &#x20;**pull request 带着绕过合并时，看出哪些规则被跳过。** 当规则集绕过列表上的人在未满足其全部规则的情况下合并 pull request 时，该 pull request 的活动现在会显示类似 “merged, skipping 2 rules” 的条目，列出每条被跳过的规则，例如必需的审查或必需的状态检查。绕过也会记录在审计事件里。
- **大型 pull request 加载每个文件。** Files changed 视图现在会在大型 pull request 上继续加载文件，而不是停在 500，包括比较两个版本时。如果列表仍然提前停止，一行会告诉你显示了多少文件。
- **把 Agent 提示词作为 Origin CI 作业和步骤运行。** 你现在可以给 Origin CI 作业或步骤一个 `prompt`，以及可选的 `model`，Cursor Agent 会在该作业的工作区里执行它，在那里运行 shell 命令并读取、编辑和搜索文件。Agent 可以把检查标为中性或跳过，而不是通过或失败，中性检查不会使整体 CI 状态失败。提示词步骤以你团队的 Origin CI 服务账户运行，因此仓库的命名空间需要一个团队所有者。
- **Origin CI 作业有更灵活的输入和更大的输出。** 在 `origin.ci.ts` 里，作业现在可以用 `inputs: ["**"]` 或 `glob("**")` 使用整个仓库。输入或输出路径上的前导 `/` 表示仓库根，即使在嵌套的包配置里也是如此，`exists()` 和 `glob()` 现在检查 commit 里的文件。作业可以发布和缓存最多 64,000 个文件的输出，包含符号链接的输出会发布而不是失败，无效的符号链接会使检查写出有问题的路径。
- **CI 作业页面显示作业在等什么。** 排队的 CI 作业现在会说明它在等哪些上游作业。作业的命令完成后，其状态读作 Publishing outputs，直到其输出被保存。作业页面链接到上游作业和需要这个作业的作业，作业日志以一行已上传输出的摘要结束：文件数、大小和上传时间。
- **在列表搜索里排除、按日期筛选和按标签筛选 pull request。** 在仓库的 pull request 列表上，你现在可以用 `-author:@me`、`-label:bug` 或 `-is:draft` 这类否定限定符排除结果，并用 `created:`、`updated:`、`merged:` 和 `closed:` 按日期筛选，使用日期、比较或 `merged:2026-09-01..2026-09-30` 这类范围。行现在显示其标签，点击一个会把它加到搜索里；`label:a,b` 匹配任一标签。已合并和已关闭的行也会说明它们何时合并或关闭，在大型仓库里加载更多页更快。
- **pull request 上的键盘快捷键表。** 在 pull request 页面上按 `?`，或从溢出菜单选择 Keyboard shortcuts，即可看到每个可用快捷键，包括切换标签和在 diff 中移动的键。
- **丢弃待处理的审查。** 当你在 pull request 上有待处理的审查评论时，pull request 标题里的审查菜单现在提供 Discard review。确认之后，Origin 会删除你待处理的审查及其全部草稿评论。
- **代码库文件视图里的符号导航和 Raw 按钮。** 当你在宽屏上[浏览文件](https://cursor.com/docs/origin/browse.md)时，悬停符号可看其签名，或点击跳到其定义和引用。文件视图在复制和下载旁边也有 Raw 按钮，在新标签里打开文件的纯内容。
- **更可靠的 code tours。** 生成失败的 code tours 现在会自动重试一次，如果默认导览中途失败，你会看到已保存的导览，而不是错误。在整个栈的导览里，每一章显示该章 pull request 所做的更改，Files you own 导览会在你切换比较时跟随你拥有的文件。当 Cloud Agent 编辑 pull request 的导览时，你会看到该编辑，而不是你更早生成的导览。
- **跳到 pull request 作者的 Cursor 个人资料。** 在 pull request 页面上，时间线、评论线程和悬停卡片里的作者名字和头像，现在会在他们有公开 Cursor 个人资料时链接到它。pull request 栈里的作者现在以与 pull request 本身相同的方式出现，页面更新时个人资料链接不再消失。
- &#x20;**应用直接推送时的规则集绕过更清楚。** 规则集 **Bypass list** 上的应用，在使用自己的安装 token 时，现在可以直接推送到阻止直接推送的分支。用某人 token 推送的应用不再得到那个人的绕过，因此它必须打开 pull request。
- **更可靠的 Git LFS 下载，文件缺失时错误更清楚。** 在[从 GitHub 镜像的仓库](https://cursor.com/docs/origin/mirror-github.md)里，当文件还不在 Origin 中时，Git LFS 下载现在会回退到 GitHub。当文件在所有地方都缺失时，错误会写出对象，并说明如何用 `git lfs push` 上传它，或用 `GIT_LFS_SKIP_SMUDGE=1` 跳过它。如果 GitHub 对下载限速，会告诉 Git LFS 稍后重试。
- **由推送触发的自动化现在知道是谁推送的。** 由向 Origin 仓库的推送触发的自动化，现在把推送者作为行动者，与 pull request、评论和标签事件已经做到的一样。基于行动者的筛选现在会匹配有效的推送，而不是跳过它们。如果推送者被隐藏或无法解析，自动化仍不会运行。

### API

- **从 Origin API 管理自动合并。** 你现在可以通过 Origin API 为 pull request 打开或关闭自动合并，查看是谁打开的以及它将如何合并，并在自动合并打开或关闭时收到 webhook 事件。这些端点和事件处于预览。详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **通过 Origin MCP 服务器创建仓库而不选择所有者。** `create_repository` [MCP 工具](https://cursor.com/docs/api/origin/mcp.md) 现在可以在没有所有者的情况下工作：Origin 会在第一次为你设置个人命名空间，并在那里创建仓库。这需要符合条件的方案，以及允许代码存储的 Privacy Mode 设置。团队成员和应用 token 仍需要传入所有者。
- **草稿、作者变更和仓库标签的新 webhook 事件。** Origin 应用可以订阅 `pull_request.unpublished`（pull request 被移回草稿时发送）和 `pull_request.commandeered`（有人接管 pull request 的作者身份时发送，载荷中有先前的作者）。对仓库标签有读权限的应用也可以订阅 `repository.label.created`、`repository.label.updated` 和 `repository.label.deleted`。暂停或撤销应用现在会立刻停止安装 webhook 投递，审查者 webhook 不再以空审查者发送。载荷详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **通过 Origin API 给 pull request 加标签时创建缺失的标签。** 通过 Origin API 和 Origin SDK 添加或设置 pull request 标签，现在接受 `create_missing`，因此自动化可以应用仓库里尚不存在的标签，而不必单独创建。现有标签按名称匹配，不区分大小写，创建标签需要管理标签的权限。没有该选项时，错误现在会列出每个缺失的标签名。详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **Origin MCP 工具接受 GitHub 风格的参数。** 为 GitHub MCP 服务器编写的 Agent 和脚本，现在可以针对 [Origin MCP 服务器](https://cursor.com/docs/api/origin/mcp.md) 运行，错误更少。工具接受 `owner/name` 字符串、`full_name`、嵌套的仓库对象，以及代替 `sha` 的 `ref`。树、commit 和 pull request 读取工具在没有 `sha` 或视图时也能工作，默认分别为 `HEAD`、元数据和摘要。

### 修复

- **Origin CI 的修复。** 包含已提交符号链接的检出不再失败或丢掉文件，带 `?` 或 `{a,b}` 的输入 glob 现在会匹配你期望的文件。重新运行推送检查会运行该检查的 commit。没有区域的 `ecrLogin` 现在使用部署的区域（或本地的 `$AWS_REGION`），而不是静默默认为 us-east-1。
- **diff 里的生成文件标记尊重 `linguist-generated=false`。** 在 `.gitattributes` 里标为 `linguist-generated=false` 的文件，不再在 pull request diff 里显示为已生成。
- **Git 推送和仓库读取的修复。** 带很多通配符的规则集会被推送快速评估，重建的仓库不再提供旧仓库的 ref，消息很大的 commit 现在会返回其元数据。
- **团队和命名空间设置的修复。** 有成员但没有席位的已合并团队不再被视为不活跃。自动合并不再停滞，审查提醒和 Slack 私信会到达，团队的隐私模式会生效。更多方案现在可以写入其个人命名空间而不出现升级提示，自动命名空间设置会在你偏好的名称已被占用时选择一个可用名称。
- **pull request 页面不再显示来自不同基线的检查。** 针对不同基线报告的检查不再出现在 pull request 页面上，包括实时更新到达时或基线移动之后。

## Oct 12, 2026 当周

### Projects

- **Project 协调者可以在你的代码所在的任何地方启动 worker。** Project 协调者可以列出你的环境和仓库，然后在持有代码的环境里启动 worker，或使用其默认设置直接在仓库上启动，即使 Project 自己没有仓库。Project 之外的环境和仓库需要你从一张点名它们的卡片批准，已批准的会出现在 Project 的允许列表里，你可以在那里撤销它们。
- &#x20;**Project Agent 在使用新机器或环境之前会询问。** 对每个团队，Project 的 Agent 现在会在第一次使用 Self-Hosted Machine、池或环境时请人批准。团队管理员可以在仪表板的 Cloud Agents → Security 下打开 [Automatically allow Projects to place their work](https://cursor.com/docs/cloud-agent/settings.md)。你已经授予的批准仍然适用，对仓库的批准也覆盖其 GitHub 或 Origin 镜像。

### Agents

- **在 Agent 结束时发送的消息会在同一次运行中得到回答。** 如果你在 Cloud Agent 即将收尾时给它发消息，Agent 会把它作为多一步接过来，而不是排成单独的后续。
- **从短 commit SHA 启动 Cloud Agent。** 你可以从 7 个或更多字符的缩写 commit SHA 在 GitHub 仓库上启动 Cloud Agent，就像 `git log --oneline` 打印的那些，而不会得到 “branch or commit not found” 错误。文本相同的分支和标签名仍优先。
- **Agent 可以更新它们没有写过的 pull request 描述。** 当你请 Cloud Agent 更新它没有打开的 GitHub 或 Cursor 托管 pull request 时，它现在会把内容加到现有描述下面，而不是拒绝，并且可以填入空描述。单仓库环境里的 Agent 创建或管理 pull request 时不再需要点名仓库，已完成的 pull request 操作在对话里显示为已完成。
- **pull request 页面上更清楚的 Agents 侧边栏。** cursor.com pull request 页面上浮动停靠栏里的 Agent 列表，在输入框正上方打开，并显示有多少 Agent 正在工作或需要注意。当你打开的 Agent 空闲，但其下的 Agent 仍在工作时，其标题会说 “Waiting on” 那些 Agent。侧边栏会显示 Retry 通知，而不是永远加载，或在 Privacy Mode (Legacy) 阻止 Cloud Agents 时告诉你。
- &#x20;**没有仓库的 Agent 上的队友后续遵循你的团队设置。** 队友可以跟进同事在没有仓库的个人环境上运行的 Cloud Agent，只要你团队的 Team Followups 和 Allow VM Sharing 设置允许。以前这些 Agent 对队友是只读的。打开或跟进别人 Agent 的队友，还必须能访问该 Agent 被授予的每个仓库。
- &#x20;**用量限制阻止 Cloud Agent 时的消息更清楚。** 当用量限制阻止 Cloud Agent 启动或接受后续时，错误会告诉你下一步做什么：打开按需用量，提高你自己或团队的按需上限，或在试用中切换到 Cursor 模型或 Auto。如果你在试用上限之后把后续切换到 Auto，Agent 会继续，而不会再次被阻止。
- &#x20;**团队模型阻止关掉 computer use 时的回答更清楚。** 如果团队管理员禁用了 computer use 所依赖的模型，Cloud Agent 会告诉你 computer use 不可用，哪个模型被阻止、它用来做什么，以及管理员可以在团队模型设置里重新启用它。
- **用键盘快捷键打开 Settings。** 在 Agent 页面上按 Cmd+,（Windows 和 Linux 上为 Ctrl+,）打开 Settings。折叠的侧边栏在你悬停时不再弹出；改为点击 Open sidebar 按钮。只有在窄于 1024px 的窗口上，打开对话才会折叠侧边栏。

### 环境

- **在仪表板上重命名环境，并找到配置即代码的环境。** 用详情页面名称旁边的铅笔按钮重命名环境。由已提交环境文件定义的环境显示 Config as code 徽章，Config as code 开关把列表筛选为它们。当构建因为 GitHub App 无法访问仓库而失败时，横幅会写出受影响的仓库，并链接到你的 GitHub 安装设置。
- **环境构建不再静默使用错误的 `environment.json`。** 当环境所跟踪的 [`environment.json`](https://cursor.com/docs/cloud-agent/setup.md) 缺失、为空或无法读取时，构建现在会失败，错误写出该文件和仓库，而不是悄悄回退到 `.cursor/environment.json` 或更早保存的配置。只有在你选择该选项时，新环境才会跟踪仓库的 `environment.json`。
- **更少的重复和被覆盖的环境。** 从仪表板创建环境，或把个人环境提升到团队，现在总会创建新环境，而不是覆盖同名或相同仓库的环境。如果已存在该名称的团队环境，会先请你重命名。在同一组仓库上启动的 Agent 会复用一个环境。
- &#x20;**环境构建缓存用你的客户管理密钥加密。** 对有[客户管理密钥](https://cursor.com/docs/cloud-agent/security.md) 的 Enterprise 团队，Cloud Agents 环境构建缓存用你的客户管理密钥所控制的密钥加密。如果你撤销对密钥的访问，或构建所在区域没有密钥，环境会在没有缓存的情况下构建。

### Self-Hosted Machines

- **Self-Hosted Machines 上的 Agent 更好地处理离线机器。** 当运行你的 Agent 的池机器离线时，Agent 会等待同一台机器回来并在那里继续，之后才移到另一台机器。如果你在 My Machine 断开时启动 Agent 或发送后续，大约 15 秒后你会得到机器离线消息，而不是等上几分钟。

### 集成

- **Jira 自动化规则和 GitHub Enterprise 上的 `@cursor` 评论会正确启动 Cloud Agent。** Jira 自动化规则以规则的提示词作为任务启动 Cloud Agent，包括没有附加工作项的规则。GitHub Enterprise Server 仓库上的 `@cursor` 评论现在可以工作，并在你属于拥有该 GitHub 应用的团队时在该团队下运行。
- **Azure DevOps 仓库可用 `org/project/repo` 简写。** 你可以用三段简写指向 Azure DevOps 仓库，例如 `my-org/My Project/portal`，比如在切换仓库或在 Microsoft Teams 里设置默认仓库时，而不必粘贴完整的 `dev.azure.com` URL。
- &#x20;**OpenTelemetry Export 里的 Cloud Agent commit。** 使用 [OpenTelemetry Export](https://cursor.com/docs/enterprise/opentelemetry-export.md) 的 Enterprise 团队，会为 Cloud Agent 推送到 GitHub 仓库的每个 commit 收到一条 `cursor.cloud_agent.commit` 日志。每条日志包含 commit SHA、仓库 URL、更改的文件数，以及可用时的行数，并链回该 Agent 运行。

### MCP

- &#x20;**Cloud Agents 遵循你团队的插件和 MCP 允许列表。** 当管理员打开 Team list 强制执行时，Cloud Agents 只连接到 Team 列表上的插件和 MCP 服务器，并且每个服务器设置的工具限制会生效。当强制执行关闭时，这些工具限制不再阻止其他 MCP 服务器。
- **Cloud Agents 不再卡在基于命令的 MCP 服务器的登录卡片上。** Cloud Agents 不再为 stdio 服务器停在 MCP 登录卡片上，这些服务器作为 Agent 机器上的命令运行，没有 Cursor 登录。以前运行会等到你点击 Skip 或卡片超时。Cloud Agents 也不再请你登录仍在加载的服务器。

### 修复

- **Agent 运行的修复。** 在 Agent 于停止 Hook 之后继续时停止它现在可以工作，同一步里的并行文件写入不再互相覆盖，Agent 不再得到重复的 AGENTS.md 或 CLAUDE.md 指令。在 CI 修复回合期间发送的后续以你的身份运行，Bugbot 的 Fix in Web 会继续现有的 pull request，而不是打开一个新的。
- **环境构建和网络允许列表的修复。** 对使用 PrivateLink 的团队，超过一小时的环境设置不再中途失败。VM 在后续时被重建的 Cloud Agent 会启动该环境最新的成功构建，更改团队环境的出站设置会触发重建。一个格式错误的网络允许列表条目现在会被跳过，而不是破坏或放宽整个策略。
- **仓库访问的修复。** 需要 Git 协议 v2 的 GitLab 实例上的 Cloud Agents 现在会启动，短暂的 GitLab 中断会被重试，失败的访问检查会显示 GitLab 的实际响应。嵌入仓库 URL 的 token 或密码会在存储之前被去掉。
- **Project 协调者和 worker 的修复。** 协调者用其自身模型的变体启动的 worker，会保持协调者的上下文窗口，而不是跳到 1M。拥有超过 1,000 个 worker 的协调者会列出其最新的 worker，在第一回合之前重命名 Project 现在会保留。
- **Self-Hosted Machines 的修复。** 落到正在关闭的机器上的运行会在池里的另一台机器上启动，被已删除运行留下的池机器会重置，以便下一个 Agent 可以认领它们。Agent 通过在下一步重连，从丢失的机器连接中恢复，Windows 机器会正确解析 Skill、插件和工作区路径。
- **MCP 连接的修复。** 不需要登录的 MCP 服务器不再在两次列出之间让工具消失，登录被撤销或过期的服务器显示为需要身份验证，而不是暴露缓存的工具。新添加的服务器或工具不再被报告为缺失，过期的服务器会话会重连，而不是使调用失败。
- **code tours 的修复。** 很高的栈里的 pull request 的 code tours 现在会生成，而不是失败，生成中途失败的导览以错误结束，而不是让查看者一直等待。
- **录制批注的修复。** 大型屏幕录制现在会得到章节、缩略图和修剪点，而不是被跳过，另一次保存同时发生时批注不再丢失。
- **Web 应用的修复。** Agents 导航抽屉在分数浏览器宽度下不再打开为空，你会看到全部已固定的 Agent，而不只是前 1,000 个。
- **Slack 回复的修复。** Slack 里的 Agent 回复会原样保留 `$1`、`$&` 或 `$$` 这类美元序列，而不是重复或丢掉消息的一部分。

## Oct 12, 2026 当周

### 集成

#### GitHub

- &#x20;**Bugbot 审查共享 GitHub 安装中的 pull request。** 当组织里的另一个团队把它的 GitHub 安装与你的团队共享时，Bugbot 会使用你团队的 Bugbot 设置，在共享仓库的 pull request 上运行。
- &#x20;**连接 GitHub 不再为 Enterprise 团队的每个仓库打开 Bugbot。** 当 Enterprise 团队或 GitHub Enterprise Server 安装连接 GitHub 时，现有仓库保持关闭，直到管理员为它们打开 Bugbot。新仓库也保持关闭，除非你更改安装的自动启用设置，这些变更会出现在团队的审计日志里。
- &#x20;**更清楚的 GitHub 访问错误。** 当 Agent 无法到达 GitHub 仓库时，错误会说明原因。未授予 Cursor GitHub App 的仓库会带授予访问的链接，要求 SAML SSO 的组织会带授权链接，[IP 允许列表](https://cursor.com/docs/integrations/github.md#ip-allow-list-configuration) 阻止会被说明。为链接到不同 Cursor 账户的安装更改设置时，会说明如何从团队仪表板卸载并重新安装它。
- &#x20;**GitHub 集成的修复。** 已合并团队的管理员可以再次管理 GitHub 和 Bugbot 安装，而不会看到 “Team is not active”。能访问超过 500 个仓库的 Agent 会得到 GitHub token，而不是失败，HTTP/2 不可用时 GitHub 请求不再失败。当 GitHub 对 token 刷新限速时，你会看到 “try again later” 消息。

#### Bitbucket

- &#x20;**Bitbucket Data Center 会说明缺失的权限。** 如果 Cursor 的应用链接授予的权限少于 Repositories Read，连接 [Bitbucket Data Center](https://cursor.com/docs/integrations/bitbucket.md) 会显示一个页面，请你的 Bitbucket 管理员在你重新连接之前提高它，设置说明会列出该权限。
- &#x20;**Bitbucket 上 Bugbot 的修复。** 通过组或工作区角色获得管理员访问的 Bitbucket Cloud 管理员，可以为仓库打开或关闭 Bugbot。当 Bugbot 更新其现有 webhook 时，重新连接仓库不再以 405 错误失败。
- **Bitbucket 的修复。** 大型 Bitbucket 工作区里的仓库列表会部分加载，而不是失败，Bitbucket Cloud 重定向时读取 pull request diff 和仓库不再失败。创建 pull request 会复用已打开的那个，即使仓库名的字母大小写不同，空的次要仓库会被跳过。

#### GitLab

- &#x20;**GitLab 凭据留在你的 GitLab 主机上。** 当 [GitLab](https://cursor.com/docs/integrations/gitlab.md) 实例把请求重定向到不同主机时，Cursor 会拒绝该重定向并给出错误，而不是把你的 token 或 OAuth 客户端密钥发到那里。同一主机的重定向，例如项目被移动或命名空间被重命名，仍会被跟随。
- **GitLab 集成的修复。** 作为次要仓库添加的空 GitLab 仓库会被跳过，而不会使 Agent 失败。缺少 token 或未知注册的 webhook 会返回错误，因此 GitLab 会显示失败，而不是报告成功。

### 仪表板与管理

#### 插件与 MCP

- &#x20;**在打开之前看出 Team list 强制执行会阻止哪些服务器。** Plugins 页面上的 Team list 设置，以及新的 Turn On Team List Enforcement 对话框，现在会列出成员在用、但不在 Team 列表上的插件和 MCP 服务器，以及各有多少成员在用。你可以一键把插件加到 Team 列表，或允许远程 MCP 服务器，对话框会在强制执行将阻止每个 MCP 服务器时警告你。参见 [MCP server trust management](https://cursor.com/docs/enterprise/model-and-integration-management.md#mcp-server-trust-management)。

#### 集成

#### SSO 与 SCIM

- &#x20;**控制谁可以通过 SSO 加入。** 组织管理员可以在组织设置里关闭 SSO 自动加入。关闭后，只有受邀或由 SCIM 预配的用户可以加入，现有成员仍像以前一样用 SSO 登录。通过你的 SSO 连接登录的其他人会被要求向管理员索取邀请。参见 [SSO settings](https://cursor.com/docs/account/teams/sso.md#additional-settings)。

#### 成员与组

- &#x20;**创建整个团队都能看到的团队组。** Members Groups 标签上的 New group 对话框现在有 Visibility 开关，因此管理员可以在创建组时就让该组及其成员对整个团队可见，而不必事后编辑。除非你打开该开关，组只对管理员可见。
- &#x20;**选择每页显示多少成员。** Members 页面现在在列表底部有 Rows per page 控件，因此大型团队的管理员可以一次显示 50、100 或 250 名成员。你的选择保存在页面 URL 里，更改它会把你带回第一页。

#### Agent 设置

- &#x20;**Project Agent 现在在每个方案上使用新机器之前都会询问。** Cloud Agents 安全设置里的 **Automatically allow Projects to place their work** 开关，现在对每个团队都以关闭开始，而不只是 Enterprise 团队，因此会有人批准 Project 的 Agent 使用的每台新机器、每个环境或每个仓库。打开了该开关的团队保持自动允许，已经授予的批准保持不变。参见 [Cloud Agents security settings](https://cursor.com/docs/cloud-agent/settings.md#security-settings)。
- &#x20;**对不会匹配的网络允许列表条目有更清楚的警告。** 在团队的 Network access 设置里，管理员现在会看到 Cloud Agent 出站会忽略哪些沙箱允许列表条目，以及对不支持的字符和通配模式的说明。`*.` 和 URL 路径这类包罗万象的条目不再被 Cloud Agent 出站接受，因此打错的条目不能打开对每个主机的访问。参见 [network access settings](https://cursor.com/docs/cloud-agent/settings.md#network-access-settings)。
- &#x20;**为 Approval Agents 选择模式。** Approval Agent 设置现在在 Configuration 下包含 Mode 选项。Default 是更快的选择，适合大多数团队。Low 成本更低、运行更慢。你需要编辑权限才能更改它。参见 [PR Routing & Approval](https://cursor.com/docs/approval-agents.md)。

#### Rollouts

- &#x20;**按仓库、服务或环境把 Rollouts 通知发到 Slack 频道。** 在 Rollouts 设置里，团队管理员可以把 Slack 频道订阅到匹配仓库、服务或环境的变更通知，并添加可选消息，包括 @提及，它会在每个新 issue 之后发到线程里。成员可以添加自己的个人订阅，团队和个人订阅以后都可以编辑。参见 [Rollouts notifications](https://cursor.com/docs/rollouts.md#notifications)。
- &#x20;**从你的 GitHub 部署向 Rollouts 发送部署事件。** 在 Rollouts 里设置部署事件时，你可以选择 **Use my GitHub deployments**，从你关注的仓库记录部署，不需要 CI 报告器。手动设置现在显示编号的、可折叠的步骤，其可复制片段会在 CI 日志里报告缺失或被拒绝的 Cursor API 密钥，而不使部署失败。来自 gitlab.com 项目的部署现在出现在部署页面上，**Version** 和 **Previous version** 链接到 GitLab commit。参见[发送部署事件](https://cursor.com/docs/rollouts.md#send-deployment-events)。

#### Cloud Agent 环境

- **查找、筛选并重命名 Cloud Agent 环境。** Environments 列表现在用 **Config as code** 徽章标记由已提交的 `.cursor/environment.json` 定义的环境，**Config as code** 开关只显示这些环境。你可以在环境的编辑页面上点击其名称来重命名，**Repo** 筛选有一个搜索框。在默认环境选择器里，未命名环境显示其仓库，例如 “web-app +3”，而不是 ID，你可以按仓库名搜索它们。参见 [environment management](https://cursor.com/docs/cloud-agent/settings.md#environment-management)。
- **Cloud Agent 环境构建无法使用其 environment.json 时的消息更清楚。** 当环境构建因为所跟踪的 `environment.json` 缺失、为空或无法读取而失败时，构建页面现在会说明这一点，并告诉你恢复或检查该文件，然后重建。在此期间 Agent 继续使用上次成功的构建。因为 GitHub App 无法访问仓库而失败的构建，现在会列出受影响的仓库，并带 **Open installation settings** 按钮，要求自托管 worker 的团队会在 **Trigger build** 操作的位置看到一条说明。参见 [Cloud Agent Builds](https://cursor.com/docs/cloud-agent/builds.md#manage-builds)。

#### 用量与分析

- &#x20;**导出整个组织的用量事件。** 有汇集用量的组织现在在组织 Usage 页面上有 Usage events 分区，带一个 Export 按钮，下载所选日期范围和筛选下该组织各团队的事件级用量。如果无法导出，例如组织同时有已合并和未合并的团队，按钮会被禁用，页面会说明原因。
- **重排用量事件列，并更容易阅读用量标签。** 在 Usage 页面上，你可以拖动用量事件表的列标题，或用键盘移动它们，排成你想要的顺序。浏览器会记住顺序，Date 和 User 保持不动。用量类型现在读作 “Additional usage” 和 “Credit grants”，Auto 请求在图表和模型筛选里显示为 Auto，或显示为带其 Auto 选项的被路由模型，由团队而不是个人或 API 密钥拥有的用量在表格和 CSV 导出里显示为 Team。

#### 安全与审计日志

- &#x20;**审计日志里有更多团队变更。** 审计日志现在记录 Cloud Agent 网络出站和沙箱网络设置的变更，以及团队仓库允许列表和阻止列表的变更，包括仓库模式。它也记录 Remote Control 事件，例如计算机被注册，或设备被配对、取消配对或撤销，并带新的 Remote Control 筛选。合并队列绕过尝试和 pull request 接管现在也会被记录，接管会显示先前的作者。参见 [audit logs](https://cursor.com/docs/enterprise/compliance-and-monitoring.md#audit-logs)。

#### 工作区设置

- &#x20;**为团队或组织添加图标。** 管理员可以在 Team profile 设置里上传、更改或移除团队图标（PNG、JPEG 或 WebP，最大 2MB）。图标出现在工作区切换器、命令面板、团队选择器和组织的团队表里。组织所有者也可以从组织 Overview 页面设置组织图标。

#### 自动化

- &#x20;**每页看到更多被标记的漏洞。** Vulnerability Scanner 页面上被标记的漏洞表现在有每页行数选择器，可选 10、25、50 或 100 行。它默认显示 25 行，而不是 5。长标题折成两行，更改页面时 Prev 和 Next 按钮不再移动。参见 [Security Agents](https://cursor.com/docs/security-agents.md)。

#### 账户与导航

- **删除账户之前看出你会失去什么。** Profile 设置里的删除账户对话框现在列出将被删除的内容，从你的 Cursor 账户的名称、邮箱和加入日期开始。如果你的账户与 Grok 账户合并，它也会在 Deleted 下显示该账户，并在 Unlinked 下显示你关联的 X 账户，因为 X 账户是取消关联，不是删除。对话框也注明已经支付的款项不符合退款条件。
- **从 Appearance 设置重置已保存的调色板。** 如果你保存了色盲或高对比度调色板，Settings > Appearance 现在会在 Color palette 行里显示它，并带 Reset 按钮。重置会显示带 Undo 的提示，因此你可以恢复该调色板。

#### 修复

- &#x20;**Rollouts 设置和 issue 页面的修复。** Rollouts 设置现在会预选你团队现有的可观测性连接，Rollouts 只在设置完成后才显示为 Enabled。如果 Rollouts 已经打开，设置仍会显示该步骤，并带指向其设置的链接。从直接链接打开的 issue 上点击 Fix，现在会从正确的 rollout 开始，指向 pull request 的链接在你正在使用的门户上打开。
- **Cloud Agent 环境的修复。** 恢复更早的团队环境版本不再移除你的个人覆盖，Secrets 行现在说明它包含团队和个人密钥，Manage 会打开与环境范围匹配的密钥。如果环境列表加载失败，Environments 页面、Slack 仓库路由规则和 Agent 环境选择器现在会显示错误或回退，而不是永远加载。Environments 分区也会识别任何已连接的源代码管理提供方，因此它加载更快，不再错误地显示 Connect Source Control 卡片。
- &#x20;**用量与分析的修复。** 团队用量事件表现在可以加载很长的日期范围，例如 30 天，而不会在大型团队上超时。用量卡片显示成员自己的支出上限，包括 $0 上限，而不是团队上限。Usage Leaderboard 不再列出服务账户、团队 API 密钥或未认证的用量，也不再合并共享邮箱或没有邮箱的成员。
- &#x20;**团队邀请和团队选择的修复。** 在没有席位层级的团队上，Copy invite link 现在使用你在邀请表单里选择的角色，因此管理员链接不再把人加为成员。当你属于多个团队时，付费团队现在排在免费团队之前，因此你不再默认落到免费团队。
- **集成和共享页面的修复。** 尚未连接 Slack 的队友，现在会把已部署 Agent 的现有 Slack 应用看作带 Connect Slack 按钮的只读行，而不是空状态。共享的 Canvas 现在在白纸上打印，没有浏览器的页眉和页脚，被禁用的 Team sharing 选项会说明团队管理员何时把它关掉。
- **仪表板导航和手机布局的修复。** 在手机上，标签条滚到屏幕边缘，点按目标更大，成员搜索和设置清单不再挤占布局。Cmd+K 命令面板现在在其他对话框之上打开，关闭它会让下面的对话框保持打开。每个仪表板标签设置自己的浏览器标题，失败的后台刷新不再用错误屏幕替换已加载的工作区，个人资料设置显示你保存的名和姓。

### 自动化

#### 触发器

- **Origin pull request 的标签触发器。** Origin 仓库上的自动化现在可以使用 Label change 触发器，它在标签被加到 pull request 或从其上移除时运行，与 GitHub 一样。
- **Origin pull request 上的内联审查评论现在会触发自动化。** 带 pull request 审查评论触发器的自动化，现在会在有人于 Origin pull request 上留下内联审查评论时运行。以前这些评论只到达在顶层 pull request 评论上触发的自动化。

#### 安全 Agent

- &#x20;**Vulnerability Scanner 按团队限制每小时扫描。** [Vulnerability Scanner](https://cursor.com/docs/security-agents.md) 自动化每个团队每小时最多运行 500 次仓库扫描。超过上限的扫描会被跳过，并在运行历史里显示为限速。
- **托管安全运行里的 Subagent 不再自行发消息。** 修复了一个问题：托管安全审查、漏洞扫描器和 PR 批准运行里的辅助 Subagent 可以自行发送 Slack 和 Teams 消息，或解决审查线程。这些运行里的 Subagent 现在会被拒绝这些操作。

#### 集成

- **自动化加载你点名的 MCP 服务器。** 按名称选择 [MCP 服务器](https://cursor.com/docs/cloud-agent/automations.md#mcp-server) 的自动化，例如 `slack` 或 `google-drive`，现在即使大小写或空格不匹配也能找到它。以前这些运行可能在没有该服务器工具的情况下启动。如果 Cursor 仍然找不到服务器，运行会显示警告，告诉你检查名称，或在 Integrations 下重新连接该服务器。
- **GitLab 关闭 MCP 时的状态更清楚。** 当 MCP 操作里的 GitLab MCP 服务器因为 GitLab 为你的组关闭了 MCP 而被阻止时，自动化编辑器现在会说明你的顶层 GitLab 组的 Owner 必须打开 MCP 客户端访问，而不是显示一个无法成功的登录提示。
- &#x20;**只有团队管理员可以连接团队的 PagerDuty 集成。** 连接、替换或移动团队的 [PagerDuty 集成](https://cursor.com/docs/cloud-agent/automations.md#pagerduty-triggers) 现在需要团队管理员，与断开它一样。连接到一个团队的 PagerDuty 账户，在管理员断开它之前不能移到另一个团队。连接页面现在会告诉你连接失败的原因：会话已结束、你不是团队管理员，或该账户属于另一个团队。

#### 自动化运行

- &#x20;**托管自动化不再在团队阻止的模型上运行。** 当团队的模型设置阻止托管自动化可以使用的每个模型，例如安全审查者或漏洞扫描器时，运行现在会以 “Model blocked” 错误失败，请团队管理员允许一个模型。以前自动化可能回退到被阻止的模型。

#### 自动化页面

- **Model 列显示你的自动化实际运行的变体。** 自动化列表、概览表和运行历史现在用你保存的 effort 和 Fast 设置标记每个自动化的[模型](https://cursor.com/docs/cloud-agent/automations.md#model)，而不是模型的默认变体。如果保存的选择不匹配单个变体，该列显示纯模型名。

#### 修复

- **自动化在哪个分支和仓库上启动的修复。** 绑定到单仓库环境的自动化，包括保存为 SSH URL 的仓库，现在在你保存的分支上运行，而不是环境的默认分支。针对特定自托管机器的自动化，在 Cursor 无法到达 git 主机时会回退到 `main`，而不是失败。因为 GitHub App 安装在另一个账户下而失败的运行，现在会说该仓库没有连接到这个团队，并说明如何重新安装应用。
- **自动化编辑器和集成的修复。** 即使没有返回登录链接，MCP 操作现在也会在服务器需要登录时显示，点击登录会得到新链接。账户上有很多服务时，PagerDuty 服务加载更快，超过 1,024 个字符的描述现在显示清楚的错误。关闭 Change Monitor 会报告失败，而不是在它继续运行时看起来成功，From Cursor 下的 Bugbot MCP 工具和安装页面现在会加载。
- **自动化列表行可用键盘操作。** 你现在可以用键盘到达并打开自动化列表里的行。Tab 把焦点从一行移到下一行。
- **长自动化运行的修复。** 采取非常大量操作的运行不再无法保存其操作历史。最旧的条目会被压缩，因此运行继续记录。

### API

- &#x20;**让目录组对整个团队可见。** Admin API 里的[团队目录组](https://cursor.com/docs/account/teams/admin-api.md#team-directory-groups)现在有 `visibility` 字段。创建或更新组时把它设为 `team`，以便每个团队成员都能看到该组及其成员，或设为 `team_admin` 以只对管理员可见。如果创建时省略它，该组只对管理员可见。
- &#x20;**修复了团队支出端点里缺失的成员支出。** [`POST /teams/spend`](https://cursor.com/docs/account/teams/admin-api.md#get-spending-data) 现在对合并计费的团队完整计入每个成员的已计费用量，因此报告的支出不再偏少。如果无法读取支出数据，该端点返回 503 错误，而不是报告零支出。

### 安全与批准 Agent

#### 安全 Agent

- **Vulnerability Scanner 的发现显示在 Vulnerability Remediation 里。** 修复了一个问题：来自 Vulnerability Scanner 的发现没有出现在 [Vulnerability Remediation](https://cursor.com/docs/security-agents.md#remediation-agents) 分区。
- **每页显示更多被标记的漏洞。** Flagged Vulnerabilities 列表现在有 **Rows per page** 选择器（10、25、50 或 100），默认显示 25 行，而不是 5。长标题折成两行，翻页时 **Prev** 和 **Next** 按钮保持原位。
- **修复了推送修复时关闭多文件发现。** 当发现点名超过一个文件时，Security Reviewer 会在每次推送时重新检查其中每个文件，因此其中任何一个里的修复都会关闭该发现。你不再需要手动解决它。你没有更改的文件里的未解决发现现在计入安全检查，因此检查状态与未解决线程一致。
- **保存大型 Security Reviewer 配置可以工作。** 保存选中了很多仓库、排除项或 Slack 频道的 Security Reviewer 不再失败。

#### 批准 Agent

- **批准评论显示每个可以批准的人。** 当 [PR Routing & Approval](https://cursor.com/docs/approval-agents.md) 策略正在等待审查，且可以批准的所有者多于被请求的人时，其评论读起来像 “Needs 1 approval from any of: @alice, @bob (+2 more)”。在 [Origin](https://cursor.com/docs/origin.md) 仓库上，列出机器人所有者的待处理策略会点名它们，并显示如何从 **Reviewers** 列表或用 `origin pr reviewer request` 请求它们的审查。

### 模型与 Cursor Router

- **新模型：Gemini 4 Argon。** [Gemini 4 Argon](https://cursor.com/docs/models/gemini-4-argon.md) 现在可在 Cursor 中使用。

## Oct 12, 2026 当周

### 团队与安装

- &#x20;**Bugbot 在共享的 GitHub 安装上于正确的团队下运行。** 当 GitHub 安装在 Cursor 团队之间共享时，仓库上的 Bugbot 审查在它被授予的团队下运行，使用该团队的设置和方案，而不是安装所有者或 PR 作者的。Bugbot 仓库列表在另一个团队已经运行 Bugbot 的仓库上显示 “Enabled by” 徽章，点名该团队，并说明你为什么不能在那里启用它们。
- &#x20;**在与你的团队共享的 GitHub 安装上管理 Bugbot。** 如果组织里的另一个团队把它的 GitHub 安装与你的团队共享，你可以在不接管该安装的情况下，从仪表板更改按仓库的 Bugbot 设置。安装详情页面把这些安装标为 Shared，并点名仍管理安装级设置的拥有团队。

### 审查

- **本地审查在 Mercurial 仓库里工作。** 当你[从 Agent 运行 Bugbot](https://cursor.com/docs/bugbot.md#run-in-your-agent) 或对本地改动运行安全审查时，它现在既审查 git 仓库，也审查 Mercurial 检出，而不运行仓库的 hooks。如果文件夹两者都没有，审查会说明并写出路径。如果你的 Mercurial 客户端太旧，无法安全跳过 hooks，它会请你升级。
- **pull request 把规则违反称为有意时，它们不再被丢掉。** 即使 pull request 描述说更改是故意的，Bugbot 现在也会保留正确标记你的团队、项目或手动[规则](https://cursor.com/docs/bugbot.md#rules)之一被违反的发现。引用 diff 并未打破的规则的发现仍会被滤掉。
- **长本地审查会带着结果完成。** 当你[从 Agent 运行 Bugbot](https://cursor.com/docs/bugbot.md#run-in-your-agent) 且审查异常地长时，Bugbot 会在设定的限制上停止调查，并用它已经验证的 bug 回复，而不是继续跑或没有任何回答地结束。

### 设置与访问

- &#x20;**Restrict Access 适用于来自 Cursor Agent 的 pull request。** 当 Cursor Agent 打开 pull request 时，团队的 Restrict Access 允许列表或阻止列表现在检查启动该 Agent 的人的 GitHub 账户，而不是机器人。当 Restrict Access 阻止审查时，Bugbot 的 “couldn't run” 评论会说明是哪个列表阻止了它，以及管理员如何修复，而不是建议增加席位上限。
- **当你的 GitLab 或 Bitbucket 账户链接到多个 Cursor 账户时，Manual Only 仍然成立。** 修复了一个问题：在那种情况下，Bugbot 仍可能在 GitLab 和 Bitbucket 上自动审查你的合并请求和 pull request。如果任一关联账户打开了 Manual Only，Bugbot 现在会等你触发它。

### 仪表板

- &#x20;**Bugbot 仪表板里的账户列表更清楚。** 覆盖表现在把第一列标为 Account，显示每个安装的 GitHub 账户名，并用 Personal 徽章标记个人账户。你管理的账户列在最前，然后是组织，然后是个人账户。Bugbot 的安装页面和 MCP 工具也可以从 Automations 和 Custom Agents 页面正确打开。
- &#x20;**从 Bugbot 仪表板登录团队 MCP 服务器。** 当 Bugbot 的 MCP 工具设置里的团队 [MCP 服务器](https://cursor.com/docs/bugbot.md#mcp-support) 需要身份验证时，其状态显示 **Needs Auth — Sign in** 按钮，打开该服务器的登录页面。每次点击都得到新的登录链接，因此过期或已使用的链接不再阻止连接。
- **用键盘快捷键从 Bugbot 页面打开 Settings。** 在 Bugbot 页面上，macOS 按 Cmd+,，Windows 和 Linux 按 Ctrl+,，打开你的 Settings，与 Cursor 桌面应用使用的快捷键相同。它在非拉丁键盘布局上工作，你在文本框里打字时什么都不做。

### 修复

- **审查可靠性的修复。** Autofix 预检查失败时，Bugbot 仍会发布其审查，并跳过该次运行的 Autofix。在 GitLab 上，遇到临时用户查找失败的手动 Bugbot 命令现在会得到说明发生了什么的回复，而不是被丢掉。Bugbot 也会再次处理它自己过时的内联评论，例如最小化先前的评论。

## 3.24

### 聊天与 Composer

- **目标、队列、调试、Canvas 和浏览器面板现在从提示词上方的药丸打开。** 你的对话目标、排队的后续、Debug Mode 复现步骤、Canvas 预览和本地开发服务器预览，各自在提示词上方有一颗药丸（Goal、Queued、Debug、Canvas 和 Open Browser），用来打开或最小化其面板。有新内容时每个面板会自己打开，例如新目标、你的第一条排队消息，或准备好的复现步骤，关闭它会保留药丸。
- **一键展开并复制 Markdown 表格。** 悬停 Agent 回复或 Markdown 文件里的 Markdown 表格，会在旁边显示 Expand table 和 Copy table 按钮。Expand table 在全尺寸视图里打开表格，列会换行，链接可用，Copy table 作为真正的表格粘贴到文档和聊天应用，或作为 Markdown 粘贴到纯文本。
- **从聊天链接下载文件。** 右键 Agent 聊天里的文件链接，选择 Download File 保存本地副本，默认是 \~/Downloads。它对 Cloud Agent 在其工作区里构建的文件也有效。
- **视频播放器的静音开关。** Agents Window 里的内联和全屏视频现在有 Mute/Unmute 按钮，每个新视频按你上次留下的方式以静音或有声开始。
- **编辑更早的消息会保留 Agent 对其上方回合的记忆。** 修复了一个问题：在重新打开或已同步的 Agents Window 对话里编辑更早的消息，会从空历史重启模型。编辑现在从该回合的状态恢复；如果找不到该状态，编辑会失败，并让你的记录稿保持完整。
- **排队的消息等待 Debug Mode 复现步骤。** 当 Debug Mode 回合以复现步骤结束时，你排队的后续不再立刻发送并藏起这些步骤。Debug 药丸留在队列旁边，直到你点击 Proceed 或 Mark Fixed，或切换出 Debug Mode。
- **Cloud Agent 可以构建 Canvas。** Cloud Agent 创建的 [Canvas](https://cursor.com/docs/agent/tools/canvas.md) 会保存到云端，并在 Canvas 标签里作为只读预览打开，包括来自 Agent 聊天里的链接。对 Cloud Agent，Canvas 也会出现在 New Tab 菜单里。
- **在 Design Mode 里用方向键选中正确的元素。** 在 [Design Mode](https://cursor.com/docs/agent/design-mode.md) 里，即使小的子元素盖在上面，你现在也可以选择容器：按 ↑ 把悬停高亮移到父元素，按 ↓ 移回去，然后点击选中它。

### Cloud Agents

- **Cloud Agent 连接的修复。** 计算机从睡眠唤醒后，Cloud Agent 会自行重连，而不是失败，对话和 Agent 列表在网络掉线、代理错误和服务器部署期间继续更新。机器消失的 Cloud Agent 会在你发送新消息时重连其工作区。
- **把 Cloud Agent 移进本地 worktree。** 如果 Cloud Agent 在你本地也克隆了的单个仓库上工作，状态栏里的环境选择器现在让你在该仓库现有的某个 Cursor [worktree](https://cursor.com/docs/configuration/worktrees.md) 里，或在一个新的里继续该 Agent，而不碰你的主检出。

### 导航与布局

- **关闭并重新打开标签会把你带回原来的位置。** 在标签上按 Cmd+W 现在会回到你之前所在的标签，即使跨分栏也是如此，关闭分栏磁贴或窗格会把焦点放回你上次使用的那个，而不是第一个或空的那个。Cmd+Shift+T 重新打开你最近关闭的任何东西，聊天或应用标签，包括 Agent、Environment、Changes、Desktop、Machine 和语音通话记录稿标签。
- **聊天标签适合其标题，悬停时显示 Agent 详情。** Agents Window 里的聊天标签现在按标题调整大小，放不下时标签条会滚动。悬停聊天标签、独立聊天的标题或磁贴标题，会显示与侧边栏相同的 Agent 卡片，在标签之间移动会移动卡片，而不必再次等待。独立聊天和磁贴现在使用与其他窗格相同的标签条，因此你可以双击标签来重命名，侧边栏隐藏时，按住 Cmd（Windows 和 Linux 上为 Ctrl）会在每个 Agent 的标签上显示其快捷键。
- **在分栏之间拖动磁贴。** 在分栏里，把磁贴的主聊天标签拖到另一个磁贴上，即可把该磁贴移到那里。从侧边栏隐藏的聊天也在其磁贴标题里保留自己的名字。
- **Subagent 和侧边聊天作为其父 Agent 上的标签打开。** 从链接、命令面板或另一个 Agent 打开 Subagent 或侧边聊天，现在会打开其父 Agent，并把 Subagent 或侧边聊天作为旁边的标签，因此父对话仍在视野里。Agent 标签条上的 + 按钮把其子 Agent 作为标签打开。Back 回到 Subagent 打开之前你所在的位置，而不是父 Agent。自动化运行的 Subagent 仍单独打开。
- **从侧边轨道和新标签菜单到达每个侧边聊天。** 侧边轨道现在列出你最近更新的五个侧边聊天，其余在 “N more” 菜单后面，当轨道折叠成图标时，单个 Side chats 按钮会打开全部。新标签菜单的 Side Chats 和 Plans 子菜单现在列出每个条目，而不是停在 10 个，超过 10 个时会加上搜索框。
- **菜单栏和 Dock 里未读 Agent 的修复。** 你标为未读的 Agent，或在你查看另一个 Agent 时完成的 Agent，现在会在菜单栏和 Dock 菜单里显示，无需重启。未读 Agent 占据菜单的前几个位置，放不下时有 “N more unread” 条目，徽章只计菜单所显示的。当某个线程未读时，Project 的行现在会亮起。
- **开发服务器恢复后浏览器标签会重新加载。** 指向当时没有在服务的本地端口的[浏览器](https://cursor.com/docs/agent/tools/browser.md)标签，现在会在终端命令或 Agent 的后台 shell 在该端口上启动开发服务器时自己重新加载，因此你不必手动刷新。你也可以用命令面板里新的 Reload Browser Tab 命令，或 Cmd/Ctrl+R 重新加载标签。
- **Agent 回复里的文件链接打开得更快。** 悬停或聚焦 Agent 回复里的文件链接，会在后台开始加载文件标签，因此你点击时文件已经准备好。

### Projects 与工作区

- **克隆仓库以便在你的机器上运行它。** 如果仓库还没有克隆到你的机器上，Run on 菜单里它的本地行现在会说 “Not cloned”。选该行即可克隆仓库。克隆时通知会显示进度。
- **把本地 Agent 加到云 Project。** 把本地 Agent 拖到侧边栏里的云 Project 上，或使用 Add to Project，使它成为该 [Project](https://cursor.com/docs/agent/projects.md) 里的 worker，而不丢失其对话。Agent 通过 Remote Control 继续在你的计算机上运行，因此该工作区需要打开 Remote Control。
- **看出 Project 的协调者正在做什么。** [Project](https://cursor.com/docs/agent/projects.md) 的协调者现在会在工作时为其当前步骤报告一段短状态，例如它正在分派或审查的子任务。状态实时显示在 Project 聊天和全屏编辑器的 Agent 叠加层里。
- **Projects 里更容易扫读的消息。** 在 [Project](https://cursor.com/docs/agent/projects.md) 的主对话里，你的消息现在显示为扁平、填充、没有边框的气泡，因此它们与 Agent 的回复区分开。填充跟随你的主题，Tech Lead 对话使用相同样式。

### 文件、改动与 pull request

- **更清楚的 pull request 栈和 PR 标签操作。** 在 PRs 托盘和 PR 标签的栈导航里，栈中已关闭的 PR 现在折进 “N closed” 行，即使只有部分 PR 属于该 Project，栈也保持分组，像 “1 PR not in this project” 这样的行标出缺口。合并会落地其未合并下游的 Origin PR，现在读作 “Merge N PRs”，并注明它作为一个 commit 落地。PR 标签菜单增加 View on Web，Fix CI 这类后续药丸会点名它们所作用的 pull request，Bugbot 发现上的 Fix with Agent 不再清除你的草稿，或把 Bugbot 报告连同你的下一条消息重发。
- **Open in Browser 对远程和云机器上的 HTML 文件有效。** 在 Remote SSH 机器、Cloud Agent 或自托管 worker 的文件树里右键 HTML 文件，选择 Open in Browser 预览本地副本。如果打不开，消息会说明原因，例如远程机器未连接或文件太大。

### 自定义与设置

- **可调整的聊天宽度。** Settings > Appearance > Agent Conversations 里新的 Chat Width 滑块设置 Agent 对话可以长到多宽，从 Narrow 到 Wide（600 到 1600px）。调宽它给表格和代码更多空间，或调窄以便阅读。
- **修复了 macOS 上切换主题时的半透明窗口色调。** 在 macOS 上，每次在浅色和深色主题之间切换时，Agents Window 的半透明背景色调现在都会更新。以前它可能保留上一个主题的色调。

### 方案与账单

- &#x20;**Buy Credits 对话框里更清楚的额度提示和自动充值。** 当模型需要额度才能运行时，用量通知现在会说明原因，如果没有可升级的方案，则把 Buy Credits 放在第一位。Buy Credits 对话框也有自动充值的 Set Up 行，因此你可以保存充值规则或关掉它，而不离开对话框。这适用于有每周用量额度的方案。
- &#x20;**Enterprise 用量图表的修复。** 在 Enterprise 方案上，Settings > Plan & Usage 里的 Cumulative Spend 图表在总额很大时仍可读，轴标签较短，例如 $1.5M，远高于当前支出的支出上限显示在图例里，而不是压扁图表。缓存的用量和图表数据立刻出现，而不是加载状态，来自上一个团队或用户的错误或过期数据横幅不再为当前的显示。

### 修复

- **macOS 上 Computer Use 的修复。** 权限缺失时，Enable Computer Use 权限窗口不再反复重新打开。窗口退出或安装中途崩溃后，Computer Use 也不再卡在说另一个 Cursor 窗口正在安装它。
- **后续输入上方托盘和药丸的修复。** 在输入上方打开托盘、药丸或提示不再移动对话，被打断的托盘动画不再跳动。批准、错误和分支不匹配提示不再能被 Escape 意外关闭，环境设置弹出层在另一个托盘盖住它时保留你的编辑，药丸保持可见，并且只在其托盘打开时显示为选中。在全屏编辑器里回答 Agent 的问题不再使视图崩溃。
- **在 Agent 之间切换的修复。** 切换回最近的 Agent 不再重新加载聊天、丢失滚动位置，或在加载时闪烁，隐藏的聊天不再捕获你的打字。Project worker 的聊天弹出窗口现在保持打开，并在其 Agent 被归档或删除时关闭。
- **侧边栏和 Composer 的修复。** 编辑器面板全屏时打字现在会到提示词，New Agent 按钮不再从分组侧边栏消失，长 Agent 标题淡出，而不是藏起同伴计数。如果从侧边栏草稿启动 Cloud Agent 失败，你的提示词会回来，而不是丢失。停止新 Agent 的第一条消息会保留浏览器面板及其标签。
- **文件搜索、文件标签和 Changes 面板的修复。** 打字时文件搜索和 Quick Open 保持响应，按 Enter 会打开你实际输入内容的顶部结果。远程工作区上的文件标签打开得更快，并在 Cloud Agent 编辑该文件时刷新。外部 git 命令之后 Changes 面板更新得更早，刷新不再撤销暂存或取消暂存。
- **没有打开编辑器时的错误修复。** 在没有打开编辑器时打开文件链接、切换进 Agent 布局，或有未保存文件发生变化，Agents Window 不再抛出错误。更改侧边栏位置，以及查看不再加载的对话的记录稿，也不再失败。
- **Cloud Agent 状态和历史的修复。** Cloud Agent 不再卡在过时状态、闪回运行中，或在过期更新到达时翻回未读，加载更早的消息会可靠地加载上一页。重新录制的视频和重拍的截图显示新内容，Project worker 标题立刻更新，只有协调者的回合播放完成提示音。在选定的自托管机器上启动的 Cloud Agent 在刷新和分叉后保持该机器，从已保存环境重试失败的启动会保留其仓库配置。
- **Agent 记录稿滚动和加载的修复。** 滚动长 Agent 记录稿不再使消息跳动，或在顶部显示一条空白带。打开的 Agent 出现得更早，并立刻显示回合中途的消息。回合完成时记录稿不再移位。
- **@ 提及菜单的修复。** 一个来源挂起时，@ 菜单不再一直卡在加载，Branch 行和过去的聊天立刻出现。对 Cloud Agent，你一聚焦输入，文件提及就会加载，在大型仓库里打字保持响应，云工作区启动时斜杠命令菜单不再变空。编辑已发送的消息现在列出与后续输入相同的过去聊天。
- **Agent 聊天的修复。** Agent 回复里的图表不再裁切工具提示或切断长标签，Windows 文件路径链接会打开文件，PR 芯片上的 Copy Link 不再打开该 PR。在 Review Changes 里取消 commit 不再继续去打开 pull request，新 Agent 模型选择器保持你刚刚发送的模型和推理力度。
- **浏览器里 Design Mode 的修复。** 当本地浏览器标签没有东西在服务，或其开发服务器在另一个工作区运行时，[Design Mode](https://cursor.com/docs/agent/design-mode.md) 药丸不再出现在该标签上。热重载之后，你选中的元素留在正确的元素上。
- **语音通话卡片的修复。** 另一台设备上的通话的语音通话卡片，在该通话变静音时不再短暂显示虚高的时长。它现在显示为在最后听到的时间结束。
- **插件 Skills、聊天同步、紧凑 Composer 和侧边栏的修复。** 加载失败的云插件 Skills 在你重新打开菜单或回到窗口时，会回到 `/` 和 `+` 菜单，在另一个窗口里启动或引导的聊天不再错过后来的回合。在狭窄的紧凑 Composer 里，长模型名不再挤占文本框，路径只在字母大小写上不同时，侧边栏不再把同一文件夹列出两次。

## 3.24

### Agent 与聊天

- **Auto-review 会在有风险的删除前停下来征求你的批准。** [Auto-review](https://cursor.com/docs/agent/security/run-modes.md) 现在会在运行递归、强制、通配或镜像删除之前询问，如果其目标的解析可能与所写的不同，例如带空格的未加引号路径、未设置的变量，或目录更改后可能失败的相对路径。匹配你某条阻止指令的命令现在总会显示批准卡片。打开 Run Everything 后，除非你的 permissions.json 限制该模式，Agent 现在会在没有沙箱的情况下运行。
- **复制的表格在文档和聊天应用里保留格式。** Agent 响应表格右键菜单里的 Copy Table，现在会作为真正的表格粘贴到 Google Docs、Notion 和 Slack 这类富文本编辑器。纯文本目的地仍得到 Markdown。
- **Agent 流入时滚动更顺。** 当 Agent 聊天跟随流入的回复时，它现在会平滑滑到新输出，而不是以不均匀的步子移动。
- **Agent Review 的修复，以及更好的 Fix in Cursor 提示词。** 来自 Bugbot 报告的 Fix in Cursor 现在会起草列出受影响文件和行范围的提示词，即使无法附加代码选择。打开或关闭聊天的 Review 标签，或还原计划 Checkpoint 时，Agent Review 标签保持打开，对曾经打开已移除的 Beta “Hide Agent Review” 设置的用户，Agent Review 会再次显示。

### Cloud Agents

- **来自编辑器的 Cloud Agent 链接在 Agents Window 里打开。** 当编辑器窗口拥有焦点时，点击 Cloud Agent 的 “Open in Cursor” 链接，现在会在 Agents Window 里打开该 Agent。以前编辑器会尝试匹配 Agent 的仓库，并可能显示 “Incorrect Repository” 对话框，而不是打开该 Agent。
- **Cloud Agent 识别内存不足导致的杀死。** 当从 Cursor 启动的 Cloud Agent 里的 shell 命令因为机器内存耗尽而被杀死时，Agent 现在会被告知，并带上内存限制，因此它可以说明失败并用更少内存重试，而不是猜测。

### MCP

- **更可靠的 MCP 服务器登录。** 其 OAuth 应用拒绝指向你自己计算机的重定向 URL 的 MCP 服务器，现在可以通过 Cursor 托管的回调页面登录。当另一个 Cursor 窗口或应用占用本地回调端口时，登录也会完成，并且不再在很大的浏览器请求头上失败。

### 自定义与设置

- **更容易把 shell 和 MCP 调用的 Hook 关联起来。** shell 和 MCP 执行 [Hooks](https://cursor.com/docs/hooks.md) 现在收到与 `preToolUse` 事件匹配的 `tool_use_id`，因此一个脚本可以从头到尾跟随一次工具调用。存在记录稿时，每个 Hook 现在都会在 `transcript_path` 里得到 `.jsonl` 记录稿，`CURSOR_TRANSCRIPT_PATH` 始终与它匹配。
- **AWS Bedrock 上的 Claude Opus 5.5 和 Sonnet 5.5。** 如果你[连接自己的 AWS Bedrock 账户](https://cursor.com/docs/customizing/aws-bedrock.md)，现在可以从 Cursor Settings 里的 Bedrock 模型列表选择 Claude Opus 5.5 和 Claude Sonnet 5.5。

### 隐私与安全

- &#x20;**管理员可以把 Agent 限制在已批准的远程工作区。** 新的托管策略 AgentRemoteWorkspaceAuthorities 会在本地 Cursor 窗口里关闭 Agent，并只允许它在远程工作区里。你可以选择把它限制为匹配逗号分隔模式列表的远程授权机构，带 `*` 通配符。如果用户在本地窗口或未批准的远程工作区里启动 Agent，他们会看到管理员已禁用它的消息。

### Git

- **Origin 因超过 100 MB 的文件拒绝推送时的清楚错误。** 当 Source Control 推送到 Origin 仓库，且推送因为文件超过 100 MB 限制而被拒绝时，错误现在会写出过大的一个或多个文件，而不是告诉你先拉取。它也会说明如何修复：用 Git LFS 跟踪该文件，并从这次推送的每个 commit 里移除它。

### 修复

- &#x20;**Okta FastPass 登录再次在内置浏览器里工作。** 在内置浏览器里用 Okta FastPass 登录不再停在 “Okta FastPass requires network permission”。浏览器现在授予 Okta Verify 所需的本地网络权限。
- **Agent 工具的修复。** Agent 对非常大的文件的编辑不再切断文件的其余部分，松散的单行替换不再拼接到错误的文本，同一步里编辑之后立刻读取会返回更新后的文件。Agent 不再在未知工具调用上挂起，后台 shell 在空闲聊天中存活。也修复了 Agent 收集 git diff 或查找分支的 pull request 时，由精心构造的分支名带来的 shell 注入风险。
- **长对话摘要的修复。** 达到上下文限制的 Agent 聊天现在会压缩并继续，而不是在限制处反复失败，摘要不再丢掉对话早期的事实。摘要之后 Agent 也会逐字保留你最新的请求，接近限制时它会复用上一回合开始的摘要，而不是每回合重新开始。
- **MCP 登录和设置的修复。** 连接 Microsoft MCP 服务器现在会显示账户选择器，对项目 MCP 服务器配置的编辑会立刻出现在设置里。
- **远程连接和文件监视的修复。** 握手期间连接掉线时，连接到 SSH、容器或隧道远程不再挂起，没有文件夹打开的远程窗口会完成连接。重连之后，资源管理器和编辑器继续接收远程文件变更，绝对的监视器排除路径现在会被遵守。检出之后的第一次刷新，Agent 的分支显示会显示新分支。
- **内置浏览器、设置、存储和崩溃处理的修复。** 新浏览器标签不再闪白，切换标签不再显示上一页，Cursor Settings 侧边栏保持固定，其底部项目可以到达。前一个实例仍在关闭时打开 Cursor 不再丢弃本地状态，带 AppArmor 4 的 Linux 上沙箱化的 Agent 命令可以再次到达网络。反复的内存不足崩溃不再用调试数据填满磁盘。
- **Tab、Customize 和 Debug Console 的修复。** 多行 Tab 建议现在在带 Windows（CRLF）行尾的文件里正确显示。Customize 里的 “Use your skills with Cloud Agents” 横幅以其正确样式渲染，调试会话获得焦点之前样式改变时，Debug Console 不再抛出错误。
- **macOS 不再请求 Music 或 Photos 访问。** 在 macOS 上，打开包含 Music、Movies 或 Pictures 文件夹的文件夹，不再触发 Apple Music、Photos 或其他访问提示。文件监视和文件搜索，包括 @ 菜单，现在会跳过这些文件夹。
- &#x20;**修复了允许列表被管理时终端批准上的 Always Run。** 当团队策略或 [permissions.json](https://cursor.com/docs/reference/permissions.md) 设置你的终端允许列表时，Agent 的终端批准卡片不再提供 Always Run。以前选择它没有效果，因此同一条命令一直请求批准。
- **更小的本地状态文件。** Cursor 现在在你退出时回收本地状态数据库里未使用的空间，因此它们会随时间缩小，而不是只增长。Cursor 限制清理运行多久，因此退出保持很快。

## v2026.10.08

### Auto-review

- **Auto-review 对有风险的删除更严格。** Auto-review 现在会在递归、强制、通配或镜像删除之前请求批准，如果其目标的解析可能与所写的不同，例如未设置的变量或路径里多余的空格。参见 [Configuring Auto-review](https://cursor.com/docs/agent/security/run-modes.md#configuring-auto-review)。

### 模型

- **Bedrock 上的 Claude Opus 5.5 和 Sonnet 5.5。** 如果你用 `agent bedrock` 设置了 [AWS Bedrock](https://cursor.com/docs/customizing/aws-bedrock.md)，现在可以从自带密钥的模型列表选择 Claude Opus 5.5 和 Sonnet 5.5。

### Skills

- **`/skills` 标出打破 Agent Skills 限制的 Skills。** 能加载但超过规范限制的 Skills，现在在 `/skills` 里显示黄色警告计数，选中一个会在其下列出要修复的内容。警告覆盖超过 64 个字符或不呈小写连字符形式的名称、与文件夹不匹配的 frontmatter `name`、超过 1,024 个字符的描述，以及大约超过 5,000 token 的正文。参见 [SKILL.md format](https://cursor.com/docs/skills.md#skillmd-file-format)。

### ACP

- **ACP 客户端现在从被监视的后台 shell 得到唤醒。** 在 [ACP](https://cursor.com/docs/cli/acp.md) 模式里，当 Agent 启动一个监视输出的后台 shell 时，提示词现在保持打开，输出匹配时 Agent 会醒来并响应。以前 Agent 会说它会被通知，但唤醒从未到达。提示词等待时，客户端显示进行中的 `Watching: <reason>` 工具调用。取消提示词或发送新的会结束等待，而不停止 shell。
- **切换团队后，ACP 会话不再在你的旧团队下继续运行。** 如果 [ACP](https://cursor.com/docs/cli/acp.md) 服务器运行期间你切换团队，`session/new`、`session/load` 和 `session/prompt` 请求现在会以 `Your team has switched to <team>. Create a new session to continue.` 失败，服务器退出。以前它继续在启动时所在的团队下工作。

### 自托管 worker

- **任意仓库池的 Agent 现在在它们认领的仓库内工作。** 在以 [`--clone-git-repos`](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#any-repo-pools) 启动的 worker 上，Agent 的工具、shell、Rules、Skills 和 Subagents 现在在为其认领克隆或复用的检出里运行，认领结束时回到 worker 目录。worker 也会重新克隆，而不是复用一个带有更早 Agent 可能留下的未跟踪 Rules、Skills 或 `AGENTS.md` 文件的检出，worker 日志会列出阻止复用的文件。
- **自托管 worker 在注册太多仓库时停止。** 当 Cursor 因为 `agent worker` 注册的仓库超过 worker 允许的数量而拒绝它时，worker 现在会退出，消息给出数量、上限，以及再次启动之前要移除多少仓库文件夹，而不是无尽地重连。
- &#x20;**在预配之前验证被认领的运行是给谁的。** `agent worker controller --identity-audience <aud>` 向每次认领索取一个短时签名的[身份 token](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#identity-tokens)，绑定到你的受众和一个新的 nonce。你的 `--spawn` Hook 收到 `CURSOR_IDENTITY_TOKEN`、`CURSOR_IDENTITY_TOKEN_EXPIRES_AT` 和 `CURSOR_IDENTITY_NONCE`，因此 Hook 可以在创建机器或交出凭据之前检查 Agent 及其所有者。如果认领回来时没有 token，控制器会释放该认领并退出。该标志只在认领模式工作，不能与 `--warm-idle` 组合。

### Cloud Agents

- **`agent env deploy` 接受注释和尾随逗号。** 传给 `agent env deploy` 的环境文件现在可以包含注释和尾随逗号，因此你可以就地为 Cloud Agent 设置加注释。无效文件会以指向解析停止位置的语法错误失败。

### 修复

- **MCP OAuth 不再发送未填充的客户端 ID 占位符。** 当 MCP 服务器的 OAuth 配置仍有未解析的 `${VAR}` 客户端 ID 时，CLI 现在把客户端 ID 视为未设置。它不再把字面占位符发给授权服务器，因此登录不再因令人困惑的客户端错误而失败。
- **Agent 工具的修复。** 模型调用未知工具时 Agent 不再挂起，超出范围的 Shell 或 Grep 参数不再结束会话。对大文件的编辑会安全失败，而不是截断它们，松散的单行替换不再拼接到错误的文本，编辑或创建文件的 shell 命令之后立刻 Read 会返回新内容。Glob 现在能找到空文件和二进制文件并跳过 `.git`，生成的图像以正确的文件扩展名保存。
- **MCP 工具的修复。** 另一个 Cursor 应用已经在使用登录回调端口时，MCP 登录不再失败。当工具似乎缺失时，Agent 现在会刷新服务器的工具列表。当工具不存在时，错误现在会列出 Agent 可以调用的工具。
- **自托管 worker 的修复。** worker 在慢链路上传大消息时不再掉线，在服务器接受它们之前不再报告自己就绪，也不再删除同一台机器上另一个 worker 推送的 git 凭据。现有检出不再因为系统或全局 git 配置而被拒绝，停止 worker 现在会打印原因。你移除的 Agent 存储在重启后保持移除，关闭时挂起的存储会在下一次认领时重新打开。
- **长会话和摘要的修复。** 达到上下文限制的会话现在会摘要并继续，而不是卡住，摘要之后你最新的请求被逐字保留。对话被摘要之后，Debug Mode 不再使回合失败，中断回合期间排队的消息所附的图像现在会在下一回合发送。
- **切换团队后 Agent 存储同步继续工作。** 只要你仍是拥有该存储的团队的成员，更改活动团队后本地 Agent 存储不再无法同步。

## October 6, 2026 发行

### Pull request

- **看出 pull request 合并还需要什么。** `origin pr requirements` 列出必需的门禁及其在 head 的状态、所需的批准、仅限人工的路径、Code Review Gate，以及栈里的阻止项。为脚本加上 `--json`。
- **更安全的合并，以及 merge-when-ready 状态。** 如果列出的 pull request 已经移动，`origin pr merge --expect-head <number>=<sha>` 会拒绝合并，`--auto --downstack` 为这个之下每个未合并的 pull request 打开 merge-when-ready。`origin pr view`、`status` 和 `requirements` 显示一行 `Merge-when-ready`，`--json mergeWhenReady` 返回它。
- **从 CLI 管理审查者和标签。** `origin pr reviewer list`、`request` 和 `remove` 管理用户、团队（`org/team`）和服务账户的审查请求。`origin pr create` 接受 `--label` 和 `--reviewer`，并在创建 pull request 之前检查两者。
- **不只等待 CI，并筛选检查。** `origin pr watch --until` 可以等待 `merged`、`head:<sha>` 或 `comment:<regex>`，`--json` 返回一个结果对象。`origin pr checks --name <pattern>` 把输出和退出码限制为匹配的检查，输出会写出检查所运行的 commit。
- **编辑和删除评论，并查看一个审查线程。** `origin pr comment edit` 和 `origin pr comment delete` 更改或移除你写的评论，`--edit-last` 或 `--delete-last` 作用于你最新的一条。`origin pr thread view <thread-id>` 打印单个审查线程，评论 JSON 现在包含 `url`。
- **把 `origin pr diff` 限制到特定路径。** 在 `--` 之后列出文件、目录或 glob，例如 `origin pr diff 123 -- src/ '*.md'`。在 pathspec 和 `--exclude` 里，`**` 现在匹配任意深度，包括顶层。
- **查看 pull request 的 Cloud Agent。** `origin pr view <number> --agents` 列出附加到 pull request 的 Cloud Agent，`--json agents` 为脚本返回它们。
- **`origin pr search` 里更多 GitHub 搜索语法。** `-label:bug` 这类否定限定符、`has:` 限定符、范围之间的 `OR`，以及 `mentions:` 现在可以工作。`origin pr search --help` 列出每个受支持的限定符。

### 栈

- **服务器端重新堆叠。** `origin stack restack --server` 让 Origin 变基你的栈，并在一个原子步骤里移动其分支，因此你的克隆什么都不强制推送。加上 `--dry-run` 先预览。

### 脚本

- **更多 `gh` JSON 字段，以及 `--json help`。** `origin pr view` 和 `origin pr list` 接受更多 `gh` 字段名，例如 `body`、`state`、`headRefName` 和 `isDraft`。向 `origin pr view` 或 `origin pr list` 传入 `--json help`，列出它们接受的字段。
- **用 `ORIGIN_REPO` 设置默认仓库。** 接受 `-R, --repo` 的命令会回退到 `ORIGIN_REPO` 环境变量，然后是你的克隆的 `origin` 远程。
- **找到 GitHub 仓库的 Origin 副本。** `origin repo from-github owner/repo` 显示镜像某个 github.com 仓库的 Origin 仓库，带其克隆 URL 和事实来源，使用你的 `gh` 登录。

### 可靠性

- **请求不再挂起。** Origin 请求默认在 60 秒后超时；设置 `ORIGIN_TIMEOUT_MS` 来更改它，或用 `0` 表示没有限制。如果 head 已经移动，`origin pr merge --match-head-commit <sha>` 会拒绝合并，常见的服务器拒绝会打印建议的修复。
- **更短、更清楚的错误。** 打错的命令会打印错误和指向 `--help` 的指针，而不是完整的帮助文本。在 Cloud Agent VM 里，`origin api` 会带着指导停止，而不是以裸 HTTP 401 失败。
- **`origin auth login` 显示你登录成了谁。** 成功登录之后，包括使用 `--api-key`，它会打印你登录的账户的邮箱。

### 修复

- **修复了栈导航和重新堆叠。** 当目标分支只存在于远程时，`origin stack up`、`down`、`top` 和 `bottom` 现在在单分支和浅克隆里工作。父分支 squash 落地之后，`origin stack restack` 不再重放子分支曾经合并进来的主干 commit。
- **修复了 `--remote` 处理。** 接受 `--remote` 的命令在它被重复或格式错误时现在会清楚地失败，以 `-` 开头的远程名不再被 git 读成选项。
- **修复了 pull request diff 和仓库参数。** `origin pr diff` 显示真实的文件模式，并用 `git apply` 干净地应用。以 `.git` 或 `/` 结尾的仓库参数会正确解析，`origin pr list --mine` 在 token 会话里给出可用的步骤。

## Oct 5, 2026 当周

### Web

- **更接近 GitHub 风格的 pull request 搜索。** pull request 搜索支持 `has:label` 和 `has:assignee`。搜索 `review-requested:<user>` 或 `review-involves:<user>` 也会匹配发给你能看到的那个人的团队的审查请求。
- **合并不再意外把 pull request 落到另一个 pull request 的分支上。** 如果 pull request 以另一个未合并 pull request 的 head 分支为目标，而它没有堆叠在其上，或以一个关闭但未合并的为目标，合并会被阻止，并有消息说明它会落到哪里。你可以先合并父级、重新设定目标、重新打开并堆叠它，或在 pull request 页面上使用 Merge anyway。
- **堆叠 pull request 的合并阻止项会说明该做什么。** 当堆叠的 pull request 无法合并时，阻止项会告诉你如何修复，例如点名要从哪个 pull request 重新堆叠，或在基线分支被删除时要重新设定到哪个默认分支。自己有合并冲突的 pull request 不再只显示为在等栈里更低处的冲突。
- **从 pull request 时间线撤销一次冲突解决。** 当冲突解决被应用到 pull request 时，Activity 时间线会连同它创建的 commit 一起显示，最近的一次有 Undo 按钮，恢复先前的 head。时间线也记录是谁撤销的，以及哪个 commit 被恢复。对堆叠的 pull request，按钮的工具提示显示撤销会影响栈里多少个 pull request。
- **在非常大的 pull request 上看见并加载每个文件。** 当 pull request 的文件多于 Files 视图一次加载的数量时，列表会显示还剩多少文件，按顶层文件夹以及按添加、删除、修改或重命名计数。在更改文件超过 50,000 个的 pull request 上，Load the next 50,000 files 会从停止的地方继续列表。
- **收件箱分区里更准确的审查筛选。** 在收件箱分区筛选里，“Has 1 or more requested reviewers” 只计算还没有人回答的审查请求，“Has an active review” 只计算来自作者以外的人的未关闭反馈，忽略已驳回的审查。一旦另一名团队成员审查了它，从你的团队请求的 pull request 就不再等你，与 GitHub 上一样。
- **自定义 code tours 最多可以覆盖 500 个文件。** 当你为自定义 code tour 挑选文件时，最多可以选择 500 个，而不是 100，Generate 不再因为文件路径太长而拒绝选择。
- **在评论里用 `#` 链接 pull request。** 在 pull request 评论里输入 `#`，会建议该仓库最近的 pull request，因此你可以链接到一个而不离开页面。以前菜单打开是空的。
- **按作者筛选仓库的 commit 历史。** commits 页面在分支选择器旁边有作者选择器。选择贡献者或输入邮箱地址，只列出该人在该分支上的 commit，并通过其 URL 分享筛选后的视图。
- **更简单的个人 Slack 通知设置。** 在 [Notifications 设置](https://cursor.com/docs/origin/settings.md#notifications) 里，每个个人 Slack 通知开关在你切换时立刻保存，保存失败时显示错误，你可以不离开页面就连接 Slack。Slack pull request 卡片有 Manage notifications 菜单，审查提醒摘要直接链接到你的通知设置。
- &#x20;**审计日志记录仓库可见性变更。** 当有人把 Origin 仓库设为公开、内部或私有时，你团队的审计日志会记录是谁改的、旧的和新的可见性，以及时间，包括 Agent 的草稿仓库被发布时。
- **更清楚的推送错误，以及更可靠的大型推送。** 当推送因超过 100 MB 的文件被拒绝时，Git 会告诉你如何修复：用 Git LFS 跟踪该文件，并用 `git rm --cached` 或 `git filter-repo` 从这次推送的每个 commit 里移除它。大型推送，包括 `git push --quiet`，在 Origin 处理它们时不再超时，被推送规则拒绝的推送会为每个被拒绝的 ref 显示消息，而不是丢掉连接。
- **对合并队列吞吐量有更多控制。** 仓库合并队列设置在 Maximum speculation depth 旁边有单独的 Maximum concurrent validations 字段，两者都接受最多 1000 的值。推测深度现在计入队列最前面的 pull request，因此深度 1 一次测试一个 pull request。新合并队列默认深度为 5，以及 5 个并发验证。
- **合并队列在落地 pull request 时不再与你的编辑竞态。** 在队列正在完成一次落地尝试时关闭、移到草稿、重新设定目标，或把 pull request 移出合并队列，会被拒绝，而不会与落地冲突。错误会写出队列正在落地到的分支，并告诉你尝试完成后该做什么。
- &#x20;**合并队列会说明规则集何时阻止它。** 如果仓库规则集阻止合并队列写入其分支或 ref，受影响的 pull request 会被移出队列，而不是留在卡住的状态。pull request 时间线里的 Removed from merge queue 行会写出该规则集，并告诉你为队列的 ref 添加排除或调整规则，然后重新入队。
- &#x20;**Origin CI 和合并队列按名称出现。** 来自 Origin CI 的检查显示为 Origin CI 应用，合并队列落地在推送 webhook 和审计日志里把 Origin Merge Queue 命名为推送者，而不是未命名账户或空的推送者。Origin 应用写下的 commit 以应用的显示名称作为作者，没有 \[bot] 后缀。
- **从 Cloud Agent 环境运行 Origin CI 作业。** 在作业、包，或作为 `origin.ci.ts` 里的工作区默认值上设置 `agentEnvironment`，作业会从该环境最新的成功构建启动，而不是容器镜像，带上你的 Cloud Agents 使用的相同工具和依赖。环境必须属于同一仓库，不能与 `image` 组合，并且只在 Linux 上运行。
- **对 Origin CI 作业有更多控制。** 在 `origin.ci.ts` 里，作业可以设置 `platform.disk`（例如 `"32Gi"`）来保留自己的磁盘大小，作业级的 `workingDirectory` 适用于每个没有设置自己的步骤。你也可以把作业放进 `group`，并在另一个作业的 `needs` 里列出该组，以等待它们全部。
- **Origin CI 配置变更。** 包的 `origin.ci.ts` 可以设置 `on`，使它只在列出的事件上运行，没有 `on` 的包跟随最近的、列出它的父工作区。你可以在配置的几个地方复用同一个对象，作业级的 `when` 会以错误失败，告诉你把条件作业留在 `jobs` 之外。Origin CI 只读取 `origin.ci.ts`：名为 `origin.ci` 的配置、对 `.ci` 文件的导入，以及包的 `extraPaths` 字段不再受支持。
- **Origin CI 检查打开其作业页面。** 在 pull request 上点击 Origin CI 检查，会打开该作业的页面，或整体检查的运行页面，而不是带日志尾部的窗格。作业页面链接到运行所测试的 commit 上该作业的 `origin.ci.ts` 文件，并显示代码获取花了多久。
- **审查收件箱的修复。** 分区计数加载时，审查收件箱不再跳来跳去。已合并或已关闭的 pull request 不再重新出现在旧分区里，它们上面的 Agent 运行不再出现在 Needs you 下。收件箱搜索会在片刻内跟上已合并 pull request 更正后的目标分支，而不是最多一小时后。
- **diff 的修复。** 以字节顺序标记开头的文件现在在 pull request diff 里正确显示第一行。
- **冲突检查的修复。** 冲突仍在检查时，合并框显示 “Checking for conflicts…”，并按住合并，而不是声称分支是干净的。
- **Origin CI 运行的修复。** 当分支有合并冲突，或与其基线没有共同历史，或无关的 `origin.ci.ts` 无法求值时，pull request 检查不再失败。路径筛选适用于分支的第一次推送和强制推送，在默认镜像上调用 `node` 的步骤能找到它。
- **代码库页面和仓库的修复。** 文件和 commit 历史里的 Load more 会一直加载，直到新条目出现。标签名称和描述按字符计数，因此 emoji 不再提前碰到长度限制。与代码库路由冲突的命名空间名称，例如 settings 或 apps，不再能被认领，Public 仓库不再同时显示 Shared 徽章。

### API

- **通过 Origin API 有更多方式查找 pull request 并自动化审查。** 你可以把 pull request 列表筛选为带有你点名的每个标签的那些，并按创建、更新或相关性在任一方向排序 pull request 搜索结果。团队 API 密钥可以在它们被限定的仓库里提交 pull request 审查。参见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- &#x20;**Origin API 里的成员邮箱和 GitHub 详情需要它们自己的范围。** 只有调用者有仓库成员读取范围时，Origin API 响应才包含行动者邮箱，没有该范围时按作者邮箱筛选 pull request 会返回权限错误。团队审查者名册需要审查者读取范围，GitHub ID 和镜像详情需要镜像读取范围。如果你的应用依赖这些字段，请请求这些范围并让管理员批准；参见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **Origin MCP 工具接受更多参数风格，并链接到评论。** Origin MCP 工具接受 GitHub 风格的参数名，例如 `owner`、`repo` 和 `pull_number`，以 `owner/name` 给出的仓库，以及任意大小写的枚举值，而不是拒绝该调用。`pull_request_read` 工具的评论和线程视图包含打开 pull request 页面上该评论或线程的 `url`。
- **Origin API 响应的修复。** 行动者的账户被删除时，API 读取和 webhook 不再失败，pull request、审查、评论和反应显示其真实作者，包括服务账户。翻阅变更列表、变更搜索结果和比较文件不再跳过或重复条目。参见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。

## Oct 5, 2026 当周

### Projects

- **Agent 可以向你 @ 提及的 Agent 发消息并读取它们。** 当你向 [Project](https://cursor.com/docs/agent/projects.md) 协调者 @ 提及另一个 Agent 时，协调者可以读取该 Agent 的记录稿并向它发消息。它也可以向自己的父协调者发消息，收到消息的 Agent 可以直接回复。访问跟随你的：协调者只到达你提及且仍能打开的 Agent。

### Agents

- **Subagent 进度实时出现在父 Agent 里。** 当 Cloud Agent 把工作交给 [Subagent](https://cursor.com/docs/subagents.md) 时，Subagent 的工具调用和消息现在会在发生时流入父任务之下，而不是只在 Subagent 完成后出现。
- **摘要里的 Agent 链接现在打开正确的页面。** 当 Cloud Agent 的摘要链接到另一个 Agent 时，Slack、GitHub、GitLab 和 Bitbucket 里的链接现在会在 cursor.com 上打开该 Agent 的页面，而不是断开。在 Web 项目板上，协调者文件里的 Agent 链接现在会打开被链接的 Agent，包括链接指向其 Changes 标签时。

### 环境

- **`environment.json` 里的注释和尾随逗号在所有地方都有效。** Cloud Agents 现在完全支持 [`.cursor/environment.json`](https://cursor.com/docs/cloud-agent/setup.md) 里的 `//` 和块注释以及尾随逗号，无论文件在哪里被读取。带注释环境文件的仓库不再得到 “set up your environment” 提示，它们的快照和网络设置现在会生效，而不是被静默忽略。

### MCP

- **Cloud Agents 处理 MCP 速率限制而不进入重试循环。** 当 MCP 服务器对工具限速时，Agent 会按服务器要求的时长暂停该工具，继续使用该服务器的其他工具，并被告知何时可以重试。反复的 Slack 速率限制现在把等待上限设为大约一分钟。

### Self-Hosted Machines

- **命名 Self-Hosted Machine 上的 Project worker 使用该机器的仓库。** 当 Project 协调者在一台特定的 Self-Hosted Machine 上启动 worker 时，worker 现在在该机器上注册的仓库或文件夹里工作，而不是 Project 的仓库。自己没有仓库的协调者现在也可以把 worker 放在命名机器上。以池而不是一台机器为目标会返回错误，请你点名一台机器。

### Slack

- &#x20;**选择你的 Slack Agent 在哪个团队下运行。** 如果你属于多个付费团队，现在可以选择从 Slack 启动的 Agent 在其下运行和计费的 Cursor 团队。连接 Slack 时选择它，或稍后用 Cloud Agents 设置里的 [Team for Slack agents](https://cursor.com/docs/integrations/slack.md#team-for-slack-agents) 设置更改。Slack 线程里的后续保持在 Agent 原来的团队下运行，你的 Slack 工作区固定的团队优先。
- **Agent 工件链接的 Slack 预览。** 指向 Cloud Agent 工件的链接，包括公开的工件分享，现在会在 Slack 里展开，带文件名和类型，视频还带长度。只有分享链接的人能访问该工件时预览才会出现，在工作区外共享的频道里会被跳过。

### 修复

- **Agent 运行和后续的修复。** 按 Stop 之后发送的消息现在会运行，而不是留在队列里，已回答的问题在重新加载后保持已回答。运行不再把另一个分支的 PR 显示为其 PR 链接。
- **仓库访问和 token 的修复。** Cloud Agents 在长运行期间刷新 GitHub token，而不是在运行中途碰到过期 token。启动时的仓库访问错误现在会点名那个为空、被拒绝或需要连接的具体仓库。
- **Cloud Agents Web 应用的修复。** Agent 终端里的 Ctrl+K 和 Ctrl+F 这类键现在会到 shell，而不是触发页面快捷键。Connect GitHub 卡片会点名 Agent 需要访问的仓库。
- **MCP 工具调用的修复。** MCP 工具调用在服务器重启后会恢复，而不是失败，无效参数错误现在显示具体原因。Google Calendar 时间范围搜索只返回该范围内的事件，对垃圾邮件和非常新的邮件的 Gmail 搜索不再返回为空。
- **Self-Hosted Machines 的修复。** 任意仓库池上的 Agent 不再捡起更早运行留在检出里的未跟踪规则或指令。当没有 worker 匹配你的标签时，错误现在会列出所请求的标签。
- **Slack 启动的修复。** 从 Slack 更改或重置你的默认模型和设置，现在对在已迁移的 Slack 工作区下关联的账户有效。从 Slack 启动的 Agent 失败时，⏳ 反应现在会被 ❌ 替换，而不是留在旁边。
- &#x20;**Remote Control 的修复。** 当 [Remote Control](https://cursor.com/docs/cloud-agent/mobile.md#remote-control) 被关闭时，你的手机现在把它显示为已禁用，而不是笼统的不可用错误。尚未链接到组织的企业团队成员，在组织管理员允许之前不再能打开 Remote Control。

## Oct 5, 2026 当周

### 集成

#### GitHub

- &#x20;**pull request 详情只限于有 GitHub 访问权限的人。** Cursor 只向其 GitHub 账户能读取该仓库的人显示 pull request 详情，即使队友已经加载过它们。其他人得到访问错误。
- **GitHub 阻止合并方法时的错误更清楚。** 从 Cursor 用仓库不允许的合并方法合并 [GitHub](https://cursor.com/docs/integrations/github.md) pull request，现在会立刻以 GitHub 自己的原因失败，例如 “Squash merges are not allowed on this repository.”。以前 Cursor 会重试，然后显示笼统的还不能合并消息。
- **GitHub 状态和设置的修复。** 待处理的 commit 状态检查现在显示为运行中，而不是排队，Actions 日志摘录不再在重定向上挂起，GitHub 用户名包含 “cursor” 的人发出的 `@cursor` 命令不再被忽略。GitHub Enterprise Server 应用安装现在落到开始设置的团队。

#### GitLab

- **GitLab 仓库列表和合并请求查找可靠加载。** 大型 [GitLab](https://cursor.com/docs/integrations/gitlab.md) 组的 Bugbot 仓库列表会用到目前为止已获取的项目加载，而不是在超时上失败，为分支查找合并请求现在对通过 AWS PrivateLink 或出站代理到达的 GitLab 实例有效。

#### Linear

- **悬停时显示完整的 Linear 团队名。** 在 [Linear 集成页面](https://cursor.com/docs/integrations/linear.md) 的团队列表里悬停被截断的团队名，即可看到全名。

#### 源代码管理

- **更稳的自托管源代码管理连接。** 当到自托管 GitLab 或 Azure DevOps 的连接短暂掉线时，Cursor 会重试一次读取请求。在 AWS PrivateLink 上，当服务器关闭连接时，GitHub、GitLab 和 Bitbucket 请求也会重试一次。

### 仪表板与管理

#### 插件与 MCP

- &#x20;**控制你的团队可以使用哪些 MCP 和插件工具。** 管理员可以从 Plugins 页面打开或关闭 Team list 强制执行，并从 URL MCP 服务器或插件的详情对话框限制其工具，从实时预览挑选允许的工具。MCPs 标签有 Allowlist rules 编辑器，标出只在 Team list 打开时生效的规则，每个服务器显示其访问状态，例如 Read-only 或 Cloud only，工具提示说明 IDE 和 CLI 为什么阻止它。参见 [MCP server trust management](https://cursor.com/docs/enterprise/model-and-integration-management.md#mcp-server-trust-management)。
- &#x20;**从一个列表浏览并添加插件和 MCP 服务器。** Plugins & MCPs 页面在一个可搜索的表里一起显示已安装的插件和 MCP 服务器，并为所选范围提供 Add 按钮。点击插件会在对话框里打开其详情，而不是单独的页面。

#### 成员与组

- &#x20;**为组成员设置软或硬支出上限。** 在组织组设置里，管理员可以把成员的月度支出上限设为 No limit、Soft limit 或 Hard limit。软上限不阻止成员，可以与节奏提醒配对。硬上限在成员的月度支出达到该金额时阻止他们。参见 [group spend limits](https://cursor.com/docs/enterprise/organization-groups.md#spend-limits)。
- &#x20;**组显示其 slug，正确标记已同步的组，并在大型团队上全部出现。** 团队和组织组列表以及组页面在每个组的名称下显示其 slug。在团队 Groups 标签上，从身份提供方同步的组被标为 Synced 而不是 Manual，Move member 不再把已同步的组作为目的地。超过 200 个组的团队现在会在 Groups 标签、Move member 列表和邀请组选择器里看到全部。
- &#x20;**有人加入你的团队时，待处理邀请会关闭。** 当有人以任何方式加入你的团队，例如 SSO、域名加入、SCIM 或直接添加时，他们对该团队的其他待处理电子邮件邀请会自动关闭，旧的邀请链接仍会把他们带到该团队。当有人通过复制的邀请链接加入时，审计日志把复制该链接的成员记录为邀请者。

#### SSO 与 SCIM

- &#x20;**从任一 SSO 页面打开 SSO 自动加入。** SSO 自动加入开关现在也出现在较旧的团队 SSO 设置页面上，两个 SSO 页面都会在你回到该标签时立刻把刚完成的 SSO 设置显示出来。当身份提供方把 SCIM 用户标为不活跃时，Cursor 移除他们的团队席位，但保留其 SSO 成员身份，因此重新激活他们不必再次添加。参见 [SCIM user management](https://cursor.com/docs/account/teams/scim.md#user-management)。

#### Agent 设置

- &#x20;**控制 Project Agent 可以在哪里运行。** Cloud Agents 安全设置里新的 **Automatically allow Projects to place their work** 开关，决定 Project 的 Agent 能否在 Project 所有者能到达的任何机器、环境或仓库上开始工作而不先询问。关闭时，Agent 在使用新的一个之前请求批准。它对 Enterprise 团队以关闭开始，对其他团队以打开开始。参见 [Cloud Agents security settings](https://cursor.com/docs/cloud-agent/settings.md#security-settings)。
- **选择你的 Slack Agent 在哪个团队下运行。** 如果你属于多个付费团队，Cloud Agents 设置里的 **Team for Slack agents** 设置会选择从 Slack 启动的 Agent 在其下运行、计费和使用设置的团队。当你的 Slack 工作区被限制到一个团队时，该设置被锁定。参见 [Team for Slack Agents](https://cursor.com/docs/integrations/slack.md#team-for-slack-agents)。

#### 自动化

- **自动化页面上的 Mine、Team 和 Explore 标签。** Mine 列出你拥有的自动化，Team 在来自 Cursor 的 Agent 旁边显示你团队的自动化，Explore 放着模板库。团队管理员默认打开在 Team 上，其他人打开在 Mine 上。
- &#x20;**Security Reviewer 和 Vulnerability Scanner 有自己的页面。** From Cursor 自动化列表显示分开的 Security Reviewer 和 Vulnerability Scanner 卡片，取代单个 Security 卡片。每个页面只列出自己的 Agent 和近期运行，其 New 按钮直接创建该类型，被标记的漏洞出现在 Vulnerability Scanner 页面上。旧的 Security 链接重定向到 Security Reviewer。参见 [Security Agents](https://cursor.com/docs/security-agents.md)。
- **部署没有被记录时，自己启动 Rollouts 检查。** 如果已合并的变更在没有记录部署的情况下上线，在 Rollouts 里该变更的页面上选择 **Run checks**，挑选环境和服务，Rollouts 就会开始检查它。推迟的检查显示为 **Deferred** 并带原因，关闭 issue 现在会要求原因，例如 **Fixed** 或 **Duplicate**。参见 [Rollouts](https://cursor.com/docs/rollouts.md#deploy-tracking)。

#### 用量与集成

- &#x20;**按 SDK 筛选用量。** 团队和组织的 Usage 页面在客户端筛选里有 SDK 选项，因此你可以单独看到通过 Cursor SDK 运行的 Agent 的支出和请求。现在对所有人可用。
- &#x20;**为整个团队连接 SharePoint。** 管理员可以从 Integrations 页面连接 SharePoint：选择委托或应用程序权限，输入你的 Microsoft 租户，授予管理员同意，并可选允许写访问。如果设置中途停止，添加 SharePoint 插件的成员会被要求询问管理员，管理员会得到回到 Integrations 完成的链接。

#### 安全与隐私

- &#x20;**审计日志里的 Bitbucket 连接变更。** 审计日志记录 Bitbucket Cloud 或 Bitbucket Server 连接被连接、断开、注册或删除的时间，以及其 token 被设置或清除的时间，并带提供方和主机。用新的 Source Control Connection 事件类型筛选它们。参见 [audit logs](https://cursor.com/docs/enterprise/compliance-and-monitoring.md#audit-logs)。
- &#x20;**更清楚的隐私模式名称。** 隐私设置现在读作 “Privacy Mode (no training)” 和 “Legacy (no storage)”。当你从个人方案邀请队友时，新的 “Share Cursor Analytics” 复选框设置新团队的隐私设置，除非你勾选它，Privacy Mode 保持打开。
- &#x20;**控制 Grok 听写和私密聊天。** 团队管理员可以从 Grok 标签为成员打开或关闭听写。组织管理员在那里也得到 Features 分区，带为组织里每个团队关闭听写或私密（临时）聊天的开关，因此所有聊天都为合规导出保留。

#### 修复

- **仪表板布局和显示的修复。** 团队设置在第一次点击时打开，方案重置和账单周期日期以你的本地时区显示，而不是 UTC。Usage 页面显示你超出月度上限多少，而不是剩余 $0，Overview 设置清单显示已加载的步骤，而不是使整张卡片失败。长名字和邮箱在悬停时以全文截断，页面在加载时不再闪出过期内容或跳动。
- **手机上仪表板的修复。** 导航抽屉可以向左滑动关闭，Members 表和 Usage 筛选适合屏幕，Usage 日期选择器不再加宽页面。团队名称编辑器会换行，并停在 200 个字符，邀请邮箱字段不再自动大写或自动更正。
- **自动化页面布局的修复。** 自动化列表不再在每个自动化的标题和作者之间留下空隙，“Showing N” 计数大小正确、间距适当，筛选标签与页面标题对齐，共享的 GitHub 和 GitLab 触发器使用通用的 git 图标。
- **Rollouts 设置的修复。** 在 MCP 工具上点击 Authenticate 不再抹掉现有的共享连接，已经连接的服务器会刷新，而不是打开登录窗口。数据源列表显示每一行，而不是一个小滚动框，通知帮助文本在移动设备上会换行。
- **环境和密钥的修复。** 环境的页面只显示该环境的历史。双击 Duplicate 创建一个副本，而不是两个，在 Edit Secret 对话框里选择 All repositories 会为每个仓库保存该密钥。
- &#x20;**集成和 MCP 身份验证的修复。** 注册 GitHub Enterprise Server 应用会把它附加到你选择的团队，现有应用列表显示该团队的应用。为服务账户对 MCP 服务器进行身份验证会把你带回你开始的设置页面，私有推理受让人选择器从每个关联团队加载组。翻阅 Origin 仓库卡片不再在翻页失败时藏起卡片或显示错误的页面。
- &#x20;**账户会话和删除的修复。** 当账户的邮箱通过身份提供方、SSO 或目录同步变成不同地址时，该账户上现有的 Cursor 会话会被登出。删除先前曾与另一个账户合并的账户不再失败。

### 自动化

#### 自动化页面

- **自动化页面上的 Mine、Team 和 Explore 标签。** 自动化页面现在打开在 Mine 上，它列出你在个人账户和团队中拥有的自动化，旁边是热门模板。Team 显示你团队的自动化，Explore 放着完整的模板库。现在对所有人可用。团队管理员落到 Team，没有团队的用户看到 Mine 和 Explore。
- **一眼看出每个自动化的工具。** 自动化页面上的 Tools 列把每个工具显示为带图标和名称的药丸。当一行的工具多到放不下时，+N 药丸显示还有多少。

#### 触发器

- **Microsoft Teams 自动化现在在每条频道帖子上触发。** 监听 Microsoft Teams 频道的自动化现在在该频道的普通帖子上运行，而不只是 @提及 Cursor 的帖子。
- **失败的 Slack 触发运行会标在触发消息上。** 当从 [Slack 消息](https://cursor.com/docs/cloud-agent/automations.md#slack-triggers) 启动的自动化失败时，该消息现在得到红色 X 反应，取代沙漏。以前沙漏消失，没有任何东西显示运行失败了。频道仍然不会得到失败帖子。

#### 安全 Agent

- **Vulnerability Scanner 遵循你的自定义指令，并跟进文件。** 团队 Vulnerability Scanner 自动化现在使用你的[自定义指令](https://cursor.com/docs/security-agents.md#custom-instructions) 来塑造它们扫描什么。扫描之后，它们通过自动化的 MCP 服务器执行这些指令所要求的后续操作，例如为每个新发现立案，并在发现的 Slack 报告里链接它们所立案的内容。

#### 自动化运行

- **运行失败时的原因更清楚。** 当组织的策略检查阻止模型请求或不可用时，运行历史现在会告诉你是重试还是联系管理员。因为没有自托管 worker 匹配其标签而失败的运行现在会写出这些标签。

#### 修复

- **Vulnerability Scanner 的修复。** [Vulnerability Scanner](https://cursor.com/docs/security-agents.md) 记录的发现会再次出现，而不是显示为空。列为 host/owner/name、没有 https\:// 的仓库现在会扫描，而不是失败。在托管自动化设置里关闭 Security 的团队不再得到定时扫描器运行。
- **Change Monitor 设置的修复。** 关闭 Change Monitor 现在会停止其 Agent，如果它们不停就请你再试。保存安装看不到的仓库，或缺少 webhook 的仓库，现在会显示具体的设置错误，而不是笼统的。嵌套的 GitLab 路径现在要求完整的 `gitlab.com/<group>/<project>` 形式，完全限定的 GitLab 项目会被正确监视。
- &#x20;**自动化编辑器和集成的修复。** 当你属于多个团队，或使用自托管的 GitHub Enterprise 或 GitLab 时，你的 GitHub 或 GitLab 连接不再显示为已断开。编辑器的 MCP 服务器选项现在列出所选团队的服务器。断开团队的 PagerDuty 集成现在会移除该团队的连接，Security Reviewer 评论和 Slack 消息里的 “Configure here” 链接会在 From Cursor 下打开该 Agent 的页面。
- **Web 和移动设备上自动化页面布局的修复。** 在窄屏幕上，自动化列表现在横向滚动，而不是切掉列，行操作固定在右侧。页面标题、标签、工具栏和列表行的间距和对齐已修复。

### API

- &#x20;**Bugbot 仓库更新端点对 Azure DevOps 仓库的错误更清楚。** [`POST /bugbot/repo/update`](https://cursor.com/docs/bugbot.md#enabling-or-disabling-repositories) 现在对 `dev.azure.com` 和 `visualstudio.com` 仓库 URL 返回 400，说明 Azure DevOps 仓库设置从 Cursor 仪表板管理，而不是告诉你安装 Cursor GitHub 或 GitLab 应用。

### 安全与批准 Agent

#### 安全 Agent

- &#x20;**分开的 Security Reviewer 和 Vulnerability Scanner 页面。** 在仪表板的 From Cursor 下，Security 卡片现在是两张卡片，Security Reviewer 和 Vulnerability Scanner。每张打开一个页面，只列出该类型的[安全 Agent](https://cursor.com/docs/security-agents.md) 及其近期运行，带创建该类型的 New 按钮，以及显示它是否启用的图标。旧的 Security 链接重定向到 Security Reviewer。
- **不完整的审查不再自动批准。** 当安全审查的一部分失败时，Security Reviewer 不再把结果当作干净的，因此它不会自动批准该 PR。如果没有审查者完成，它不发布发现，并留下现有线程。当后续检查超时或失败时，更早的发现保持打开。
- **已接受的发现不会在每次推送时再次发布。** 如果你在没有修复的情况下解决一个发现，例如接受该风险，后来发现同一问题的推送不再打开新评论。同一行上的不同问题仍会得到自己的评论，回来的已修复问题会再次被报告。
- **失败的大型审查更早完成。** 当非常大的安全审查上每个审查者都失败时，审查现在会提前停止，而不是重试到预算用完。

#### 批准 Agent

- **pull request 上更清楚的批准要求。** [PR Routing & Approval](https://cursor.com/docs/approval-agents.md) 检查、评论和审查者悬停卡片现在准确说明什么能通过审查，例如带建议审查者的 “Needs 1 approval from any human reviewer”，来自一组所有者中任意一个的一次批准，或来自所有被点名所有者的批准。自动批准的低风险 pull request 不再显示残留的批准要求。

### 模型与 Cursor Router

- **新模型：Claude Haiku 5.5。** [Claude Haiku 5.5](https://cursor.com/docs/models/claude-haiku-5-5.md) 现在可作为选择加入的模型在 Cursor 中使用。在 Cursor Settings > Models 里打开它。
- **Mistral 模型的修复。** 曾以验证错误失败的 Mistral 请求现在会完成，模型的推理显示为思考，与其回答分开。
- **阿里巴巴和 Mistral 模型的厂商名称。** 模型列表现在用其厂商标记这些模型。

## Oct 5, 2026 当周

### Bitbucket

- &#x20;**更清楚的 Bitbucket Data Center 支持。** Bugbot 仪表板现在按服务器主机名命名 Bitbucket Data Center 组织，并把它们标为 Bitbucket Data Center，而不是 bitbucket.org。Bugbot 不再在 Bitbucket Data Center 上提供 Autofix，其审查脚注不再建议在那里切换到直接推送。[Bitbucket Data Center](https://cursor.com/docs/integrations/bitbucket.md) 需要 Teams 或 Enterprise 方案。

### 审查

- **本地审查会说明它们跳过了什么。** 当你[从 Agent 运行 Bugbot](https://cursor.com/docs/bugbot.md#run-in-your-agent) 或对本地改动运行安全审查，且 diff 大到无法完整审查时，结果现在会说明覆盖了多少更改的文件，并列出未被审查的那些，因此你可以接下来审查它们。这些被跳过文件里的发现不再被当作无关而丢弃。如果每个被报告的发现都被滤掉，Bugbot 会说审查并不干净，而不是报告没有 bug。
- &#x20;**Manual Only 停止从已合并 pull request 自动学习。** 当仓库或你的个人设置把 Bugbot 设为 Manual Only 时，它不再在 pull request 合并或关闭时自动[学习规则](https://cursor.com/docs/bugbot.md#learned-rules)，该学习也不再计入你的 Bugbot 用量。`@cursor remember` 评论仍会教它规则。

### 仪表板

- &#x20;**Bugbot 分析里一致的每日计数。** [Bugbot 分析](https://cursor.com/docs/bugbot.md#analytics) 仪表板现在按 UTC 日计数被审查的 pull request，因此移动日期范围时一天的数字不再改变。PRs reviewed 总计在整个范围内把每个 pull request 计一次，日期选择器标为 UTC。审查分析 API 现在只计数所请求范围内的审查，因此当你请求所有 PR 状态时它与仪表板一致。

### 修复

- &#x20;**审查和 Autofix 的修复。** 启用只运行一次后，审查仍在进行时推送的 commit 现在会被审查，而不是跳过，即使新推送没有 diff，增量审查也保留你的 BUGBOT.md 规则。审查评论不再在 Cloud Agent 并未启动去修复问题时说它已启动；它们会说 Agent 未能启动，或没有问题达到你的 Autofix 严重性阈值。授予 Enterprise 团队的仓库上的审查现在在该团队下运行，而不是安装所有者。
- **PR 摘要的修复。** 如果 PR 摘要挂起或失败，Bugbot 不再在你的 PR 描述或评论里留下过期的 “generating a summary” 占位。它会移除它，或显示 “Unable to generate summary.”。你粘贴在摘要下的图片、视频和文件，在 Bugbot 刷新它时不再被抹掉。
- **Bugbot 仪表板和集成的修复。** 从 Bugbot 仪表板连接自托管 GitLab 实例现在会把它链接到正确的团队，应用安装期间的仓库选择器现在会缩小以适合窗口，因此其搜索框不再开到屏幕外。企业应用重新同意之后为 Azure DevOps 仓库打开 Bugbot，现在会自动恢复，而不是失败。GitHub App 被卸载或仓库被移除时，按仓库的 Bugbot 设置会被清理。

## 1.13.0

### Review

- **Review 里的源分支和基线分支。** [Cursor for iOS](https://cursor.com/docs/cloud-agent/mobile.md) 里的 pull request Review 屏幕在标题下显示源分支和它合并进去的分支，基线分支跟随 pull request 的当前目标。即使长名字在屏幕上被切断，VoiceOver 也会读出完整分支名。

### 修复

- **聊天和审查显示。** 当标签跨多行时，来自 Cloud Agent 的文本引用显示为卡片，而不是原始标记。当 Cloud Agent 的 diff 大到无法显示时，Review 屏幕会说它有太多文件，或有些文件太大。无法在移动设备上渲染的可视化会告诉你在 Cursor 桌面应用里打开它们。

## 3.23

### 聊天与 Composer

- **Cmd+F 可靠地搜索聊天。** 只要聊天或提示词有焦点，或焦点在编辑器面板和 Customize 之外的任何地方，Cmd+F 现在就会在聊天记录稿里打开查找。文件、diff、终端和浏览器查找只在你正在该面板里工作时打开，全屏对话托盘现在有自己的查找栏。聊天查找也只匹配你能看到的文本，搜索时不再闪出 “0 of 0”，并使用与 diff 和浏览器查找相同的黄色高亮。
- **用键盘在长对话里移动。** 按 Cmd/Ctrl+Up 或 Cmd/Ctrl+Down 在你的消息之间跳转，按 Cmd/Ctrl+Shift+Up 或 Cmd/Ctrl+Shift+Down 跳到对话的顶部或底部。只要提示词是空的，这些快捷键在提示词有焦点时也有效。
- **用 `#` 引用 pull request。** 在提示框里输入 `#`，从你的 pull request 里挑选，包括当前 Agent 打开的那些，并把一个作为上下文加到你的消息。这在后续问题输入和编辑已发送消息时也有效。
- **最小化或移除提示词上方的计划。** 提示词上方的计划卡片现在有 Minimize 按钮，把它收成一颗 Plan 药丸，因此你可以继续聊天而不挡路。右键 Plan 或 Canvas 药丸并选择 Remove 来清除它。计划卡片也优先于后续建议，并在另一张卡片关闭后打开。
- **批准提示说明它们为什么出现。** 当 Agent 要求编辑或读取工作区之外的文件，或编辑受保护的配置文件时，批准卡片现在会说明原因，例如 “This edits a file outside your workspace.”。读取批准卡片显示完整文件路径，并带 Allow 按钮。
- **排队的消息不再丢失。** 你在 Agent 工作时排队的消息，在回合结束时不再被丢掉。

### Cloud Agents

- **把本地文件拖进 SSH、WSL 和开发容器 Agent 的提示词。** 当你把计算机上的文件或文件夹拖入、粘贴或附加到远程机器上运行的 Agent 的提示词时，Agents Window 会把它复制到那台机器并提及该副本，因此 Agent 可以读取它。副本放到工作区之外的单独上传文件夹，因此它们从不显示为工作区改动，慢的复制会显示进度通知。
- **把文件夹拖到 Cloud Agent 提示词上以附加其文件。** 把文件夹拖到 Cloud Agent 的提示词上，现在会附加其中的文件，跳过隐藏文件。每次拖放最多附加 20 个文件，当文件夹里还有更多、只加了前 20 个时，通知会告诉你。
- **Cloud Agent 提示词里的 Slack 提及可读。** 当 Cloud Agent 从 Slack 启动时，提示词里被提及的人现在显示为 @Name，而不是原始的 Slack 用户 ID。如果安装了 Slack 应用，点击提及会在 Slack 应用里打开那个人，否则在 Web 上的 Slack 里打开，Find 会按所显示的名字匹配。
- **新的 Cloud Agent 使用你仓库的默认环境。** 当你为新 Agent 选择 Cloud，通过点击或用 Cmd+; 循环时，Agent 现在在该仓库上运行，由 Cursor 选择其环境。以前它可能预选另一个队友保存的环境。如果 Agent 带着警告启动，例如回退到没有你环境设置的默认镜像，聊天里的环境状态行会显示该警告、计数它，并自己打开。
- **Continue on Cloud 显示已保存的环境名称。** Continue on Cloud 选项现在显示每个已保存环境的名称，因此你可以区分同一仓库上的两个环境。
- **Cloud Agent 链接打开正确的 Agent。** 来自 cursor.com/agents、Slack、Linear、Teams 或 Copy Deep Link 的 Cloud Agent “Open in Cursor” 链接，现在会在 Agents Window 里打开该 Agent，即使你以前没打开过它，即使 IDE 有焦点。以前该链接可能把你丢到新聊天。如果找不到该 Agent 或你没有访问权限，你现在会看到错误。
- **侧边聊天现在在 Cloud Agent 上工作。** 你可以用 `/side`、+ 菜单，或选中文本并选择 Add to Side Chat，从 Cloud Agent 打开[侧边聊天](https://cursor.com/docs/agent/overview.md#side-chats)，与本地 Agent 上相同。侧边聊天作为自己的 Cloud Agent 运行，因此你可以提问而不打断主运行。Projects 内部不提供侧边聊天，它们使用线程。

### Projects 与工作区

- **重命名项目时 Edit Workspace 保留你的聊天。** 在 Edit Workspace 里重命名多根项目，现在会把它的本地聊天和草稿移到重命名后的项目，而不是把它们留下。你也可以移除文件夹，直到只剩一个。

### 文件、改动与 pull request

- **从 Copy 菜单复制 Agent 的 pull request 链接。** 当 Agent 打开了 pull request 时，面板和侧边栏里 Agent 的 Copy 菜单现在包含 Copy PR Link。它复制你偏好的审查目的地的链接，例如 GitHub 或 Graphite，并用提示确认。
- **更清楚的 pull request 状态，以及更快的 PR 操作。** 其检查只在等某人批准的 pull request，现在显示在黄色的 Action Required 组下，而不是 CI Failing，可合并的 PR 读作 Ready。在 PRs 托盘里，栈中已合并的 PR 折进你可以点击展开的 “N merged” 行，在托盘或应用轨道里中键点击 PR 行会在浏览器里打开它。在 PR 标签里，按 Cmd+Enter（Windows 和 Linux 上为 Ctrl+Enter）在讨论线程里提交回复。

### 导航与布局

- **用方向键切换命令面板筛选。** 当命令面板里的搜索框为空时，按 Left 或 Right 在筛选药丸之间移动，例如 All、Agents、Files 和 Actions。这时页脚会显示方向键提示。
- **把 Subagent 和聊天链接拖进分栏。** 你可以把 Subagent 从 Subagent 托盘，或对话里的聊天链接，拖到对话磁贴上，在分栏窗格里打开它。上、下、左、右拖放区现在在宽或窄的窗格上更可靠地工作。
- **Cursor Light 主题里更清楚的语法颜色。** Cursor Light 和 Cursor Light Colorblind 现在给每种代码自己一致的颜色，因此代码更容易扫读。类型用蓝色下划线，属性是蓝色，装饰器和宏是橙色，参数是斜体。
- **按 Escape 显示或隐藏 Apps 面板。** 当焦点不在文本框里，且没有打开对话框或菜单时，Escape 切换 Apps 面板，如果它是全屏则先退出它。提示词里的 Escape 也会关闭 Composer 上方的信息性托盘，用 Escape 关闭对话框、菜单或全屏视频不再同时切换 Apps 面板。

### 自定义与设置

- **从 Plan & Usage 购买额度并管理你的方案。** 在有每周用量额度的方案上，达到每周上限现在会显示带 Buy Credits 和 Upgrade Plan 的通知。Buy Credits 打开应用内购买对话框，购买完成后封锁解除。Plan & Usage 设置也显示 Credits 分区，带你的预付余额、待处理充值和自动充值，以及一行显示你的每周用量何时重置。
- **需要确认的 MCP 工具在 Auto-review 里总会询问。** 修复了一个问题：Auto-review 分类器可能批准服务器标记为需要确认的 MCP 工具调用，例如合并，而不给你显示批准提示。除非你使用 Run Everything 或已把该工具加入允许列表，这些调用现在会显示批准卡片。
- **修复了指向无扩展名脚本的 Windows Hooks。** 在 Windows 上，命名没有扩展名脚本的 Hook 命令，例如插件的 `scripts/run`，现在会运行它旁边匹配的 `.ps1`、`.exe`、`.bat` 或 `.cmd` 文件。在此修复之前，这些 Hooks 会打开一个新控制台窗口，从未收到输入，并在什么都没做的情况下报告成功。
- **修复了属于多个团队的人的仓库访问规则。** 管理员仓库允许列表和阻止列表现在只来自你的活动团队，因此另一个团队的允许列表不再阻止你应该能打开的仓库。切换团队会立刻应用新团队的规则。
- **从任意文件标签打开或关闭 Cursor Tab。** 代码文件标签上的 “...” 菜单现在总会包含 Cursor Tab 开关，因此你可以不离开编辑器就打开或关闭 Tab 补全。你的键绑定风格，例如 Vim 或 Emacs，在该菜单里也保持选中，而不是更新后重置为 Default。
- **设置现在说明 macOS 上缺失的菜单栏图标。** 如果 macOS 在 System Settings > Menu Bar > Allow in the Menu Bar 下关掉了 Cursor，Menu Bar Icon 设置现在会说明这一点，并提供 Open Settings 按钮把它打开。当更早的隐藏图标偏好把它挡在视野之外时，Cursor 也会自动恢复该图标。
- **对固定按需上限的更清楚说明。** 有个人固定按需上限时，Settings > Plan & Usage 现在说明请求会在上限处停止，已经在运行的请求所产生的超额不会被计费，除非你在该账单周期提高上限。它以前说超过上限的用量稍后会被计费。团队管理的上限保持其现有措辞。

### 修复

- **Agents 托盘和 Subagents 的修复。** Back 和 Forward 现在会步入和步出从 Agents 托盘打开的 Subagent 预览，包括从侧边聊天打开的预览。折叠的 Agents 药丸不再把已停止的 Subagent 显示为需要注意，失败的本地 Subagent 不再卡在托盘里（你仍可以从其 Task 块打开它们）。云 Subagent 的 Task 卡片现在即使 Subagent 在卡片出现之后才启动，也会显示其名称和进度。
- **Cloud Agent 创建和后续的修复。** 未能启动 Cloud Agent 的提示词会回来，以便你重试，连续的后续按你发送的顺序到达。
- 聊天里的最后几条消息不再藏在待处理问题面板后面。
- 超过 100,000 个字符的未发送草稿现在能在重新加载后保留，而不是回来时是空的。
- 拖进后续 Composer 的图片现在只附加一次，不是两次。
- 输入后续不再使 Agent 面板崩溃。
- 已回答的 Agent 问题不再重新打开。
- 在对话里选中文本时，Cmd+L 把它加到该对话，包括侧边聊天。
- **提示词上方托盘和药丸的修复。** 提示词上方的药丸在托盘或横幅打开或关闭时不再跳动或闪烁，把一个托盘换成另一个现在会在原地交叉淡入，而不移动布局。插件安装确认现在位于其他建议之上，确认按钮旁边有 View Details，插件已添加的通知可以用 Escape 关闭。
- **Agent 回合可靠性的修复。** 提供方解码错误现在会自动重试。
- **MCP 服务器登录的修复。** 连接到 Azure DevOps 这类由 Microsoft Entra 支持的 MCP 服务器，不再在每次登录时请求同意，因此管理员已经授予同意的租户不再落到 “Approval required.”。
- **Windows、git 和非 QWERTY 键盘的修复。** 插件现在随 git 2.26 和 2.27 加载，在 Windows 上，关闭模态之后最小化、最大化和关闭按钮不再消失。在 AZERTY 和 Dvorak 这类布局上，物理 Z 和 Y 键上的快捷键，例如 Cmd+W，不再触发撤销或重做。
- **分支和 git 操作的修复。** 自动化分支选择器立刻显示默认分支，本地 Agent 从其 shell 打开的 pull request 现在会被检测。
- **Cloud Agent git 操作的修复。** Cloud Agent 工作时，Fix Merge Conflicts 保持在其加载状态，检出无法更改的 Cloud Agent 不再出现 Checkout Branch 和 Fast Forward Branch。
- **侧边栏和项目选择器的修复。** 从 Agent 的右键菜单选择 Rename 现在会可靠地打开重命名字段，侧边栏页脚在启动时立刻显示你的账户名和头像。当每个 Agent 都被固定或在项目里时，Chats 标题使侧边栏自定义菜单仍可到达，No Repo 分区不再提供一个什么都不做的 Remove from Sidebar 操作。在项目选择器里，长文件夹路径在中间缩短，使文件夹名保持可见，用键盘高亮一行时行控件会出现。
- **未读徽章与侧边栏一致。** Agents Window 打开时，Dock 徽章和菜单栏未读计数不再计入不在侧边栏里的 Cloud Agent。
- **无障碍修复。** 屏幕阅读器现在会读出 Composer 菜单里高亮的项目及其位置，例如斜杠命令，把 Agent 磁贴命名为 “Panel 1”、“Panel 2”，并带 “Panel actions” 菜单，把可折叠的侧边栏分区标题当作可展开按钮。聊天记录稿里的按钮在你用键盘到达时显示焦点环，对话框打开时更可靠地聚焦主按钮，因此 Enter 确认。
- **登录、截图和终端链接的修复。** 不稳定的公司代理后面的 Enterprise 成员，不再在一次失败的团队成员身份检查后被登出。隐藏或最小化窗口的 Agent 截图在 5 秒后超时，而不是挂起，已完成的终端命令不再为在另一个终端里启动的服务器自动打开浏览器。
- **语音聊天的修复。** 没有得到回复的更早口头请求，在后来的请求完成后不再在通话的其余时间保持打开。当 Agent 在等你时，语音会说明一次它不能接受新请求，而不是重复自己。隐藏聊天不再隐藏浮动通话控件，因此 Hang up 留在屏幕上。
- **Agent 问题和工具调用的修复。** Agent 在连接掉线后询问的问题和计划批准，不再用更早的回复来回答。参数略有格式错误的内置工具调用现在会运行，而不是失败，加载失败的原生代码分块器现在只禁用语义搜索。当你在新输出流入时滚过底部，记录稿也会保持其位置。
- **稳定性和本地存储的修复。** 在 Private Inference 和 Cursor 管理的模型之间切换，现在会在应用它的重启之后保持切换。前一个实例仍在关闭时打开 Agents Window 不再丢弃本地状态，反复的内存不足崩溃不再用数 GB 的文件填满调试数据文件夹。Okta FastPass 登录再次在内置浏览器里工作。
- **Agents Window 的修复。** 当 GitHub 对请求限速时，Agent 的 pull request 状态继续显示最后已知的状态，而不是无法加载。固定的 Build 卡片在半透明自定义主题下不再透出内容。关闭 Settings 直接回到同一对话，其记录稿和 Composer 保持完整。

## 3.23

### 方案与账单

- &#x20;**Plan & Usage 显示你的方案和预付额度。** 在 Premium、Plus、Super 和 Ultra 方案上，Cursor Settings > Plan & Usage 按名称显示你的方案和用量重置日期。这些方案上的个人账户也得到 Credits 分区，带预付余额、待处理充值和自动充值规则，以及添加额度或更改规则的按钮。

### Agent 与聊天

- **批准提示现在告诉你 Agent 为什么在问。** 当 Agent 要求读取或编辑工作区之外的文件，或编辑受保护的配置文件时，批准卡片显示原因。
- **图像生成失败时的错误更清楚。** 当 Agent 无法生成图像时，它现在会告诉你原因，而不是显示原始请求错误。内容策略阻止会建议用你自己的话描述主题，速率限制或提供方中断会请你再试。

### Cloud Agents

- **Cloud Agent 提示词里的 Slack 提及显示为可读的名字。** 当你打开从 Slack 启动的 Cloud Agent 时，其提示词里被提及的人显示为 `@Name`，而不是原始的 Slack 用户 ID，对话里的查找匹配你看到的名字。点击提及会在 Slack 里打开那个人。

### 编辑器

- **扩展宿主挂起时，AI 功能会自行恢复。** 如果为 Cursor 的 AI 功能提供动力的扩展宿主大约一分钟没有响应，Cursor 会自动重启它，因此你不再需要重新加载窗口。它也会以递增的延迟继续重启崩溃的宿主，并在你调试扩展时跳过自动重启。

### 隐私与安全

- **隐私模式现在阻止自动崩溃调试上传。** 打开隐私模式后，崩溃或内存不足事件之后，Cursor 不再自行上传调试数据或紧急内存配置文件，即使你在会话中途打开了隐私模式。

### 修复

- **修复了指向无扩展名脚本的 Windows Hooks。** 在 Windows 上，指向没有文件扩展名的脚本的 [Hook](https://cursor.com/docs/hooks.md) 命令，现在会运行它旁边匹配的 `.ps1`、`.exe`、`.bat` 或 `.cmd` 文件。以前这些 Hooks 会打开一个游离的终端窗口，并在没有运行的情况下报告成功。
- **修复了附加到聊天时透明图像变黑。** 当 Cursor 缩小你附加的大图像时，透明区域现在保持透明。
- **修复了 Investigate Error in Chat 接管其快捷键。** 只有当光标在错误、警告或 AI lint 上时，Cmd+Shift+D 才运行 Investigate Error in Chat。在其他地方，该快捷键走它的其他绑定。
- **修复了 Apply 为非常大的 Cloud Agent diff 写入空白文件。** 当 Cloud Agent 更改的文件多于可以显示的数量时，Review Changes 现在会警告文件内容不可用，而不是说没有更改，Apply 被阻止，而不是写入空文件。
- **Agent 可靠性的修复。** 你在 Agent 工作时排队的后续消息不再丢失，`/summarize` 失败时会显示错误。`.cursorignore` 规则加载失败时，Agent 回合不再挂起。当 Auto-review 允许该调用时，需要确认的 MCP 工具仍会请求批准。
- **Cloud Agents 的修复。** Cloud Agent 聊天在睡眠或网络掉线后重连，并一次一页地加载更早的回合而不停滞。后续按发送顺序处理，并保持你选择的模型变体。
- **Agent 聊天记录稿的修复。** 你回答之后，Agent 在聊天里问的问题不再重新打开，聊天重新加载时大型未发送草稿不再丢失。
- **MCP 登录的修复。** 用 Microsoft Entra 登录的 MCP 服务器，在管理员已批准该应用时不再每次都请求同意。
- **Git 的修复。** 在 macOS 上，当 PATH 上的 git 无法运行时 Cursor 不再挂起，并回退到系统或 Homebrew 的 git。插件随 git 2.26 和 2.27 加载。
- **无障碍修复。** 屏幕阅读器现在会读出 Agent 输入的 `/` 和 `@` 菜单里高亮的项目及其位置。Agent 聊天里的按钮在你用键盘到达时显示焦点环。
- &#x20;**团队和企业策略的修复。** 仓库访问规则现在只来自你的活动团队，因此另一个团队的允许列表不再阻止你的仓库。短暂的网络错误期间，团队隐私模式不再回退到限制性默认值，不可靠代理后面的 Enterprise 成员不再在临时的成员身份检查失败后被登出。
- **编辑器的修复。** 扩展宿主不再在启动时死锁，Cursor 加载期间开始的搜索不再失败。Ctrl+I 和 Ctrl+L 不再落到卡在 “Loading Chat” 的聊天，在 AZERTY 或 Dvorak 布局上撤销/重做不再截获其他快捷键。

## October 1, 2026 发行

### Pull request

- **按邮箱请求审查者。** `origin pr edit --add-reviewer` 接受一个或多个邮箱，如果邮箱没有恰好匹配一个审查者候选就会失败，并打印被请求的是谁。
- **指向新评论的链接。** `origin pr comment` 打印新评论的 id，以及打开它的 URL。
- **更兼容 `gh` 的 JSON。** `origin pr view` 和 `origin pr list` 接受 `--json headRefOid`。变更输出把通过应用或机器人采取的操作显示为 “Name via app”，JSON 输出包含 `performedVia` 字段。
- **门禁检查不再算作 CI。** `origin pr checks` 在设置其待处理、失败或无检查退出码时忽略 Gate 审查检查。当无法读取目标的规则时，`origin pr view` 对 CI 和批准显示 “unknown”，而不是 “no”。
- **`origin pr stack` 已移除，用于挑选 pull request 的 `--branch` 也已移除。** 改为把分支命名为位置目标。`origin pr checkout --branch` 不变。

### 可靠性

- **更安全的分支名。** `origin pr create --push` 和 `origin pr checkout` 使用完全限定的 ref，因此与标签冲突的分支名可以工作，无效名称以 `'<name>' is not a valid branch name` 失败。`origin pr create --push` 也会推送名称以 `-` 开头的分支。
- **速率限制上的自动重试。** 当 Origin 对请求限速时，CLI 会等待并重试，打印 “Origin rate limit reached; retrying in Ns.”。

## 1.12.0

### Projects

- **Project 聊天聚焦于对话。** Project 的协调者聊天显示你的消息、它的回复、问题和批准，没有 Worked 或思考分区，你连续发送的消息会被分组在一起。
- **Project 工作时的实时状态。** Project 的协调者运行时，其聊天显示状态行，例如它报告的步骤、Running commands、Messaging an agent 或 Writing a reply。

### Agents

- **Agent 链接在应用里打开。** 在 Agent 聊天里点 cursor.com/agents 链接，包括指向 Subagent 的链接，会在 Cursor for iOS 里打开该 Agent，共享的 Agent 链接直接打开到 Agent 的聊天，而不是收件箱。
- **更早的消息在你滚动时加载。** 在 Agent 聊天里向上滚动会自动加载更早的消息，并在它们加载时把你正在读的消息保持在原位。
- **语音通话消息有标签。** 从语音通话发送的消息显示带波形图标的 Voice call 标签。
- **在长聊天里拖动浏览。** 按住并拖动滚动指示器，在长对话里移动。

### Review

- **Review 里的大型 diff。** 对非常大的 diff，Review 屏幕列出每个更改的文件并带行数，并在内容超过查看限制时说明，pull request 审查现在会加载超过前 50 个的 commit，最多 250 个。

### 修复

- **Projects。** 协调者运行时，Project 的收件箱行显示其加载器，worker 完成、出错或重启时 worker 行会更新，来自 Project worker 的图片和媒体在加载放弃时带 Retry 选项加载，Live Activity 上已完成的 Project 显示整个 Project 运行了多久，而不是 0m。
- **模型选择器。** 为 Cloud Agent 后续选择 Auto，会把 Agent 切换到 Auto，而不是保持先前的模型。
- **大文件和代码块。** 打开非常大的已编辑文件会显示 File too large to preview，并带 Copy 选项，而不是挂起，对大文件有编辑的聊天保持响应，滚动带行号的代码块时的崩溃已修复。
- **发送和选择器。** 发送之后立刻把应用放到后台，不再显示虚假的 Couldn't start agent 或 Couldn't send message 错误，Workspace 选择器的 Recents 不再列出不再可用的仓库或环境。
- **视觉打磨。** 在底部面板上横向拖动不再使它偏离轴线，大的 pull request 编号显示时没有千位分隔符，重叠的头像完全盖住后面的那些。
- **MCP 登录失败。** 登录云 MCP 服务器失败时，聊天里的登录卡片把它显示为失败，而不是跳过。

## 1.0.35

- **自定义工具知道是哪个会话调用了它们。** `local.customTools` 条目的 `execute` 回调现在收到 `context.sessionId`，即调用该工具的本地会话。用 `Task` 启动的 Subagent 传入它们自己的 id，因此宿主可以为每个子 Agent 保持分开的状态。仅 TypeScript；运行时没有会话 id 时未设置。
- **本地 Agent 和 TypeScript 类型的修复。** 本地 Agent 现在在启用 FIPS 的主机上工作，现有的本地 Agent 存储会自动移到新位置。从同一进程或 VM 快照恢复的 Agent 不再复用请求和会话 ID，因此并发运行不会碰撞或卡住。`LocalSubagentInherit` 类型声明现在无需未发布的内部包即可解析，使用新导出的 `LocalSubagentResourceProviderFields` 和 `LocalToolExecutor` 类型。

## 1.0.34

- **Subagent 可以继承父级的执行器和工具限制。** 设置 `local.subagentInherit`，使由 `Task` 工具启动的 Agent，包括嵌套的，使用与父级相同的自定义读取、写入和 shell 执行器以及所报告的工作区路径，以及相同的允许和排除工具。在 `Agent.create()` 上传入它，或为一次 `send` 覆盖它。仅 TypeScript 本地 Agent。如果你不设置它，Subagent 的行为与以前相同。
- **修复了长运行 Agent 里请求在掉线连接上停滞。** 当 Agent 在运行之间空闲时，SDK 现在会在 29 秒后关闭未使用的 HTTP/2 连接。它也会在复用之前检查已经安静了大约一分钟的连接。请求不再被写进服务器已经关闭的连接，它们以前会在那里挂起，直到停滞超时中止它们。
- **停滞的连接可重试且有界。** 当连接停滞时，运行以可重试的 `NetworkError` 失败，代码为 `connection_stalled`，一连串停滞重试在 3 分钟后停止，而不是无限重试。
- **随 SDK 安装的依赖更少。** 安装 `@cursor/sdk` 不再把 `@connectrpc/connect-node` 或 `undici` 5.x 加到你项目的依赖树，因此安装更小，你不再从这些包得到版本冲突或审计警告。

## v2026.09.28

### 修复

- **Grok 回合不再在被拒绝的输出上结束。** 当提供方在该步骤的任何工具运行之前拒绝 Grok 模型的输出时，Agent 现在会重试该步骤一次，而不是以错误结束回合。
- **空回复之后没有额外回合。** 你恢复会话或后台任务完成后，空的模型回复不再触发不想要的重试或继续回合。
- **自托管 worker 从沉默的连接恢复。** 到 Cursor 的连接沉默了 90 秒的 worker 现在会自行重连，而不是看起来已连接却收不到工作。如果你的账户使用 Privacy Mode (Legacy)，worker 现在会在启动时退出，并带说明如何切换到 Privacy Mode 的消息，而不是无尽地重连。
- **自托管 worker 在回合之间保持 Agent 的状态打开。** 在共享和池自托管 worker 上，Agent 的后续步骤复用其打开的状态，而不是从头重新打开，来自不同 Agent 的认领不再在先前 Agent 已经释放的状态上失败。
- **图像生成错误会说明下一步做什么。** 当图像提供方拒绝请求时，Agent 现在会显示一条你可以据此行动的消息，而不是 “Request failed with status code 400.”。对内容策略阻止，消息建议描述主题时不要点名商标角色、品牌或真实人物。对速率限制和提供方中断，它说片刻后再试。
- **超时的 shell 命令保留其输出。** 当长时间运行的命令把输出写到文件然后超时，Agent 现在得到该文件的路径、大小和行数，而不是空结果，因此它可以读取输出并继续工作。
- **CLI 运行你选择的确切模型。** `--model` 和 `/model` 现在运行你选择的模型，即使其 ID 以另一个模型的 ID 开头。以前 CLI 可能静默运行更短的基础模型。
- **macOS worker 按 Activity Monitor 的方式报告内存。** macOS 上的自托管 worker 不再把文件缓存计为已用内存，因此空闲的 Mac 不再看起来几乎内存耗尽。

## Sep 28, 2026 当周

### Web

- **查找已合并的 pull request。** 仓库的 pull request 列表有一个带计数的新 Merged 标签，搜索框里的 `is:merged` 现在筛选到已合并的 pull request。
- **Request Changes 审查现在阻止合并。** 来自有写权限的人的 Request Changes 审查会阻止 pull request 合并，直到它被解决，驳回别人的 Request Changes 审查需要写权限。你还没提交的待处理审查不再算作批准或阻止项，pull request 页面上的审查按提交时间排序。
- **请求应用时选择仓库，并取消待处理请求。** 当你请求安装应用时，可以选择全部仓库或特定仓库，安装链接里的仓库会被预选。待处理请求显示其状态，你可以从该状态按钮取消它。有人请求安装时，命名空间所有者或团队管理员会收到电子邮件，你的请求被批准或拒绝时你会收到电子邮件。安装页面也显示应用的发布者、验证和网站、View Listing 按钮，以及你登录的账户。
- **IP 允许列表适用于人，不适用于你的应用。** 当你的团队有入站 IP 允许列表时，它现在只适用于用户凭据，因此应用、服务账户和 Agent 不再被阻止。允许列表也在 Origin 网页和仓库下载上检查，最多可以有 1,000 个条目。所有者可以通过 Origin API 读取和替换允许列表；参见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **更清楚的 Git 错误和更安全的推送。** 当 Git 被拒绝访问 Origin 仓库时，它现在会说明原因，包括使用了哪个 Cursor 账户以及如何切换。推送大于 100 MB 的文件会被拒绝，错误写出文件及其大小。到 `HEAD` 或 `refs/` 之外的 ref 名的推送会被拒绝，而不是移动默认分支，格式错误的 ref 名会被单独拒绝，推送的其余部分仍会落地。
- **Blame 跟随重命名。** Blame 现在按 `git blame` 的方式跟随文件重命名，因此重命名文件里的行保留写下它们的 commit，而不是指向重命名。看起来像 commit 哈希的分支和标签名现在解析到该分支或标签，commit 列表和比较使用与 Git 相同的合并基和排序。
- **CODEOWNERS 更接近 GitHub。** CODEOWNERS 模式现在遵循 gitignore 规则，因此目录和锚定模式按 GitHub 的方式解析所有者，带下划线的登录名被接受。邮箱条目只解析到能审查该仓库的人。当无法确定代码所有者时，例如因为文件太大或更改的文件太多，代码所有者要求会被阻止并带说明，而不是静默通过或失败。
- **规则集被更严格地强制执行。** 进入被阻止合并的活动规则集冻结的分支的 pull request，现在会显示 “Merging is blocked by a ruleset.”，除非你可以绕过它。带 Origin 不识别的规则类型的活动规则集现在会阻止合并，而不是被跳过，保存无法在那种规则集里运行的规则会以错误失败。即使你可以绕过它们，Merge when ready 现在也会等待规则集规则通过。
- **merge when ready 的修复。** Merge when ready 不再合并检查运行之后推送的 commit，会等待新链接的父 pull request 先合并，关掉它会可靠地停止待处理的合并。已合并的 pull request 现在记录实际落地它的 commit。
- **更少的虚假合并冲突。** 在基线分支上插入的行旁边有编辑的 pull request，当 Git 会干净合并它们时现在显示为可合并，而不是报告冲突。当 pull request 的 head commit 缺失时，它会请你再次推送该分支，而不是显示错误。
- **堆叠 pull request 的修复。** 堆叠 pull request 的基线落地并被重新设定目标之后，其 diff 和重新堆叠只包含它自己的 commit。重新堆叠保留与主干合并的冲突解决，包括到 squash 落地的父级上。在其栈合并的那一刻被起草或关闭的 pull request 现在显示为已合并。
- **大型和重命名变更上 diff 的修复。** 重命名的文件按 Git 配对它们的方式配对，行数对最终换行和行尾变更与 Git 一致，深层嵌套变更的 diff 现在会加载，而不是失败。在非常大的 pull request 上，Load more 现在获取下一批文件。
- **pull request 页面的修复。** 重新打开 head 或基线分支已被删除的 pull request，现在会告诉你先恢复该分支。当路径有空格或非 ASCII 字符时，永久链接现在会打开正确的文件。通过应用行事的作者和审查者会在其名字旁边显示该应用。

### API

- **通过 Origin API 准备并检查合并。** 一个新调用对照当前基线重建 pull request 的测试合并 ref，并报告它是可合并、有冲突还是待处理，并带合并、基线和 head commit。可合并性响应也包含计算它们所依据的 head commit。参见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **把检查运行限定到一个基线 commit。** 应用可以在发布检查运行时传入基线 commit，使检查只对以该基线的 pull request 计数，基线 commit 出现在检查响应、Origin SDK 和检查运行 webhook 里。以失败状态发布的检查运行保持该状态。参见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **更多 Origin 可通过 Origin API 使用。** 你可以读取、添加和移除 pull request 评论上的反应，检查用户在仓库上的权限，并列出时间范围内的 commit。仓库、commit 和 pull request 的响应，以及 pull request webhook，现在链接到 Cursor 上的页面。规则集绕过用户现在使用公开用户 ID，这是一个破坏性变更。详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。

## Sep 28, 2026 当周

### 环境

- **环境构建始终开启。** 环境构建不再能被关闭。每个不是草稿的环境现在都会得到自动构建，Cloud Agents 从最新构建启动。以前关闭了构建的环境现在显示其活动构建和反复的构建失败，Trigger build 对话框不再警告构建已关闭。
- **Fix Build 在快照已过期的构建上工作。** 在快照已过期的失败环境构建上点击 Fix Build，现在会启动 Agent，而不是失败。Agent 启动最新的成功构建，并从失败构建的日志开始工作。从 Dockerfile 构建失败启动 Agent 也会用调查并修复该构建的提示词预填 Composer。
- **更少因源代码管理小故障导致的环境构建失败。** 自动环境重建在临时的源代码管理访问失败后继续运行，只在连续三次这样的失败后停止。因为自托管源代码管理服务器宕机而失败的构建，现在被报告为源代码管理中断。
- **看出哪些环境变更来自 API。** 在环境的版本历史里，通过公开 API 做的保存现在被标为 “API”。
- **通配允许列表条目包含父域名。** `*.example.com` 这样的网络允许列表条目现在也让 Cloud Agents 到达 `example.com` 本身，而不只是其子域名。

### Agents

- **固定的 Agent 跟随你跨设备。** Web Agent 列表上固定的 Agent 现在保存到你的账户，因此你的固定跟随你跨浏览器和设备。Agents 侧边栏现在把 Agent 分成 Today、Last 7 days，以及开始时折叠的 Older 分区。
- **从已关闭的 pull request 启动 Agent。** 你现在可以从 head 分支已被删除的已关闭 pull request 启动 Agent。Agent 从基线分支上的新分支工作，面板会告诉你这一点。
- **Agent 无法启动时的错误更清楚。** 如果 Agent 因为你失去了对其某个仓库的访问而无法启动，错误现在会点名该仓库。当只有你的 Other Models 用量用完时，错误读作 “Out of Other Models usage”，并建议切换到命名模型或提高按需用量。当 Agent 的机器内存耗尽时，错误建议在更少文件上或以更低并行度运行内存重的步骤。
- **Web 上更快的 Agent 页面。** 在 cursor.com 上打开 Cloud Agent 对话不再重新获取已经在加载或已是最新的改动，回到 Agent 列表更快，因为列表在后台加载。

### Slack

- **Slack 模型选择器使用 Web 模型目录。** Cloud Agents Slack 设置模型选择器现在从与 Web 选择器相同的模型目录构建其列表，并保持你保存的默认值可选。Slack 模型命令现在接受你可以使用的任何模型，而不是固定列表。
- **从 Slack 设置频道的 Self-Hosted 池。** 任何团队成员现在都可以在 Slack 里运行 `@Cursor pool set <name> channel` 或 `@Cursor pool unset channel`。对频道默认仓库或 Self-Hosted 池的每次变更都会在频道里宣布。

### 修复

- **Agent 运行和后续的修复。** 在 Agent 第一步早期停止它之后发送后续，现在可以工作，而不是以 VM not found 错误失败。在 Best-of-N 标签之间切换现在会加载所选候选的对话。在上次构建之后其已保存环境发生了变化的 Agent，不再在启动时以构建版本不匹配失败。
- **多仓库环境的修复。** 即使你的某个多仓库环境有冲突的克隆路径，Cloud Agents 现在也会正常启动；以前那一个环境会使每次启动失败。保存其生成名称列出很多仓库的环境不再失败。
- **Cloud Agents 仪表板的修复。** 如果 Cloud Agents 设置页面无法加载隐私设置，它现在会显示带 Try again 按钮的错误。运行页面上失败的加载也提供 Try again，运行摘要读作 “1 run”，而不是 “1 runs”。

## Sep 28, 2026 当周

### 集成

#### GitHub

- **你的全部 GitHub 安装和账户都会加载。** 如果你有很多 GitHub App 安装，连接 GitHub 并挑选仓库现在会显示全部，而不是错过第一页之后的那些。如果几个已连接的 GitHub 账户中有一个的 token 过期，其他的仍会加载，而不是以 “GitHub access token is no longer valid” 失败。
- **新创建的仓库。** 当 GitHub 短暂无法返回仓库详情时，例如刚创建之后，Cursor 不再拒绝你能写入的 GitHub 仓库的访问。
- **GitHub Enterprise Server 上的 Cloud Agent 后续。** 通过 GitHub Enterprise Server 应用启动的 Cloud Agents，现在会为拥有该应用的团队接收 pull request 和 CI 更新，而不是这些 webhook 事件被拒绝。
- **GitHub 事件期间 GitHub 保持连接。** 当 GitHub 短暂拒绝有效的刷新 token 时，Cursor 保持你的 GitHub 连接，而不是删除它，只有在它持续失败时才移除 token，因此你不必重新连接 GitHub。

#### GitLab

- **更清楚的 GitLab 访问错误。** 在你缺少 Maintainer 或 Owner 访问的 GitLab 项目上开始工作，现在会一致地说需要 GitLab Maintainer 访问，而不是请你重新连接，token 冷却错误会以分钟或小时说明剩余等待。
- **连接 GitLab 会清除 token 冷却。** 连接 GitLab 之后，你的个人和团队仓库立刻可用，而不必等更早 token 错误留下的冷却结束。
- **组织管理员可以管理团队的 GitLab 和 Bitbucket 连接。** 不是团队成员的组织管理员现在可以加载并管理该团队的 GitLab 和 Bitbucket 集成，而不是得到错误。

#### Bitbucket

- **评论命令无法运行时的回复。** 当 @cursor 无法从 Bitbucket pull request 评论运行命令时，Cursor 现在会在 pull request 上回复原因，例如需要关联的 Bitbucket 账户或写权限，而不是忽略该评论。

### 仪表板与管理

#### Cloud Agents

- **一键修复失败的 Cloud Agent 环境构建。** 当环境构建在安装期间或在其 Dockerfile 上失败时，环境和构建页面显示 **Fix Build** 按钮，启动 Agent 来调试它，包括快照已过期的构建。快照已删除的构建显示 **Snapshot expired** 芯片，不能被激活，停用活动构建时，如果没有其他构建可以接手，会警告你。因为你的自托管源代码管理服务器不可达而失败的构建现在会说明这一点。参见 [Cloud Agent Builds](https://cursor.com/docs/cloud-agent/builds.md#debug-a-build)。

#### 自动化

- &#x20;**对 Approver Agents 里的标签触发器有更多控制。** 标签变更触发器现在可以在标签被添加、移除或两者之一时触发，你可以把它限制到一个标签名。编辑 Agent 时这些设置现在会被保存。
- &#x20;**Vulnerability Scanner 运行出现在 Security Agents 运行历史里。** Security Agents 页面上的 Recent Runs 现在在安全审查者运行旁边列出 Vulnerability Scanner 运行。

#### SSO 与 SCIM

- &#x20;**更可靠的 SCIM 目录同步。** 对拥有其 SCIM 目录的组织，身份提供方为链接到该组织的团队推送的组，现在成为已同步的组，而不是旧式目录组。在至少有 10 个用户的目录里，如果身份提供方短暂报告每个用户都没有组，目录同步不再移除全部目录组成员身份。

#### 修复

- **Cloud Agent 环境构建的修复。** 构建日志在最新一行打开，并继续跟随新输出，直到你向上滚动。当构建表的一页返回为空、但存在更早的构建时，它现在会告诉你使用 **Next**，而不是让你卡住，**Trigger build** 对话框不再警告构建已关闭。
- **Cloud Agents 设置和运行的修复。** Cloud Agents Runs 页面上的 Run history 图表在加载时不再把页面往下推。页面打开时 Cloud Agent 设置会刷新，`*.example.com` 这样的网络访问条目现在也匹配 `example.com`。
- &#x20;**组和团队的修复。** 在组的 Members 标签上，宽表上的行操作菜单留在屏幕上。当你在组织里创建团队时，团队管理员列表把你放在第一位并默认选中你。
- **移动设备上仪表板的修复。** 在移动设备上，导航菜单按钮现在位于顶栏左侧，设置里长的 GitHub Enterprise 实例名会被截断。
- **GitLab 和 Bitbucket 集成的修复。** 当仪表板里选中的团队是你不是其成员的团队，例如已取消的团队时，Integrations 页面上的 GitLab 和 Bitbucket 连接不再加载失败。它们显示为只读。
- **Bugbot 设置的修复。** 旧的 Bugbot 许可证链接在 Automations 里打开 Bugbot 设置。

### 自动化

- **pull request 触发器和评论里的 GitLab 支持。** Comment on PR 操作接受 GitLab 合并请求 URL，包括自托管 GitLab。Git pull request 触发器可以用嵌套的 GitLab 组（group/subgroup）或作为组织范围的组织 URL 保存，无效条目显示错误。
- **更清楚的运行失败。** 当运行因为配置问题失败时，例如缺失的分支、无法访问的仓库、被团队区域阻止的模型、API 密钥速率限制，或缺失的 Slack 频道，运行历史会显示具体原因以及该做什么。Agent 内存耗尽或机器停止响应的运行会说明这一点，后来成功的运行显示为已成功。
- **自动化运行的修复。** 有超过一个日程的自动化现在在每个日程上运行，而不只是第一个。打开 pull request 步骤现在为服务账户拥有的运行和更早的团队自动化打开 PR。由 pull request 评论或标签触发的 Security Reviewer 自动化再次运行，而不是被跳过，有 Slack 访问的自动化不再在每一步重新列出 Slack 频道时停滞。
- **团队选择和 PR Approver 的修复。** 在 cursor.com 上，自动化操作在你选中的团队下运行，而不是你的默认团队。当分配被关闭或失败时，PR Approver 审查不再说审查者已被分配；它们会说需要人工审查。

### API

- **跳过无问题评论。** [`POST /bugbot/review`](https://cursor.com/docs/bugbot.md#trigger-a-review) 接受可选的 `postSuccessComment` 布尔值。把它设为 `false`，没有新发现的审查就不会发布 “found no new issues” 评论。它默认为 `true`。
- **免费 Bugbot 审查的成本。** [`GET /analytics/team/bugbot-reviews`](https://cursor.com/docs/bugbot.md#review-analytics) 现在为基于席位和免费试用的审查返回 `cost_cents`，显示其未打折的模型成本，并增加 `billed` 字段。当你的团队没有为该审查被收费时，`billed` 为 `false`。

### 安全与批准 Agent

- **Admin Only 标签。** 如果你不能更改 Security Agents，其页面现在在页面标题旁边显示 Admin Only 标签。
- **不关联 Slack 也能保存。** 只要其 Send to Slack 频道没有改变，你现在可以保存对安全 Agent 的编辑而不连接自己的 Slack 账户。
- **更可靠的安全审查。** 安全审查保留正好在截止时间完成的结果，重试瞬时连接错误，并在所选模型不可用时回退到另一个模型。

### 模型与 Cursor Router

- **新模型：Claude Sonnet 5.5。** [Claude Sonnet 5.5](https://cursor.com/docs/models/claude-sonnet-5-5.md) 现在可在 Cursor 中使用。
- **新模型：GLM 5.3 和 GLM 5.3 Flash。** 来自 Z.ai 的 [GLM 5.3](https://cursor.com/docs/models/glm-5-3.md) 和 [GLM 5.3 Flash](https://cursor.com/docs/models/glm-5-3-flash.md) 现在可在 Cursor 中使用。它们默认关闭；在 Cursor Settings > Models 里打开它们。

## Sep 28, 2026 当周

### 仓库设置

- **按仓库配置 Bugbot。** 在 GitHub pull request 上，Bugbot 读取 `.cursor/config/bugbot.yaml` 文件，为该仓库设置其触发器、审查力度、增量审查、PR 摘要和风险分数。文件里的值覆盖团队设置，该文件可以降低 Autofix，但绝不能打开它。仪表板里的 Bugbot Settings 分区链接到配置文件的文档。

### Autofix

- **在 New Branch 模式下，Autofix 离开你的分支。** 当 Autofix 设为 New Branch 时，它现在把修复留在单独的分支上，绝不提交到 pull request 自己的分支。

### 审查与检查

- **Bugbot 检查不再卡住。** 被取消或超时的 Bugbot 运行现在将其检查完成为中性，而不是留在进行中。当基线分支得到不改变 pull request 合并基的新 commit 时，Bugbot 仍会发布其审查，被取消运行的检查会说明它为什么被取消。
- **过大和被限速的审查的结果更清楚。** 当 pull request 里没有文件适合 Bugbot 的审查预算时，Bugbot 会发布其 “too large to review” 评论和中性检查，而不是错误。碰到临时 GitHub 速率限制的手动 Bugbot 命令现在会得到请你几分钟后重试的回复。Bugbot 也不再把符号链接目标当作源文件来审查。

### Bitbucket

- **Bitbucket 上更好的 Bugbot。** Bugbot 现在可以审查来自分叉的 Bitbucket Cloud pull request。当 Bugbot 在 Bitbucket 上拒绝评论命令时，它会回复原因。失败评论渲染为 Markdown，而不是转义的 HTML，当 Bitbucket 的登录服务短暂不可用时，Bugbot 会重试审查，而不是跳过它。团队允许列表接受包含空格的 Bitbucket 昵称。

### 仪表板

- **仪表板里更清楚的安装权限。** 在你的个人 Bugbot 仪表板上，团队拥有的 GitHub 组织安装显示为只读，更改会被拒绝，并带切换到该团队仪表板的消息。没有付费方案就无法更改的个人安装也是只读的，并说需要付费订阅。
- **仪表板修复。** Bugbot 规则和学习设置里的仓库选择器和列表把仓库显示为 owner/name，搜索匹配所有者。刷新之后 Set License Cap 开关反映已保存的上限。如果 Bugbot 仪表板视图渲染失败，它会显示带 Try again 按钮的消息，而不是空白页。

### 账单

- **团队限制适用于按 token 计费的 Bugbot 运行。** 在按 token 计费 Bugbot 的团队上，运行不再被安装用户的个人支出上限阻止；只适用团队的上限。Bugbot 不再以 Unpaid Admin 角色的成员身份运行，并发布评论说明如何修复。在没有 Stripe 账单的团队上更改 Bugbot 许可证现在会显示清楚的错误。

### API

- **在 API 触发的审查上跳过无问题评论。** Bugbot 审查触发 API 接受 `postSuccessComment`。把它设为 `false`，即可在该审查上跳过 “found no new issues” 评论。

## 3.22

### Agents

- **失败的 Agent 现在显示清楚的 Error 状态。** 失败的 Agent 现在在侧边栏里得到红色的 Error 状态，有自己的 Error 组，以及 Show > Status 下的 Error 选项，默认打开。
- **聊天状态栏里的用量环。** 当你的月度用量超过显示阈值时，聊天状态栏显示带 “Usage N%” 工具提示的用量环。点击它打开用量详情，详情现在浮在对话之上，并用 Escape 关闭。
- **更少不必要的 Auto-review 阻止。** Auto-review 不再仅仅因为阻止指令禁止后来的发送或应用，就阻止起草电子邮件、暂存改动或预览部署这类准备步骤；最终的提交步骤仍会被检查。当分类器返回不完整的决定时，审查失败也更少。
- **最多固定 75 个 Agent。** 固定 Agent 的上限现在是 75，从 50 提高，因此你可以在侧边栏顶部保留更多 Agent。
- **Grok 4.7 现在正确说出自己的名字。** 当你问 Grok 4.7 它是谁时，它现在会说它是 Grok 4.7，一个由 SpaceXAI 训练的模型。以前它可能称自己为 Composer 或更早的 Grok 版本。
- **Voice Submit Keywords 在任何语言里都有效。** 非拉丁文字里的提交关键词，例如西里尔文或中文，现在会在你听写时触发自动提交。“envíalo” 这类带重音的关键词也会正确匹配，而不是被弄乱。
- **推理陷入循环时 Agent 会恢复。** 当 Agent 的推理开始重复同一行时，Cursor 现在会停止该步骤，并带着继续前进的推动重试一次，因此运行继续，而不是停滞。
- **在任何 Agent 聊天里看出未读回复从哪里开始。** 云和本地 Agent 聊天现在标记你还没看过的回复从哪里开始，并显示跳到它们的 New Messages 药丸，就像 Project 聊天已经做到的那样。

### Projects

- **Create Project 保留你的工作并选对仓库。** 在 Create Project 对话框里选择 Add Models，现在会打开 Settings 而不丢失你的项目名称、外观或模型选择，重新打开对话框会恢复它们。对话框不再在你可以创建之前等待仓库列表，它保留你从 Repos 或 Recents 选择的仓库，而不是切换到同一仓库的已保存环境，并默认使用与你的 New Chat 草稿相同的环境。

### Cloud Agents

- **更清楚的 Cloud Agent 错误，以及一键恢复。** Cloud Agent 错误现在显示在输入上方的托盘里，包括运行正在自动重试时，或其会话过期之后。当运行失败时，错误在适用处提供单个下一步，例如 Retry、Manage Usage 或 Manage Environments。为机器离线的 Cloud Agent 打开终端，现在会打开一个说明原因的 Terminal 标签，机器再次可达时它会自动连接。
- **从链接启动 Cloud Agent 不再随机挑选仓库。** 从应用内提示词或深链接打开 Cloud Agent，以前在你没有本地或最近仓库时会预选列表里的第一个仓库。现在如果你有的话，它使用你当前的仓库或最近的 Cloud 仓库，否则打开一个新的 Cloud Agent，仓库选择器只需一点即可到达。
- **在项目选择器里找到前 5,000 个之外的 Bitbucket 仓库。** 在超过 5,000 个仓库的 Bitbucket 工作区里，在项目选择器里打字现在也会搜索 Bitbucket，因此前 5,000 个之后的仓库会出现在结果里。不搜索地浏览仍列出前 5,000 个。

### 文件、改动与 pull request

- **只审查 Agent 刚刚更改的内容。** Changes 标签现在有 Last Agent Turn 范围，只显示 Agent 最新回合的编辑。点击回合结束摘要里的 Review 或文件，会在该范围里打开 Changes 标签，因此你可以检查最新的编辑，而不必翻找更早的改动。
- **Markdown 预览保持你的位置，并显示 Agent 存储图片。** 从 Markdown 预览切换离开再回来，现在会恢复你的滚动位置和光标，而不是跳到顶部。来自 Agent 存储的 Markdown 文档也会渲染其存储图片，而不是断开的图片链接。

### 导航与布局

- **更相关的命令面板搜索结果。** 在命令面板里搜索 Agent，现在会把记录稿匹配你查询的聊天排在只松散匹配名称或 PR 链接的聊天之上，输入 PR 编号（带或不带 #）仍能找到其聊天。没有匹配的搜索现在显示 “No results found”，而不是建议一个新 Agent，你已经能看到的结果在其他分区完成加载时可被选中。
- **屏幕阅读器可以区分提示框。** 每个提示框现在对屏幕阅读器有自己的名字，例如 “Prompt”、“Side chat prompt”、“Subagent prompt”、“Fullscreen prompt” 或 “Design mode prompt”。在分栏布局里，名字包含磁贴编号，例如 “Prompt, tile 2”。提示框里的菜单选项也被读作其列表的一部分。
- **Linux .deb 和 .rpm 安装的更新通知。** 通过 apt 或 dnf 安装的 Cursor 现在会在有新版本可用时告诉你，而不是让更新保持关闭。选择 Update 会显示带 Copy Command 按钮的确切升级命令，Later 会把该版本延后。
- **打开 Rust 项目不再把 CPU 占满。** 打开语言服务器后，仅仅因为文件夹包含 Rust，Agents Window 不再启动 rust-analyzer 和完整的 cargo 检查。现在当你打开 Rust 文件时服务器才启动，你禁用的语言扩展保持关闭。
- **扩展宿主挂起或持续崩溃时，Agents Window 会自行恢复。** 以前，冻结的扩展宿主可能让 Agent 请求卡住且没有错误，直到你重新加载窗口。如果该扩展宿主 60 秒没有响应，它现在会被自动重启，请求恢复。如果它持续崩溃，它会以递增的延迟继续重启，而不是三次崩溃后放弃。调试器可能附加到扩展宿主时，挂起后的自动重启会被跳过。
- **在命令面板里查找侧边聊天。** 在命令面板里搜索现在能找到侧边聊天。每个都被标为 Side chat，显示其父 Agent 的名字，并在其父 Agent 上打开。

### 自定义与设置

- **MCP 服务器显示每一页工具。** 对其工具、提示词或资源分页的 MCP 服务器现在显示每一页，而不只是第一页。
- **Hooks 可以把 Subagent 绑回其对话。** `subagentStop` Hook 现在收到 `child_conversation_id`，即 Subagent 自己对话的 ID，因此审计和策略脚本可以把 Subagent 活动绑回其父级。
- **`/subscribe` 在 pull request 合并后清理。** 对一个 pull request 的订阅现在会在该 pull request 合并或关闭时自行关闭。其分支上的 CI 订阅也会关闭，除非另一个未合并的 pull request 仍在使用该分支。

### 修复

- **Projects 的修复。** 当命名云环境的仓库是未发布草稿时，项目选择器显示其名称，而不是 “No Repo”。在 Project 里的 Agent 之间切换会保持最近的对话已加载，因此回到一个不再重新加载其记录稿，有分栏时，未聚焦窗格里的对话留在屏幕上。
- **Subagent 托盘的修复。** Subagent 托盘打开时，Agents 药丸保持可见并显示为选中。
- **Composer 和排队后续的修复。** 回合结束时排队的后续不再消失，大型未发送草稿在重新加载时不再被清除。带关键词建议的后续输入不再使 Agent 面板崩溃，编辑先前消息时滚动再次表现正确。
- **聊天记录稿的修复。** 最后几条消息不再藏在待处理问题面板后面，已回答的问题不再重新出现，可视化卡片显示错误，而不是永远旋转。
- **上下文摘要的修复。** 长聊天被摘要之后，Agent 保留你最新的请求，而不是把它丢在自动提醒后面，失败的摘要不再用占位符替换你的对话历史。在摘要中途停止 Agent 现在会及时取消，而不是显示服务器错误。
- **Cloud Agents 和 Move to Cloud 的修复。** 新的 Cloud Agent 保持你选择的模型，当前工作区之外的 Cloud Agent 不再发送虚假的 “Done” 通知，在后台醒来的聊天会在侧边栏里上移。对已保存状态丢失了回合的聊天，Move to Cloud 可以工作。
- **内置浏览器的修复。** 网页不再能把虚假的元素选择注入 Agent 上下文；只有真实点击才选择元素。隐藏的浏览器标签不再在后台继续渲染，DevTools 只在你第一次打开控制台时启动，因此浏览器标签使用更少 CPU 和内存。浏览器页面和 DevTools 以自己带标签的行出现在 Process Explorer 里。
- **Agent 上下文读取的修复。** 启动时的 .cursorignore 扫描失败时，Agent 上下文读取不再停滞。

## 3.22

### Agent

- **Auto-review 不再阻止草稿和预览。** 当你添加像 “don't send emails” 或 “don't deploy” 这样的阻止指令时，[Auto-review](https://cursor.com/docs/agent/security/run-modes.md) 现在只把它应用到发送或部署的那次调用。起草、暂存、预览、计划或读取的调用会运行，而不是停下来等待批准。
- **聊天里更清楚的 Agent 工具调用。** 当 Agent 读取其待办列表时，聊天显示列表本身。pull request 操作显示其结果或错误，而不是被隐藏，卡在批准上的 Agent 会说 “Approval required” 或 “Approval denied”，而不是 “Couldn't start”。
- **推理循环时 Agent 会重试。** 如果 Agent 的推理一遍遍重复同一行，Cursor 会停止该步骤，并带着继续前进的推动重试一次。
- **语音提交关键词在任何语言里都有效。** 语音提交关键词，即发送你提示词的口头词语，现在保存在 Settings 里，并在西里尔文或中文这类非拉丁文字里触发提交。重音字母被保留，因此 `envíalo` 不再保存为 `envalo`。

### MCP

- **更可靠的 MCP 服务器登录和工具加载。** 登录受 OAuth 保护的 [MCP 服务器](https://cursor.com/docs/mcp.md#static-oauth-for-remote-servers) 对更多服务器有效，包括在注册时拒绝 Cursor 重定向 URL，或稍后请你再次登录的那些。对其工具、提示词或资源分页的服务器现在暴露全部，而不只是第一页，有很多 MCP 工具的 Agent 不再因超过模型的工具上限而失败。

### Hooks

- **Hooks 可以知道工具调用来自哪个 Subagent。** 当工具在 Subagent 内部运行时，`preToolUse`、`postToolUse` 和 `postToolUseFailure` [Hooks](https://cursor.com/docs/hooks.md) 现在收到 `parent_tool_call_id`，即启动该 Subagent 的 Task 工具调用的 ID。`subagentStop` Hooks 收到 `child_conversation_id`，因此审计脚本可以把已完成的 Subagent 链接到它自己的对话。

### 浏览器

- **在内置浏览器里查看并撤销 Agent 的设备模拟。** 当 Agent 把标签调整到手机大小的视口时，[浏览器](https://cursor.com/docs/agent/tools/browser.md)标签显示带 Reset 按钮的 “Device emulation · W×H” 徽章，Agent 的回合结束时模拟会被清除。浏览器也使用更少内存，因为 DevTools 只在你第一次打开控制台时启动，网页不再能伪造发给聊天的元素选择或区域截图。

### 源代码管理

- **Source Control 操作再次运行 Git hooks。** 修复了一个回归：来自 Source Control 视图的提交、推送、拉取、检出、分支创建和克隆跳过了仓库 hooks，例如 `pre-commit`、`commit-msg`、`pre-push`、`post-checkout`、Husky 和 Git LFS。运行 hooks 的命令在 Git 输出通道里标为 `(hooks)`。状态刷新和自动获取这类后台活动仍跳过它们。

### 更新

- **Linux 上 apt 和 dnf 安装的更新通知。** 如果你从 `.deb` 或 `.rpm` 软件包安装了 Cursor，有新版本可用时 Cursor 现在会告诉你。点击 **Update** 查看 `apt-get` 或 `dnf` 升级命令并用 **Copy Command** 复制它，或点击 **Later** 跳过该版本。

### 修复

- **Agent 聊天和后续队列的修复。** 回合结束时排队的后续不再消失，引导回退到队列时也不再消失。“Worked for” 时长包括最终回复之后运行的工具。
- **失败的启动扫描之后，Agent 上下文读取不再挂起。** 如果第一次 `.cursorignore` 扫描在启动时失败，Cursor 会重试它，而不是让 Agent 上下文读取在会话的其余时间卡住。
- **Cloud Agents 的修复。** 切换账户或团队之后，斜杠菜单不再显示上一个团队的托管 Skills，当前工作区之外的 Cloud Agent 不再发送虚假的 “Done” 通知。
- **编辑器和工作台的修复。** Cmd/Ctrl+Shift+B 再次运行 Run Build Task，已删除文件的未保存备份不再在每次窗口打开时重新打开。Worktree 获取在早于 2.29 的 git 版本上工作。如果把 Cursor 连接到其服务器的本地扩展宿主挂起，Cursor 会重启它，使窗口重连，除非调试器可能已附加，并且如果该宿主反复崩溃，它会继续重启它。被取消的符号和调用层次请求不再泄漏内存。
- **用量横幅、模型选择器和聊天可视化的修复。** 你在模型选择器里启用的模型在模型列表刷新时不再消失。用量上限横幅不再叠在 Composer 里的用量上限错误之下，当你的组织隐藏额度请求时也不再显示 Request Credit 按钮。当内容从未到达时，聊天里的可视化卡片显示错误，而不是永远加载，图表工具提示不再移动周围的聊天。

## September 24, 2026 发行

- **按 commit 查找 pull request。** `origin pr list --commit <sha>` 列出 Origin 为某个 commit 记录的 pull request。
- **JSON 输出里的 `reviewDecision`。** `origin pr view --json reviewDecision` 返回审查决定，与 `gh` 一致。
- **来自标准输入的请求体。** `origin api --input -` 从标准输入读取请求体，而不是以 “Unknown argument: -” 失败。
- **CloneKit 修复。** `origin repo clone-fast` 不再在并行范围下载期间以虚假的 EPIPE 错误失败，它在工具包下载期间查找默认分支，而不是在之前。

## v2026.09.22

### Auto-review

- **Auto-review 把阻止指令与调用实际做的事放在一起权衡。** 在像 “Send comms that include external people,” 这样的指令下，它现在倾向于允许只起草、预览、计划或读取的 MCP 调用，并倾向于阻止发送、发布、公开、应用或删除的调用。按 [Configuring Auto-review](https://cursor.com/docs/agent/security/run-modes.md#configuring-auto-review) 所示把指令加到 `permissions.json`。

### 自托管 worker

- **任意仓库池 worker 复用你已经克隆的检出。** 使用 [`--clone-git-repos`](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#any-repo-pools)，worker 在其 worker 目录里复用所认领仓库的干净检出。它获取所请求的分支、标签或 commit，并在认领结束时保留该检出，因此大型仓库不再在每次认领时重新克隆。worker 日志会说明它为什么复用或跳过每个检出。worker 也会拒绝从它无法写入的目录启动，克隆失败时在终端里打印 git 的错误。
- **温池为后续唤醒休眠的 worker。** 当后续到达、其 worker 处于休眠时，`agent worker controller --warm-idle` 现在会以 `CURSOR_WAKE=1` 和该 worker 的 ID 运行你的 `--spawn` Hook，与先认领再派生的模式一致。如果 Hook 恢复了机器，后续会在其原始工作区里继续，而不是落到磁盘为空的空闲备用机上。这适用于有[重连窗口](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#hibernation)的池。

### 修复

- **修复了卡在重复同一行推理的 Agent。** 当 Agent 的推理不断重复单行时，该步骤现在会停止，并带着停止斟酌、继续前进的提醒重试一次，而不是循环。
- **修复了摘要丢失你最新的请求或你的对话。** 摘要长对话之后，Agent 现在逐字保留你最近的消息，而不是一条提醒，因此它留在任务上。如果摘要失败，或你在它运行时按 Stop，Agent 保留更早的对话，而不是用空的或被中断的摘要替换它。
- **修复了自托管 worker 上的身份验证错误和错误的规则。** 用 API 密钥启动的 worker，在第一个小时之后打开桌面会话时不再以身份验证错误失败。Agent 的第一回合现在从其自己的工作区加载 Rules、Skills 和插件，而不是另一工作区留在机器上的缓存集合。
- **修复了 Grok 4.7 和 Opus 5.5 的模型指令。** 被问到时，Grok 4.7 现在把自己标识为 Grok 4.7，而不是声称是 Composer。Opus 5.5 现在得到与 Opus 5 相同的沟通指令，这些指令要求在第一次工具调用之前用一行说明它即将做什么，并以结果开头的简短收尾摘要。

## 1.0.32

- **在 Agent 等待后台 Subagent 时引导它。** 当 Agent 结束其回合去等待后台 Subagent 时，`run.steer(text)` 以前会解析为 `revert_to_followup`，因此你的消息会等到该工作完成。现在引导立刻作为下一回合运行，后台结果仍在之后到达。仅 TypeScript 本地运行。
- **知道一个步骤何时已完成请求工具。** 一旦模型完成列出一个步骤的工具调用，`onDelta` 现在会收到带 `callCount` 的 `tool-requests-listed` 更新，而这些工具可能仍在运行。用它来判断一个步骤里的每个工具调用何时已经开始，例如在你的 UI 里分组或批量处理一个步骤的工具调用。
- **Cloud Agent 创建的修复。** 对 Cloud Agent，第一次 `send()`（它在服务器上创建 Agent）在 SDK 生成 Agent id 时不再以 id 冲突失败：它用新 id 重试，或在它自己更早的创建已经成功时继续使用该 Agent。当进程从同一快照被恢复超过一次时，SDK 生成的 Agent 和运行 id 也不再重复。你自己固定的 id 仍会报告冲突。
- **桥接忽略你项目的 `.env` 和 `bunfig.toml`。** 独立的 `cursor-sdk-bridge` 可执行文件不再从工作目录加载 `.env` 或 `bunfig.toml`，SDK 启动桥接时工作目录通常是你的项目。检出里的文件不再能更改桥接的端点、token 或预加载代码。

## 1.10.0

### Projects

- **Projects。** Cursor for iOS 现在支持 Projects。你的 Projects 出现在收件箱自己的分区里；点一个打开其聊天。在 iPhone 上，New Project 行从一个名称启动 Project，并可选择仓库、分支和模型。如果团队管理员为团队关闭 Projects，Projects 分区和 New Project 会被隐藏。

### 修复

- **iPad 聊天。** 在 iPad 上，键盘升起时聊天保持可读，而不是在底部被模糊。

## Sep 21, 2026 当周

### Web

- **用你团队证书颁发机构的 SSH 证书登录仓库。** 团队管理员可以从 API Keys 页面的 SSH certificate authorities 分区添加受信任的 SSH 证书颁发机构，因此成员可以用短时的 OpenSSH 证书克隆、获取、推送和使用 LFS，而不必注册单独的密钥。管理员也可以要求证书，这会阻止已注册的 SSH 密钥和通过 HTTPS 的 API 密钥，而证书和 Origin CLI 继续工作。同样的控制在 Origin API 里可用；参见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **在 Origin API 上构建堆叠 pull request 工作流。** Origin API 返回的、并在 pull request webhook 里携带的 pull request，现在包含其栈和父 pull request，你可以在创建或更新 pull request 时设置或清除栈父级。你也可以列出栈里的每个 pull request。字段详情和对旧父字段的破坏性变更见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **squash 合并为每个做出贡献的人保留署名。** 当你 squash 合并 pull request 时，合并 commit 现在为被 squash 的 commit 的每个作者，以及这些 commit 已经列为共同作者的任何人，添加 Co-authored-by 尾注。Cursor 的 Agent 和机器人地址会被跳过，没有人被列出两次。当机器人的 pull request 被 squash 合并时，合并它的人以及批准其最新版本的人被记为共同作者。
- **精确选择应用安装到哪里，并逐一审查权限缩减。** 应用安装链接现在为特定命名空间打开同意页面。如果你可以在不止一个地方安装该应用，你先从 “Choose where to install” 列表里挑选，安装按钮会写出你挑选的命名空间。当已安装的应用要求更少权限时，其页面显示 “Permissions can be reduced” 通知，在审查页面上你可以保留或移除它将丢掉的每个权限。
- **审计日志现在记录仓库克隆和获取，并显示是谁编辑或删除了评论。** 当有人克隆或获取 Origin 仓库时，你团队的审计日志现在会记录它，并带他们的邮箱以及他们使用的是 HTTPS 还是 SSH。后台自动获取和机器行动者被排除。pull request 评论编辑和删除的审计条目现在点名做出更改的人，而不是评论的原作者。
- **合并和堆叠 pull request 的修复。** 没有剩余可合并内容的 pull request 现在会被阻止，而不是落地一个空 commit。带已经合并的更低变更的栈现在落到正确的基线，重新堆叠在停下时列出冲突文件，被取消的重跑不再把通过的检查变成红色，或在准备好就合并时阻止合并。重新打开的 pull request 也会跟上它们关闭期间推送的 commit。
- **pull request diff 和 Files changed 视图的修复。** 在巨大文件上 Expand all 不再冻结标签，超过文件上限的 pull request 让你加载更多文件，代码所有者盾牌回来了，你待处理的审查评论立刻显示为草稿。
- **pull request 页面和仓库设置的修复。** 用 `is:merged` 这类不受支持的筛选搜索仓库的 pull request，现在会显示说明该筛选并提供 Clear search 的空状态，而不是静默显示默认列表。仓库和命名空间权限设置只在 Admins 和 Members 有访问权限时列出他们，在你的个人资料上没有名字和邮箱时提交建议的编辑，现在会说明缺什么。
- **code tours 的修复。** 导览更新之前创建的 code tours 现在用最新版本重新生成，而不是显示过时内容，提前准备的导览仍立刻出现。导览不再包含来自模型的游离规划笔记，自定义导览保持不变。
- **Git 操作的修复。** 被拒绝的推送现在在每个 ref 旁边显示真实原因，而不是笼统的解包错误。对带非 ASCII 字符的路径，blame 和文件历史是正确的，名称里有连续点的仓库现在在克隆和推送 URL 里工作。
- **被取消的 Bugbot 检查的修复。** 当 Bugbot 审查在 pull request 上被取消时，其检查现在显示为已取消，而不是中性，因此后来变为必需的检查不再能在没有完成审查的情况下通过。留在 pull request 已经移过的 commit 上的检查仍显示为中性，除非该检查是必需的。

### API

- **应用可以代表命名空间成员行事。** Origin 应用现在可以请求短时的安装用户 token，以命名空间成员的身份行事，按用户 ID 或邮箱命名，用于 API 调用以及 HTTPS git 克隆、获取和推送。以这种方式完成的工作在 API 响应、webhook、审计日志和 PR 页面上被归于该成员 “via” 该应用。安装需要新的 User Delegation 权限；详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **更多 pull request 工作流可通过 Origin API 和 webhook 使用。** 你现在可以通过 Origin API 和 Origin SDK 删除 pull request 评论，按 head commit 筛选 pull request，看出每次检查运行写入做了什么，并读取某个版本的潜在合并 commit。webhook 现在覆盖评论反应，并点名是谁移除了标签，过期的应用安装 token 返回清楚的错误。详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。

## Sep 21, 2026 当周

### 环境

- **在草稿环境构建仍在运行时激活它。** 你现在可以在仍在进行中的草稿构建上点击 Activate，它一完成就成为你环境的活动构建。激活快照已被删除的草稿仍被阻止，并且不再更改你保存的环境设置。
- **默认开启的环境构建像已打开的构建一样工作。** 自己没有构建设置、构建默认开启的环境，现在显示其 Active Build 和失败构建警告，并在构建被触发时重建，因此其中的 Agent 从更新的构建启动。

### Agents

- **长运行模式已从 Web 移除。** Web 模型选择器里的 Long-running 选项、其时间限制药丸，以及团队的 Long-running agents 设置已经消失。从 Web 启动的 Cloud Agents 始终以标准模式运行。
- **协调者可以切换 worker 的模型。** Project 协调者现在可以在给 worker 发消息时把它移到不同的模型。新模型从 worker 的下一回合开始生效。

### Self-Hosted Machines

- **Slack、GitHub 和 Linear 里更短的自托管选项。** 在 Slack、GitHub 或 Linear 请求上加 `sh=t`，即可在你的自托管池上运行 Agent。`self_hosted` 也接受 `t`/`f` 和 `1`/`0`，三个表面现在接受相同的选项。写在 Slack 代码块内部的选项不再启动或配置 Agent。
- **接管 Self-Hosted Machines 上的桌面。** 接管 Cloud Agent 的桌面现在会到达你正在查看的机器，包括 Self-Hosted Machines 上的 Agent。更旧的机器回退到先前的接管。
- **你自己机器上的 Agent 更小心地处理 git 和设置。** Self-Hosted Machines 上的 Cloud Agents 现在在提交之前检查机器的 git 名称和邮箱，并且只在都没有设置时才设置仅仓库的回退。它们在机器上请求凭据，而不是假定仪表板密钥或已登录的 GitHub CLI。在 My Machine 上，Agent 偏好项目本地设置，而不是系统范围的安装和全局配置更改。
- **Slack 使用频道的默认 Self-Hosted 池。** Slack 里的 `@Cursor` 启动现在在设置了频道默认 Self-Hosted 池时使用它，然后是团队默认。如果启动被拒绝，回复会写出频道默认值，以及如何用 `@Cursor pool set <name> channel` 更改它。

### 模型

- **Start 方案上的 Grok 4.7。** Start 方案用户现在可以为 Cloud Agents 选择 Grok 4.7，固定为中等力度和标准速度。

### 可靠性

- **图像生成失败时的错误更清楚。** Cloud Agent 里的图像生成失败现在显示可读的说明，而不是原始状态码。
- **修复了把某些本地聊天移到云端。** 当聊天的已保存状态没有回合时，不输入新消息就把本地聊天移到 Cloud Agent 不再以 “Missing required fields” 错误失败。Cloud Agent 现在以空闲开始，保留聊天的历史，并运行你的下一次后续。

### 修复

- **环境和 Agent 启动的修复。** 启动 Cloud Agent 不再覆盖环境已保存的仓库，没有给出分支时启动默认分支。失败的环境构建现在报告具体原因（克隆、源代码管理访问或 pod 启动），用 Agent 范围密钥启动的 Agent 现在从环境的构建启动。
- **Self-Hosted Machines 的修复。** 在接受任意仓库的自托管池上启动的 Agent 和 Subagent 现在会被接走，而不是永远等待，空闲或运行已删除的机器立刻空出来，而不是显示为忙碌。从 GitHub 分离的 Origin 仓库上的 Agent 现在会收到其配置的密钥。
- **Cloud Agents 里 MCP 连接器的修复。** Cloud Agents 列出的连接器工具现在接受对它们做出的调用，包括带自定义标签的连接器账户。管理员授予的、由插件支持的 MCP 服务器现在会提供其工具，即使仪表板标签与插件名不同，或 Agent 以部署服务账户运行。环境信息工具现在显示每个已文档化的 `environment.json` 字段，对 Subagent 它报告实际强制执行的网络出站策略。
- **Agent 运行和后续的修复。** 在 Agent 的机器正在被重建时发送的后续现在会立刻保存到队列，因此在新机器准备好之前它不再看起来丢失。临时查找失败之后，Agent 到其 Origin pull request 的链接不再消失。用特定模型变体启动或跟进的 Agent，现在用该变体对照你团队的模型策略检查，因此它们不再被错误阻止。卡在从未开始的排队运行后面的 Agent，一小时后再次接受后续，而不是拒绝它们。
- **Agent pull request 里工件的修复。** Agent 放在 pull request 描述里表格或其他 HTML 内部的工件链接，现在渲染为可用的链接，而不是原始文本。无法确认存在的文件不再让 pull request 得到公开工件链接，有很多工件的 Agent 不再在后来的回合里把同样的工件重新宣布为新的。
- **Slack 消息格式的修复。** Slack 里的 Agent 回复保持链接完整，包括带括号或 `&` 的 URL，以及带方括号的链接文本。粗斜体文本、`~~~` 代码围栏、任务列表复选框和图片（现在显示为链接）正确渲染，`__init__` 或 `2*3*4` 这样的文本不再被错误地套上样式。
- **Cloud Agents 页面和仪表板的修复。** 在 cursor.com/agents 上，Agent 的第一页现在在页面渲染时于服务器上获取，因此完整页面加载时它出现得更快。在机器设置里，远程控制开关现在说明它为什么关闭，以及谁可以打开它。运行摘要统计卡片也会重排以适合可用宽度。

## Sep 21, 2026 当周

### 集成

#### GitHub

- **仓库访问立刻更新。** 你连接、重新连接或断开 GitHub 之后，你可以访问哪些仓库的变更会立刻生效，而不是等缓存过期。

#### GitLab

- **重命名和移动的项目。** GitLab 项目被重命名或移动之后，Cursor 解析当前项目，而不是过期的副本。
- **断开自托管 GitLab 会保留你的 GitHub 连接。** 在 Integrations 页面上断开自托管 GitLab 实例，现在会断开该实例，即使你当前的团队看不到它，而不是断开你的 GitHub 账户。

#### Azure DevOps

- **Bugbot 仓库状态。** Azure DevOps Bugbot 仪表板不再把已启用的仓库显示为已禁用。

### 仪表板与管理

#### Cloud Agents

- **更清楚的 Cloud Agent 环境构建失败。** 失败的环境构建现在说明出了什么问题，例如 “Git provider isn't connected”、“No access to a repository” 或 “GitHub IP allow list blocked access”，而不是笼统的错误。你可以在草稿构建仍在运行时激活它，构建日志现在包含 **Ask Agent** 链接。参见 [Cloud Agent Builds](https://cursor.com/docs/cloud-agent/builds.md#debug-a-build)。
- &#x20;**把 Cloud Agents 限制到选定的组。** Enterprise 管理员现在可以在 Cloud Agents 访问设置里把 Restrict By 设为 Groups，并挑选哪些组可以使用 Cloud Agents，而不是按名字列出成员。
- &#x20;**为服务账户设置支出上限。** 仪表板里的服务账户列表现在显示每个账户的支出上限和当前支出。管理员可以按账单周期编辑账户的上限，或把它设为 No limit。

#### 修复

- &#x20;**修复了团队 Groups 标签。** Groups 标签不再对某些组以 “invalid int 32” 错误加载失败。
- &#x20;**修复了成员和邀请。** 邀请屏幕现在拒绝电子表格、PDF 和长于 255 个字符的地址，而不是创建损坏的邀请。新创建的邀请链接现在立刻出现，而不是看起来缺失，从 Active Sessions 撤销你当前的会话现在会把你登出，而不是显示错误。
- &#x20;**模型访问设置更一致地生效。** 保存 Models 页面的一个分区，不再意外打开或关闭处于 7 天审查窗口的新 Cursor 模型。只有明确的 Enable now 或 Keep disabled 选择会被保存。你团队的模型阻止和发行延迟设置，现在在 Auto 路由到该模型时也会生效。
- &#x20;**更清楚的 Network Access Control 设置。** 设置现在说明阻止列表只适用于本地沙箱，列出每个列表支持的模式，并在条目（例如带端口的 IP 地址或路径）在沙箱列表里不会按预期表现时警告。
- &#x20;**修复了团队所有实例的 GitLab。** 在 Integrations 页面上，团队 GitLab 实例的 GitLab 仓库同步和你的个人连接状态现在使用你选中的团队，因此它们正确加载和同步。

### 自动化

- **Slack 触发器和频道选择器的修复。** 跨工作区共享的私有频道里由 Slack 触发的自动化，现在会为身为该频道成员的所有者触发。大型工作区上的 Slack 频道选择器在慢加载之后不再错过频道，Refresh 重新加载完整的频道列表。
- **自动化运行的修复。** 由 pull request 评论或 CI 完成触发的 Security Reviewer 自动化再次运行，而不是被跳过，其父 Agent 不再活跃的运行会以清楚的消息失败，而不是重试。
- **自动化编辑器的修复。** 从自动化的操作添加或编辑自定义 MCP 服务器，不再丢掉 OAuth 范围这类身份验证设置。
- **Origin 仓库更快的分支选择器。** 当你为 Origin 仓库上的自动化触发器设置分支时，默认分支现在立刻出现，而不是等完整的分支列表加载。

### API

- **组织审计日志。** 组织 API 密钥可以调用 [`GET /organizations/audit-logs`](https://cursor.com/docs/account/organizations/organization-admin-api.md#get-audit-logs) 来读取整个组织的审计事件。按时间范围、事件类型、搜索文本、用户或单个关联团队筛选，并带分页。
- **组织 API 密钥过期。** 创建组织 API 密钥时，你现在可以把它设为在 30、90 或 180 天，或一年后过期。密钥过期后，它不再对组织 API 进行身份验证。
- **一次查找很多 commit。** [commit 查找端点](https://cursor.com/docs/account/teams/ai-code-tracking-api.md#get-commit-details) 接受最多 100 个完整 commit 哈希的逗号分隔列表。三个或更多哈希的列表以前返回 404；超过 100 个现在返回 400。

### 安全与批准 Agent

- **对发现的更聪明的后续。** 部分修复的发现被视为已处理，失败的后续检查让现有线程保持原样。
- **重叠的发现保持可见。** 与已确认的发现重叠的新发现会作为自己的评论发布。
- **模型回退。** 当首选模型容量不足时，安全审查回退到下一个模型。
- **每次运行都可打开。** Security Agents 运行历史里的每次运行现在都可点击。运行打开其会话记录稿，没有记录稿时打开运行摘要。

### 模型与 Cursor Router

- **新模型：Grok 4.7。** [Grok 4.7](https://cursor.com/docs/models/grok-4-7.md) 现在可在 Cursor 中使用。
- **新模型：Claude Opus 5.5。** [Claude Opus 5.5](https://cursor.com/docs/models/claude-opus-5-5.md) 现在可在 Cursor 中使用。
- **更短的 Grok 模型名。** [Grok 4.5](https://cursor.com/docs/models/grok-4-5.md) 和 [Grok 4.6](https://cursor.com/docs/models/grok-4-6.md) 现在在模型选择器及其工具提示里以这些名字出现，没有 “Cursor” 前缀。
- **Auto 遵循团队模型设置。** 当团队管理员阻止 Cursor 提供的模型，例如 [Grok 4.7](https://cursor.com/docs/models/grok-4-7.md)，或团队对新模型的发行延迟尚未过去时，Auto 不再把该团队的请求路由到它。

## Sep 21, 2026 当周

- **Origin 上更准确的 Bugbot 检查。** 在 Origin pull request 上，出错或无法发布其审查的 Bugbot 运行现在会使 Bugbot 检查失败，而不是报告中性。被新推送取消的运行被标为已取消，因此它不再满足必需的 Bugbot 检查。
- **只有 Bitbucket 的团队可以完成 Bugbot 设置。** 只通过 Bitbucket 连接的团队，在 Bugbot 设置期间不再看到连接 GitHub 或 GitLab 的提示。设置步骤现在把 Bitbucket 连接显示为已连接。
- **审查会越过过大的文件继续。** 当一个更改的文件大到 Bugbot 无法审查时，Bugbot 跳过该文件，仍审查其他文件。以前，一个过大的文件可能让 Bugbot 跳过它之后的每个文件。

## 3.21

*除非选择加入，Enterprise 团队从 3.21.9 起获得 Projects。*

### Agents

- **带文件模式的团队规则现在只适用于匹配的文件。** 修复了限定到文件模式的团队规则，例如 `*.py`，被加到每个 Agent 对话。它们现在在 Agent 读取匹配的文件或你把一个加到上下文时生效，因此无关的聊天不再在它们上面花费 token。没有文件模式的团队规则仍适用于每个对话。
- **修复了用量上限提供 Switch to Auto 时卡住。** 当用量上限提供模型切换时，上限横幅现在在升级按钮旁边显示 **Switch to Auto**，提示词保持可编辑。选择 Switch to Auto 会清除横幅，并用 Auto 重发你的消息，使对话可以继续。
- **正在等你的云 Subagent 现在更显眼。** 当云 Subagent 停下来问你一个问题，或需要你登录 MCP 服务器时，Agents 药丸显示黄色状态点，托盘里该 Subagent 的行被标为需要注意，因此你可以解除阻塞，而不是等一个停滞的运行。
- **视频审查 Subagent 不再审查没有交给它们的视频。** 当 Agent 请 Subagent 审查视频时，视频文件现在必须被附加。只出现在提示词里的文件路径不再算数。如果没有附加视频，Subagent 会用纯文本说明，而不是编造审查。

### 聊天与 Composer

- **编辑排队消息或目标时的 Save 对勾。** 当你编辑排队的消息或目标时，发送按钮变成 Save 对勾。
- **Agent 流入时滚动更顺。** 当聊天跟随流入的回复时，它现在滑到新文本和工具卡片，而不是跳动。
- **引导提示打开 Settings。** 队列上方的 Steer New Messages? 横幅现在在 New Messages 处打开 Settings，而不是替你更改设置。消息第一次引导正在运行的 Agent 时，一次性通知会说明这一点，并链接到该设置。

### Projects

- **Create Project 选择更聪明的默认值并说明失败。** Create Project 对话框现在预选你正在查看的 Agent 的云仓库或环境，或你的 New Chat 草稿的，包括多仓库环境。创建项目失败时，对话框显示原因，而不是笼统的重试消息，如果你的方案阻止它，通知包含 Upgrade 按钮。
- **重新分配时 Project worker 会被重命名。** 当 Project 的协调者给 worker 一个非常不同的任务时，它现在可以重命名该 worker，使其侧边栏里的名字匹配新工作。

### Cloud Agents

- **看出哪个自托管 worker 在运行你的 Agent。** 在自托管池上运行的 Agent 在聊天状态栏里显示 worker 的名字及其池。
- **从项目选择器按名称查找任意云环境。** 在项目选择器里打字会搜索你的全部云环境，包括第一页之外的，并高亮匹配的文本。访问仍在检查时，Run on 菜单显示带加载旋转指示的 Cloud，而不是藏起它。
- **把 PDF、代码、归档和其他文件附加到 Cloud Agent。** Cloud Agent 提示词现在接受最多 10 MB 的任何非图像文件，来自拖放或 + 菜单，因此 Agent 可以在其环境里读取它。视频和空文件尚不支持，文件无法附加时你会看到消息。

### 文件、改动与 pull request

- **Origin 仓库的 pull request 在 Agents Window 里打开。** 当 PR Link Destination 设为在 Cursor 内部打开时，点击在 Origin 上跟踪的仓库的 github.com pull request 链接，现在会在 Agents Window 里打开 PR 标签，而不是你的浏览器。Origin pull request 上的 Bugbot 和 Automations 评论现在显示原生的 Fix with Agent 和 Add to chat 操作，而不是原始图像按钮。
- **你打开的浏览器标签留在你的工作区。** 你自己在 Agents Window 里打开的浏览器标签现在随工作区持久化，并在你切换 Agent 时保持可见，就像你自己的终端。Agent 打开的浏览器保持绑在该 Agent 上。每个工作区也保留自己的 cookie、登录和存储，退出 Cursor 会清除每个工作区的浏览器数据。
- **语法高亮现在匹配你的编辑器主题。** diff、PR 审查卡片和计划标签里的代码，用与编辑器相同的语法和主题颜色高亮。你更改或导入主题时它会更新，不再丢掉数字字面量这类颜色。
- **Markdown 预览渲染数学并记住你所在的位置。** 包在 `$$` 里的显示数学现在在 Markdown 预览里渲染为格式化的公式。切换标签再回来时，预览也按文件保留其滚动位置和光标。
- **从更多地方更容易到达终端。** Agent 终端和后台 shell 的 Terminals 药丸和托盘现在也出现在全屏编辑器的 Agent 叠加层里。点击无头后台 shell 现在在标签里打开其输出，终端已经打开时 Toggle Terminal 会再次隐藏面板，Project Agent 上的 Terminals 药丸再次打开和关闭终端托盘。
- **更新的 pull request 状态。** Cloud Agent pull request 现在每几分钟刷新 CI 检查、审查者和可合并性，因此过期的 Debug CI Failure 药丸会在检查变绿后清除。
- **修复了 Git 配置使用 include 时缺失的 Git 远程。** 用 Git 配置里的 `include` 或 `includeIf` 拉入的文件中定义的远程现在会被检测。包含空格的远程 URL，以及带注释的配置分区，也会被正确读取。

### 导航与布局

- **固定的 Cloud Agent 跨设备同步。** 你固定的 Cloud Agent 现在为所有人保存到你的账户，因此它们在窗口和设备之间保持固定。侧边栏的 Pinned 分区也在启动时立刻显示它们，而不是等待 Agent 列表加载。
- **Cmd+K 面板里更好的 Agent 搜索排序。** 当你用 Cmd+K 搜索 Agent 时，对话包含你的搜索的 Agent 现在排在只按名称或 PR 链接松散匹配的 Agent 之上。输入 pull request 编号仍能找到其 Agent。
- **Open IDE 打开你挑选的文件夹。** 在 Agents Window 里运行 `cursor .` 或用 Open Folder 选择文件夹之后，Open IDE 现在在编辑器里打开该文件夹，而不是空窗口，即使在你启动 Agent 之前。

### 自定义与设置

- **MCP 批准和登录的修复。** 当团队的设置或权限文件管理允许列表、该选择没有持久效果时，MCP 工具批准提示不再提供 “Always Allow”。已保存的登录被拒绝的 MCP 服务器，例如 X，现在显示为需要身份验证并带登录选项，而不是加载失败。Cloud Agent 里的 MCP 登录卡片不再被提前关闭，你已经回答过的卡片不再重新出现。
- **用 Esc 或 Cmd+W 关闭 Automations 面板。** Automations 面板现在在 Esc 或 Cmd+W（Windows 和 Linux 上为 Ctrl+W）上关闭，就像 Settings 和 Customize。以前 Esc 在那里什么都不做，Cmd+W 可能提示你关闭整个窗口。当你在自动化内部时 Esc 仍后退一步，你在字段里打字时它不会关闭面板。

### 修复

- **长运行上 Agent 可靠性的修复。** 不断重复同一工具调用的 Cloud Agent 现在得到与本地 Agent 相同的、打破循环的推动。有很多工具调用的长回合不再卡在一遍遍摘要上下文却腾不出任何空间。当模型以不同格式发送其补丁时，文件编辑不再失败。
- **Cloud Agent 工作区和斜杠菜单的修复。** 失去连接的 Cloud Agent 工作区会就地修复，而不需要重新加载窗口。切换账户或团队之后，Cloud Agent 斜杠菜单不再显示上一个团队的 Skills。
- **侧边栏的修复。** 在仓库分区上点击 + 会正好在该分区的仓库上启动新 Agent。即使子 Agent 仍在工作，Project 行也显示未读点，Projects 分区折叠时不再显示展开箭头，侧边栏折叠时标题栏图标不再移位。
- **工作区搜索和 SSH 工作区的修复。** 工作区搜索在云和远程工作区上不再挂起。它在工作区连接或重连时显示消息，如果远程主机停止响应则以清楚的消息结束。添加带几个远程文件夹的 SSH 工作区也更快。
- **模型选择器的修复。** 已保存的仅 MAX 模型变体，例如 1M 上下文，现在只在你关掉 MAX 时重置，而不是在非 MAX 聊天里使用选择器时。当团队取消 “Restrict to Auto” 模型策略时，选择器不再继续显示限制通知或藏起搜索栏。
- **浏览器的修复。** 本地 HTML 文件上的 Open in External Browser 会打开你的系统浏览器，当管理员阻止浏览器自动化时，你保存的浏览器模式不再被重置为 Off。
- **`/subscribe` 适用于 Origin 仓库。** 托管在 Cursor Origin 上的仓库里的 Cloud Agent，现在用 Origin 订阅工具等待其 CI 结果和 pull request 活动，而不是轮询。Agent 选择与 pull request 链接匹配的工具，因此 Origin 链接不再走到 GitHub 工具并失败。
- **Agent 面板的修复。** 当仍打开的聊天在后台被卸载时，Agent 面板不再被 “Something went wrong” 屏幕替换。聊天留在屏幕上。

## 3.21

### 浏览器

- **每个工作区分开的浏览器会话。** 内置浏览器现在为每个工作区分开保留 cookie、登录和存储，因此一个项目的会话不再带进另一个。参见[会话持久化](https://cursor.com/docs/agent/tools/browser.md#session-persistence)。退出 Cursor 会清除每个工作区的浏览器数据。元素选择和区域截图只响应你自己的点击，因此网页不能把虚假选择注入聊天。

### Agent

- **Agent 响应开始得更快。** 你聚焦聊天时，以及你发送消息时，Cursor 会在后台准备 Agent，因此第一句响应到达得更早。
- &#x20;**带文件模式的团队规则只适用于匹配的文件。** 限定到 `*.py` 这类模式的团队规则以前会被加到每个 Agent 对话。现在 Agent 只在读取匹配的文件或你把一个加到上下文时才选用它。没有模式的团队规则仍始终适用。参见[团队规则如何应用](https://cursor.com/docs/rules.md#format-and-how-team-rules-are-applied)。
- **@ 菜单不再显示不起作用的 Docs 选项。** Docs 类别和 Add New Custom Docs 选项已经停止把文档加到 Agent 上下文，因此它们从 @ 提及菜单里消失了。更早对话里的 Docs 芯片仍会显示。
- **引导提示打开 Settings。** 队列上方的 Steer New Messages? 横幅现在在 New Messages 处打开 Settings，而不是替你更改设置。

### 编辑器

- **Markdown 编辑器里的数学和更好的代码高亮。** Markdown 编辑器把 `$$` 显示数学块渲染为格式化的公式。Markdown 和计划编辑器里的代码块得到与代码编辑器相同的语法高亮和主题颜色，并在你切换主题时更新。

### 修复

- **被移除的行在 Agent 编辑 diff 里保持被移除。** 在为 Agent 编辑显示的 diff 里，以 “--” 开头的被移除行保持为被移除的行。
- **编辑先前消息的修复。** 编辑并重新提交更早的消息会可靠地发送你编辑后的文本并刷新 Checkpoint。就地编辑时，Cmd+/ 为该消息打开模型选择器，Escape 关闭打开的选择器而不取消编辑，Shift+Tab 有效，Cmd+Enter 提交设置被尊重。
- **Cloud Agents 的修复。** [Cloud Agent](https://cursor.com/docs/cloud-agent.md) 工作区不再显示文件夹信任提示，或你无法修复的文件监视器警告。
- **Agent 可靠性的修复。** 长回合不再在每一步卡住压缩上下文，当模型把补丁包在 JSON 里时 Agent 编辑不再失败。Add Folder to Workspace 之后聊天保持附在其文件夹上，你聚焦 Agent 聊天时其上的未读徽章会清除。
- **模型选择器的修复。** 需要 Max Mode 的已保存模型变体，例如 1M 上下文，在你于没有 Max Mode 的聊天里使用模型选择器时不再重置；它们只在你关掉 Max Mode 时恢复。当团队取消其 “Restrict to Auto” 模型策略时，选择器丢掉旧的限制通知并带回搜索栏。
- **MCP 服务器的修复。** 当团队或权限文件控制 MCP 允许列表时，[MCP](https://cursor.com/docs/mcp.md) 批准提示不再提供 “Always Allow”。已保存 token 被撤销的 MCP 服务器会请你再次登录，而不是显示连接错误。Cloud Agent 里的 MCP 登录卡片不再被提前关闭，你已经回答过的卡片不再重新出现。
- **git 和编辑器的修复。** 通过 git 配置里的 `include` 或 `includeIf` 定义的 Git 远程会被检测，状态栏 blame 始终匹配活动编辑器。当环境变量名以数字开头时，扩展宿主不再无法启动，已删除或不可读文件的备份不再在每次窗口打开时重新打开。在 Review Changes 窗格里，更早的刷新不再覆盖更新的 Issues Found 列表。
- **远程工作区里的搜索不再挂起。** 当搜索尚不可用时，它现在会超时并显示错误，而不是永远等待。
- **Agent 可视化的修复。** 来自 `/visualize` 的可视化卡片现在在 IDE 里渲染。图表里的阴影区域不再渲染成纯黑，可视化加载行与其他工具调用对齐。

## September 17, 2026 发行

- **`origin auth status` 里的团队详情。** `origin auth status` 打印你的团队、角色和组织 id，`--json` 为脚本把它们打印为 JSON。

## 1.9.0

### Composer

- **从 Files 附加任意文件类型。** 当你在 Composer 里添加上下文时，Files 选择器现在让你挑选任意文件，而不只是固定的类型列表。图片作为图片附加，归档、音频或源代码等其他文件作为文档附加。视频不受支持，并会显示消息。
- **听写时看见你的话。** 当你在 Composer 里听写时，实时字幕在你说话时显示你的话，仍在识别的部分以灰色斜体显示。

### Agents

- **Agent 聊天里的预览卡片。** 当 Cloud Agent 分享其正在运行的应用时，Cursor for iOS 显示带截图、标题和描述的预览卡片。点 Preview 在 cursor.com 上打开 Agent 的桌面视图。
- **MCP 登录请求。** 等待你登录 MCP 服务器的 Cloud Agent 在收件箱和 Live Activity 上显示为 Needs Attention，直到你点 Skip 或 Authenticate。然后登录卡片会说服务器已通过身份验证，或身份验证被跳过。

### Agent 列表

- **在 iPhone 上折叠 Pinned 和 Drafts。** 点 Agent 列表里的 Pinned 或 Drafts 标题，折叠或展开该分区。该设置会带到 iPad 侧边栏。

### Review

- **审查 Origin pull request。** 在 Cursor Origin pull request 上，Review 按钮打开一个面板，你可以在那里 Comment、Approve 或 Request Changes，并带消息。

### 修复

- **Agent 聊天。** Agent 消息里的链接在你一点就打开，新 Agent 的提示词固定在顶部，而不是回复开始时跳动。部分加载的已完成对话会重新加载而不重复消息，加载动画的点再次可见。
- **pull request 和仓库名。** 点从链接打开的草稿 pull request 上的 Mark Ready，现在会把它标为可审查。托管在 Cursor Origin 上、或 URL 以 `.git` 结尾的仓库显示为 `owner/repo`。
- **Composer 和键盘。** 重命名 Agent 不再把 Composer 栏连同提醒的键盘一起往上推。Composer 占位符在新 Agent 和后续文本之间平滑变化，Composer 上方的快速操作药丸在原地淡入，打开 Reduce Motion 时是普通淡入。
- **复制和视觉打磨。** 统计加载时，个人资料面板显示图表标题和网格线。进行中的标签读作 “Working” 和 “Thinking”，Customize 面板分区被重命名为 Show 和 Agent Properties。

## September 16, 2026 发行

- **更清楚的栈状态。** `origin pr stack` 显示栈里每个 pull request 与其父级的关系，以及修复它的操作。

## v2026.09.15

### ACP

- **Agent Client Protocol (ACP) 客户端可以把 Subagent 显示为自己的会话。** 宣告 `subagents` 能力的客户端把每个 Subagent 作为链接到父 Task 工具调用的子会话得到，带流入的文本、思考、工具调用，以及最终的已完成、失败或已取消状态。对这些客户端，提示词会等待其后台 Subagent，并在同一回合里包含它们的结果。取消提示词会停止它们，加载已保存的会话会再次显示其过去的 Subagent。没有该能力的客户端继续接收 [`cursor/task`](https://cursor.com/docs/cli/acp.md#cursortask) 通知。

### 登录

- **登录信任你操作系统的 CA 证书。** `agent login` 为其身份验证请求使用的、可感知代理的获取，直接或通过 [`HTTPS_PROXY`](https://cursor.com/docs/cli/reference/configuration.md#proxy-configuration)，现在把你系统的受信任 CA 证书加到捆绑集合，因此一旦你公司的根证书在系统信任存储里，公司 TLS 检查后面的登录就能工作。

### 修复

- &#x20;**带文件模式的团队规则不再加载进每个提示词。** 限定到 `*.py` 这类模式的团队规则，现在只在 Agent 读取匹配的文件时生效。没有模式的[团队规则](https://cursor.com/docs/rules.md#format-and-how-team-rules-are-applied)仍适用于每个请求。
- **`agent models` 列出你团队的 Bedrock 模型。** 当你的团队通过 IAM 角色连接 AWS Bedrock 时，`agent models` 和 `--list-models` 现在显示你所在区域的 Bedrock 模型，而不是空列表。该列表与 `--model` 接受的一致。
- **shell 命令在 macOS computer-use worker 上运行。** 在以 [`--computer-use`](https://cursor.com/docs/cloud-agent/self-hosted/computer-use.md#macos) 启动的 Mac worker 上，shell 命令不再在运行之前以 “The shell command returned no exit status” 失败。
- **Agent 工具和回合的修复。** 当 Agent 等待正在运行的 shell 命令时，它现在会在命令完成或其输出匹配 Agent 正在监视的模式时立刻继续，而不是空闲到超时。当模型把补丁包在 JSON 里时，文件编辑不再中断。回合重试失败的连接之后，停止 Agent 仍然有效。
- **长回合和 Auto-review 的修复。** Claude 和 GPT 模型上有很多工具调用的长回合，不再卡在每一步压缩上下文却腾不出空间。当分类器回复漏掉决定时，Auto-review 现在会从被阻止的动作得出决定。
- **自托管 worker 的修复。** 以 [`--clone-git-repos`](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#any-repo-pools) 启动的 worker 现在会克隆没有方案存储的仓库，例如 `github.com/owner/repo`，而不是因为 git 把它们读成本地路径而失败。当 Cursor 对 `agent worker controller` 限速时，它等待服务器的 `Retry-After` 时间，最多 1 小时。带着过期认领重连的池 worker 现在会丢掉它，而不是拒绝其下一个 Agent，账户验证失败的 worker 显示服务器的原因，而不是裸 “Error”。
- **每个模型上的循环提醒。** 当任何模型上的 Agent，不只是 Composer，不断重复相同的工具调用或消息时，它现在会得到尝试不同做法的提醒，而不是循环到碰到步骤上限。
- **请 Agent 停止目标会关闭它。** 当你请 Agent 停止朝 [`/goal`](https://cursor.com/docs/cli/reference/slash-commands.md) 工作，它现在可以把该目标标为完成，而不是让它保持活动。

## September 15, 2026 发行

### 仓库

- **编辑仓库设置。** `origin repo edit` 设置默认分支、可见性、允许的合并方法和合并时删除分支。`origin repo view` 显示这些设置，`origin repo list --json <fields>` 把仓库打印为 JSON，并可选 `--jq` 筛选。
- **从 CLI 管理访问。** `origin repo access` 和 `origin namespace access` 为团队、用户和组织组列出、授予、更改和移除访问，并列出你可以分配的角色。在你个人命名空间里的仓库上，`origin repo access` 也列出、授予和移除外部协作者，带 Read 或 Write 访问。
- **管理 GitHub 镜像。** `origin repo mirror` 开始镜像过渡，监视其状态，按需同步 ref，并分离镜像，带 `--json` 输出。
- **验证迁移。** `origin repo verify-migration` 比较 GitHub 仓库的默认分支、可见性、合并设置、标签和规则集与其 Origin 副本，不匹配时以 1 退出。用 `--namespace` 检查命名空间里的每个仓库。
- **规则集、标签和应用。** `origin ruleset create`、`update` 和 `delete` 从标志或 JSON 规范管理规则集，`origin ruleset import --from-github` 转换 GitHub 规则集或分支保护。`origin label` 列出、创建、编辑、删除并批量导入标签。`origin app` 管理 Origin 应用、其签名密钥和 webhook 投递。
- **更严格的 CloneKit 下载。** `origin repo clone-fast` 对照工具包的大小检查每个范围响应。如果服务器停止遵守字节范围，它把包作为一个文件下载，返回错误字节数的范围会使工具包下载以错误失败。

### Pull request

- **设置合并 commit 消息。** `origin pr merge` 接受 `--subject`、`--body` 和 `--body-file`，为单个 pull request 设置 commit 消息。当栈作为一个 commit 落地时，这些标志被忽略，而不是使合并失败。
- **非常大的 pull request 的 diff。** `origin pr diff` 和 `origin pr view` 分页获取 diff，因此非常大的变更不再在过大的响应上失败。
- **从 CLI 请求更改。** `origin pr review --request-changes`（`-r`）提交请求更改的审查，而不是报错。在 `--approve`、`--request-changes` 或 `--comment` 中恰好传入一个。

## Sep 14, 2026 当周

### Web

- **更清楚的仓库访问，人员或组离开时访问会被移除。** 代码库和仓库设置里的 Permissions 标签现在叫 Access，那里的每个组显示它是 Team Group、Org Group，还是来自身份提供方的 Synced Group，并带成员数。当有人离开团队或组织时，他们失去通过其组拥有的仓库访问。当组织组被删除时，它授予的仓库和命名空间访问会被自动移除。
- **团队或组变更影响你的仓库访问时的电子邮件。** 当你加入或离开团队或组时，Origin 现在每次变更给你发一封电子邮件，列出你获得或失去访问的仓库和代码库。访问电子邮件里的每个资源链接到其在 Origin 上的页面。
- **重新设计的 Apps 页面，带 Marketplace 和更清楚的安装屏幕。** 代码库设置里的 Apps 页面现在打开到用于浏览应用的 Marketplace，并有已安装应用的单独 Manage 视图。安装屏幕显示应用是 Verified by Cursor（意味着它列在 Marketplace 里）还是 Not verified by Cursor。它也有更简单的仓库选择。应用页面可以列出发布者的公开 Cursor 插件，仓库 Apps 设置的每个查看者都可以打开已安装应用的页面。
- **更快的 pull request 和仓库列表页面。** pull request 页面现在在首次绘制时显示标题、状态、分支、Auto-merge 徽章和现有审查者，而不是占位符。仓库列表和仓库的 Pull requests 列表随页面渲染其第一页。
- **用建议构建 pull request 搜索。** pull request 列表上的搜索框现在在你打字时建议 `author:`、`label:`、`assignee:`、`review:`、`is:` 和 `sort:` 这类限定符，以及仓库协作者和标签这类匹配值。识别出的限定符在框里高亮，因此你能看出哪些筛选生效。
- **子模块更新在 pull request diff 里正确显示。** 当 pull request 添加、更新或移除子模块时，Files changed 标签现在显示带子模块旧 commit 和新 commit 的卡片，而不是错误或空文件。当两个 commit 来自 GitHub 或 Origin 上的同一仓库时，卡片链接到它们之间的比较。
- **仓库下载立刻开始，并解压到一个文件夹。** 从代码库页面下载仓库归档现在直接返回文件，即使它还没准备好，因此你不再需要等待和重试。归档解压到单个 `owner-repo-shortsha/` 文件夹，并以此名称下载，匹配其他 Git 主机使用的布局。详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **Bugbot 和 Security Review 检查的名字更清楚。** pull request 上的 Bugbot 检查现在在一个 Cursor 组下显示为 “Cursor / Cursor Bugbot” 和 “Cursor / Cursor Bugbot Autofix”，而不是把同一名字重复两次。Security Review 检查现在出现在 “Cursor Security Agent” 下，以自动化名称作为检查标题，而不是内部账户名。
- **从 Codebase 欢迎屏幕创建仓库。** 如果你可以创建仓库、还没有仓库，并且没有关闭 Codebase 欢迎屏幕，它现在在 Sync from GitHub 下有 Create Repo 按钮，打开 New Repo 对话框。
- **更安全的推送、应用安装和变基。** 推送名称不是有效 UTF-8 的分支或标签，现在只以清楚的错误拒绝该 ref，推送的其余部分仍会落地。为无法写入 Origin 的命名空间创建或安装应用现在会被拒绝，与仓库创建一致。Origin 重放的 commit，例如变基 pull request 时，现在保留其 jj change-id，因此 jj 用户保持变更身份。
- **堆叠 pull request 和重新设定目标的修复。** 把 pull request 重新设定到它已经有的基线，或到已合并祖先的分支，不再把它从栈上分离，已合并的栈成员现在显示它们落地的分支作为其基线。重新堆叠在包含合并 commit 的分支上工作，重新设定目标之后的 CODEOWNERS 审查请求匹配合并检查所强制的规则，重新打开的 pull request 对照当前基线的新合并运行 CI。
- **pull request diff 和 API 的修复。** diff 超时现在显示超时错误，而不是笼统的错误，HTTP API `:syncMirror` 端点再次工作，浏览没有 commit 的仓库会报告空仓库错误。
- **按 SHA 搜索 commit。** 仓库的 Commits 页面现在有搜索框。输入 commit SHA 的至少五个字符来预览匹配的 commit，或按 Enter 列出以该 SHA 开头的 commit。

### API

- **通过 Origin API 搜索文件内容。** Origin API 里新的 Grep Contents 操作在任意 ref 上搜索仓库里的文件，并返回匹配的行。你可以使用正则表达式或精确文本，要求周围的上下文行，并用包含和排除 glob 缩小搜索。详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **从 API 检查 pull request 能否合并。** Origin API 里的新预览端点告诉你 pull request 是否可合并，如果不可，是什么在阻止它：必需的检查、批准、代码所有者批准、合并冲突，或其栈的形状。你可以传入预期的 head commit，以便 pull request 已经移动时答案快速失败。详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **按近期活动排序 pull request，并只列出已合并的。** 当你通过 Origin API 列出 pull request 时，现在可以按上次更新而不是创建时间排序，并筛选为只含已合并的 pull request。按更新时间排序在 Origin MCP 服务器的 pull request 列表工具里也有效。详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **通过 Origin API 删除分支。** 你现在可以用 Origin API 从脚本或集成删除分支，而不推送删除。默认分支和镜像仓库里的分支不能以这种方式删除，来自被删除分支的未合并 pull request 会被关闭。详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **通过 Origin API 把仓库加到现有应用安装。** 命名空间管理员现在可以通过 Origin API 把仓库加到应用安装，而不必再次走安装流程。该调用只添加仓库，绝不更改应用的权限。如果应用尚未安装，错误现在会说明管理员必须在浏览器里完成首次安装，并包含要打开的链接。列出安装的仓库现在也接受对仓库名或 `owner/repo` 的不区分大小写筛选。详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **pull request 标签的 webhook。** Origin 应用可以订阅 `pull_request.label.added` 和 `pull_request.label.removed` 事件，因此你的自动化可以在 pull request 上的标签变更时做出反应。每个事件包含 pull request 和标签，标签添加事件也包含是谁添加的。载荷详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **webhook 投递暂停时的电子邮件警报。** 如果你的 Origin 应用的 webhook 投递在反复失败后被自动暂停，命名空间管理员以及创建或最后编辑该应用的人现在会收到一封电子邮件，带失败摘要、恢复步骤，以及管理投递的链接。接收方现在有最多 10 秒响应，然后投递超时，从 5 秒提高。检查运行创建和完成载荷不再包含顶层 `actor`；改用 `check_run.actor`。参见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **应用和仓库创建的范围变更。** 通过 Origin API 读取应用详情现在需要命名空间应用读取范围，创建或镜像仓库需要 “Create repositories” 范围。详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。

## Sep 14, 2026 当周

### Agents

- **在别处归档的 Agent 的后续现在能送达。** 如果你在另一个客户端上归档 Agent，例如桌面应用，然后从你在 Web 上已经打开的标签发送后续，Cloud Agents 现在会取消归档该 Agent 并送达你的消息，而不是失败。这从 Agent 聊天、重新提交的消息、侧边聊天，以及 pull request 页面上的审查 Agent 侧边栏都有效。已删除的 Agent 保持关闭。
- **计划和限制对话框出现在你启动 Agent 的地方。** 当 Cloud Agent 因为你的方案、用量定价、模型选择或受保护范围而无法启动时，说明原因的对话框现在在你开始的页面上打开，对 cursor.com 的 Agents 区域内的页面而言，而不是稍后在 Agents 页面上。
- **草稿 pull request 保持草稿。** 修复了一个问题：即使你的仓库规则说要保持草稿，Cloud Agents 也会自行把它们的草稿 pull request 标为可审查。Agent 现在遵循你的指令和仓库指导，并在你要求时仍把 PR 标为就绪。
- **修复了在另一个分支或 commit 上启动时 Agent 读取默认分支。** 当你在特定分支或 commit 上启动 Cloud Agent 时，它现在会等到该 ref 被检出之后才运行任何工具，因此它不再先从默认分支读取文件或描述它。
- **被移到另一台机器时，Cloud Agents 保留其环境和文件。** 以前，如果你在发送后续时 Agent 的环境被移动，Agent 可能报告环境不可达，并在一个新的环境里继续，丢失你的文件。现在 Agent 显示 “Reconnecting to your environment. This can take a few minutes.”，自行重试，并在同一环境里继续，每个文件都在。如果重连太久，它会请你再次发送消息，而不是从头开始。
- **最多固定 75 个 Agent。** cursor.com 上的 Agent 列表现在让你最多固定 75 个 Agent，从 50 提高。
- **被阻止的模型现在在你发送后续时立刻被拒绝。** 如果你把 Cloud Agent 后续切换到团队管理员已阻止的模型，你现在会立刻得到 Model Blocked 错误。以前后续会被排队，然后在运行中途失败，或悄悄在不同模型上运行。
- **Agents Web 应用上页面布局的修复。** 打开对话、仓库或 pull request 不再在侧边栏折叠时使页面跳动，如果你保持侧边栏折叠，或在窄窗口里打开对话，侧边栏会以已经折叠的状态加载。滚动条出现时 Agent 列表不再横向移位，推广、Cursor Learn 横幅或连接源代码管理提示在页面加载后出现时也不再下移。
- **Cloud Agent 运行和后续的修复。** 排队的后续、仓库 Skills 和斜杠命令现在在侧边聊天里加载，来自 Composer 的仓库命令药丸把其内容传给 Agent。来自 GitHub 速率限制的 PR 创建失败现在会说要等多久。
- **更少失败的启动和后续。** 当 Cloud Agent 无法得到 worker 来启动其会话时，它现在会自行重试；如果仍然不能，它会说 “Cloud Agent could not get a worker to start your session; please retry in a few minutes.”。后续回合不再从工作区捡起另一个账户的个人规则或 Skills。失败或已归档 Agent 的斜杠命令菜单停止等待永远不会加载的命令，从 Agent 的 Context 删除的文件夹不再带着残留文件重新出现。
- **在命名分支上更快的启动。** 在明确命名的分支上启动的 Cloud Agents，无论是默认分支还是另一个，现在可以使用更快的启动路径，而不是回退到更旧的启动。

### Projects

- **团队管理员的 Projects 设置现在适用于 Cloud Agents。** 当团队管理员关闭 Projects 时，该团队的 Project Agent 不再出现在 Cloud Agent 列表里，启动新 Project 会以 “Projects are disabled for your team.” 失败。个人账户，以及没有更改该设置的团队，保持 Projects 打开。
- **Projects 协调者和 worker Agent 的修复。** 协调者现在可以在 Project 的仓库以新名称发布之后启动 worker，PR 标签编辑、环境草稿和共享链接也使用已发布的名称。强制向 worker 提交消息不再让协调者对它的调用卡在运行中。当 worker 无法创建时，协调者得到真实原因，例如碰到嵌套上限，而不是重试笼统的错误。
- **云 worker 共享 Project 的 Context。** Project 协调者创建的云 worker 现在把协调者的 Context 当作自己的，因此它们保存在那里的文件在整个 Project 中可见。把 worker 移出 Project 会再次给它自己的 Context。

### 环境

- **环境构建的修复。** 卡在进行中的构建不再阻止定时构建，卡住的构建在 9 小时后被标为失败，而不是 24 小时。环境列表对大型团队也会加载，而不是超时。因为团队的构建账户无法访问仓库而失败的团队环境构建，现在用启动该 Agent 的人的访问重建。

### Self-Hosted Machines

- **自托管池现在尊重仓库访问。** 在团队的 Self-Hosted Machines 池上启动 Cloud Agent，如果你无法读取该仓库，现在会以访问错误失败。池列表只显示你有访问权限的仓库，限制到某些仓库的 API 密钥只能使用这些仓库的池。
- **没有仓库的自托管 Agent 可以设置一个。** 在 Self-Hosted Machines 上没有仓库启动的 Agent，现在遵循你 worker 的规则和凭据来找到或克隆它们需要的仓库，而不是说它们没有仓库访问。如果没有规则说明如何得到该仓库，Agent 会说它没有被配置，而不是猜测克隆 URL。
- **自托管和私有 worker 的修复。** 机器仍在启动的池上的 Agent 会等待它，而不是失败，死亡的 worker 连接会重连或重新分配，而不是以关闭传输错误失败，Windows worker 上的 Agent 不再破坏显示它们的客户端。共享父 worker 的 Subagent 不再使父工作区变成孤儿。

### 集成

- **来自 Slack、GitHub 和 Linear 的更可靠的自托管路由。** 在请求里提到 `self_hosted` 这个词不再意外把 Agent 送到 Self-Hosted Machine。用 `self_hosted=true` 明确选择加入，它现在在内联代码里和带尾随标点时也有效。在 GitHub 和 Linear 里，围栏代码块内部的选项被忽略。在 Slack 里，没有仓库的自托管池的 `pool=<name>` 现在会在该池上启动 Agent，而不是无法匹配团队的默认仓库。
- **Jira 启动使用正确的仓库主机。** 从 Jira 启动、默认仓库设为 `owner/repo` 的 Cloud Agents，现在针对你已连接的 GitLab 或 Azure DevOps 主机上的该仓库运行，而不是假定 github.com。

### MCP

- **有很多 MCP 工具的 Cloud Agent 不再在模型工具上限上失败。** 当 MCP 服务器暴露的工具多于模型接受的数量时，Cloud Agent 现在会限制它们发送的 MCP 工具，优先保留浏览器和自定义工具，而不是使请求失败。
- **空的 Cursor 托管仓库的清楚错误。** 在托管于 Cursor 的空仓库上启动 Cloud Agent 或环境构建，现在会说该仓库是空的，并请你添加初始 commit，而不是显示笼统的失败。

### 仪表板

- **Cloud Agents 设置和机器页面的修复。** 切换团队之后，My machines 列表不再显示上一个团队的机器，其页面保存在 URL 里，因此链接和返回导航落到正确的页面。以任何方式关闭 New environment 对话框，包括浏览器返回，现在会清除草稿，而不是留下过期的仓库、名称和范围。Preferences 和 Security 这类设置分区有可分享的锚点链接，计数显示千位分隔符。

## Sep 14, 2026 当周

### 集成

#### Slack

- **自动化里的完整机器人警报。** 从 Slack 触发的自动化现在读取使用附件块的机器人警报的完整内容，而不只是其一行回退文本。

#### GitLab

- **大型 GitLab 组和仓库。** GitLab 仓库选择器在大型组织上不再超时，解析分支或标签在有数千个分支的仓库上有效。

#### Azure DevOps

- **过期 Azure DevOps 访问的重新连接提示。** 当 Microsoft Entra 拒绝你的 Azure DevOps 刷新 token 时，Cursor 停止重试，仪表板显示连接卡片，以便你重新连接。
- **大型 Azure DevOps 访问 token。** 当你的 Azure DevOps 访问 token 大于 4 KB 时，仪表板和 Cloud Agent 启动不再以服务器错误失败。
- **Azure DevOps 仓库选择器里按组织的错误。** 当一个 Azure DevOps 组织拒绝列出其项目时，仓库选择器在该组织上显示访问错误，而不是使整个选择器失败。

### 仪表板与管理

#### 成员与组

- &#x20;**从身份提供方推送的组现在显示为团队组。** 当身份提供方通过 [SCIM](https://cursor.com/docs/account/teams/scim.md#directory-groups) 推送目录组时，Cursor 在仪表板里创建匹配的团队组，并保持其成员同步。在身份提供方里把用户从该组移除，会把他们从团队组移除，删除该组会把它退役。此变更之前同步的组继续按原来的方式工作。
- &#x20;**被移除的成员现在在所有地方被登出。** 当你从未连接到 SSO 的团队移除成员时，Cursor 结束他们的全部活动会话，因此他们立刻失去访问。只有该成员仍属于同一组织里的另一个团队时，会话才保持活动。你的审计日志流也得到一个新的凭据已撤销事件，记录该成员的会话、仓库访问和 Cloud Agent git token 发生了什么。
- **在组织里选择你的主团队。** 如果你是组织里两个或更多已设置账单、至少有一个席位、且订阅未被取消的团队的直接成员，在仪表板里选中该组织中的一个团队时，在 My Settings > More 下挑选一个主团队。当没有指定团队时，Cursor 将该团队用于你的账户，或者你可以把它设为 None。
- &#x20;**在新的 Access 标签上看见组的 Codebase 访问。** 团队和组织组页面有 Access 标签，显示该组对你团队 Codebase 的访问以及任何特定仓库的访问，并带更改它的 Manage 链接。
- &#x20;**组织管理员出现在团队成员列表里。** 团队成员列表现在包含关联组织的管理员，标为 Org Admin。

#### 工作区设置

- &#x20;**为你的团队打开或关闭 Projects。** 管理员可以使用 Team Settings 里 Chat 下的 Enable Projects 开关，控制团队成员能否在 Cursor 里使用 Projects。关闭时，Projects 对该团队上的每个人隐藏。
- &#x20;**每个团队的数据导出。** Data Exports 标签不再限于 Enterprise 团队。任何有权限读取导出的团队成员都可以打开它，并下载 Cursor 为该团队准备并发布的归档，包括订阅已结束的团队。导出链接直接打开到正确的团队。
- &#x20;**成员机器上的团队 MCP 服务器（此后已从仪表板移除）。** 本周在 Plugins & MCPs 标签的 Team MCP Servers 分区增加了 Run team MCP servers on members' machines 开关。Plugins & MCPs 标签不再显示该开关。团队和成员也各自可以配置最多 1,000 个 MCP 服务器。
- &#x20;**把不止一个 Jira 站点连接到团队。** 已连接 Jira 站点的管理员现在在仪表板的 Jira 页面上看到 Connect another site 操作。它打开 Atlassian Marketplace，在新站点上安装 Cursor，你回来时站点列表会刷新。
- **从设置回到 Agents 的更清楚方式。** 设置侧边栏标题现在在 Cursor 标记旁边显示 Back to Agents 链接。

#### 用量与 Cloud Agents

- &#x20;**Security Reviewer 支出在 Usage 页面上更容易跟踪。** Security Reviewer 用量现在归于 Security Reviewer，你可以按它筛选用量事件。当你按 Security Reviewer 筛选时，每次审查运行显示为一行，包括它派生的审查者 Agent，而不是被拆到很多行。
- **精确看出 Cloud Agent 环境构建用了什么。** 环境[构建](https://cursor.com/docs/cloud-agent/builds.md)页面现在有 Configuration 分区，显示构建的来源、安装和启动脚本、终端和 environment.json。构建日志位于可折叠的 Build output 面板，带 Ask an agent about these logs 按钮，它们在超过一分钟后继续流入，而不断开或重复行。

#### 修复

- **仪表板页面在加载时不再跳动。** Overview、Usage、Billing、Cloud Agents 和 Integrations 页面为横幅、用量表和发票预留空间，仪表板在首次加载时不再横向移位。
- &#x20;**修复了 MCP 服务器和密钥设置。** Choose Tools 对话框留在屏幕上，其标题和 Use Selected Tools 按钮可见，滚动长工具列表，并把长工具描述修剪为三行，悬停或点击时显示全文。编辑 MCP 服务器不再丢掉范围这类额外的身份验证设置。新密钥的更新警告现在只在同一范围内存在该名称的密钥时出现。
- &#x20;**团队组上的 MCP 限制现在生效。** 保存在团队组上的 MCP 设置现在对该组的成员强制执行。在链接到团队组的 SCIM 目录组上编辑 MCP 设置，会把它们保存到该团队组，而不丢掉其现有限制。

### 自动化

- **从卡片网格启用 Cursor 构建的 Agent。** 自动化页面的 From Cursor 分区把每个 Agent 显示为带已启用或已禁用徽章，以及 Enable 或 Manage 按钮的卡片。
- **更清楚的删除确认。** 从列表或其设置页面删除自动化，会打开一个对话框，写出该自动化，并警告删除无法撤销，取代浏览器的确认弹出框。
- **Security Reviewer 的修复。** 当触发仓库被删除或从 GitHub App 移除时，Security Reviewer 继续同步，覆盖组织范围触发器里的仓库，并重试连接失败的 Origin、GitLab 和 Bitbucket 仓库。
- **Origin 仓库上的自动化。** Security Reviewer 现在在团队 Origin 仓库的 pull request 上触发。来自自动化的 Origin 检查运行按产品名称分组，例如 Cursor Automation，每次运行以其自动化命名。

### API

- **按 ID 或名称查找组。** [组织组](https://cursor.com/docs/account/organizations/organization-admin-api.md#organization-groups)对象现在包含 `publicId`，[List Organization Groups](https://cursor.com/docs/account/organizations/organization-admin-api.md#list-organization-groups) 接受 `name` 查询参数，返回具有该确切名称的组。
- **筛选组织成员。** [`GET /organizations/members`](https://cursor.com/docs/account/organizations/organization-admin-api.md#list-organization-members) 现在接受 `email`、`userId`、`search`、`organizationRole` 和 `teamId` 查询筛选。`email` 和 `userId` 各自接受最多 100 个逗号分隔的值。
- **团队目录组。** [Admin API](https://cursor.com/docs/account/teams/admin-api.md#team-directory-groups) 增加 `/teams/directory-groups` 端点，以列出、创建、读取、更新和删除团队的目录组，包括每个组的名称和月度支出上限，并按用户 ID 添加或移除成员。

### 安全与批准 Agent

- **审查之后的 MCP 操作。** 当审查完成时，Security Reviewer 使用 Agent 配置的 MCP 服务器，执行你的自定义指令所要求的操作。
- **重新设计的运行记录稿。** 打开在 Cursor 托管上运行的 Security Reviewer 运行，会显示样式类似 Cloud Agents 的记录稿。

### 模型与 Cursor Router

- **Cloud Agent 后续保持 1M 上下文。** 在 [GPT-5.6 Terra](https://cursor.com/docs/models/gpt-5-6-terra.md) 的 1M 上下文变体上启动的 Cloud Agent，现在在 Web 后续模型选择器里显示 1M，后续以 1M 运行，而不是 272K。

## Sep 14, 2026 当周

- **Origin 上更清楚的 Bugbot 检查名称。** 在 Origin pull request 上，Bugbot 的检查现在命名为 “Cursor / Cursor Bugbot” 和 “Cursor / Cursor Bugbot Autofix”。已经列出 Bugbot 的必需检查规则继续匹配。

## 3.20

*除非选择加入，Enterprise 团队从 3.21.9 起获得 Projects。*

### Agents

- **停止 Subagent 现在也停止其嵌套的 Subagent。** 在 Subagent 上点击 Stop，或 Stop all，现在也停止它启动的每个 Subagent，包括已被卸载的已完成或空闲的，以及仍在运行的后台 Subagent。这些 Subagent 正在等待的问题会被取消，父任务显示为已停止。
- **目标和后台任务现在保持你聊天的 Auto-review 设置。** 当目标自行继续，或后台任务唤醒 Agent 时，它现在使用与你在该聊天里输入的消息相同的 Auto-review 设置。
- **请 Agent 停止目标会关闭它。** 当你请 Agent 停止目标时，它现在可以把该目标标为完成，而不是让它保持活动。
- **已保存的 MAX 模型变体保持选中。** 在没有 MAX Mode 的聊天里挑选模型，不再重置你保存的仅 MAX 变体，例如 1M 上下文窗口或 Fast。这些已保存的选择现在只在 MAX Mode 实际关闭时重置。
- **多核 Linux 机器上不再有 CPU 采样停滞。** 在 Linux 上，Cursor 不再在多核机器上采样 CPU 用量时停滞。

### 聊天与 Composer

- **不离开父聊天就能回答 Subagent 的问题。** 当 Subagent 在等一个问题时，父聊天的问题托盘现在把它显示在 “Question from” 和该 Subagent 的名字之下，在 Subagent 行上点击 Answer 也会把它的问题带到那里，包括云 Subagent。你也可以把托盘收成显示计数并重新打开它的 Questions 药丸，问题里的文件和网页链接现在点击时会打开。
- **提示词上方的状态药丸更容易扫读。** 提示词上方的药丸现在先列出实时状态（待处理输入、Changes、Agents、Terminals、Canvas、Queue），然后是快速操作，托盘打开时状态药丸保持可见。Agent 的命令运行时 Terminals 药丸显示旋转指示，分支不匹配药丸现在读作 Checkout Branch，分支名在工具提示里。
- **用 Page Up、Page Down、Home 和 End 滚动聊天。** Page Up 和 Page Down 大约滚动一页，Home 和 End 跳到对话的顶部或底部。Agent 仍在回复时按 End，会让视图继续跟随新输出。
- **Agent 响应里更干净的链接和代码块。** Agent 响应里的长裸 URL 现在显示为站点的主机加上缩短的路径，因此它们不再横跨对话。标有文件扩展名的代码块，例如 Agent 为 `.rs` 文件使用的文件引用块，现在有语法高亮，而不是显示为纯文本。
- **提示词里更可靠的文件附件。** 你现在可以像 JSON 文件一样从 + 菜单附加 `.har` 文件。你添加的文档再次显示为文件提及，并在你发送之前于本地和云之间切换新聊天时保持附加。从 Cloud Agent 提示词移除文件的提及，现在会阻止该文件被上传。
- **录制语音输入时的实时麦克风计量。** 你听写时，语音工具栏现在在录制时间旁边显示紧凑的实时计量，因此你能看出麦克风在拾取你。背景噪声沉成点，只有你说话时条才会升起。
- **聊天里更清楚的图。** Agent 内联绘制的图现在带标题和可选的短副标题打开，与图表使用的相同标题。说明留在聊天回复里，而不是图内部，轮廓更深，因此形状一眼就能读出，图按聊天列调整大小，直到较窄的宽度。

### Projects

- **更简单的 Create Project 对话框。** Create Project 对话框有新布局：图标选择器、居中的名称字段，以及选择项目 Workspace 和 Model 的行。
- **SSH 和 WSL 多根工作区的 Edit Workspace。** 你现在可以对通过 SSH 或 WSL 运行、且工作区文件在你计算机上的多根工作区使用 Edit Workspace，来重命名它们并添加或移除文件夹。Add Folder 在该远程机器上打开文件夹浏览器。开发容器仍不提供 Edit Workspace。

### Cloud Agents

- **重试因错误停止的 Cloud Agent。** 当 Cloud Agent 以可重试的错误停止时，Agents Window 现在在记录稿里显示 Retry 按钮，恢复该运行而不发送新消息。笼统的失败显示 “This run hit an unexpected error. Retry or send a follow-up below.”，而不是原始错误，用新消息打断正在运行的 Cloud Agent 现在会显示像 “Changing course…” 这样的状态文本。
- **云环境配置的验证。** 编辑 `environment.json` 现在会验证并自动补全出站允许列表和模式、内联 Dockerfile 内容、基础镜像和测试字段。

### 文件、改动与 pull request

- **在 Changes 标签里切换仓库并筛选文件。** 当工作区有不止一个仓库或 Cursor worktree 时，Changes 标签标题里的选择器让你在它们之间切换，worktree 会被如此标记。本地改动文件树也获得搜索，以及显示或隐藏已删除和测试文件的选项，筛选生效时，暂存、取消暂存和丢弃全部只作用于你能看到的文件。
- **空的编辑器面板标签现在帮你开始。** 空的 Files 和 Browser 标签现在打开到可搜索的起始页，因此你可以不离开标签就找到文件或页面，Browser 起始页包含建议的近期项。空的 Changes 标签让你在分支有 pull request 时搜索它们，没有可显示内容的 Changes 标签现在会说该分支上的改动将出现在那里。
- **pull request 操作失败时的错误更清楚。** 当合并、创建、标为就绪、关闭或重新打开 pull request 失败时，错误提示现在会写出该操作并显示实际原因，如果你重试它会再次出现。

### 导航与布局

- **Remove from Sidebar 和 Archive All 现在只作用于你点击的组。** 移除侧边栏组，包括云或命名环境组，只隐藏该组，并把该仓库的其他组留在原处。组上的 Archive All 现在也归档尚未加载的云 Agent，Undo 会恢复它们。除非打开了减弱动态效果，侧边栏行在重新排序时也会动画。
- **更宽敞的窗口镶边和一致的图标。** Agents Window 标题栏、标签条，以及文件、diff、pull request 和终端面板标题更高，macOS 窗口控件旁边有更多空间。搜索面板、窗口控件、对话框和快速选择里的图标现在使用 Cursor 自己的图标集，在带标签和仅图标轨道之间切换时，应用轨道的 Collapse/Expand 按钮保持原位。

### 自定义与设置

- **更容易的 MCP 设置，以及更可靠的 MCP 登录。** 当几个 MCP 配置条目指向同一服务器时，MCP 视图现在把它们显示为一个组，每个条目有标签。使用 `${env:NAME}` 占位符的插件 MCP 配置现在会解析这些值。对管理员已经授予同意的租户里的 Microsoft 服务器，以及公司证书颁发机构后面的连接，登录也更可靠地工作。
- **OpenAI、Anthropic 和 Google 更清楚的 API 密钥设置。** 在 Settings、Models 里，每个提供方现在有自己带标签的 API Key 字段，密钥保存后出现的 Use API Key 开关，以及对 OpenAI 而言，带自己字段的 Base URL 覆盖开关。离开字段或按 Enter 时密钥会保存，你可以通过清空字段移除已保存的密钥。
- **MCP、插件、规则和 Hooks 设置在 Customize 里打开。** 打开 MCP、插件、规则或 Hooks 设置的命令和链接，包括 Automations 里的 MCP 链接，现在去到匹配的 Customize 页面。Customize 里的 MCP 服务器在需要登录、正在连接或已降级时显示黄色状态点。
- **安全修复。** 不受信任工作区里的终端在你拒绝或关闭信任提示后不再启动 shell，打开 `cursor://` 扩展链接在该链接被信任或你批准之前不再运行内置处理程序。派生的进程和远程终端不再继承 `NODE_OPTIONS` 和 `LD_PRELOAD` 这类注入变量，接收二进制命令结果或 webview 资源的扩展只得到所请求的字节，而不是共享的内部内存。
- **`/canvas` 用于你会回来的工作。** Agent 现在为你可能重访、改进或分享的报告、仪表板和数据量大的调查创建 Canvas。

### 修复

- **Cloud Agents 的修复。** Cloud Agent 现在在第一次点击时可靠打开，如果 Agent 还没准备好会短暂重试，并且不再请你信任每个 Agent 的工作区。
- **Subagents 和 Project worker 的修复。** 正在运行的 Project worker 在 Subagent 托盘里不再看起来已完成，worker 计数不再被夸大，已关闭的出错 worker 保持关闭。来自后台 Subagent 的批准提示会到达你，失败 worker 的启动提示词不再落到你的新 Agent 输入里。
- **New Agent 屏幕的修复。** 点击 New Agent 现在会可靠地显示新 Agent 输入。仍在加载的聊天、视图重新加载、工作区切换、平铺的对话或全屏编辑器不再用先前的聊天或空白窗格替换它。用 `cursor <folder>` 打开文件夹也总会开始一个新 Agent 草稿，而不是什么都不做。
- **聊天记录稿的修复。** 聊天里的图表和图不再保持空白，悬停 pull request 链接不再使聊天窗格变空白，分叉缺少消息内容的聊天现在可以工作，而不是失败。远程工作区上记录稿里的文件链接打开得更快，用户消息不再以错误的高度渲染。
- **提示词自动补全尊重 .cursorignore。** 提示词里的自动补全建议不再包含被 .cursorignore 排除的文件。
- **设置搜索的修复。** 设置搜索不再显示重复结果。

## 3.20

### Agent

- **Agent 默认可以使用浏览器。** 浏览器工具现在默认打开，因此 Agent 可以打开你的应用并检查它自己的 UI 改动，而不必你先打开[浏览器](https://cursor.com/docs/agent/tools/browser.md)。为团队关闭了浏览器功能的管理员会保持它们关闭。
- **停止 Subagent 现在也停止其嵌套的 Subagent。** [Subagent](https://cursor.com/docs/subagents.md) 上的 Stop，或 Stop all，现在也停止它启动的每个 Subagent，因此嵌套的工作不再在后台继续运行。来自已停止 Subagent 的待处理问题会被关闭。
- **用键盘翻阅 Agent 聊天。** Agent 聊天聚焦时，Page Up 和 Page Down 滚动一页，Home 和 End 跳到顶部或底部。回复流入时按 End 会继续跟随新输出。
- **听写时的实时麦克风计量。** 当你向 Agent 输入[听写](https://cursor.com/docs/agent/prompting.md#voice-input)时，录制指示现在是跟随麦克风电平的紧凑计量，因此你能看出 Cursor 听得到你。
- **聊天里的长链接不再占据对话。** 来自 Agent 的长原始 URL 现在显示完整主机名和缩短的路径，查询字符串和片段被折叠。点击链接仍打开完整地址。
- **修复了 HAR 文件不附加到 Agent 提示词。** 你挑选或拖进 Agent 提示词的 HAR 文件现在会附加，而不是被丢掉。

### 模型

- **Claude Fable 5.1 做出更小、更聚焦的编辑。** 在 [Claude Fable 5.1](https://cursor.com/docs/models/claude-fable-5-1.md) 上的 Agent 运行现在使用为该模型编写的指令。它写更平实的文字，更少请求许可，留在任务范围内，并做出更小、有针对性的编辑。
- **修复了已保存的仅 Max 模型变体重置。** 在没有 [Max Mode](https://cursor.com/docs/models-and-pricing.md#max-mode) 的聊天里挑选模型，不再重置已保存的仅 Max 变体，例如 1M 上下文窗口或 fast mode。

### MCP 与插件

- **修复了其 MCP 服务器需要你自己的值的插件的登录。** MCP 配置引用 `${env:NAME}` 值的[插件](https://cursor.com/docs/plugins.md)，现在在你安装或编辑该插件时为每个值显示一个字段，Cursor 填入你输入的内容。以前占位文本会原样送到服务器，这使某些市场插件的登录中断。
- **MCP、插件、规则和 Hooks 设置在 Customize 里打开。** 打开 MCP、插件、规则或 Hooks 设置的命令和链接现在去到匹配的 Customize 页面。Customize 里的 MCP 服务器在需要登录、正在连接或已降级时显示黄色状态点。
- **共享服务器的 MCP 条目为一组。** 当几个 [MCP](https://cursor.com/docs/mcp.md) 配置条目指向同一服务器时，MCP 列表把它们显示为一组，以这些条目共享的名称作为标题。如果你在组里选择超过一个条目，每个选中的条目得到自己的标签。

### Cloud Agents

- **修复了编辑 Cloud Agent 环境文件时的虚假错误。** 编辑 `.cursor/environment.json` 不再把有效字段标为错误。编辑器现在验证并自动补全 `egressAllowlist`、`egressMode`、`image`、内联 `build.dockerfileContents`、`chromeExecutablePath` 和 `enable_testing`。参见 [cloud agent setup](https://cursor.com/docs/cloud-agent/setup.md)。

### 编辑器与 Tab

- **签名帮助记住你挑选的重载。** 当你在 TypeScript 和 JavaScript 签名帮助里循环到不同的重载时，你继续输入参数时它保持选中。
- **Tab 自动导入适用于 Pyrefly。** 使用 Python 的 Pyrefly 语言服务器时，Tab 现在会提议导入 Pyrefly 报告为未定义的名称，就像它已经为 Pylance 和 basedpyright 做的那样。

### 修复

- **安全修复。** 不受信任工作区里的终端在显示信任错误后不再启动 shell，`cursor://` 扩展链接在你批准之前不再运行处理程序。Cursor 启动的进程，包括远程终端，不再继承可以注入代码的环境变量，例如 `NODE_OPTIONS` 和 `LD_PRELOAD`。JavaScript 和 TypeScript 语言功能现在在不受信任的工作区里关闭。
- **修复了 MCP 登录和连接。** 当你的租户管理员已经授予同意时，登录 Microsoft [MCP](https://cursor.com/docs/mcp.md) 服务器不再请求管理员批准，使用 OAuth 动态客户端注册的服务器不再以 `invalid_scope` 错误失败。在 Windows 上，企业证书颁发机构后面的 MCP 服务器，在证书位于 Intermediate 或机器范围存储时现在会连接。
- **修复了 Subagents。** 来自后台 [Subagents](https://cursor.com/docs/subagents.md) 的工具调用的批准提示现在会出现，而不是被自动拒绝，更早对话的 Subagent 卡片可靠加载。
- **修复了 Agent 聊天记录稿。** Restore Checkpoint 之后，被恢复的回合和后来的回合不再显示过期的页脚，Agent 输入里的自动补全建议跳过被 `.cursorignore` 排除的文件。
- **修复了编辑器稳定性和布局。** 你滚动或调整大小时，Webview 与其编辑器保持对齐。在 Linux 上，Cursor 在重负载下不再用尽文件描述符。内置 Browser 的 DevTools 再次加载，通过 `include` 或 `includeIf` 定义的 Git 远程会被检测。

## September 8, 2026 发行

- **打印你的身份验证 token。** 运行 `origin auth token` 打印 CLI 被配置使用的访问 token。
- **Git 用 `CURSOR_AUTH_TOKEN` 进行身份验证。** 当设置了 `CURSOR_AUTH_TOKEN` 时，通过 Origin 凭据助手的 git 操作会在已存储的凭据之前使用它，因此无头作业可以导出该 token，而不必运行 `origin auth login`。
- **过期 token 的清楚错误。** 如果 `CURSOR_AUTH_TOKEN` 已过期，CLI 会停止，并带该 token 必须刷新的消息，而不是发送它并以 401 失败。
- **控制 CloneKit 回退。** `origin repo clone-fast --fallback never` 以非零退出，而不是回退到 `git clone`，该命令现在报告是哪个阶段停止的。

## Sep 7, 2026 当周

### Web

- **堆叠的 pull request 作为一个 commit 合并，你的 commit 文本会落地。** 合并栈里的 pull request 现在把它连同它下面未合并的 pull request一起作为一个 commit 落地。当你合并单个 pull request 时，你在合并弹出框里编辑的 commit 标题和消息现在会按所写的原样落地。
- **暂停、恢复并测试应用 webhook 投递。** 你的应用设置页面现在有 Webhook Deliveries 分区。一旦应用有 webhook URL，该分区显示投递是活动还是已暂停，并有 Pause、Resume 和 Send Test Delivery 按钮。投递暂停时排队的事件会被丢掉，恢复时不会补发。Origin 也可能自行暂停投递，但只在接收方在 72 小时内每次投递都失败、且该时间内至少有 20 次失败投递之后。
- **代码库设置有侧边栏和可链接的分区。** 代码库设置现在使用侧边栏而不是标签，顶部有 Back to Codebase 链接。每个分区，例如 Permissions 和 Apps，有自己可以收藏或分享的 URL，更旧的设置链接重定向到正确的分区。
- **下载你仓库的副本。** 仓库设置现在在 Advanced 标签上有 Data 分区，带 Download repository 按钮，保存最新默认分支的 tarball。Origin API 客户端可以为任意 ref 请求同一归档；参见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **看出 commit 背后的 pull request。** 当 commit 作为 Origin pull request 的 head 被推送，或是 Origin 落地 pull request 时创建的合并 commit 时，其 commit 详情现在包含 References 分区，列出这些 pull request 的分支，并带指向每个分支及其 pull request 的链接。每个 pull request 显示它是打开、草稿、已合并还是已关闭，长列表折叠在 More 和 Less 后面。引用出现在托管于 Origin 的仓库上，不出现在从 GitHub 镜像的仓库上。
- **更容易扫读的 pull request 列表。** pull request 列表现在显示更大的、会换行而不是截断的标题，每个标题下有一行显示 pull request 编号、其作者，以及它是多久前打开的。行在悬停时高亮，并匹配 commits 表的样式。
- **pull request 改动里的图像预览。** pull request 的 Changes 标签里添加或更改的 PNG、JPEG、GIF 和 WebP 文件现在显示预览，而不是空的二进制文件卡片。透明图像位于棋盘背景上，因此浅色图标在浅色主题里、深色图标在深色主题里保持可见。
- **到 Origin 仓库的更安全推送。** Origin 现在拒绝删除仓库默认分支的 `git push`，错误为 “refusing to delete the current branch”，与 Git 和 GitHub 一致。其他分支仍可被删除。`refs/pull/` 和 `refs/changes/` 下的 pull request ref 在你推送时不再出现，也不能被推送覆盖。
- **pull request 基线必须是现有分支。** 创建或重新设定 pull request 的目标，现在会拒绝是 commit SHA、标签或不存在的分支的基线，并带基线必须命名现有分支的错误。以前这些基线会被接受，但使 pull request 无法为 CI 得到合并 ref，或无法被合并。
- **Merge when ready 不再卡在永远无法合并的 pull request 上。** 如果排队的合并因永久性原因失败，例如缺少权限或 pull request 不再存在，merge when ready 现在会自己关掉，而不是无限重试。合并冲突和已改变的 head 仍让它保持打开。
- **在 pull request 上重新运行检查。** 其应用允许重试的已完成检查，现在在 pull request 上显示重新运行按钮，你也可以用新的 Origin API 端点重新运行它们。检查读作待处理，如果它是必需的，在应用发布新结果之前仍阻止合并。应用收到新的 `repository.check_run.rerequested` webhook，检查运行现在包含 `rerequested_by`。
- **pull request 页面和登录的修复。** Cursor 会话检查的短暂问题不再把你从 pull request 页面登出，栈页面加载更快，对大型组织仍能打开。
- **仓库读取和合并错误的修复。** 长增量链仓库上的文件和 blame 读取不再失败。合并没有共同历史的分支，以及检查超过 200 个 pull request 的栈能否合并，现在返回清楚的错误，而不是内部服务器错误。
- **仓库页面和链接的修复。** Origin 的 git 主机上 GitHub URL 格式的 commit 链接（`/owner/repo/commit/<sha>`）现在重定向到仓库页面，而不是失败。当仓库的镜像状态改变时，Pinned 和 Recently Viewed 仓库卡片现在更新其来源图标，“Origin access is not enabled” 错误现在指向 cursor.com/codebase 以请求访问。

### API

- **从 API 管理 Origin 应用。** 你现在可以通过 Origin API 创建、列出、查看和更新你命名空间的应用，并添加或撤销它们用来身份验证的签名密钥，因此你可以编写应用设置脚本并轮换密钥，而不用 Web UI。应用响应也包含拥有的命名空间、描述、网站和默认范围。端点详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **从 Origin API 管理仓库镜像。** 你现在可以从 Origin API 更改镜像仓库的镜像状态，把它从上游来源分离以使其成为原生仓库，并轮询过渡作业直到完成。端点和所需范围见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **通过 API 合并时选择 merge 或 squash。** Origin API 的 Merge Pull Request 操作和 `merge_pull_request` MCP 工具接受可选的合并方法，`merge` 或 `squash`。省略它以使用仓库的默认值。如果你要求仓库不允许的方法，错误以其他代码托管使用的同一句话开头，因此现有合并工具能识别它。参见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **服务账户密钥可以读取仓库设置。** 服务账户的仓库范围团队 API 密钥现在可以通过 Origin API 读取仓库设置、规则集和标签，因此自动化可以审计规则集并比较设置，而不需要用户 token。更改设置仍需要用户。详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **Origin API webhook 里有更多详情。** webhook 载荷里的安装对象现在包含安装上次更新的时间，评论创建事件携带新线程的创建和更新时间，每个事件载荷现在都有样例文档。字段详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **Origin API 评论和 webhook 的修复。** 通过 Origin API 用超出文件末尾的行范围创建内联评论或审查，现在会以清楚的错误失败，写出该文件及其行数，而不是被接受。短暂的数据库中断打断投递时，webhook 事件不再被丢掉；它们会被重试。详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **从 Origin API 管理所有者和仓库访问。** 你现在可以通过 Origin API 列出、授予、更改和移除用户、组及团队组在所有者或仓库上的访问。每次授予显示其权限级别。端点和所需范围见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **从 Origin API 更新仓库设置。** 你现在可以通过 Origin API 更改仓库的默认分支、允许的合并方法、合并时自动删除分支，以及可见性。仓库响应也包含这些设置，因此你可以以编程方式读取和更新它们。端点详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。

## Sep 7, 2026 当周

### Agents

- **你接管其桌面时，Agent 会退开。** 当你接管 Cloud Agent 的桌面时，Agent 停止使用计算机，而不是与你争用，computer-use Subagent 一完成，桌面就会被释放。
- **你点名默认分支时启动更快。** 以明确点名的仓库默认分支启动的 Cloud Agents，就像桌面应用所做的那样，现在复用预构建的环境，与不点名分支的启动相同，因此它们更早到达第一句响应。

### Projects

- **Project 协调者和 worker 显示正确的状态。** Project 协调者现在只在它实际给你发了消息的回合之后被标为未读，它发给你的消息现在出现在 Agent 的对话历史里。已完成的 worker 和 Subagent 在新消息或重新提交的消息打断报告其完成的回合时，保持标为已完成。

### Pull request

- **来自镜像仓库的 pull request 在正确的地方打开。** 当仓库在 GitHub 和 Origin 之间镜像时，Cloud Agents 现在在作为事实来源的那一侧打开 pull request，并告诉你它们用了哪一个。如果 Agent 无法创建 pull request，它会报告原因，而不是用另一个工具绕过。镜像仓库的 pull request 链接现在指向 pull request 实际所在的 GitHub、GitHub Enterprise 或 Origin 主机，Agent 不再把 commit SHA 作为基线分支。
- **合并批准显示 Agent 将要落地的确切 commit。** 当 Cloud Agent 请求批准以合并 Origin 仓库上的 pull request 时，请求现在在移动批准面板和 Slack 里都显示 Agent 选择的 commit 主题和消息。你批准的是实际进入历史的 commit，而不只是 pull request 标题。

### 订阅

- **默认分支上的 CI 订阅不再在每次合并时唤醒 Agent。** 当 Cloud Agent 订阅仓库默认分支上的 CI，例如 `main` 时，它现在为下一个完成的 CI 结果唤醒一次，然后订阅关闭。要让 Agent 继续监视 pull request 的 CI，让它订阅该 PR 的分支。

### 环境

- **环境设置的修复。** 有很多环境的团队，环境列表加载更快。环境设置介绍对话框里的编号步骤现在均匀对齐。

### Self-Hosted Machines

- **自托管 worker 上 Cloud Agents 的修复。** 尝试附加到自托管 worker 上 Agent 的工作区，现在会立刻显示 “Workspace runs on a self-hosted worker”，而不是等待一个永远不会就绪的工作区。
- **自托管 worker 认领的修复。** 重连的自托管 worker 不再卡在过期认领上，因此它们可以再次被新 Agent 接走。认领拒绝不再显示为 “No self-hosted workers available”，从未收到消息的已认领 worker 现在会在空闲超时后被释放。

### Slack

- **为 Slack 设置团队默认 Self-Hosted 池。** 团队管理员可以运行 `@Cursor pool set <name>`，使 `@Cursor` 提及在该 Self-Hosted 池上运行，而不必每次加 `pool=`。如果该池服务于任意仓库，不点名仓库的提及仍可以在那里启动 Agent。`@Cursor pool` 和 `@Cursor settings` 显示当前默认值，`@Cursor pool unset` 清除它。
- **修复了到达 Agent 时没有线程的 Slack 后续。** 当你在 Cloud Agent 发布回复之前于 Slack 线程里提及 Cursor 时，Agent 现在收到整个线程，包括转发的消息和文件，而不只是你最新的消息。从后续对话框发送的后续得到相同的完整上下文。
- **在另一个团队的 Agent 线程里提及 @Cursor 现在会启动新 Agent。** 当你在 Agent 属于不同 Cursor 团队的 Slack 线程里提及 @Cursor 时，Cursor 现在会为你启动一个新的 Cloud Agent。以前它会拒绝，并建议 Team Followups，而那不能跨团队共享 Agent。如果后续仍被拒绝，消息现在会说该 Agent 属于另一个团队。
- **从 Slack 启动 Agent 的修复。** 当 Slack 启动失败时，回复现在会写出仓库和真实原因，例如没有访问、找不到仓库、源代码管理提供方已断开，或 GitHub 不可达，并为最后那种情况链接到 GitHub Status 和 Try Again 按钮。当更早的启动较慢时，在一个线程里多次提及 Cursor 不再启动重复的 Agent，你的 Slack 工作区迁移之后，你保存的默认仓库和分支现在仍会生效。

### Jira

- **来自 Jira 的更可靠的 Cloud Agents。** 从 Jira 启动的 Cloud Agents 继续使用最新的 Jira Agent 协议工作，并在运行时显示为工作中，而不是暂停。来自 Jira 的每个新请求现在开始自己的对话，活动任务上的后续消息不再失败，启动时停止 Agent 现在会在它启动后暂停它一次。

### 修复

- **Cloud Agent 可靠性的修复。** 停止 Agent 不再显示虚假的提供方错误，未跟踪文件挡路时后续不再无法恢复分支，environment.json 里未命名的终端现在会启动。后续里的插件斜杠命令现在会为尚未就绪的插件短暂等待，而不是跳过它。

### Web 应用

- **Agents Web 应用的修复。** 在 cursor.com 的 Agents 侧边栏里点击 Agent 不再弹回上一个 Agent，后退和前进会恢复你打开过的标签。从 Web 启动的新 Agent 现在在创建后显示其日志和对话，而不是缺少它们。

## Sep 7, 2026 当周

### 集成

#### GitHub

- **准确的 GitHub 连接错误。** 如果连接 GitHub 失败，回调页面现在会说明你是被限速、碰到了授权问题，还是缺少安装，并显示错误代码和请求 ID，而不是笼统的速率限制消息。
- **超过仓库上限时的清楚错误。** 当请求需要访问的仓库多于 GitHub 在一个安装 token 里允许的数量时，Cursor 现在会说明该上限，而不是请你重新安装 GitHub App。
- **团队市场从私有仓库刷新。** 插件市场刷新现在可以通过同一组织里另一个团队拥有的 GitHub App 安装进行身份验证，因此子团队上的市场会从私有仓库获取更新。

#### GitLab

- **把自托管 GitLab 连接到正确的团队。** 从 Integrations 页面连接自托管 GitLab 实例，现在会链接所选团队，其仓库在那里填充，而不是你的默认团队。
- **可靠的主机移除。** 删除 GitLab 主机现在会移除其全部仓库，即使其中一些的 webhook 清理失败。

#### Bitbucket

- **完整的 Bitbucket Cloud pull request 列表。** 列出已关闭或全部 Bitbucket Cloud pull request，现在包括已合并、已拒绝和已被取代的。
- **更快的 Data Center 注册错误。** 当主机不可达时，注册 Bitbucket Data Center 实例现在会在数秒内失败，而不是在重试中挂起。
- **Bitbucket Cloud pull request diff。** 当 Bitbucket 重定向请求时，来自 Bitbucket Cloud 的 pull request diff 现在会加载。

### 仪表板与管理

#### 管理

- &#x20;**从仪表板下载团队数据导出。** Enterprise 管理员现在有 Data exports 标签，列为其团队准备的导出，每个归档有下载链接。订阅结束后该标签仍可用，因此你仍可以取回团队的数据。在 Enterprise 方案上可用。
- &#x20;**关闭 Security Agents 或 Approval Agents 之前确认。** 在团队设置里关闭 Security Agents 或 Approval Agents 现在会打开确认对话框，你可以选择分享为什么要关掉它。
- &#x20;**从你配置的服务器构建 MCP 允许列表。** 在团队 MCP Configuration 里，MCP Allowlist 的 Add Rule 菜单让你输入自定义规则，或挑选你团队已配置的 MCP 服务器之一，每个标为 Allowed by default、Already allowed 或 Blocked by allowlist。对 URL MCP 服务器，Choose Tools 在需要时于你登录之后列出该服务器的工具，因此你可以挑选策略允许的工具，而不是输入它们的名字。
- &#x20;**确认对新 Cursor 模型的决定。** 在 Models 页面上，为处于 7 天审查窗口的新 Cursor 模型选择 Enable now 或 Keep disabled（以前是 Block）现在会请你确认。待处理行显示该模型将被自动启用的日期，不再带 New model 标签。

#### 分析

- &#x20;**重新设计的团队 Analytics 图表。** [Analytics 页面](https://cursor.com/docs/account/teams/analytics.md)上的 Active Users 图表现在把 CLI、Cloud Agents 和 Bugbot 活动堆叠起来，并带 All 线。每个图表共享一个调色板，其下有可点击的图例。悬停图表以下载其数据，或复制匹配的 API curl 命令。

#### 成员

- &#x20;**Team Admin 和 Org Admin 现在分开标记。** 成员列表、角色选择器、CSV 导出和组织成员详情页面现在把团队管理员显示为 Team Admin，把组织管理员显示为 Org Admin。
- &#x20;**Members > Set Cap 现在在更高上限仍适用时警告。** 如果你把成员的支出上限设得低于更高的组或团队上限，仪表板会告诉你改为降低该上限。以前保存会通过且没有消息，成员的上限保持不变。

#### SSO 与 SCIM

- &#x20;**SCIM 使团队所有的组和组织访问与你的身份提供方保持同步。** 当目录组映射到团队拥有的组时，[SCIM](https://cursor.com/docs/account/teams/scim.md) 现在把它的成员加到该团队，包括从中缺失的现有成员。

#### 修复

- **修复了自动化编辑器里的几个问题。** 当自动化以服务账户运行时，用户 MCP 服务器现在在工具选择器里变暗，并带工具提示。模型加载时模型选择器不再显示原始模型 ID，MCP 服务器行上的移除按钮始终可见。
- &#x20;**修复了更改错误成员身份的 SCIM 组映射。** 当目录组已经映射到别处时，映射不再把用户加到额外的团队，或重新填满根团队，也不再移除仍被另一个已映射组覆盖的成员。目录数据不完整时，完整同步现在会推迟移除。
- **修复了 Cloud Agent 环境的问题。** 无法创建团队环境的成员不再能以 Team 范围保存新环境。当环境构建因为仓库的 GitHub App 属于另一个团队而失败时，构建页面现在会显示说明这一点的警告。

### 自动化

- **Auto 档现在保存在自动化里。** 在自动化的模型选择器里为 Auto 选择 Cost、Balance 或 Intelligence 现在会正确保存，云运行以你挑选的档启动。以前每个档都会重新打开并作为 Balance 运行。
- **模型选择器不再闪出原始模型 ID。** 自动化提示词编辑器里的模型选择器会等待模型列表加载，而不是短暂显示内部模型 ID。
- **自动化保存的修复。** 在其主机使用主机级服务账户的自托管 GitLab 仓库上保存自动化，在设置 webhook 时不再失败。

### 安全与批准 Agent

- **GitLab 和 Bitbucket 审查。** Security Reviewer 现在审查 GitLab 合并请求和 Bitbucket pull request，在变更上发布摘要评论和状态检查。
- **运行历史里有更多运行。** Security Agents 运行历史现在包括在 Cursor 托管上运行的 Security Reviewer 运行。点击一个打开其会话视图。
- **关闭之前确认。** 为你的团队关闭 Security Agents 或 PR Routing & Approval 现在会请你确认，你可以选择说明原因。

### 模型与 Cursor Router

- **新模型：Muse Spark 1.3。** 来自 Meta 的 [Muse Spark 1.3](https://cursor.com/docs/models/muse-spark-1-3.md) 现在可在 Cursor 中使用。
- **Cursor Grok 4.6 直接看见图像。** [Cursor Grok 4.6](https://cursor.com/docs/models/grok-4-6.md) 现在把你附加的图像和工具返回的图像作为图像接收，而不是根据对它们的文本描述工作。

## Sep 7, 2026 当周

- **大型生成文件不再阻止审查。** 在 `.gitattributes` 里标为 `linguist-generated` 的文件，例如锁文件，即使大于 2 MB 也会被排除在 Bugbot 的大小检查之外。Bugbot 审查 pull request 的其余部分，而不是因为它太大而跳过。
- **Bugbot 仪表板对话框用 Escape 关闭。** Bugbot 仪表板里的禁用、批量仓库操作和取消对话框现在在你按 Escape 时关闭。
- **Azure DevOps 修复。** 当 pull request 摘要会超过 Azure DevOps 的 4,000 字符描述限制时，Bugbot 把完整摘要作为评论发布，并在更短的描述里说明这一点。租户检查没有点名租户的组织现在可以启用 Bugbot，而不是租户发现失败。当 Azure DevOps pull request 事件包含其 head commit 时，Bugbot 排队审查而不先从 Azure DevOps 读取该 pull request，因此短暂的 Azure DevOps 或 Entra 中断不再使该事件失败。

## September 4, 2026 发行

- **用 CloneKit 更快地克隆 CI。** 对打开了 CloneKit 的仓库，`origin repo clone-fast` 下载预构建的克隆工具包，验证它，获取更新的对象，检出默认分支，并打印每个阶段花了多久。
- **更可靠的大型克隆。** `origin repo clone` 提高 git 的 HTTP post 缓冲区，因此大型获取不再在连接掉线后以 “unable to rewind rpc post data” 失败。

## 1.0.31

- **后台 Subagent 会回报。** 当 Agent 在后台运行 Subagent 时，其结果现在作为同一次运行上的后续回合返回给父级，而不是在父回合结束时被丢掉。`run.stream()` 在这些回合期间继续产出，`run.wait()` 在它们之后解析。本地 Agent，TypeScript 和 Python。
- **批注自定义工具。** `local.customTools` 条目上的 `annotations` 把 MCP 工具批注（`title`、`readOnlyHint`、`destructiveHint`、`idempotentHint`、`openWorldHint`）传给模型。它们只是描述性提示；SDK 不强制它们。仅 TypeScript。

## 3.19

### Agents

- **停止 Subagent 现在停止其嵌套的 Subagent。** 从 Agents Window 停止 Subagent 现在也停止它启动的每个 Subagent，包括你没有打开过的。以前只有选中的 Subagent 停止，而其子级继续运行。父任务也被标为已中断。
- **Subagent 显示你要求的模型。** Subagent 卡片现在显示其任务所请求的模型和 Subagent 类型。使用模型名更旧拼写的任务调用现在在匹配的模型上运行，而不是失败。
- **用 Vercel 部署时的错误更清楚。** 当 deploy-with-vercel Skill 碰到 Vercel 错误时，例如项目已存在、Vercel 应用缺少对你仓库的访问、Hobby 方案限制，或受保护的部署，Agent 现在会转述实际错误以及下一步做什么。如果 Vercel 应用无法访问你的仓库，Agent 会链接已经填入该仓库的应用安装页面。

### 聊天与 Composer

- **把 Agent 回复里的表格复制为 Markdown。** 右键 Agent 回复里的表格并选择 Copy Table，把它复制为 Markdown，准备粘贴到文档、issue 或另一个聊天。同一菜单也提供 Copy Message 来复制整个回复。
- **`/summarize` 在 Cloud Agent 上工作。** 在 Cloud Agent 上运行 `/summarize`（或输入 `/compact`）现在在 Cloud Agent 本身上摘要该对话，腾出上下文而不离开聊天。如果 Agent 仍在工作，你会看到请你等待的通知，对从云移到本地的 Agent，`/summarize` 不再错误地拒绝。
- **折叠 Questions 托盘并回到它。** 当 Agent 问你问题时，你现在可以把 Questions 托盘收成 Agent 标题里的一颗药丸，显示有多少问题在等待，然后点击它重新打开托盘并回答。托盘也有更大的问题文本，步骤导航移到了页脚，只有一个问题时其标题说 “Question”。
- **重新提交更早的消息时的选择更清楚。** 当你在文件已经更改之后编辑并重新提交更早的消息时，对话框现在问 “Revert files to this message?”，并说明无论哪种方式后来的消息都会被移除。选择 Revert Files 把文件恢复到该点，或选择 Keep Files 重新提交而不碰你的改动。
- **你继续说话时，听写文本会稳定下来。** 语音听写期间，语音提供方已经定稿的文本不再在你停止说话之前显示为待处理。

### Cloud Agents

- **查看并管理你的 Cloud Agent 在监听什么。** 当你的 Cloud Agent 有活动订阅时，聊天输入上方会出现带计数的 **Listening** 药丸。点击它查看 Agent 正在等待的每个事件或日程，以及它上次触发或被设置的时间，移除你不再需要的，或打开完整的 Subscriptions 标签。
- **从 Run on 更快地挑选团队池和远程机器。** 对要求自托管 worker 的团队，Run on 菜单现在直接列出有空闲机器的近期活跃池，不再显示 Cloud 选项。在 Remote Machines 下，有空闲机器的池被首先建议，并显示每个池的仓库，用 Remote Control 把本地 Agent 交给远程机器时，始终使用你挑选的确切机器，即使有几台同名。
- **重连远程工作区而不重新加载窗口。** 当 Agents Window 无法恢复到远程工作区或 Cloud Agent 的连接时，它现在显示带 Reconnect 按钮的通知，保持打开直到你对它采取行动，因此你不必再重新加载窗口。如果手动重连失败，通知会说工作区仍未连接，并提供 Try again。Cloud Agent 连接也在大约三分钟后停止自动重试，而不是永远重试。
- **更多 Cloud Agent 的 Desktop 和 Environment 标签。** 在你自己的机器或私有 worker 上运行的 Cloud Agent，一旦 worker 已连接并在共享其桌面，现在就会显示 Desktop 标签，而不必重新选择该 Agent。如果你关闭 Environment 标签，可以从新标签菜单或折叠的应用轨道重新打开它，该标签里的密钥和构建现在适用于 Agent 启动所依据的已保存环境。

### 文件、改动与 pull request

- **从 Finder 或 Explorer 把文件粘贴到 Files 树。** 在 Finder 或 File Explorer 里复制文件，点进 Files 树，按 Cmd/Ctrl+V 把它们加到所选文件夹或当前文件的文件夹。Files 树里的复制和剪切现在使用系统剪贴板，Paste 始终出现在上下文菜单里。
- **就在你需要的地方创建文件和文件夹。** 在 Files 面板里，悬停文件夹以显示在该文件夹内创建项目的 New File 和 New Folder 按钮。文件夹也显示打开和关闭的文件夹图标，展开箭头在悬停时出现。
- **从 GitHub 合并队列移除 pull request。** 当 pull request 在 GitHub 合并队列里时，PR 标题现在显示 Remove From Queue 按钮，而不是禁用的 In Merge Queue 标签。确认之后，PR 离开队列；稍后加回去会把它放在末尾。
- **更快的 pull request 标签操作。** pull request 标签菜单里的 Close PR 现在立刻关闭 PR，而没有确认对话框，PR 标题旁边的复制和编辑按钮始终可见。栈导航会自行刷新，并在打开时刷新。
- **用一个快捷键折叠或展开 diff 里的每个文件。** 在 Diff、Agent Changes 或 pull request 标签里，macOS 上按 `Ctrl+Shift+;`，Windows 和 Linux 上按 `Shift+Alt+;`，折叠每个文件卡片，再按一次展开它们。标签的菜单现在在 Collapse All 和 Expand All 旁边显示该快捷键。

### 导航与布局

- **从 macOS Dock 跳到未读 Agent。** 在 macOS 上，使用 Agents Window 之后右键 Cursor Dock 图标，现在会显示列出有新结果的 Agent 的 Unread 分区，点击一个会打开它。仍在运行的 Agent 不再算作未读。
- **从退出对话框跳到正在运行的 Agent。** 当你在 Agent 仍在工作时退出或重启，点击确认对话框里列出的任意 Agent，即可取消退出并打开该 Agent，以便你查看它。
- **更精确的 Remove from Sidebar。** Remove from Sidebar 现在恰好隐藏你点击的组，例如命名的云环境、仓库的 Cloud Agent，或整个仓库，在那里创建 Agent 会把该组带回来。
- **命令面板键盘导航。** 在 Cmd+K 面板里，Tab 再次在搜索框和筛选芯片之间移动焦点；用 Cmd+\[ 和 Cmd+]（Windows 和 Linux 上为 Ctrl+\[ 和 Ctrl+]）更改筛选。

### 自定义与设置

- **Cloud Agent 更可靠的 MCP 登录。** 在桌面上为 Cloud Agent 登录 MCP 服务器，现在通过回到 Cursor 的本地重定向完成，因此拒绝自定义应用链接的服务器可以工作。
- **Customize 里更清楚的插件详情。** 对你已安装的插件，Customize 里的插件页面现在显示插件自己的版本，以及当插件列出时指向其主页和仓库的链接。如果你的 Cursor 版本无法安装某个插件，其页面把安装显示为不可用。
- **更清楚的自动化状态和设置。** 如果你无法打开或关闭某个自动化，其详情和 Run History 页面现在显示普通的 Active 或 Inactive 状态，而不是变灰的开关。打开自动化现在会把其已保存的环境加载进表单，Microsoft Teams 频道选择器使用复选框，并把你选中的频道列在顶部。
- **Index New Folders 已从设置中消失。** 设置不再显示 Index New Folders 开关，网络诊断不再运行 Codebase Indexing 检查。

### 修复

- **修复了 Windows 公司网络上的证书错误。** 在 Windows 上，MCP 服务器（包括 MCP 登录）和其他网络请求现在信任来自 Intermediate 存储，以及 IT 通过组策略部署的机器范围和企业 CA 存储的证书。这修复了你通过公司代理连接或连接到内部 MCP 服务器时 `UNABLE_TO_VERIFY_LEAF_SIGNATURE` 这类错误。
- **Subagent Task 卡片和状态的修复。** Subagent 的 Task 卡片稳定为已停止或已完成，而不是永远旋转，Subagent 托盘和 Agent 列表显示准确的状态，没有已完成 Subagent 的重复或滞留行。归档其云 Subagent 已完成的 Agent 不再请求确认。在第一次响应之前失败的云 Subagent 不再把其启动提示词留在 New Chat 输入里。
- **Subagent 托盘的修复。** Subagent 行也只在 Subagent 运行在与其父级不同的地方时显示环境图标，自托管 worker 用桌面图标。
- **引导消息的修复。** Cloud Agent 无法在运行中途接受的引导消息，现在作为可编辑的后续移到队列，而不是看起来已发送，引导不再出现两次、固定在聊天顶部，或在队列里留下游离行。
- **后续的修复。** 你在刚创建的 Cloud Agent 上开始输入的后续，在你切换离开再回来时不再丢失。
- **在 Cloud Agent 里编辑更早的消息会清除过期的后续。** 在 Cloud Agent 里编辑更早的消息会清除过期的排队后续，并使用你挑选的模型。
- **Agent 问题的修复。** 在 Agent 提问时输入后续，现在会立刻关闭问题托盘并把该问题标为已跳过，来自已完成运行的问题不再卡在待处理。
- **MCP 服务器和 Hooks 的修复。** 连接到使用 OAuth 动态客户端注册的 MCP 服务器不再以无效范围错误失败，到 Stripe Link MCP 服务器的 OAuth 登录现在会完成。`beforeMCPExecution` Hooks 收到服务器的命令或 URL，而不是空值。
- **本地 Agent 运行的修复。** 文件和符号建议尊重 `.cursorignore`，目标继续和后台唤醒遵循聊天的 Run Mode。
- **性能和稳定性修复。** 来自已关闭工作区的通知不再滞留在托盘里，Cursor 在 Linux 上不再用尽文件描述符。用 /fork 分叉或复制长本地聊天也更快，因为其消息被原样复制，而不是重写。
- **聊天和提示词的修复。** 带损坏的已保存 Canvas 的聊天不再使 Agent 面板崩溃，HEIC/HEIF 图像正确附加。分享过大的聊天现在给出清楚的错误，Report Bug 会显示它正在发送或已经失败。
- **Agents Window 面板、标签和菜单的修复。** 磁盘上的文件改变时，Markdown Preview 和 Plans 标签现在会刷新。浏览器、图像和 PDF 标签里的浮动工具栏不再藏在全屏 Composer 后面，顶栏按钮可靠响应点击，即使工作区仍在加载 Changes 也会打开，设置搜索不再让窗格被裁切。
- **Customize 里 Skills 的修复。** Customize 的 User 分区里的 Skills 不再在从仪表板加载团队规则之前等待出现。

## 3.19

### Agent

- **语音听写会自动检测你所说的语言。** Agent 输入里的听写识别你所说的语言，而不是假定美式英语。录音时显示的语音设置菜单里，Auto-Detect 是新的默认值，你仍可以在那里挑选特定语言。参见 [语音输入](https://cursor.com/docs/agent/prompting.md#voice-input)。
- **`/summarize` 在 Cloud Agent 聊天里可用。** 在 Cloud Agent 对话上运行 `/summarize` 会在 Cloud Agent 上摘要该对话，而不是什么都不做。在聊天输入里输入 `/summarize` 或 `/compact` 也会触发摘要，如果 Agent 仍在工作，你会得到一条通知。
- **把 Agent 回复里的表格复制为 Markdown。** 在 Agent 回复里的表格上右键，选择 Copy Table 将其复制为 Markdown，或选择 Copy Message 复制整条回复。
- **重新提交更早的消息时，提示更清楚。** 当你编辑并重新提交更早的消息，且你的文件自那一点以来已经改变时，Cursor 会询问 “Revert files to this message?”，并说明无论哪种选择，更晚的消息都会被移除。选择 Revert Files (Enter) 把文件恢复到那一点，或选择 Keep Files (Shift+Enter) 保留你当前的改动。在 Ask Mode 中，重新提交不会改动你的文件，也不会询问。参见 [Checkpoints](https://cursor.com/docs/agent/overview.md#checkpoints)。
- **长聊天上的 Fork Chat 更快。** 在长本地聊天上使用 Fork Chat 更快，因为其消息被原样复制，而不是重写。

### Subagents

- **从折叠卡片批准 Subagent 的文件编辑和 MCP 工具调用。** 折叠的 [Subagent](https://cursor.com/docs/subagents.md) 卡片对待处理的文件编辑和 MCP 工具调用显示 Allow，而不只是终端命令，因此你可以不展开 Subagent 就批准。
- **停止一个 Subagent 也会停止其嵌套的 Subagent。** 当你停止一个 Subagent 时，它启动的每个 Subagent 也会停止，包括你从未打开的那些，因此没有后台工作继续运行。父任务被标为已中断。

### MCP 与插件

- **云 MCP 服务器的登录更可靠。** 登录云 MCP 服务器通过本地浏览器重定向完成，修复了以无效重定向错误拒绝登录的服务器，并给你最多 15 分钟完成同意。在 Customize 里，当你回到 Cursor 时，服务器的身份验证状态会自行更新，移除云账户会请你确认。参见 [MCP 身份验证](https://cursor.com/docs/mcp.md#authentication)。
- **插件页面显示更完整的详情。** 你的 Cursor 版本无法安装的插件会显示禁用的 Install 按钮，并带清楚的消息。Customize 里的插件页面显示作者声明的版本、主页和仓库链接。

### 远程开发

- **重新连接远程工作区时不必重新加载窗口。** 当 Cursor 无法重新连接到远程工作区时，它会显示一条持久的 “Unable to reconnect to the remote workspace” 通知，带 Reconnect Now 按钮，而不是请你重新加载窗口的对话框。正在重新连接的进度通知不再提供 Reload Window，因此重试时你保持窗口状态。

### 修复

- &#x20;**修复了 Windows 公司网络上的证书错误。** 在 Windows 上，MCP 服务器、MCP 登录和扩展信任来自 Intermediate 存储，以及机器范围、组策略和企业 CA 存储的证书。当 IT 把证书部署到这些存储时，这修复了 `UNABLE_TO_VERIFY_LEAF_SIGNATURE` 这类 TLS 错误。
- **Subagent 的修复。** 需要批准的 Subagent 内部工具调用会显示批准卡片，而不是停滞。在超过 100 个云 Subagent 的运行里，它们不再从 Agent 列表中消失。
- **排队消息和引导消息的修复。** 排队消息保持它们入队时的模式，当 Agent 有未回答的问题时不再被发送两次。编辑并重新提交 Cloud Agent 消息会使用你挑选的模型，启动 Cloud Agent 之后的第一条后续不再丢失。
- **Cloud Agent 状态和流式输出的修复。** Cloud Agent 不再卡在过期状态上。Agent 变为空闲并恢复之后，对话会继续流式输出新内容，Cursor 空闲之后的第一条 Cloud Agent 后续不再以 “Canceled” 错误失败。
- **MCP 登录的修复。** 连接到使用 OAuth 动态客户端注册的 MCP 服务器不再以无效范围错误失败，登录 Stripe Link MCP 服务器不再以无效重定向错误失败。缓存的登录链接缺失时，Authenticate 按钮会获取一条新的登录链接。
- **Agent 聊天记录稿的修复。** 回合结束后，聊天不再卡在生成中，因此排队消息会发出。分享过大的聊天会显示清楚的错误。
- **Agent 提示词输入的修复。** 你挑选模型或关闭模型选择器之后，焦点回到提示词。附加 HEIC 和 HEIF 图片再次可用，提示词里的自动补全不再建议被 `.cursorignore` 排除的文件。
- **Linux 上的性能修复。** 在多核机器上采样 CPU 用量时，Cursor 不再周期性暂停，一阵内部消息也不再耗尽窗口的文件描述符。
- **Agent 工具和 Hooks 的修复。** 当 Agent 等待终端命令时，命令一结束，或其输出匹配它正在等待的内容，它就会停止，而不是总是耗尽完整超时。列出子文件夹时，Agent 的目录列表遵循父文件夹的 `.cursorignore` 模式。`beforeMCPExecution` Hooks 得到 MCP 服务器的命令或 URL，而不是空值。
- **编辑器菜单和对话框的修复。** 你手动打开的 Quick Fix 和重构菜单，不再在灯泡于后台刷新时变空白或无法运行。在简单文件选择器里，输入以 `/` 结尾的文件夹路径会打开该文件夹。隐藏的对话框不再捕获键盘输入，因此打字和快捷键继续可用。
- **Skills 和 Agent Store 链接的修复。** Customize 视图里的 Skills 不再等到团队规则加载完成才出现。在 Cloud Agent 聊天里点击内置 Skill 会打开其 SKILL.md 文件，而不是空标签，在聊天里点击 Agent Store 文件引用会打开最新版本，而不是过期副本。
- **方案识别的修复。** Pro Student 订阅者被识别为付费用户，因此他们得到正确的模型访问和用量横幅，而不是 Free 方案限制。

## Aug 31, 2026 当周

### Web

- **让特定的人和应用绕过规则集。** 在仓库的 **Settings > Rules and Protections** 里，规则集对话框现在有 **Bypass list**。你最多可以添加 15 个有仓库访问权限的人或已安装的应用，并把每一个设为 **Always** 或 **Pull requests only**。绕过参与者可以把 pull request 合并进规则集原本会阻止的 ref，例如带有 block-merges 规则时。
- **应用创建者可以管理他们创建的应用。** 当你创建 Origin 应用时，你现在成为它的管理员，因此你可以查看并更新其设置、签名密钥和图标，而不必是命名空间管理员。命名空间和团队管理员仍可以管理该应用，应用设置的权限检查现在与操作本身给出相同的答案。
- **更清楚的应用权限更新。** 当你更新已安装应用的权限时，审查页面现在列出新权限、将被撤销的权限，以及你已经授予的权限的可折叠列表。请求写权限的应用现在也会得到匹配的读权限，因此它们可以读取被允许更改的数据。
- **更简单的应用页面和更清楚的发布者详情。** 创建应用不再要求 slug，代码库设置下的应用页面现在使用应用的 ID，因此链接保持稳定。应用详情页面显示谁创建了它以及谁最后更新了它。在仓库的 Apps 设置标签里点击已安装的应用会打开其安装详情，策略对话框在自己的分区里列出应用范围。
- **仓库访问变化时的电子邮件通知。** 当你被授予或失去仓库或命名空间的访问权限时，包括通过组或团队成员身份带来的变化，Origin 现在会给你发电子邮件。你自己做出的变更不会收到邮件，用 Agent 开始新项目时创建的临时草稿仓库也不会。
- **更清楚的仓库访问控制。** 代码库和仓库 Permissions 标签上的 Add People 按钮和对话框现在叫 Grant Access，已经有访问权限的人或组会显示其当前访问级别，包括自定义策略，而不是 “Already added.”。仓库设置现在把 Private 可见性描述为 “Only users with direct access and codebase admins.”。
- **在个人仓库上请求审查。** 在个人账户拥有的仓库上，pull request 审查者选择器现在列出所有者和你已授予仓库访问权限的人，你可以请求他们审查。以前个人仓库的选择器不显示任何人。
- **在你自己的 pull request 上评论更简单，回复留在待处理审查里。** 在你撰写的 pull request 上，审查按钮现在写着 Comment，并打开只含评论的 Composer，没有批准或请求更改选项。对审查线程的回复现在加入你的待处理审查，并在你提交之前保持私有。
- **pull request 页面上可读的作者筛选。** 当你在仓库的 pull request 页面上从筛选下拉菜单挑选作者时，搜索框和 URL 现在显示这个人的名字，例如 `author:"Ada Lovelace"`，而不是不可读的 ID。使用名字的分享链接仍会解析到正确的人。
- **审计日志现在显示是谁推送到了仓库。** 你团队审计日志里的仓库推送条目现在记录推送用户的邮箱，而不是 “unknown”。由应用或服务账户做出的推送改为显示该账户。审查作者和 CI 检查参与者现在显示为公开 ID（`user_…`、`app_…`、`sa_…`），而不是较旧的内部 ID 格式。
- **合并和重新堆叠 pull request 的修复。** Squash 合并不再在 pull request 仍打开时落到基线分支上，栈合并不再在合并已经落地后以孤立 commit 错误失败。基线分支在同一时刻移动时，合并和重新堆叠不再失败，当 pull request 记录的基线偏离其分支时，重新堆叠仍然可用。镜像到 GitHub 的仓库里的合并不再等待 GitHub 推送。
- **pull request 页面的修复。** Code Tours 里由 Agent 生成的图片正确加载，由 Cursor Agent 签名的 commit 显示 Cursor Agent 的名字和头像。堆叠 PR 上的 Code Tours 在基线改变后不再显示过期的导览。
- **克隆、获取和推送可靠性的修复。** 大型或镜像仓库的克隆和获取不再在服务器准备包时以 504 失败或中断，仓库恢复期间也不再短暂丢失分支或标签。镜像被关闭时，推送不再打印虚假的镜像警告，当仓库元数据无法读取时，pull request diff、可合并性检查和 blame 不再返回服务器错误。

### API

- **通过 Origin API 提交文件并创建分支。** 你现在可以通过 Origin API 把文件变更直接提交到现有分支，并在给定 commit 上创建新分支，而不需要本地克隆。提交可以要求分支仍指向预期的 commit，因此并发写入不会互相覆盖。Origin Go SDK 两者都支持。详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **应用现在在 Origin API、SDK 和 webhook 里以 ID 和显示名称标识。** 应用对象、应用参与者和 webhook 应用载荷，包括测试 ping，不再包含 `slug` 字段。检查运行组不再返回已弃用的 `app_name`，因此改读 `actor.display_name`。如果你的集成以应用 slug 为键，请改用应用 `id`。受影响的字段见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **新的 webhook 事件，以及失败投递更快恢复。** 应用可以订阅新的 `repository.metadata.updated` webhook，它在仓库的默认分支改变时触发，并包含完整仓库。已安装的应用现在也会在命名空间被重命名时收到 `installation.updated` 事件，带新的命名空间和仓库坐标。失败的投递首先在 5 秒后重试，因此短暂的网络闪断会更快恢复。详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **从 API 筛选 pull request 评论，并对整个文件评论。** 你现在可以从 Origin API 和 Go SDK 列出某个创建时间窗口内的 pull request 评论，或只列出来自特定线程的评论，并在整个已更改文件上而不是单行上打开评论线程。详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **Origin API 里更精确的检查运行和 pull request 列表。** 你现在可以按检查名称和状态（queued、in progress 或 completed）筛选某个 commit 的检查运行，针对 commit 或套件的检查运行列表现在只返回每次运行的最新尝试，而不是已被取代的重试。按作者筛选 pull request 现在也接受用户的邮箱地址，匹配不区分大小写。详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **API 响应和 webhook 现在更能说明是谁做了什么。** Origin API 响应和 webhook 里的用户参与者现在包含显示名称，对有公开资料的用户还包含其 handle。应用参与者包含应用注册的显示名称，被请求的审查者包含其邮箱。REST 响应也会返回持有默认值的字段，例如 `draft: false`，而不是把它们省掉。字段详情见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。
- **Origin API 规范现在列出每个端点需要的凭据和范围。** Origin API 的 OpenAPI 规范里的每个操作现在说明哪些凭据类型可以调用它（app、installation 或 user），以及它需要哪些范围。不需要额外范围的操作，例如应用和安装元数据以及 webhook 投递，被标为仅身份验证。额外的仓库 tarball 和 matching-refs 路由上重复的操作 ID 已修复，因此客户端生成器不再在该规范上失败。参见 [Origin API changelog](https://cursor.com/docs/api/origin/changelog.md)。

## Aug 31, 2026 当周

### Agent 运行

- **来自订阅的无谓唤醒更少。** 订阅了 GitHub CI 或 pull request 事件的 Cloud Agent，不再因重复的通过 CI 结果、它们自己的推送和 PR 编辑，或标签、标题和受理人变更而唤醒。失败，或失败之后的第一次变绿，仍会唤醒 Agent。当事件不需要行动时，Agent 现在安静地结束回合，而不是在 Slack 或 PR 上发布填充回复。
- **插件斜杠命令在后续里可用。** 你现在可以在给 Cloud Agent 的后续里使用插件斜杠命令，即使该插件在 Agent 启动时没有被选中：Agent 会在运行该回合之前安装插件。如果插件无法安装，Agent 会说明这一点，并请你移除该命令后再次发送，或启动一个新 Agent。
- **协调者消息更快到达忙碌的 worker。** 在 Projects 里，协调者发给正处于回合中的 worker 的消息，现在默认投递进该回合，而不是等待回合结束。没有在运行的 worker 会把它作为下一条消息收到。

### 环境

- **更清楚的环境设置 Agent。** 你第一次在 Web 上打开环境设置 Agent 时，一个带视频的简短对话框会说明 Agent 将做什么：安装并验证你的应用，询问密钥或网络访问，并提示你审查并保存配置。设置 Agent 现在以仓库命名，例如 “Set up my-repo environment”，当链接的环境没有配置时，记录稿不再显示环境行。
- **用 OIDC token 把云角色固定到单个环境。** Agent 环境内的身份 token 端点现在接受 `sub_claim: "environment_id"`，它把 Agent 的环境 ID 放进 token 的 `sub` 声明，因此只匹配 `sub` 的验证方，例如 AWS STS，可以信任一个环境。如果 Agent 没有环境，签发会失败，而不是回退到默认主体。
- **修复了网络访问受限的 Cloud Agent 里 Origin CLI 命令失败的问题。** 在 Cursor 上托管的仓库上工作、且网络出站受限的 Cloud Agent，现在会在 Git 主机之外自动允许 Origin API 主机，因此 `origin` CLI 和 API 命令能够连接，而不是失败。其他仓库和你的自定义允许列表不变。

### Self-Hosted Machines

- **修复了 Linear 里的 `pool` 标签，使运行前往你的 Self-Hosted Machines 池。** 父标签为 `pool` 的 Linear issue 或项目标签，现在会把 Cloud Agent 运行送到该名称的池，与文档所述一致。当你把 issue 分配或委托给 Cursor、而没有评论可读时，这也有效。以前这些运行会悄悄使用 Cursor 托管的机器。在 `@Cursor` 评论里写 `pool=<name>` 仍会覆盖该标签。
- **Agent 可以找到你的 Self-Hosted Machines。** 当你让 Cloud Agent 在自托管机器上运行工作时，它现在可以列出你有权访问的已连接机器并挑选一台。以前 Agent 可能以工具未找到错误失败，看起来像你的机器不见了。

### 集成

- **Cursor 托管的 pull request 链接在 Slack 里有更好的预览。** 来自 Cursor 托管仓库的 pull request 链接现在在 Slack 里展开，带描述的格式化摘录，并保持简短，以便预览在 Slack 移动端保持紧凑。指向 pull request 标签的链接，例如其改动或 commit，现在也会展开。

### 通知

- **来自 Cloud Agent 的吵闹推送通知更少。** Cloud Agent 不再把回合完成通知推送到你的手机或浏览器。当 Agent 提问或需要你批准时，你仍会收到推送通知。

### 性能

- **更快的环境加载。** 对有许多服务器的用户，MCP 服务器配置现在并行加载。环境列表加载也更快，对有许多仓库的大型团队不再超时。

### 修复

- **环境和仓库设置的修复。** 用已删除环境的名称保存环境现在会创建一个新的，用未知环境名称启动 Agent 会复用该仓库的现有环境。多仓库 Agent 不再因仓库列表里缺失的 GitLab 仓库而失败。仓库检出现在会在临时代理错误时重试，使用 Customer PrivateLink 的团队不再在环境构建期间看到克隆失败，或在运行中途失去 git 访问。
- **自托管 worker 的修复。** 当有空闲 worker 可用时，自托管 worker 上的 Cloud Agent 不再在更早的失败尝试之后无法启动或恢复，也不再以屏幕录制错误失败。插件已经安装在 worker 上时，插件斜杠命令和 Skills 现在会出现。在设置了仓库的 worker 上，协调者和 worker 通知以及 PR 操作也再次可用。
- **MCP 身份验证以及管理其他 Agent 的 Agent 的修复。** Cloud Agent 不再停下来请你对已经连接的 MCP 服务器进行身份验证，在 MCP 身份验证期间停止 Agent 现在会关闭 Authenticate 卡片，而不是让它保持待处理。当 Agent 启动或向其他 Agent 发消息时，启动卡片现在在新 Agent 的第一回合之后完成，被移除的排队消息以已取消结束，而不是永远等待。当 Subagent 因为其模型的包含用量已用完而无法启动时，错误现在会点名该模型。在 iOS 应用 1.9.0 及更高版本上，需要 MCP 登录的 Cloud Agent 现在显示登录卡片，而不是回退消息。
- **Cloud Agent 运行的修复。** 从模型注册表中的模型启动的 Cloud Agent 现在以你在模型选择器里挑选的 effort 和参数运行，而不是模型的默认值。Cloud Agent 运行和集成启动现在使用你账户当前的 Privacy Mode，而不是过期的设置。你发给正在运行的 Agent 的消息现在按你发送的顺序到达，即使更早的消息错过了 Agent 的当前步骤并等待下一回合。

### Web 应用

- **手机上更干净的 Cloud Agents 布局。** 在移动端，cursor.com 上的 Agent 页面使用稍大的文字、没有 Dashboard 标签的居中顶部导航，以及把你带回 Agent 的标志。搜索和来源筛选现在与 Agent 列表排在一行，Composer 的输入更高，菜单按钮显示你的头像。

## Aug 31, 2026 当周

### 集成

#### GitHub

- **被暂停的 GitHub App 安装有更清楚的错误。** 当 Cursor GitHub App 安装被暂停时，Cursor 现在会说明这一点，并把你指向 GitHub 组织设置里的应用状态。
- **过期的 Bugbot 评论会被清除。** 当 Bugbot 清除 GitHub pull request 上一次先前运行的审查时，其正文会被替换为简短的 “Stale Bugbot comment from a previous run.” 说明。其他人的评论保持不动。
- **正确的 GitHub Enterprise 安装。** 当来自不同 GitHub Enterprise Server 实例的安装 ID 冲突时，Cursor 现在会挑选你的团队或账户所拥有的 GitHub Enterprise 应用的安装。

#### Azure DevOps

- **名称里有空格时 Bugbot 仍能工作。** 对于组织、项目或仓库名称包含空格的 Azure DevOps 仓库，Bugbot 不再静默失败。

### 仪表板与管理

#### Cloud Agents

- &#x20;**从 Cloud Agents 仪表板管理 Self-hosted Machines 池。** Team Pools 现在叫 Self-hosted Machines。All Self-hosted Machines 页面列出每个池的仓库、活跃和空闲机器、机器总数，以及 Sessions 列。管理员可以设置池的 Reconnect Window，或删除池而不让其已连接的机器下线。参见 [自托管池](https://cursor.com/docs/cloud-agent/self-hosted/pool.md)。
- &#x20;**一眼看到 Cloud Agent 活动，并在新的 Runs 页面上看完整运行历史。** Cloud Agents 页面现在打开时带你所选日期范围的 Total runs、Success rate 和 Recent runs 卡片。选择 All Runs 打开 Runs 页面，带运行历史图表、运行表格，以及触发器、状态和创建者筛选。团队管理员看到整个团队的运行，个人用户看到自己的。

#### 成员、组与 SSO

- &#x20;**从你的组织移除成员。** 组织管理员现在可以从组织 Members 页面上成员行菜单选择 Remove from Organization。确认之后，此人失去对组织里每个团队的访问，并可以转到个人 Cursor 方案。你不能用这种方式移除自己。
- &#x20;**创建组织团队时从现有团队复制设置。** 在组织里创建团队时的新步骤让你挑选源团队，并选择要复制哪些设置：模型、安全、规则和命令、Hooks、MCP 和插件策略、支出上限，以及团队访问默认值。预览显示每个类别将复制什么，并列出你需要重新设置的连接，例如 GitHub App 或 MCP 服务器。
- &#x20;**目录组移入组时不会丢失支出上限。** 已经移入[组织组](https://cursor.com/docs/enterprise/organization-groups.md)的 SCIM 目录组不再在 Groups 页面或目录设置里出现两次，尚未移动的显示 Legacy 徽章。按已移动的目录组筛选分析或成员时，会包含其新组的成员。个人支出上限更低的成员会保留它，而不是得到该组更高的上限。
- &#x20;**按用量和支出上限排序组成员。** 在从身份提供方同步的组的 Members 标签上，点击 Username、用量、On-Demand Spend 或 Limit 列标题进行排序，再点一次反转顺序。按上限排序时，没有上限的成员出现在最后。
- &#x20;**SCIM 同步移除团队成员之前的警告。** 当你把团队或组切换为 Synced，或更改它从哪些目录组同步时，仪表板现在会在保存前请你确认。警告说明所选组之外的成员会被移除，并且切换回 Manual 不会恢复他们。进一步了解 [SCIM 预配](https://cursor.com/docs/account/teams/scim.md)。

#### 分析

- &#x20;**刷新后的 Analytics 图表。** Analytics 页面和仪表板概览上的图表共享一个调色板、更紧凑的布局，以及在手机和平板上的两列磁贴网格。AI commits 图表把编辑器系列标为 Desktop，并用 Primary Branch 复选框替换 Primary Branch Only。参见 [团队分析](https://cursor.com/docs/account/teams/analytics.md)。

#### 设置与导航

- &#x20;**管理模型设置里有更多模型，Fable 数据保留同意更简单。** 较新的模型现在出现在管理模型设置里，因此管理员可以启用、禁用或限制它们，Muse Spark 模型出现在 Models 页面的 Meta 下。已经接受 Claude Fable 5 数据保留同意的团队不必为 Fable 5.1 再次接受。在组织和组设置里，豁免分区现在叫 “Anthropic: Claude Fable Data Retention Exemption”，并为包含承包商或其他第三方的组织链接到 Organization Groups。
- &#x20;**团队设置里更清楚的 Origin 开关。** 团队设置的 Origin 分区现在有 Enable Origin 开关：打开意味着你的团队可以创建 Origin 仓库。它替换了旧的 “Disable creating Origin repositories” 开关，你当前的设置不变。说明解释了关闭 Origin 会使现有仓库变为只读，并隐藏 Codebase 标签。
- **共享的组织链接打开你自己的组织。** 指向 cursor.com/organization 的链接现在在你组织的仪表板里打开同一页面，并保留路径的其余部分和任何查询字符串。如果你的账户不属于某个组织，你会看到未找到页面。
- **移动端导航更容易，页面标题更清楚。** 在手机上，点仪表板页面标题即可从下拉菜单跳到另一个分区，并从标题栏的头像打开你的账户。Overview、Analytics 和 Usage 页面现在在顶部显示标题。

#### 修复

- &#x20;**修复了 SCIM 组同步。** 通过 SCIM 预配的成员现在会被加到组织组并传递给映射的团队，而不是被跳过，已同步的组不再卡在错误状态。添加组到团队的映射不再移除现有团队成员。用重复名称创建组，或删除仍有 SCIM 映射的组，现在会说明出了什么问题。
- &#x20;**修复了多团队组织的 Integrations 页面。** 断开 GitLab 现在断开你所选的团队，GitHub Enterprise、GitLab Self-Hosted 和 Bitbucket Data Center 行会加载该团队的安装。当组织里的另一个团队已经注册同一主机时，自托管 GitLab 连接不再失败。长实例名称现在以省略号结尾，集成行在移动端布局正确。
- &#x20;**Cloud Agent 密钥不再显示另一个团队的密钥。** 当你切换团队时，密钥列表现在显示所选团队的密钥，创建、编辑和删除密钥作用于该团队，而不是你的默认团队。
- **滚动时 Cloud Agents 设置页面保持其标题。** 环境详情页面、Repository Routing、Team Secrets 和 My Secrets 现在有粘性面包屑标题，取代旧的返回链接。Environments 列表有紧凑的 Repo 筛选，Edit 现在是环境页面上的主要操作。
- **修复了设置和管理页面里的工具提示和 diff 故障。** Members 页面上的 Billable Seats 工具提示现在在其标签下方打开，而不是藏在标题后面，Audit Log 值 diff 不再显示游离的 “No newline at end of file” 标记。API Keys 和 Protected Git Scopes 分区的布局也更一致。

### 自动化

- **挑选环境时搜索远程机器。** 自动化环境选择器里的 Remote Machines 菜单现在可以按机器名称、路径、仓库或池搜索，并把你自己的机器归在 My Machines 下。池会显示有多少 worker 处于活跃，以及它们是否忙碌。
- **Approval Agents 只提供受支持的仓库。** 当你设置 Approval Agent 时，触发器菜单以及组织和仓库选择器只提供 Approval Agents 支持的仓库，因此你不能再挑选 GitLab 或 Bitbucket 仓库。如果仍选中了不受支持的仓库，保存会显示错误，请你移除它。
- **自动化启动和运行的修复。** GitLab 仓库上的自动化不再在仓库需要重新链接时无法启动，由已移除团队成员拥有的自动化会被可靠地禁用。
- **队友可以管理团队自动化。** 删除团队自动化现在从你所选的团队进行，而不是在访问检查上失败，创建者以外的队友可以在 Security Reviewer 这类托管审查 Agent 上保存 Slack 频道。

### API

- **从 API 管理组织组。** [Organization API](https://cursor.com/docs/account/organizations/organization-admin-api.md#organization-groups) 现在可以创建、重命名和删除组，并设置或清除组的月度支出上限。组响应包含 `memberCount` 和 `monthlySpendingLimitDollars`。
- **模型访问覆盖每个可配置的模型。** [模型访问](https://cursor.com/docs/account/organizations/organization-admin-api.md#model-access)路由现在列出并接受你能配置的每个模型，包括隐藏模型及其全部参数。不可用、生命周期已结束或内部的模型不再出现，并会被拒绝。
- **批量设置支出上限。** [Admin API](https://cursor.com/docs/account/teams/admin-api.md#set-user-spend-limits-in-bulk-preview) 增加预览中的 `POST /teams/user-spend-limits`，在一次请求里为最多 100 名团队成员设置或清除支出上限。响应把每个成员报告为 `updated`、`unchanged` 或 `failed`。

### 安全与批准 Agent

- **搜索你的安全 Agent。** Security Agents 列表有一个按名称筛选 Agent 的搜索框。

### 模型与 Cursor Router

- **新模型：Claude Fable 5.1。** [Claude Fable 5.1](https://cursor.com/docs/models/claude-fable-5-1.md) 现在可在 Cursor 中使用。打开 Privacy Mode 时，或在 Enterprise 方案上，必须先接受 Anthropic 的数据保留条款才能使用它。
- **新模型：Gemini 3.8 Flash。** [Gemini 3.8 Flash](https://cursor.com/docs/models/gemini-3-8-flash.md) 现在可在 Cursor 中使用。
- **GPT-5.6 在 Max Mode 中的 Fast mode。** 你现在可以在 Max Mode 中为 [GPT-5.6 Sol](https://cursor.com/docs/models/gpt-5-6-sol.md)、[GPT-5.6 Terra](https://cursor.com/docs/models/gpt-5-6-terra.md) 和 [GPT-5.6 Luna](https://cursor.com/docs/models/gpt-5-6-luna.md) 打开 Fast。以前，打开 Fast 会把这些模型切出 Max Mode 的长上下文。

## Aug 31, 2026 当周

### 审查

- **当 pull request 大到无法审查时，Bugbot 会说明。** 当 pull request 改动超过 100,000 行或 3 million 个字符时，Bugbot 跳过审查并发布评论，说明该 pull request 大到无法审查，并建议你把它拆成更小的 pull request。
- **Bugbot 保留 Security Reviewer 的审查。** 当 Bugbot 在 pull request 上重新运行时，它只隐藏自己更早的审查。来自 Security Reviewer 的审查现在保持可见。
- **短暂网络错误导致的失败审查更少。** Bugbot 在发布之前重新检查 pull request 时的短暂网络错误，不再使该运行失败并重新开始审查。

### 学习规则

- **学习规则在 Origin 仓库上可用。** Bugbot 现在从 Origin 仓库上已合并的 pull request 学习规则。以前，在 Origin pull request 上学习会失败。
- **限制模型的团队也可以使用学习规则。** 如果你的管理模型设置阻止了 Bugbot 的默认学习模型，学习会回退到另一个允许的模型。如果每个选项都被阻止，Bugbot 会跳过学习运行而不是失败，并且不会为此向你计费。

### 仪表板

- **Bugbot 仪表板修复。** 在窄屏或移动屏幕上，表格不再让整个页面横向滚动，点击分区标题以复制其链接时，只有标签本身会响应。

## v2026.08.26

### 持久会话

- **断开之后仍让 Agent 保持运行。** 用 `agent persist` 启动持久会话，用 `/detach` 分离，稍后用 `agent persist attach` 重新连接。用 `agent persist list|stop` 管理会话，或用 `agent persist --resume` 继续现有聊天。

### 自托管 worker

- **查看者观看 Agent 的实时桌面。** 以 `--computer-use --share-desktop` 启动的 worker 现在共享 Agent 使用的托管桌面，而不是单独的会话。Cursor 会清除过期的 worker 自有显示器，以便后来的会话干净启动。
- **为后续唤醒休眠的 worker。** `agent worker controller` 可以通过 `--spawn` Hook 唤醒已认领、处于休眠的 worker，因此后续在重连窗口内回到同一工作区。它的队列监视现在使用服务器发送事件，以减少轮询并处理速率限制。
- **computer use 在 macOS worker 上可用。** 在 Mac 上用 `agent worker --computer-use start` 启动 worker，Cloud Agent 就可以在其已登录的桌面上点击、输入和截图。首次启动会安装 Cursor Computer Use 辅助应用，`agent worker debug` 会确认它在那里。按 [macOS 设置](https://cursor.com/docs/cloud-agent/self-hosted/computer-use.md#macos) 所述，向该辅助应用授予 Accessibility 和 Screen Recording。

### Enterprise 与团队控制

- &#x20;**团队模型限制在 CLI 中生效。** 当管理员限制你团队的模型，例如仅限 Auto 时，`agent models` 和 `/model` 选择器只显示你可以使用的模型。把被阻止的模型传给 `--model` 会以限制消息退出，列表之外的已保存模型会切换到允许的模型。管理员在 [模型访问控制](https://cursor.com/docs/enterprise/model-and-integration-management.md#model-access-control) 里设置。

### 修复

- **修复了停止派生空闲 worker 的温池。** [温池](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#warm-pool)控制器（`agent worker controller --warm-idle <count>`）在 Cursor 的 API 返回它不认识的字段时无法读取池列表。它现在忽略这些字段，并继续派生空闲 worker。

## 1.0.30

- **长时间运行的本地 Agent 保持其凭据新鲜。** TypeScript 和 Python 里的本地运行会在短期访问 token 过期之前刷新它，因此运行超过一小时的 Agent 不再因身份验证错误失败。云运行不受影响。

## 1.0.29

- **自定义工具上的输出 schema。** TypeScript 里的 `outputSchema` 和 Python 里的 `output_schema` 为自定义工具的结构化结果声明 JSON Schema，并向模型宣告为该工具的 MCP 输出 schema。结果不会对照它进行验证。仅本地 Agent。

## 1.0.28

- **把 SDK 作为单个文件发布。** 在 Bun 下，`@cursor/sdk` 现在解析为一个扁平的单文件包，因此 `bun build --compile` 无需更改导入即可工作，也不再出现 `Cannot find module './986.js'`。`@cursor/sdk/bundled` 和 `@cursor/sdk/bundled/sqlite` 把同一构建作为显式入口暴露给 esbuild 等其他单文件打包器。仅 TypeScript。

## v2026.08.11

### 引导与 Subagents

- **先引导正在运行的回合，再中断。** Agent 工作时按 Enter，现在会在安全边界把你的排队消息送进活动运行以引导它，只有再次按 Enter 才会中断该回合。
- **单回合运行会等待其 Subagent。** 无头和单回合运行会在退出前排空被委托的 Subagent 并计入它们的完成，而不是在后台 shell 或开发服务器仍在运行时切断。
- **阅读完整的 Subagent 记录稿。** 深入某个 Subagent 会显示其完整、有序的记录稿，包括提示词、思考、工具调用和最终回复，而不是只有工具行。
- **选择 Explore Subagent 的模型。** 从 `/config` 把 Explore Subagent 设为默认、禁用、继承父模型，或某个特定模型。
- **后台任务的后续回来了。** 已完成后台任务的完成后续，在交互式和无头会话里再次触发。

### Skills、Custom Modes 与目标

- **粘滞的 Skills 和 Custom Modes。** Skill 斜杠条目在你按 Enter 时附加一次，Option+Enter 把由模式支持的 Skill 作为粘滞 Custom Mode 调用，它保持活动直到你退出。
- **托管 Skills 可以附带 Markdown 资源。** 托管 Skill 同步现在会把 Skill 的嵌套 Markdown 文件物化到 `~/.cursor/skills-cursor` 下，因此引用额外资源的 Skills 可以工作。
- **Skill 发现会跳过隐藏目录。** Skill 和 Subagent 扫描不再进入隐藏的点目录，以避免大型嵌套文件夹造成的缓慢加载。
- **持久目标。** `/goal` 用活动或暂停状态行跟踪持久目标，在空闲和无头运行之间继续该目标，并在 Ctrl+C 时暂停它。此功能正在逐步推出，并且受门控。

### 模型

- **Auto 固定在选择器顶部。** Auto 固定在 `/model` 列表顶部，与 IDE 一致。
- **编辑模型的参数会选中它。** 在 `/model` 里调整参数现在会选中该模型，因此按 Esc 会让你更新后的选择保持活动。
- **无头模式保留 Max Mode 变体。** 需要 Max Mode 的无头 `--model` 选择现在会把它发送出去，因此最大上下文变体不再被压低。

### MCP 与插件

- **已知主机的 MCP 登录更简单。** 从 `/mcp` pager 登录 Slack 这类服务器时使用默认身份验证，避免动态客户端注册失败。
- **已安装插件的 Hooks 会运行。** 已安装插件定义的 Hooks，包括用 `--plugin-dir` 加载的那些，现在会执行，并在插件重新加载时刷新。
- **插件配置里的密钥保持掩码。** 插件配置表单会掩码凭据命名和密钥类型的字段，例如 token、API 密钥和密码。

### 终端与渲染

- **内联图片预览。** 附加和生成的图片在支持的终端里内联渲染，其他地方使用文本回退。
- **LaTeX 数学渲染为 Unicode。** Markdown 里的数学渲染为适合终端的 Unicode，而不是原始 TeX。
- **更干净的 shell 输出和记录稿。** 长 shell 输出不再被注入零宽空格，因此复制的路径和 token 保持完整；命令在当前目录运行时，工作目录说明会被隐藏；隐藏的工具调用组不再留下空行；排队的 “+N more pending” 批准预览会无间隙地堆叠。
- **响应更快的 `@` 文件面板。** 在大型仓库里，`@` 面板计算文件建议时，打字仍保持响应。
- **更稳定的自定义状态行。** 自定义 `statusLine` 命令在流式更新期间保持其节流，并且只在命令或工作目录改变时重启。

### 批准与安全

- **带计时的模式切换批准。** 批准模式切换现在使用带 15 秒倒计时的 pager 界面，无人值守时自动拒绝，与 IDE 一致。
- **更加固的 git 命令。** 仓库控制的 git 配置，包括 fsmonitor、hooks、attributes 和外部 diff 程序，不再在 Agent 自己的 git 操作期间运行。
- **沙箱读取访问和通过 SSH 的 git。** 沙箱策略增加了读取访问边界，macOS 和 Linux 上受沙箱保护的 git-over-SSH 经由 SOCKS 代理路由，因此允许列表中的远程可以工作，被拒绝的远程以清楚的错误失败。
- **Sudo 使用你最新的输入。** 提交 sudo 密码现在发送的正是你输入的内容，而不是过期的渲染值。

### 可靠性与启动

- **更快的启动。** 语法高亮文法和后台 Composer 客户端在启动路径之外懒加载，缓慢的插件列表会回退到无插件会话，而不是使启动停滞。
- **长时间的无头会话不再挂起。** 长时间运行的无头 HTTP/1.1 会话上卡住的上传会被检测并重试，而不是静默停滞。
- **网络文件系统上更快恢复。** 恢复选择器并行扫描会话，并在完整的跨工作区列表加载时保持选中你最新的聊天。

### 自托管 worker

- **对自托管 worker 有更多控制。** worker 可以用 `agent worker --pool <name>` 加入命名池，默认在空闲一小时后释放（`--idle-release-timeout 0` 让它们保持运行），用 `--worker-dir` 从非 git 目录启动，获取数据目录锁以防止重复守护进程，并在认领和释放时运行 session-start 和 session-end Hooks。

### Enterprise 与安装

- &#x20;**管理员命令拒绝列表在 CLI 中强制执行。** 团队管理员的命令拒绝列表在本地 shell 执行中强制执行，并按请求刷新，带有 killswitch。由管理员控制。
- &#x20;**MDM 登录策略。** CLI 登录会发送并强制执行 MDM 登录策略，包括组织、团队、邮箱和域名允许列表，并报告强制执行的拒绝。由管理员控制。
- **Windows 卸载可以移除你的数据。** Windows 卸载程序可以选择删除 Cursor 用户数据，包括存储 CLI 凭据的 `~/.cursor` 文件夹。
- **更有韧性的安装脚本。** 当 curl 损坏时，安装脚本回退到 wget 或 python3。

## v2026.07.20

- **`--trust` 在交互式会话里可用。** 以前 `--trust` 要求无头模式。在交互式会话里传入它，现在会预先信任工作区并跳过信任对话框，记录与你接受时对话框写入的相同已保存信任决定。
- **登录时给出清楚的错误，而不是挂起。** 当回调端口已被占用时，MCP OAuth 登录会以可操作的消息快速失败，而不是挂在 “Listening for the OAuth callback” 上。在 macOS 上，启动时的钥匙串失败现在会说明原因和修复方法，例如解锁已锁定的钥匙串，或注销后再登录，而不是裸退出码。
- **长时间会话里模型目录保持最新。** CLI 每 10 分钟在后台重新获取模型目录，因此新发布的模型会出现在 `/model` 和快捷方式里，而不必重启。如果刷新失败，CLI 保留当前目录，并在下一个间隔重试。
- **更干净的渲染。** Agent 进入下一步时，已完成的工具行不再消失一帧，带 emoji 或 CJK 文本的 markdown 表格保持列对齐，输入 `/tmp` 这类路径后跟一个空格不再让斜杠面板卡在 “No matches” 上，Subagent 行去掉了 Ctrl+O 展开提示（该快捷键仍然有效）。
- **修复了分支监视器导致的 CPU 空转。** 使提示词页脚的 git 分支保持最新的监视器无法附加时，最常见的是在 Linux 上耗尽 inotify 监视上限之后，它会在紧密循环里重试，把一个 CPU 核心打满。失败的附加现在会指数退避，分支切换仍会立刻更新页脚。


---

## 站点地图

[所有文档页面概览](/llms.txt)
