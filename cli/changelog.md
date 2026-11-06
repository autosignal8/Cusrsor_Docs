> 原文：https://cursor.com/docs/cli/changelog

# Cursor CLI 更新日志

Cursor CLI 最新发布的功能、改进和修复。运行 `agent --version` 查看已安装版本，运行 `agent update` 原地升级。

## v2026.10.22

### Cloud Agent 环境

- **用 `agent env deploy --env` 更新现有环境。** 传入 `--env <id>`，使用 `agent env list` 显示的公开 ID，把环境文件重新部署到现有 Cloud Agent 环境上。环境会保留其名称和仓库，因此 `--repo` 变为可选。如果 `--name`、`--repo` 或 `--scm-repo-node-id` 与已存储的环境不匹配，命令会停止，并给出说明不匹配之处的错误。

### 自托管 worker

- **自托管 worker 在你选择的团队下运行。** `agent worker start` 和 `agent worker debug` 现在会在你于 `agent login` 时选择的团队，或通过 `agent team switch` 选择的团队下注册并检查 worker，而不是使用账户的默认团队。如果该团队不允许你使用 Cloud Agents，worker 会退出，消息中会指出该团队并建议 `agent team switch`。
- **自托管 worker 的修复。** 共享同一台机器的 worker，即使另一个 worker 正在运行较旧构建，现在也能启动编辑器服务器；该服务器下载期间，worker 会立即应答编辑器连接，而不会挂起。worker 无法服务的连接现在会立即失败，机器繁忙时后端连接不再断开，会话的第一轮不再遗漏启动它的人的 Skills。

### Subagents

- &#x20;**自定义 Subagent 在其模型无法运行时不再失败。** 当自定义 Subagent 固定到一个被团队管理员设置阻止、需要 Max Mode 或不可用的模型时，它现在会在父 Agent 的模型上运行，而不是报错退出。Subagent 的结果会注明它实际运行的模型，以便 Agent 知道固定的模型被跳过了。见[配置的模型何时不会被使用](https://cursor.com/docs/subagents.md#when-the-configured-model-wont-be-used)。
- **Composer 2.5 Subagent 按你选择的速度运行和计费。** 设为 Composer 2.5 的 Subagent 现在以标准速度运行并计费，而不是快速档。Composer 2.5 Fast 上的 Subagent 以快速速度运行，不会因 "Unknown model ID" 错误而失败。
- **Subagent 的修复。** 当 Agent 等待一个已失败或已停止的 Subagent 时，它现在会看到失败，而不是把该 Subagent 当作已完成。

### Rules

- **Agent 编辑文件时，嵌套的 `AGENTS.md` 规则现在会应用。** 当 Agent 在子目录中编辑或创建文件时，它现在会看到该目录的 [`AGENTS.md`](https://cursor.com/docs/rules.md#agentsmd) 和嵌套的始终应用规则，即使它从未先读取那里的文件。每条规则在每次对话中只展示一次，glob 范围的规则和根规则的行为与以前相同。

### 修复

- **后台总结的修复。** 长时间的 Agent 会话现在会保留在后台计算的总结，而不是丢弃它们并再次总结。
- **工具调用显示的修复。** 编辑、shell 和任务工具调用现在会在 Agent 仍在编写它们时显示为已开始。
- **Agent 图像生成和 web fetch 的修复。** 用中文、日文、泰文和类似文字书写的图像生成提示词不再因过短而被拒绝。当截断落在 emoji 内部时，web fetch 不再在长页面上失败，视频生成现在会说明它为什么无法读取输入图像。
- **Auto-review 的修复。** 当服务器把工具的输入 schema 作为 JSON 字符串发送时，MCP 工具调用的 Auto-review 现在能看到该 schema。
- **MCP 服务器的修复。** 单个无效工具，或没有 type 的工具 schema，不再隐藏 MCP 服务器其余的工具；无法列出其工具的服务器现在会向 Agent 报告错误。
- **Windows 的修复。** Agent 在 PowerShell 中运行的 shell 命令现在使用 UTF-8，因此非 ASCII 输出不再显示为乱码。暂存的更新不再被崩溃或被终止进程留下的标记拦住，后台更新检查不再闪现控制台窗口。

## v2026.10.17

### Hooks 和 Subagents

- **工具后 Hooks 现在会为 Subagent 触发。** 匹配 `Task` 工具的 [`postToolUse`](https://cursor.com/docs/hooks.md#posttooluse) 和 `postToolUseFailure` Hooks 现在会在 Subagent 完成或失败时运行。以前，只有 `preToolUse` 会为 Subagent 触发。Hook 返回的任何 `additional_context` 现在都会到达主 Agent，针对 `Task` 的 Claude Code `PostToolUse` 匹配器也同样有效。
- **Subagent 中的 Hooks 会得到正确的记录。** Subagent 记录现在保存在父对话的文件夹内，位于 `agent-transcripts/<parent>/subagents/` 下。在 Subagent 内部运行的 Hooks 现在会收到该文件作为 [`transcript_path`](https://cursor.com/docs/hooks.md#common-schema) 和 `CURSOR_TRANSCRIPT_PATH`。

### 自托管 worker

- **健康检查在关闭过程中持续跟踪自托管 worker。** 对于以 `--management-addr` 启动的 worker，[`/readyz`](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#monitoring) 现在会在 worker 关闭期间继续应答，返回 503 并带有 `draining: true`，以便编排器停止向它发送工作。如果几次重试后仍无法绑定管理地址，worker 现在会启动失败，而不是在没有健康检查的情况下运行。池 worker 在丢弃过期认领后也会退出，以便你的主机替换它们。
- **自托管 worker 的修复。** 运行 `agent worker` 的 worker，在本地重度构建使机器停顿几秒时不再断开连接，也不再会在短暂被提供另一个 Agent 时于会话中途退出。关闭终端或失去 SSH 会话现在会干净地关闭 worker，正在关闭的 worker 会拒绝新的运行，而不是接受一个它无法完成的运行。

### Agent

- **修复了未答复的批准被报告为已取消。** 如果在工具调用仍在等待你批准时发送新消息，Agent 不再告诉你你中断或取消了它。在暂停之前已经完成的工具调用，在 Agent 恢复时也不再运行第二次。

### Rules

- **Windows 上规则匹配的修复。** 即使工作区路径和规则路径仅在盘符大小写上不同，嵌套的 `AGENTS.md` 文件、[Rules](https://cursor.com/docs/rules.md) 和 Skills 现在也会在 Windows 上应用。

## v2026.10.15

### Agent 工具

- **Grep 和 Glob 会搜索工作区之外的文件夹。** 当 Agent 要求搜索你打开的工作区之外的文件夹时，Grep 和 Glob 现在会搜索该文件夹。以前，它们会悄悄搜索工作区，并返回错误位置的结果。`.cursorignore` 和 Read 拒绝规则仍然适用于工作区之外的文件，Glob 现在会说明其结果何时被截断。

### Cloud Agents

- **`agent env` 命令适用于你的个人环境和公开 ID。** `agent env get <repoUrl>` 现在会显示你为该仓库保存的[个人环境](https://cursor.com/docs/cloud-agent/setup.md#environment-resolution-order)，每个都带有其 `environment.json`，`agent env list` 会首先显示每个环境的公开 ID，JSON 输出中也是如此。`agent env delete` 接受该公开 ID，并在你使用旧的数字 ID 时说明应传入什么。`agent env get` 不再接受 `--ref`。

### 自托管 worker

- **在自托管 worker 上给慢速 MCP 服务器更多时间。** 使用可重复的 `agent worker --mcp-tool-timeout server=seconds` 或 `server/tool=seconds` 选项，提高或降低 stdio [MCP](https://cursor.com/docs/cloud-agent/self-hosted/my-machines.md#mcp-servers) 工具调用可以运行的时长，而不是固定的 60 秒。Worker 也会遵循 stdio MCP 服务器的 `startupTimeoutSec`，因此启动缓慢的服务器不再会在 90 秒后被切断。
- **Windows worker 会说明缺少 Git Bash。** 在 Windows 上，`agent worker start` 现在会退出并给出消息，说明 worker 需要 Git for Windows，包括 Git Bash，以及在哪里安装，而不是稍后失败。同一条消息会追加到 `%LOCALAPPDATA%\cursor-agent\worker-startup.log`，`agent worker --help` 会列出该要求。

### 修复

- **Agent 搜索、读取、编辑和 shell 工具的修复。** 超时的搜索现在会保留部分结果，无效的正则表达式或空模式会得到明确错误，而不是匹配所有内容。带有巨大偏移的读取不再结束会话，编辑不再只静默更改多个重叠匹配中的第一个。Shell 输出不再把 stdout 和 stderr 行混在一起，等待后台命令会跟踪它，而不是睡满整个超时时间。
- **Subagent 的修复。** 当 Subagent 失败时，Agent 现在会看到 Subagent 的 ID 和失败原因，而不是通用错误。被要求使用带有额外 `cursor-` 前缀，或不存在的 `-fast` 后缀的模型名的 Subagent，现在会运行所请求的模型，而不是报错或切换到 Composer。恢复失败的 Subagent 会保留其原始类型和模型。
- **长会话、内存和总结的修复。** 长会话使用更少内存：非常长的待办列表，以及在后台总结期间结束的轮次，不再使 Agent 膨胀；如果总结无法在接近上下文限制时启动，轮次也不再失败。总结之后的上下文用量更准确，过大的网页搜索结果和失控的工具调用流不再溢出上下文，停止 Agent 会保留已经返回的工具结果。
- **自托管 worker 的修复。** 当 Agent 存储打开缓慢时，worker 不再使 Agent 认领超时，并且在启动它的进程退出后仍能识别正在运行的 Cursor 服务器。当同一台机器上的另一个 worker 在下载期间安装相同的服务器构建时，worker 也不再失败。
- **Windows 上 Hooks 和 shell 命令的修复。** 失败或指向找不到的命令的 Hooks 现在会被报告为失败，而不是静默成功。Agent 也不再尝试在 Windows 上对 shell 命令使用 tmux。
- **文件建议、已归档 Agent 和本地地址的修复。** 在 macOS 上从主文件夹运行 CLI 时，构建 `@` 文件建议不再对 Desktop、Documents 或 Library 等受保护文件夹触发权限提示。向已归档的 Agent 发消息现在会明确说明这一点，而不是显示通用的未找到错误，提及 "archived" 的无关错误也不再被误报。`[::1]` 等 IPv6 主机现在会被识别为环回地址，完整的链路本地范围会被视为私有。

## v2026.10.08

### Auto-review

- **Auto-review 对有风险的删除更严格。** 对于目标解析结果可能与所写内容不同的递归、强制、通配符或镜像删除，例如路径中未设置的变量或多余的空格，Auto-review 现在会在执行前请求批准。见[配置 Auto-review](https://cursor.com/docs/agent/security/run-modes.md#configuring-auto-review)。

### 模型

- **Bedrock 上的 Claude Opus 5.5 和 Sonnet 5.5。** 如果你用 `agent bedrock` 设置了 [AWS Bedrock](https://cursor.com/docs/customizing/aws-bedrock.md)，现在可以从自带密钥的模型列表中选择 Claude Opus 5.5 和 Sonnet 5.5。

### Skills

- **`/skills` 会标记超出 Agent Skills 限制的 Skills。** 已加载但超出规范限制的 Skills 现在会在 `/skills` 中显示黄色警告计数，选中其中一个会在其下列出需要修复的内容。警告涵盖名称超过 64 个字符或不是小写连字符形式、frontmatter 中的 `name` 与文件夹不匹配、描述超过 1,024 个字符，以及正文超过大约 5,000 tokens。见 [SKILL.md 格式](https://cursor.com/docs/skills.md#skillmd-file-format)。

### ACP

- **ACP 客户端现在会从被监视的后台 shell 收到唤醒。** 在 [ACP](https://cursor.com/docs/cli/acp.md) 模式下，当 Agent 启动一个监视输出的后台 shell 时，提示词现在会保持打开，每当输出匹配时 Agent 就会唤醒并响应。以前 Agent 会说它会收到通知，但唤醒从未到来。提示词等待期间，客户端会显示一个进行中的 `Watching: <reason>` 工具调用。取消提示词或发送新提示词会结束等待，而不会停止该 shell。
- **切换团队后，ACP 会话不再在旧团队下继续运行。** 如果在 [ACP](https://cursor.com/docs/cli/acp.md) 服务器运行时切换团队，`session/new`、`session/load` 和 `session/prompt` 请求现在会以 `Your team has switched to <team>. Create a new session to continue.` 失败，并且服务器退出。以前，它会继续在启动时所在的团队下工作。

### 自托管 worker

- **任意仓库池 Agent 现在在它们认领的仓库内工作。** 在以 [`--clone-git-repos`](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#any-repo-pools) 启动的 worker 上，Agent 的工具、shell、Rules、Skills 和 Subagents 现在会在为其认领克隆或复用的检出中运行，认领结束时再回到 worker 目录。如果检出中有早期 Agent 可能留下的未跟踪 Rules、Skills 或 `AGENTS.md` 文件，worker 也会重新克隆，而不是复用该检出，worker 日志会列出阻止复用的文件。
- **自托管 worker 在注册过多仓库时会停止。** 当 Cursor 拒绝一个 `agent worker`，因为它注册的仓库数超过 worker 允许的数量时，worker 现在会退出并给出消息，说明数量、限制，以及再次启动前要移除多少个仓库文件夹，而不是无尽地重新连接。
- &#x20;**在配置认领的运行之前，先验证它是给谁的。** `agent worker controller --identity-audience <aud>` 会向每个认领请求一个短时签名的[身份 token](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#identity-tokens)，绑定到你的 audience 和一个新的 nonce。你的 `--spawn` hook 会收到 `CURSOR_IDENTITY_TOKEN`、`CURSOR_IDENTITY_TOKEN_EXPIRES_AT` 和 `CURSOR_IDENTITY_NONCE`，因此 hook 可以在创建机器或分发凭据之前检查该 Agent 及其所有者。如果认领返回时没有 token，控制器会释放该认领并退出。该标志仅在认领模式下有效，不能与 `--warm-idle` 组合。

### Cloud Agents

- **`agent env deploy` 接受注释和尾随逗号。** 传给 `agent env deploy` 的环境文件现在可以包含注释和尾随逗号，因此你可以就地为 Cloud Agent 设置添加注释。无效文件会失败，并给出指向解析停止位置的语法错误。

### 修复

- **MCP OAuth 不再发送未填充的客户端 ID 占位符。** 当 MCP 服务器的 OAuth 配置仍有未解析的 `${VAR}` 客户端 ID 时，CLI 现在会把客户端 ID 视为未设置。它不再把字面占位符发送给授权服务器，因此登录不再因令人困惑的客户端错误而失败。
- **Agent 工具的修复。** 当模型调用未知工具时，Agent 不再挂起，会话不再因超出范围的 Shell 或 Grep 参数而结束。对大文件的编辑会安全失败，而不是截断它们，松散的单行替换不再拼接错误的文本，编辑或创建文件的 shell 命令之后立即进行的 Read 会返回新内容。Glob 现在能找到空文件和二进制文件并跳过 `.git`，生成的图像会以正确的文件扩展名保存。
- **MCP 工具的修复。** 当另一个 Cursor 应用已经在使用登录回调端口时，MCP 登录不再失败。当某个工具似乎缺失时，Agent 现在会刷新服务器的工具列表。当工具不存在时，错误现在会列出 Agent 可以调用的工具。
- **自托管 worker 的修复。** Worker 不再会在慢速链路上传大消息时断开连接，不再会在服务器接受之前报告自己已就绪，也不再会删除同一台机器上另一个 worker 推送的 git 凭据。现有检出不再会因为系统级或全局 git 配置而被拒绝，停止 worker 现在会打印原因。你移除的 Agent 存储在重启后保持移除，关闭时挂起的存储会在下一次认领时重新打开。
- **长会话和总结的修复。** 达到上下文限制的会话现在会总结并继续，而不是卡住，你最新的请求在总结后会逐字保留。对话被总结后，Debug Mode 不再使一轮失败，在被中断的轮次期间排队、并附加了图像的消息，现在会在下一轮发送。
- **切换团队后，Agent 存储同步仍然有效。** 只要你仍是拥有该存储的团队的成员，更改活跃团队后，本地 Agent 存储仍会正常同步。

## v2026.09.28

### 修复

- **Grok 轮次不再因被拒绝的输出而结束。** 当提供方在该步骤中的任何工具运行之前拒绝 Grok 模型的输出时，Agent 现在会重试该步骤一次，而不是以错误结束这一轮。
- **空回复之后不再多出一轮。** 在你恢复会话或后台任务完成之后，空的模型回复不再触发不需要的重试或延续轮次。
- **自托管 worker 会从静默连接中恢复。** 与 Cursor 的连接静默 90 秒的 worker 现在会自行重新连接，而不是看起来已连接却收不到工作。如果你的账户使用 Privacy Mode (Legacy)，worker 现在会在启动时退出，并给出说明如何切换到 Privacy Mode 的消息，而不是无尽地重新连接。
- **自托管 worker 在轮次之间保持 Agent 的状态打开。** 在共享和池化的自托管 worker 上，Agent 的后续步骤会复用其打开的状态，而不是从头重新打开；来自另一个 Agent 的认领也不再会在前一个 Agent 已经释放的状态上失败。
- **图像生成错误会说明下一步该做什么。** 当图像提供方拒绝请求时，Agent 现在会显示一条你可以据此行动的消息，而不是 "Request failed with status code 400."。对于内容政策拦截，消息会建议描述主题时不要点名商标角色、品牌或真实人物。对于速率限制和提供方中断，它会说稍后再试。
- **超时的 shell 命令会保留其输出。** 当长时间运行的命令把输出写入文件然后超时，Agent 现在会得到该文件的路径、大小和行数，而不是空结果，因此它可以读取输出并继续工作。
- **CLI 运行你选择的精确模型。** `--model` 和 `/model` 现在会运行你选择的模型，即使它的 ID 以另一个模型的 ID 开头。以前 CLI 可能会静默运行更短的基础模型。
- **macOS worker 按 Activity Monitor 的方式报告内存。** macOS 上的自托管 worker 不再把文件缓存计为已用内存，因此空闲的 Mac 不再看起来几乎耗尽内存。

## v2026.09.22

### Auto-review

- **Auto-review 会把阻止指令与调用实际所做的事放在一起权衡。** 在类似「发送包含外部人员的通信」这样的指令下，它现在更倾向于允许只起草、预览、规划或读取的 MCP 调用，并更倾向于阻止发送、发布、公开、应用或删除的调用。按[配置 Auto-review](https://cursor.com/docs/agent/security/run-modes.md#configuring-auto-review) 中所示，把指令添加到 `permissions.json`。

### 自托管 worker

- **任意仓库池 worker 会复用你已经克隆的检出。** 使用 [`--clone-git-repos`](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#any-repo-pools) 时，worker 会在其 worker 目录中复用所认领仓库的干净检出。它会获取请求的分支、标签或 commit，并在认领结束时保留该检出，因此大型仓库不再在每次认领时重新克隆。Worker 日志会说明它为什么复用或跳过每个检出。Worker 也会拒绝从它无法写入的目录启动，并在克隆失败时在终端中打印 git 的错误。
- **暖池会为后续消息唤醒休眠的 worker。** 当后续消息到达、而该 Agent 的 worker 处于休眠时，`agent worker controller --warm-idle` 现在会以 `CURSOR_WAKE=1` 和该 worker 的 ID 运行你的 `--spawn` hook，与先认领再启动的模式一致。如果 hook 恢复了机器，后续消息会在其原始工作区中继续，而不是落到一块空磁盘的空闲备用机上。这适用于带有[重连窗口](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#hibernation)的池。

### 修复

- **修复了 Agent 卡在重复同一行推理。** 当 Agent 的推理不断重复同一行时，该步骤现在会停止，并带上一条提醒重试一次，提醒它停止斟酌并继续推进，而不是循环。
- **修复了总结丢失你最新的请求或对话。** 总结长对话之后，Agent 现在会逐字保留你最近的消息，而不是一条提醒，因此它会留在任务上。如果总结失败，或你在它运行时按 Stop，Agent 会保留较早的对话，而不是用空的或被中断的总结替换它。
- **修复了自托管 worker 上的身份验证错误和错误的规则。** 使用 API key 启动的 worker，在第一个小时之后打开桌面会话时，不再因身份验证错误而失败。Agent 的第一轮现在会从其自己的工作区加载 Rules、Skills 和 Plugins，而不是机器上另一个工作区留下的缓存集合。
- **修复了 Grok 4.7 和 Opus 5.5 的模型指令。** 被问到时，Grok 4.7 现在会表明自己是 Grok 4.7，而不是声称自己是 Composer。Opus 5.5 现在会得到与 Opus 5 相同的沟通指令，这些指令要求在第一次工具调用之前用一行说明它即将做什么，并用一段以结果开头的简短收尾总结。

## v2026.09.15

### ACP

- **Agent Client Protocol (ACP) 客户端可以把 Subagent 显示为自己的会话。** 宣告了 `subagents` 能力的客户端会把每个 Subagent 作为链接到父 Task 工具调用的子会话，带有流式文本、思考、工具调用，以及最终的已完成、已失败或已取消状态。对于这些客户端，提示词会等待其后台 Subagent，并在同一轮中包含它们的结果。取消提示词会停止它们，加载已保存的会话会再次显示其过去的 Subagent。没有该能力的客户端继续接收 [`cursor/task`](https://cursor.com/docs/cli/acp.md#cursortask) 通知。

### 登录

- **登录信任你操作系统的 CA 证书。** `agent login` 用于其身份验证请求的、可感知代理的获取，无论是直接还是通过 [`HTTPS_PROXY`](https://cursor.com/docs/cli/reference/configuration.md#proxy-configuration)，现在都会把系统信任的 CA 证书加入捆绑集合，因此一旦公司的根证书在系统信任存储中，在企业 TLS 检查之后登录也能工作。

### 修复

- &#x20;**带有文件模式的团队规则不再加载到每个提示词中。** 范围限定为 `*.py` 这类模式的团队规则，现在只在 Agent 读取匹配文件时应用。没有模式的[团队规则](https://cursor.com/docs/rules.md#format-and-how-team-rules-are-applied)仍然应用于每个请求。
- **`agent models` 列出你团队的 Bedrock 模型。** 当你的团队通过 IAM 角色连接 AWS Bedrock 时，`agent models` 和 `--list-models` 现在会显示你所在区域的 Bedrock 模型，而不是空列表。该列表与 `--model` 接受的内容一致。
- **Shell 命令在 macOS computer-use worker 上运行。** 在以 [`--computer-use`](https://cursor.com/docs/cloud-agent/self-hosted/computer-use.md#macos) 启动的 Mac worker 上，shell 命令不再在运行前以 "The shell command returned no exit status" 失败。
- **Agent 工具和轮次的修复。** 当 Agent 等待一个正在运行的 shell 命令时，它现在会在命令完成，或其输出匹配 Agent 正在监视的模式时立即继续，而不是空闲直到超时。当模型把补丁包在 JSON 中时，文件编辑不再中断。在一轮重试失败的连接之后，停止 Agent 仍然有效。
- **长轮次和 Auto-review 的修复。** 在 Claude 和 GPT 模型上带有许多工具调用的长轮次，不再在每一步都卡在压缩上下文却无法释放空间。当分类器的回复遗漏了决定时，Auto-review 现在会从被阻止的动作中得出决定。
- **自托管 worker 的修复。** 以 [`--clone-git-repos`](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#any-repo-pools) 启动的 worker 现在会克隆没有 scheme 存储的仓库，例如 `github.com/owner/repo`，而不是因为 git 把它们读成本地路径而失败。当 Cursor 对 `agent worker controller` 限速时，它会等待服务器的 `Retry-After` 时间，最长 1 小时。带着过期认领重新连接的池 worker 现在会丢弃该认领，而不是拒绝它的下一个 Agent；账户验证失败的 worker 会显示服务器的原因，而不是光秃的 "Error"。
- **每个模型上的循环提醒。** 当任何模型上的 Agent（不只是 Composer）不断重复相同的工具调用或消息时，它现在会收到一条提醒，去尝试不同的做法，而不是循环直到触及步骤限制。
- **让 Agent 停止一个目标会关闭它。** 当你让 Agent 停止朝着 [`/goal`](https://cursor.com/docs/cli/reference/slash-commands.md) 工作时，它现在可以把该目标标为完成，而不是让它保持激活。

## v2026.08.26

### 持久会话

- **断开连接后保持 Agent 运行。** 用 `agent persist` 启动持久会话，用 `/detach` 分离，稍后用 `agent persist attach` 重新连接。使用 `agent persist list|stop` 管理会话，或用 `agent persist --resume` 继续已有聊天。

### 自托管 worker

- **查看者观看 Agent 的实时桌面。** 以 `--computer-use --share-desktop` 启动的 worker 现在共享 Agent 使用的受管桌面，而不是单独的会话。Cursor 会清理过期的、由 worker 拥有的显示，以便后续会话干净地启动。
- **为后续消息唤醒休眠的 worker。** `agent worker controller` 可以通过 `--spawn` hook 唤醒已认领、处于休眠的 worker，因此后续消息会在重连窗口期间返回同一工作区。它的队列监视现在使用服务器发送事件，以减少轮询并处理速率限制。
- **Computer Use 在 macOS worker 上可用。** 在 Mac 上用 `agent worker --computer-use start` 启动 worker，Cloud Agents 就可以在其已登录的桌面上点击、输入和截屏。首次启动会安装 Cursor Computer Use 辅助应用，`agent worker debug` 会确认它在那里。按 [macOS 设置](https://cursor.com/docs/cloud-agent/self-hosted/computer-use.md#macos) 中的说明，向该辅助应用授予辅助功能和屏幕录制权限。

### Enterprise 和团队控制

- &#x20;**团队模型限制在 CLI 中生效。** 当管理员限制你团队的模型，例如只允许 Auto 时，`agent models` 和 `/model` 选择器只显示你可以使用的模型。把被阻止的模型传给 `--model` 会退出并给出限制消息，已保存但不在列表中的模型会切换到允许的模型。管理员在[模型访问控制](https://cursor.com/docs/enterprise/model-and-integration-management.md#model-access-control)中进行此设置。

### 修复

- **修复了停止生成空闲 worker 的暖池。** 当 Cursor 的 API 返回它不认识的字段时，[暖池](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#warm-pool)控制器（`agent worker controller --warm-idle <count>`）无法读取池列表。它现在会忽略这些字段，并继续生成空闲 worker。

## v2026.08.11

### 引导和 Subagents

- **引导正在进行的轮次，然后再中断。** 在 Agent 工作时按 Enter，现在会在安全边界把你排队的消息送入当前运行以引导它，只有当你再次按 Enter 时才会中断这一轮。
- **单轮运行会等待其 Subagent。** 无头和单轮运行会排空委派的 Subagent，并在退出前包含它们的完成，而不是在后台 shell 或开发服务器仍在运行时切断。
- **阅读完整的 Subagent 记录。** 深入一个 Subagent 会显示其完整、有序的记录，包括提示词、思考、工具调用和最终响应，而不只是工具行。
- **选择 Explore Subagent 的模型。** 从 `/config` 把 Explore Subagent 设为默认、禁用、继承父模型，或一个特定模型。
- **后台任务的后续消息回来了。** 已完成后台任务的完成后续消息，在交互式和无头会话中再次触发。

### Skills、Custom Modes 和目标

- **粘滞 Skills 和 Custom Modes。** 按 Enter 时，Skill 斜杠条目会附加一次；Option+Enter 会把由模式支撑的 Skill 作为粘滞 Custom Mode 调用，并保持激活直到你退出。
- **受管 Skills 可以附带 Markdown 资源。** 受管 Skill 同步现在会在 `~/.cursor/skills-cursor` 下具体化 Skill 的嵌套 Markdown 文件，因此引用额外资源的 Skills 可以工作。
- **Skill 发现会跳过隐藏目录。** Skill 和 Subagent 扫描不再进入隐藏的点目录，避免大型嵌套文件夹导致加载缓慢。
- **持久目标。** `/goal` 以激活或暂停状态行跟踪一个持久目标，在空闲和无头运行中继续该目标，并在 Ctrl+C 时暂停它。此功能正在逐步推出并设有门控。

### 模型

- **Auto 在选择器中保持置顶。** Auto 固定在 `/model` 列表顶部，与 IDE 一致。
- **编辑模型参数会选中它。** 在 `/model` 中调整参数现在会选中该模型，因此按 Esc 会让你更新后的选择保持激活。
- **无头模式保留 Max Mode 变体。** 需要 Max Mode 的无头 `--model` 选择现在会发送它，因此最大上下文变体不再被压低。

### MCP 和 Plugins

- **已知主机的 MCP 登录更简单。** 从 `/mcp` 分页器登录 Slack 等服务器时使用默认身份验证，避免动态客户端注册失败。
- **Plugin Hooks 从已安装的 Plugins 运行。** 由已安装 Plugins 定义的 Hooks，包括用 `--plugin-dir` 加载的那些，现在会执行，并在 Plugins 重新加载时刷新。
- **密钥在 Plugin 配置中保持掩码。** Plugin 配置表单会掩码以凭据命名和密钥类型的字段，例如 token、API key 和密码。

### 终端和渲染

- **内联图像预览。** 附加和生成的图像会在支持的终端中内联渲染，其他地方则回退为文本。
- **LaTeX 数学渲染为 Unicode。** Markdown 中的数学会渲染为适合终端的 Unicode，而不是原始 TeX。
- **更干净的 shell 输出和记录。** 长 shell 输出不再被注入零宽空格，因此复制的路径和 token 保持完整；命令在当前目录运行时，工作目录说明会被隐藏；隐藏的工具调用组不再留下空行；排队的 "+N more pending" 批准预览会无间隙地堆叠。
- **响应迅速的 `@` 文件面板。** 在大型仓库中，`@` 面板计算文件建议时，输入仍然保持响应。
- **更稳定的自定义状态行。** 自定义 `statusLine` 命令在流式更新期间保持其节流，并且只在命令或工作目录改变时重启。

### 批准和安全

- **带计时的模式切换批准。** 批准模式切换现在使用带 15 秒倒计时的分页器 UI，无人看管时自动拒绝，与 IDE 一致。
- **加固的 git 命令。** 由仓库控制的 git 配置，包括 fsmonitor、hooks、attributes 和外部 diff 程序，在 Agent 自己的 git 操作期间不再运行。
- **沙箱读取访问和通过 SSH 的 git。** 沙箱策略增加了读取访问边界，macOS 和 Linux 上沙箱内通过 SSH 的 git 会经由 SOCKS 代理路由，因此 allowlist 中的远程可以工作，被拒绝的远程会以明确错误失败。
- **Sudo 使用你最新的输入。** 提交 sudo 密码现在发送的正是你输入的内容，而不是过期的渲染值。

### 可靠性和启动

- **更快的启动。** 语法高亮语法和 background-composer 客户端在启动路径之外懒加载，缓慢的 Plugin 列表会回退到没有 Plugin 的会话，而不是拖住启动。
- **长时间的无头会话不再挂起。** 长时间运行的无头 HTTP/1.1 会话上卡住的上传会被检测并重试，而不是静默停顿。
- **在网络文件系统上更快地恢复。** 恢复选择器并行扫描会话，并在完整的跨工作区列表加载时保持选中你最新的聊天。

### 自托管 worker

- **对自托管 worker 有更多控制。** Worker 可以用 `agent worker --pool <name>` 加入命名池，默认在空闲一小时后释放（`--idle-release-timeout 0` 让它们保持运行），可以用 `--worker-dir` 从非 git 目录启动，获取数据目录锁以防止重复守护进程，并在认领和释放时运行会话开始和会话结束 Hooks。

### Enterprise 和安装

- &#x20;**管理员命令拒绝列表在 CLI 中强制执行。** 团队管理员的命令拒绝列表在本地 shell 执行中强制执行，并按请求刷新，带有紧急开关。由管理员控制。
- &#x20;**MDM 登录策略。** CLI 登录会发送并强制执行 MDM 登录策略，包括组织、团队、电子邮件和域名 allowlist，并报告强制执行的拒绝。由管理员控制。
- **Windows 卸载可以移除你的数据。** Windows 卸载程序可以选择删除 Cursor 用户数据，包括存储 CLI 凭据的 `~/.cursor` 文件夹。
- **更有弹性的安装脚本。** 当 curl 损坏时，安装脚本会回退到 wget 或 python3。

## v2026.07.20

- **`--trust` 在交互式会话中可用。** 以前 `--trust` 需要无头模式。在交互式会话中传入它，现在会预先信任工作区并跳过信任对话框，记录与你接受时对话框所写相同的已保存信任决定。
- **登录时给出明确错误，而不是挂起。** 当回调端口已被占用时，MCP OAuth 登录会快速失败并给出可操作的消息，而不是挂在 "Listening for the OAuth callback" 上。在 macOS 上，启动时的钥匙串失败现在会说明原因和修复方法，例如解锁已锁定的钥匙串，或登出后再登录，而不是一个原始退出代码。
- **模型目录在长会话中保持最新。** CLI 每 10 分钟在后台重新获取模型目录，因此新发布的模型会出现在 `/model` 和快捷方式中，无需重启。如果刷新失败，CLI 会保留当前目录，并在下一个间隔重试。
- **更干净的渲染。** Agent 进入下一步时，已完成的工具行不再消失一帧；带有 emoji 或 CJK 文本的 markdown 表格保持列对齐；输入 `/tmp` 这样的路径后再跟一个空格，不再让斜杠面板卡在 "No matches" 上；Subagent 行去掉了 Ctrl+O 展开提示（该快捷键仍然有效）。
- **修复了分支监视器导致的 CPU 空转。** 当保持提示词页脚 git 分支最新的监视器无法附加时，最常见的是在 Linux 上耗尽 inotify 监视限制之后，它会在紧密循环中重试，把一个 CPU 核心占满。失败的附加现在会指数退避，分支切换仍然会立即更新页脚。

## v2026.07.13

### Plugins 和 MCP

- **从 shell 管理 Plugin 市场。** 无需交互式会话即可编写市场管理脚本：`agent plugin marketplace add <git-url>` 注册一个市场（用 `--git-ref` 固定分支、标签或 commit），`list` 打印每个市场的名称、范围和 git URL（脚本使用 `--format json`），`update` 从其仓库重新索引一个市场，`remove` 删除用户范围的市场。
- **重复的 MCP 配置只运行一个服务器。** 当同一个远程 MCP 服务器在不止一个范围中配置，例如你的用户 `mcp.json` 和项目的 `.cursor/mcp.json`，CLI 现在运行单个实例，而不是重复实例。从服务器 `/mcp` 详情视图中新的 Configure Scope 选项卡选择哪个范围的配置优先。该选择按项目保存。

### 用量和模型

- **在 `/usage` 中查看方案用量和支出。** `/usage` 现在显示你账户的包含用量计量，带有 Auto 和 API 细分、相对于限额的按需支出、方案名称和计费周期重置日期。Enterprise 池化方案会得到当前计费周期与上一等长周期对比的支出图表，以及指向用量仪表板的链接。
- **修复了 Max Mode 一直开着。** 选择需要 Max Mode 的变体，例如 `/fast` 或 1M 上下文选项，不再让 Max Mode 对其他模型和未来会话静默保持启用：当你的选择不再需要它时，它会清除，而显式的 `/max-mode` 选择保持粘滞。MAX 指示器也回到了提示栏中，位于模型参数摘要之后。
- **修复了慢速连接上空白的 `/model` 选择器。** 如果模型目录获取在启动时超时，`/model` 会在整个会话中保持空白。现在打开选择器会重新获取目录，打开的选择器会在列表到达时立即填入。

### 批准和信任

- **自动接受网页搜索。** 新的 Auto-Accept Web Search 权限让 Agent 运行网页搜索时不必每次暂停等待批准。它默认关闭。在 `/config` 的 Permissions 下切换它，或在 `cli-config.json` 中设置 `autoAcceptWebSearch`。在交互式、无头、Subagent 和 Agent Client Protocol 运行中都会遵守。
- **从 shell 批准提示引导 Agent。** Shell 批准上的跳过选项现在标记为 "Skip & tell the agent what to do instead"，选择它会打开一个输入框，其消息会作为跳过该命令的原因发送给 Agent。提交空消息只是跳过。
- **Worktree 设置遵守工作区信任。** 来自 `.cursor/worktrees.json` 的设置命令现在只在工作区被信任之后运行，途径包括信任提示、已保存的信任决定，或无头运行中的 `--trust`。不受信任的工作区会跳过设置，并给出可见通知。

### 可靠性

- **恢复聊天更快。** 恢复选择器会立即显示当前工作区的聊天，并在后台填入其他工作区；当你存储的模型已经缓存时，选择一个聊天不再等待模型目录获取。
- **网络闪断不再终止运行。** DNS 失败和网络不可达错误，例如 VPN 切换期间的那些，会像其他传输错误一样带退避重试。如果网络持续中断，CLI 会显示一条可操作的提示，涵盖网络、VPN、DNS 和代理配置，而不是原始网络错误。
- **Subagent 能挺过瞬时失败。** 短暂的网络错误不再用终端错误终止正在运行的 Subagent。Subagent 轮次以与常规轮次相同的策略重试，当存在上一个 Checkpoints 时从其恢复。
- **更新不再破坏正在运行的会话。** 自动更新清理可能会删除长时间运行的会话所启动的已安装版本，使它在轮次中途崩溃。正在运行的进程现在会把其安装目录标记为使用中，以便清理跳过它；如果安装文件仍然消失，例如 Homebrew 升级之后，CLI 会请你重启，而不是以模块错误失败。
- **`/btw` 修复。** 旁路问题在长会话或已压缩会话中不再因内部 blob 错误而失败，你输入问题时斜杠面板不再弹到问题上方。

## v2026.07.06

### 模型和 Skills

- **新安装从 Auto 开始。** 全新的 CLI 安装默认使用 Auto 模型路由。已有选择不变；随时用 `/model` 或 `--model` 切换。
- **用斜杠命令即时切换模型。** 输入 `/opus` 或 `/composer` 这样的快捷方式直接跳到一个模型，不需要选择器；每个系列的快捷方式会记住你上次的选择。当当前模型支持时，`/fast` 切换 Fast。在 `/config` 的 Model slash commands 下禁用快捷方式。
- **把仅供模型使用的 Skills 排除在斜杠命令之外。** 在 Skill 的 `SKILL.md` frontmatter 中设置 `user-invocable: false`，把它从 `/` 自动完成和键入的 `/skill-name` 解析中隐藏，同时仍对模型可用。

### 可靠性

- **修复了每轮取消状态导致的内存增长。** 长聊天不再累积 abort 监听器和控制器；每一轮结束时都会释放它们。
- **退出会迅速把你带回 shell。** 退出不再等待 MCP 服务器或其他后台任务收尾，从工作区信任提示退出不再闪现 "Trusting workspace…"。
- **修复了同时运行多个 Agent 时的配置损坏。** 并发的 CLI 进程可能交错写入 `cli-config.json` 并阻塞后续启动。每次写入现在先暂存到自己的临时文件，再进行原子重命名。

### 会话和 Subagents

- **从任意目录恢复聊天。** `agent ls`、`agent --resume` 和 `/resume` 默认打开跨工作区的 All chats。用 Left/Right 切换到 This workspace；来自其他目录的聊天会显示文件夹标签，恢复其中一个会加载完整对话，而不是空对话。
- **Subagent 在恢复之间保留其上下文。** 已完成的 Subagent 会持久化 Checkpoints，因此恢复其中一个会还原其先前上下文，而不是从空开始。后台 Subagent 在完成通知中包含其最终消息，恢复不可用的 Subagent 会明确失败。

### 登录和 MCP

- **用二维码从另一台设备登录。** 在 `agent login` 或首次运行引导期间，按 `q` 显示同一登录 URL 的二维码，然后用手机扫描，即可在 SSH 上完成身份验证，而无需复制长链接。窄终端和非交互式会话继续只显示 URL。
- **过滤 MCP 服务器并立即看到登录开始。** 输入以过滤 `/mcp` 服务器列表。选择 Login 会立刻显示 "Preparing login…"，SSH OAuth 说明不再使用 `ssh -N`，后者会在某些代理上失败。
- &#x20;**修复了 MCP allowlist 和批准的 bug。** 团队网络 allowlist 现在接受带显式端口的 HTTP(S) 来源，例如非默认端口上的本地服务器。当团队管理员工具控制为空或未设置时，个人 MCP 工具批准再次有效。

### 输入和终端

- **长提示词保持在六行可视范围内。** 提示栏和排队消息编辑器会滚动以保持光标可见，而不是无界增长。从历史中召回时，大段粘贴保持折叠为胶囊，同时 Agent 收到其完整内容，提交后 Working 会立即出现。
- **修复了 Ctrl+Z 之后的终端状态。** 挂起 CLI 会恢复你 shell 的键盘模式，直到你 `fg`。界面短暂停顿时，Backspace 不再吞掉紧接在它之后输入的字母，Agent 流式输出时输入保持响应。
- **修复了排队的 sudo 提示。** 每个 sudo 请求都会打开一个新的密码提示，而不是停在 "Authenticating…"。提交期间 Escape 和 Ctrl+C 可以取消，掩码使用 `•` 而不是 `*`，以避免与终端字体连字错位。
- **Debug Mode 卡片使用 Debug Mode 颜色。** 复现步骤决策卡片使用红色的 debug 强调色，而不是 Plan Mode 的黄色，因此当前模式保持清晰。

## v2026.06.29

### 工作区和命令

- **从命令行启动多根会话。** 重复 `--add-dir <path>` 在启动时添加目录，包括与 `--workspace` 一起使用。`/add-dir` 会立即刷新斜杠 Skills 和自定义命令；只有当你希望 Agent 自动发现新 Skills 时才需要重启。
- **Plugin 重新加载会刷新命令。** 重新加载 Plugin 会刷新其斜杠命令和面板。长的 Skill 和自定义命令名称会正确解析，浏览时 `/add-dir` 保持目录补全打开。
- **排队的后续消息在第二次 Enter 时发送。** 在空提示词上再次按 Enter，停止当前轮次并立即发送你排队的消息，包括 `beforeSubmitPrompt` hook 正在运行时。
- **修复了 Agent 运行期间的输入延迟。** 输出流式传输时，输入和排队后续消息不再在每次按键时重新渲染完整记录。
- **修复了模型选项被重置。** 在 `/model` 中更改 Fast、推理强度或上下文会保留你其他兼容的选择，并保持 Max Mode 同步。

### Cloud 和 Auto-review

- **云端转移保留模型和工作区上下文。** 对于有 Cloud Agents 访问权限的 Git 仓库，转移会保留所选模型和工作区路径。提示词会显示转移状态，`Esc` 或 `Ctrl-C` 取消，失败细节保持可见。
- **Auto-review 会考虑被调用的指令。** 管理员可以控制可用性。当你的账户和所选模型启用了 Auto-review 时，分类器可以在决定工具调用是否需要批准之前，检查被调用的 Skill 和由文件支撑的自定义命令文件。现有的硬性批准边界不变。

### 身份验证和 MCP

- **在没有 macOS 钥匙串的沙箱中运行 Cursor。** 设置 `AGENT_CLI_CREDENTIAL_STORE=file`，把凭据以未加密形式存储在仅所有者可访问的文件中。使用跨运行持久的私有存储；打包的 Unix 构建在此模式下也会跳过系统 CA 加载。
- **修复了虚假的 MCP 连接错误。** 当工具或指令可用时，正常工作的服务器不再在 `/mcp` 和 `agent mcp list` 中显示为已断开。

### 可靠性和更新

- **Windows 更新抑制 PowerShell 进度输出。** 原生更新不再把 PowerShell 的进度条画在 CLI 上，也不再承担其下载开销。
- **修复了长 CLI 会话中的内存增长。** CLI 现在在每个 Checkpoint 只保存新的记录条目，而不是重新加载并重写完整对话。

## v2026.06.22

### Auto-review

- **Auto-review 运行模式。** Cursor 的 Auto-review 运行模式来到 CLI：它是 Allowlist 和 Run Everything 之间的中间地带，让 Agent 以更少的批准提示继续前进。Shell、MCP 和 Fetch 调用按顺序检查：allowlist 中的调用立即运行，可以放入沙箱的调用在沙箱中运行，其余交给分类器，由它允许该调用、尝试不同做法，或请你批准。用 `--auto-review`、在 `/config` 中，或用 `/auto-review` 打开它，并用 `permissions.json` 中的 `allow`/`block` 指令引导分类器。

### 工作区

- **命名的多目录工作区。** 一次在多个仓库上运行 Agent：会话中用 `/add-dir` 添加目录，用 `/save-workspace` 保存该集合，稍后用 `/load-workspace` 重新加载，或用 `--workspace` 限定范围启动。

### 命令

- **`/rewind` 默认开启。** 逐轮撤销时间线不再需要在 `/config` 中打开。
- **提示词中任意位置的 `/vim`。** 从任意位置触发内联 Vim 编辑，不只是空提示词。
- **更丰富的 `/copy`。** 从逐步选择器中挑选多步回复的单个步骤，把 Agent 的响应与你自己的消息一起复制，复制长回复时终端不会停顿。
- **共享机器上的 `/logs`。** 调试日志按用户写入，因此多用户主机不会遇到权限错误，`/logs` 路径在屏幕上停留更久。

### 终端体验

- **提示词历史按对话分开。** 上箭头召回的是你在本次会话中输入的内容，而不是所有聊天共享的单一历史。
- **任务列表显示正在运行的一切。** 前台 shell 和 Subagent 与后台任务一起出现在任务分页器中。
- **更稳定的渲染。** 一轮结束后 Mermaid 图保持绘制，长 shell 输出预览会被裁剪而不是换行，调整大小时屏幕只清除一次而不是两次。
- **你的草稿在恢复选择器之后仍然保留。** 取消 `/resume` 会保留你已经输入的内容。
- **计划编辑。** 修订计划时，Esc 返回 Vim 普通模式。

### 可靠性

- **长会话中更低的内存。** 修复了一处泄漏：每轮的 abort 信号一直持有对话状态。
- **缺少批准状态时不会崩溃。** 会话能够容忍尚未记录批准模式的凭据存储。

### MCP 和 Skills

- **编辑器提供的 MCP 服务器受到信任。** 通过 Agent Client Protocol（Zed 和其他编辑器）传入的 MCP 服务器会加载，而不是被静默丢弃。
- **MCP 工具在 Plugin 重新加载后仍然可用。** Plugin 重新加载后 MCP 租约会刷新，因此其工具不再卡在 "Not connected" 错误上。
- **通过符号链接找到的 Skills。** 发现 Skills 时，Skills 菜单会跟随符号链接目录。

### 安装和更新

- **可靠的通道切换。** 切换发布通道会在第一次尝试时生效，并立即获取目标通道的构建。
- **Windows 和 shim 修复。** Windows 启动器匹配带时间戳的版本目录，`cursor` shim 在把未设置变量视为失败的 shell 上不再报错。

### Enterprise 和团队控制

- &#x20;**Auto-review 的团队门控。** 管理员控制 Auto-review 是否对其成员可用。
- **稳定的自托管 worker 身份。** `worker start` 会等待 bridge 连接后再报告就绪，worker 为每个已认证用户保留稳定的逻辑 ID，因此共享机器上的集群能把正确的 worker 对应到正确的人。

## v2026.06.09

### 终端体验

- **更干净的编辑显示。** 文件编辑以无边框方式渲染（一个 `Editing`/`Edited` 标题加上 diff），并用记忆化的 diff 渲染，因此大型编辑不会拖慢 UI。
- **更快、更丰富的恢复选择器。** 会话元数据被缓存，因此即使在网络文件系统上 `/resume`（Ctrl+Y）也能快速打开，带有 Created 和 Last updated 列以及可靠的排序。
- **工作状态固定在提示词上方。** 进度、token 计数（可选的已用时间显示）和提示保持在一个稳定的位置。
- **长 shell 输出从顶部截断。** 你看到的是流式命令的最新输出，而不是最旧的。
- **换行的 URL 仍然可点击。** 长链接在每个换行上都重新发出超链接代码。
- **模型选择器显示 Max Mode 状态**，页脚的模型摘要去掉了冗余标签。

### 命令

- **`/fork`** 把当前对话分支为一个副本，并沿着另一条路径继续（别名 `/branch`、`/duplicate`）。
- **`/update`** 从会话内部原地更新 CLI。
- **`/context`** 可视化上下文窗口被什么占用，按类别分解。
- **`/logs`** 每个会话都会写入调试日志；`/logs` 显示路径并复制到剪贴板。
- **后台任务控制。** 任务查看器中的方向键导航，以及一个终止快捷键。

### 可靠性

- **HTTP/2 keepalive ping。** 流中途的静默连接停顿会在数秒内被检测并重试。
- **传输中断会自动重试。** 网络级取消和中止不再像按下了 Ctrl+C 那样结束这一轮。
- **冻结修复。** 消除了由布局反馈循环导致的硬 UI 冻结、首次运行引导之后的输入冻结，以及粘贴时被吞掉或重排的按键。
- **慢速网络上更快的首次绘制。** 团队设置查找不再阻塞交互式启动（高延迟连接上大约 2 秒）。
- **在受限网络上可用。** 当企业防火墙阻止配置服务时，应用功能默认值。
- **direnv 支持。** Agent 的 shell 会自动加载 `.envrc`，在交互式 shell 和由 Agent 派发的 shell 中都是如此。
- **拖放和粘贴修复。** 文件拖放路径完整到达，在 macOS 上粘贴复制的图像文件会附加实际图像。

### Enterprise 和团队控制

- &#x20;**管理员可以禁用无头模式。** 一项团队设置会在整个组织范围内阻止非交互式 CLI 使用。
- &#x20;**「Run Everything」控制。** 自动运行在整个产品中一致重命名；由管理员控制的自动运行把命令 allowlist 视为始终可用的基线。
- &#x20;**MCP 用户扩展治理。** 针对 MCP 服务器和工具的管理员「Allow User Extension」开关在运行时强制执行。
- &#x20;**团队管理的 MCP 服务器。** 集中配置的服务器可靠加载，并带有服务器组选择；MCP 工具策略与终端自动运行设置解耦。
- **通过 SSH 的 MCP OAuth。** 从 SSH 会话对远程 MCP 服务器进行身份验证时，CLI 会显示端口转发说明。

## May 20, 2026

- **Composer 2.5 是新 CLI 会话的默认模型**。
- **`/summarize`。** 从 `/compress` 重命名以匹配 IDE；`/compact` 和 `/compress` 仍作为别名，别名会直接执行。
- **通过 `~/.cursor/settings.json` 使用本地 Plugins。** 把 `enabled_plugins` 键指向本地 Plugin 文件夹；不需要市场。
- **多根 worker。** 自托管 worker 用可重复的 `--worker-dir` 标志跨越多个仓库，并保持稳定身份。
- **Rewind 保留图像。** 回退到较早的一轮会把该轮的图像附件还原到提示词。
- **更快的 `/plugins`。** 更快的 Plugin 详情，每个 Plugin 的 MCP 服务器会链接到 `/mcp` 管理。
- **MCP 工具在会话内刷新。** 从 `/mcp` 登录、启用或禁用服务器会立即更新 Agent 可用的工具。
- **浅色模式下 diff 可读。** 字符级 diff 高亮在浅色终端主题上清晰可辨。
- **Hooks 接受通过 stdin 传入的载荷。** 避免 argv 长度限制，并让载荷不出现在进程列表中。

## May 14, 2026

- **Vim 可视模式。** 带有删除和更改操作符的可视选择；当前 Vim 模式显示在页脚。
- **Ctrl+G 在 `$EDITOR` 中打开你的提示词。** 在真正的编辑器中编写长提示词，再把结果放回提示栏。
- **MCP 管理焕然一新。** `/mcp` 打开按服务器划分的详情视图：浏览工具 schema、登录、登出（清除已存储的 OAuth 凭据）、启用和禁用，全程无需离开会话。
- **无头运行等待 MCP 工具。** 修复了启动竞态：缓慢的 stdio MCP 服务器会从 `-p` 运行中缺失。
- **嵌套的 Rules 和 Skills。** 子目录中的 `.cursor/rules` 和 `.cursor/skills` 会在各处被发现，与 IDE 一致。
- **长对话立即重绘。** 完整重绘只渲染最近的轮次（用 `/full-conversation` 退出此行为），让旧会话中的调整大小和恢复保持快速。
- **Agent 流的代理支持。** 流式连接遵循 `HTTPS_PROXY`，使企业代理支持端到端完整。
- **自动运行在恢复后仍然有效。** 恢复对话会根据你的配置和团队策略重新计算 Run Everything。
- **模型变体在无头模式下有效。** 传给 `--model` 的 Fast 或高推理强度模型 slug 在 `-p` 模式中保留其参数。
- **早期按键会被缓冲**，并在 UI 加载后重放；修复了 SSH 和 tmux 上的过期输入。
- **tmux 焦点感知。** 在未聚焦的窗格中，提示词光标停止闪烁（需 `focus-events on`）。
- **病态 diff 安全渲染。** 非常长的行在语法高亮之前会被截断，因此压缩文件无法拖住 UI。

## May 7, 2026

- **Plugin 市场。** 通过 git URL 添加市场（`/plugin marketplace add`），按范围浏览和管理市场，查看每个 Plugin 来自哪个市场，并用 `--plugin-dir` 加载本地 Plugin 目录。从 Claude Code 导入的 Plugins 与原生 Plugins 一起出现。
- **Ctrl+L 像 shell 一样清屏**，清除屏幕和回滚缓冲区，会话继续运行。
- **从第一次按键起输入顺畅。** 修复了事件循环停顿（大型仓库上大约 1 秒），它会把早期按键攒成一批；关闭分页器不再重绘整个屏幕。
- **Skills 无处不在。** Skills 在交互式、无头和编辑器集成模式中加载；`/skill-name` 调用在 `-p` 打印模式中有效。
- **Linux 剪贴板图像粘贴。** 在 Wayland 和 X11 上粘贴图像。
- **`/model` 预输入。** 输入 `/model sonnet`，选择器会以预过滤状态打开，而不会提交你的提示词。
- **主题跟随你的终端。** 在终端的浅色和深色之间切换会自动重绘 CLI。
- **带持久重试的停顿检测。** 停顿的流会被检测并重试，而不是挂起；在 HTTP/2 行为异常的网络上，后端可以回退到 HTTP/1 传输。
- **`/mcp` 按范围分组。** 服务器组织在 User / Project / Team 之下，失败的服务器会显示其实际错误。
- **隐私加固。** 对于无存储团队，对话总结会失败并停止，以免把内容写入存储。
- **委派的 worker token。** 团队服务账户可以铸造短时、用户范围的 token，使自托管 worker 归属于特定团队成员运行。
- **中断会保留部分结果。** 停止一轮会保留进行中 shell 命令的输出，后续消息会把正在运行的工具移到后台，而不是终止它们。

## April 2026

- **`/rewind`：撤销 Agent 轮次。** 交互式时间线把文件和对话状态还原到任意较早的一轮，带有每轮文件 diff、仅还原对话，以及 `/undo`/`/restore` 别名（在 `/config` 中启用）。
- **桌面通知。** 当一轮完成，或 Agent 被你挡住时（批准、问题、sudo），在 iTerm2、Ghostty、Warp、Kitty 和 Terminal.app 上收到通知，支持 tmux/screen 透传和感知焦点的静音。批准通知包含待处理的命令。
- **交互式 `/config`。** CLI 内部的完整设置编辑器取代手工编辑 JSON，包括版本和账户页面。
- **自定义状态行。** 把 `statusLine` 指向你自己的命令，并在提示词页脚渲染其输出（带有实时 token 用量数据）。
- **`/btw` 旁路问题。** 在轮次中途问一个快速的只读问题；答案流入一个可关闭的浮层，带有完整对话上下文，并且从不触及你的对话历史。
- **更轻的启动。** MCP 加载移出首次绘制路径，服务器配置和模型在运行之间缓存，语法高亮包从 9.1 MB 缩小到 2.4 MB，一次性命令完全跳过 UI 初始化。
- **HTTP/2 连接池默认开启。** 并行工具调用和 Subagents 的吞吐量更好。
- **Vim 查找动作。** `f`/`F`/`t`/`T`/`;`/`,`，可与操作符组合（`df,`、`ct.`）。
- **图像粘贴快捷键。** Ctrl+V 粘贴剪贴板图像；粘贴复制的图像文件会解析出实际图像。
- **Markdown 和 diff 渲染。** 更紧凑的间距、带样式的标题、带上下文行的 git 风格统一 diff、准确的新文件 diff，以及修复了在许多主题中不可见的深色背景上的灰色文本（包括通过 SSH 的深色模式）。
- **页脚中的上下文。** 你输入时，工作目录、git 分支，以及你的分支上打开的 PR（作为可点击链接）保持可见。
- **`?` 快捷键备忘单、`/copy`、`/rename`。** 发现输入快捷键，复制过去的消息，并为会话命名。
- **一次一个问题。** 澄清问题逐个呈现，并带有自由形式的 "Other" 答案选项。
- **无头改进。** `status`/`about` 的 `--format json`，Plan Mode 可与 `-p` 一起使用，错误会记录在记录中，以便脚本检测失败的运行。
- **Hooks 可靠触发。** `afterAgentThought`/`afterAgentResponse` 事件在 CLI 中发出，并接受 Claude Code 格式的 hook 响应。
- **全局 MCP 服务器自动批准。** 来自 `~/.cursor/mcp.json` 的服务器无需逐工作区提示即可加载（项目级服务器仍需要批准），MCP 批准屏幕会隐藏 URL 中的密钥。
- **从 git URL 安装 Plugins。** 把仓库 URL 粘贴到 Plugin 搜索中即可直接安装。
- **信任和批准加固。** Worktrees 继承工作区信任，批准提示不会被意外的 Enter 跳过，反复使用后 CLI 会提议持久化 "Run Everything"。

## March 2026

- **CLI 中的 Subagents。** 并行 Agent 在交互式、无头和编辑器会话中本地执行，并带有实时状态，继承你的凭据、Rules 和批准策略。Max Mode 和自定义密钥设置会传播到 Subagents。
- **Run Everything 是独立开关。** 自动批准由 `/auto-run`（以及 `--force`/`--yolo`）控制，与 Shift+Tab 模式循环分开，团队配置的批准模式在无头运行中受到尊重。
- **Plugins 到来了。** 从 `/plugin` 浏览市场，并在用户或项目范围安装或卸载 Plugins；Plugin 的 Skills、斜杠命令、Subagents 和 MCP 服务器都会加载到会话中。
- **重试默认开启。** 断开和停顿的流在所有模式中自动重试，分类后的服务器错误（例如速率限制）以其真实消息渲染。
- **`permissions.json` 支持。** CLI 读取与 IDE 相同的终端/MCP allowlist 文件。
- **身份验证的企业代理支持。** 登录和 API key 验证经由 `HTTPS_PROXY` 路由，打通 Zscaler 风格的网络。
- **后台任务。** 双击 Ctrl+B 把正在运行的 shell 送到后台，无头运行会等待后台工作完成（为 `stream-json` 消费者发出事件），完成通知渲染干净。
- **自托管 worker 改进。** 不需要 sudo，修复了空闲断开，自动 token 刷新，Prometheus 指标端点，Windows 支持，自定义名称，以及代理支持。
- **来自其他工具目录的 Skills。** Skills 也会在 `.claude/skills`、`.agents/skills` 和 `.codex/skills` 中被发现。
- **远程 MCP OAuth 修复。** 严格的 OAuth 服务器（state/scopes）可与 `/mcp login` 一起工作，`${VAR}` 占位符在各处的 MCP 配置中展开。
- **`--image` 无处不在。** 在任何会话中附加图像，不只是打印模式。
- **编辑器集成。** 通过 Agent Client Protocol（Zed、JetBrains）进行模型和模式选择，尊重主机提供的 MCP 服务器，带有思考和文件位置的更丰富流式输出，以及 Skill 斜杠命令。
- **通过 SSH 检测到锁定的 macOS 钥匙串。** 明确的「unlock your keychain」消息取代了含糊的凭据失败。
- &#x20;**管理员控制。** 网络允许和拒绝列表无条件强制执行（包括 MCP 流量），自动运行尊重管理员禁用的沙箱，图像生成在运行前询问。
- **Bedrock 上的 Claude Sonnet 4.6。** 已加入自带密钥的模型列表。
- **更丰富的 hook 载荷。** 每轮 token 用量和稳定的会话 ID。

## February 2026

- **Git worktree。** `--worktree`/`-w` 在隔离的 worktree 中运行 Agent（用 `--worktree-base` 指定基分支），让 Agent 的改动离开你的检出；沙箱完全理解 worktree 布局。
- **修复了无头挂起。** 当以打开的 stdin 管道启动时（Node、Python、CI runner），`-p` 运行不再阻塞，部分不可达网络上的连接故障转移从大约 20 秒降到 8 秒。
- **`--yolo`。** 完全自主的运行，在信任、MCP 和命令批准上一致地自动批准。
- **使用你自己的凭据的 AWS Bedrock。** `agent bedrock` 配置访问密钥或团队 IAM 角色；在交互式、无头和编辑器会话中都可用。
- **自动重新连接。** 传输错误会重试，并显示可见的「Reconnecting…」指示。
- **审查 UI。** 统一的 `/changes`（Ctrl+R），带有只显示本次会话编辑的 Session 选项卡，用 `o`/`O` 在编辑器中打开 diff，审查时有 zen 开关。
- **供脚本使用的 token 用量。** `stream-json` 输出中的每轮输入/输出/缓存 token 总计和 `request_id`；无头记录写入与 Claude Code 兼容的 JSONL。
- **可重复的 `-H/--header`。** 以 curl 风格传入多个标头。
- **沙箱策略文件。** `~/.cursor/sandbox.json` 和 `.cursor/sandbox.json` 受到遵守（包括通过 SSH），网络访问有明确模式，当沙箱已启用但不可用时，CLI 会以明确原因快速失败。
- **Plan Mode 改进。** 持久的计划菜单，带有 Build Locally / Build in Cloud，计划内容会正确转移到 Cloud Agents。
- **更快的启动，更利落的轮次。** 并行化初始化，推迟更新检查，以及乐观的消息渲染。
- **渲染改进。** Markdown 表格按终端宽度换行，思考块渲染 markdown，shell 命令获得语法高亮，Mermaid 渲染更多图表类型。
- **跨终端的输入修复。** Vim `r`（替换字符）、Alt+Delete 删除单词、iTerm2 中的 Ctrl+J 换行、Windows Delete 键、Wayland 剪贴板支持，以及会话本地的提示词历史。
- &#x20;**Enterprise 归属控制。** 无论本地设置如何，管理员禁用的 commit/PR 归属都在 CLI 中强制执行。
- **被阻止的截屏有明确错误。** macOS 权限失败会显示警告和变通方法，而不是静默失败。

## January 2026

- **Hooks。** 会话开始/结束、带后续循环的停止 Hooks（自主的「继续下去」模式）、压缩前，以及 Subagent 生命周期 Hooks；读取并合并 Claude Code `settings.json` Hooks；管理员可以推送团队管理的 Hooks，优先级为 enterprise > team > project > user。
- **权限和沙箱与 IDE 对齐。** 三模式模型（Run Everything / Auto-Run in Sandbox / Ask Every Time）、交互式 `/sandbox` 菜单、沙箱命令的默认拒绝网络代理，以及在 CLI 中强制执行的团队管理员网络 allowlist。
- **`sudo` 可用。** 需要 sudo 的命令会触发掩码密码提示，通过锁定的本地套接字传递；模型永远看不到你的密码。
- **Plan Mode 和 Ask Mode。** `--mode plan|ask` 启动标志、`/plan <prompt>`、Shift+Tab 模式循环，以及问题、模式切换和计划审查的原生 UI，计划保存到磁盘。
- **流式输出时输入仍然响应。** 缓冲输入，推迟渲染流式输出，排队的后续消息立即提交。
- **交接给 Cloud Agents。** 输入 `&` 把实时对话转移到 Cloud Agent 并继续工作。
- **`--continue`。** 无需查找 ID 即可恢复你最近的聊天；记录持久化到磁盘，供工具和 Hooks 使用。
- **无头信任强制执行。** 在不受信任的工作区中，非交互式运行会失败并给出指导，除非传入 `--trust`（或 `--force`）。
- **MCP 管理。** `/mcp list` 是带有状态、登录、启用和工具浏览的交互式分页器；修复了 OAuth 登录；项目配置正确覆盖用户配置；从批准提示进行工具级 allowlist。
- **CLI 中的 Skills、Rules 和命令。** `/skills` 浏览和内联 `/skill-name` 调用、精选的内置 Skills，以及带内联编辑的交互式 `/rules` 和 `/commands` 浏览器。
- **`/model` 自动完成和 Max Mode。** 带友好名称的模糊模型搜索，加上 `/max-mode`，页脚指示器跨会话持久。
- **Markdown 渲染。** 对齐的表格、可点击的链接、水平线和文件审查中的词级内联 diff。
- **Subagent UI。** 并行 Subagents 的实时渲染，带有每个 Agent 的状态、流式活动和 token 计数。
- **编辑器集成的基础。** 通过 Agent Client Protocol（Zed 等）进行会话恢复、真正的 Agent 模式、细粒度工具渲染和自定义斜杠命令。
- **Windows 可靠性。** 在受限的 PowerShell 执行策略下可用，检测 Git Bash，修复基于浏览器的登录；剪贴板支持落地于 Windows 和 Linux。
- **身份验证和更新。** 过期 token 会提示重新登录，而不是崩溃；瞬时服务器错误不再把你登出；并行自动更新无法损坏安装；`--disable-auto-update` 关闭后台更新。
- **更快的启动。** 编译缓存和懒初始化把温启动时间缩短大约 10%。

---

## 站点地图

[所有文档页面概览](/llms.txt)
