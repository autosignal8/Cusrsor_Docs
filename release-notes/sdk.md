> 原文：https://cursor.com/docs/release-notes/sdk

# Cursor SDK 发布说明

陆续发布到 Cursor SDK 的最新功能、改进和修复，涵盖 npm 上的 `@cursor/sdk` 与 PyPI 上的 `cursor-sdk`。

## 1.0.40

- &#x20;**选择 SDK agent 在哪个团队下运行。** 如果你的登录属于不止一个团队，把 `teamId` 传入 `Agent.create(...)` 的选项，让该 agent 在这个团队下运行。同一选项也适用于 `Agent.list`、`Agent.get`、归档和删除调用，以及 `Cursor.me`、`Cursor.models.list` 和 `Cursor.repositories.list`。该选项不会被保存，因此在 `Agent.resume` 和后续调用上需要再次传入。仅 TypeScript。
- **拒绝工具调用的 Claude Code hook 现在会在本地 agent 中阻止这次调用。** 修复了一个 bug：`PreToolUse` hook（来自 Claude Code 的 `settings.json`）返回了 `permissionDecision: "deny"`，位置在 `hookSpecificOutput` 之下，但本地 SDK agent 仍然执行了这次工具调用。本地 agent 现在会把这种[嵌套响应](https://cursor.com/docs/reference/third-party-hooks.md#response-format-compatibility)视为拒绝。
- **本地 agent 存储打不开时，本地运行仍保留输出。** 如果一次运行进行到中途时本地数据库打不开，已经流式输出过文本的回合现在会以这段文本作为结果结束，而不是以错误结束。运行会继续在内存中进行，流会发出一条 `status` 消息，警告进程退出后这次运行的检查点可能不可用。

## 1.0.39

- **Python 运行会报告失败原因。** 运行在启动之后失败时，Python SDK 中的 `run.wait()` 现在会返回 `error_code` 和 `error_message`，这两个字段位于 `RunResult` 上，流的结果事件也带有同样的字段。以前 Python 只能看到一个错误状态。
- **本地 MCP 服务器和转录的修复。** 在本地运行里对单独一次 send 传入 `mcpServers`，不再丢掉从 `settingSources` 加载的 MCP 服务器。主目录缺失或只读时，本地 agent 也会继续写转录：设置了 `CURSOR_DATA_DIR` 时使用它，否则使用一个私有临时目录。

## 1.0.38

- **修复了只能在 Max Mode 下运行的模型导致本地 agent 失败的问题。** 用需要 Max Mode 的模型变体创建的本地 agent（例如 500k 上下文的选择）现在会自动带上 Max Mode，而不再因参数无效而失败，并在之后的 send 和 resume 中保持这一设置。`Cursor.models.list()` 用 `isMaxMode` 标记这些变体，`ModelSelection` 接受可选的 `maxMode`。
- **Computer use 的工具调用会出现在运行事件中。** agent 使用 computer use 时，工具调用事件现在会带上有类型的 `computerUse` 调用，而不再丢掉它。`args.actions` 列出每个动作（`click`、`type`、`scroll`、`key`、`drag`、`screenshot` 等），`result` 报告动作数量、耗时、屏幕稳定时间，以及可选的截图路径或错误信息。
- **`zod` 现在是对等依赖。** `@cursor/sdk` 现在使用你自己安装的 `zod`（3.x 系列中的 3.25 或更高版本，或 4.x），因此你的 schema 和 SDK 的 schema 共用同一份。导出的 schema 用 `zod/v3` API 构建，两个受支持的版本都包含该 API。如果项目里还没有 `zod`，请把它加进依赖。
- **启动较慢的 stdio MCP 服务器可以花更长时间来连接。** 本地 agent 现在会遵守 MCP 配置里 stdio 服务器上的 `startupTimeoutSec`，因此启动慢的服务器不再会在固定的 60 秒连接时限处失败。最长可以设为 5 分钟；没有这项设置的服务器仍默认 60 秒。
- **短暂的服务器故障不再让一次性 SDK 请求失败。** 当服务器报告一次可以安全重试的临时故障时，SDK 会在最多 1 秒的短暂随机延迟之后，把一次性请求重发一次，而不是立刻失败。agent 运行这类流式调用永远不会被重放。
- **后台 Subagent 会在启动它们的工具调用返回之后继续运行。** 本地 agent 在后台启动 Subagent 时，该 Subagent 不再会在启动它的工具调用一返回就被取消。
- **修复了沙箱运行因沙箱辅助程序不可执行而失败的问题。** SDK 的平台包现在会带着可执行权限安装沙箱辅助程序。
- **修复了使用命名云环境的 Python 云端启动。** 从 Python SDK 用命名云环境、且不带仓库来启动 Cloud Agent 时，不再会校验失败。

## 1.0.35

- **自定义工具能知道是哪个会话调用了自己。** `execute` 回调（属于 `local.customTools` 的一个条目）现在会收到 `context.sessionId`，也就是调用该工具的本地会话。用 `Task` 启动的 Subagent 会传入自己的 id，因此宿主可以为每个子 agent 分别保存状态。仅 TypeScript；运行时没有会话 id 时，该值不会被设置。
- **本地 agent 与 TypeScript 类型的修复。** 本地 agent 现在可以在启用 FIPS 的主机上运行，已有的本地 agent 存储会自动移到新位置。从同一进程或 VM 快照恢复的 agent 不再复用请求 ID 和会话 ID，因此并发运行不会冲突或卡住。`LocalSubagentInherit` 的类型声明现在无需未发布的内部包即可解析，它使用新导出的 `LocalSubagentResourceProviderFields` 和 `LocalToolExecutor` 类型。

## 1.0.34

- **Subagent 可以继承父级的执行器和工具限制。** 设置 `local.subagentInherit` 后，由 `Task` 工具启动的 agent（包括嵌套的）会使用与父级相同的自定义读取、写入和 shell 执行器，以及相同的上报工作区路径，还有相同的允许工具和排除工具。在 `Agent.create()` 上传入，或针对单次 `send` 覆盖。仅 TypeScript 本地 agent。如果不设置，Subagent 的行为与以前一样。
- **修复了长时间运行的 agent 在连接断开时请求停滞的问题。** 当 agent 在各次运行之间空闲时，SDK 现在会在 29 秒后关闭未使用的 HTTP/2 连接。复用一条大约静默了一分钟的连接之前，也会先检查该连接。请求不再被写进服务器已经关闭的连接；以前这种请求会一直挂起，直到停滞超时将其中止。
- **停滞的连接可以重试，并且有上限。** 连接停滞时，运行会以可重试的 `NetworkError` 失败，错误码为 `connection_stalled`；连续的停滞重试会在 3 分钟后停止，而不是无限重试。
- **随 SDK 安装的依赖更少。** 安装 `@cursor/sdk` 不再把 `@connectrpc/connect-node` 或 `undici` 5.x 加进项目的依赖树，因此安装体积更小，你也不会再从这些包收到版本冲突或审计警告。

## 1.0.32

- **在 agent 等待后台 Subagent 时引导它。** 当 agent 结束当前回合、转去等待后台 Subagent 时，`run.steer(text)` 以前会解析为 `revert_to_followup`，于是你的消息要等到那项工作完成。现在这次 steer 会立刻作为下一回合执行，后台结果仍会在之后到达。仅 TypeScript 本地运行。
- **知道某一步何时已经列完工具请求。** 模型列完某一步的工具调用后，`onDelta` 现在会收到一条 `tool-requests-listed` 更新，其中带有 `callCount`，而这些工具可能仍在运行。用它来判断一步中的每个工具调用是否都已开始，例如在你的 UI 里把这一步的工具调用分组或批量处理。
- **Cloud Agent 创建方面的修复。** 对于 Cloud Agent，第一次 `send()` 会在服务器上创建该 agent。当 agent id 由 SDK 生成时，这次调用不再因 id 冲突而失败：它会换一个新 id 重试，或者如果它自己更早的那次创建已经成功，就继续使用该 agent。进程从同一快照被恢复不止一次时，SDK 生成的 agent id 和 run id 也不再重复。你自己固定的 id 仍会报告这一冲突。
- **Bridge 会忽略你项目中的 `.env` 和 `bunfig.toml`。** 独立的 `cursor-sdk-bridge` 可执行文件不再从工作目录加载 `.env` 或 `bunfig.toml`。SDK 启动 bridge 时，这个工作目录通常就是你的项目。检出目录中的文件不能再改变 bridge 的端点、token 或预加载代码。

## 1.0.31

- **后台 Subagent 会把结果回报上来。** agent 在后台运行 Subagent 时，其结果现在会作为同一次运行上的后续回合返回给父级，而不再在父回合结束时被丢掉。`run.stream()` 会持续产出这些回合，`run.wait()` 会在这些回合之后才返回。适用于 TypeScript 和 Python 的本地 agent。
- **为自定义工具添加注解。** `annotations`（位于 `local.customTools` 条目上）会把 MCP 工具注解（`title`、`readOnlyHint`、`destructiveHint`、`idempotentHint`、`openWorldHint`）传给模型。它们只是描述性提示；SDK 不会强制执行。仅 TypeScript。

## 1.0.30

- **长时间运行的本地 agent 会保持凭据有效。** TypeScript 和 Python 中的本地运行会在短期访问 token 过期之前刷新它，因此运行超过一小时的 agent 不再因认证错误而失败。云端运行不受影响。

## 1.0.29

- **自定义工具上的输出 schema。** TypeScript 的 `outputSchema` 和 Python 的 `output_schema` 为自定义工具的结构化结果声明一份 JSON Schema，并作为该工具的 MCP 输出 schema 告知模型。结果不会对照它做校验。仅本地 agent。

## 1.0.28

- **以单个文件的形式发布 SDK。** 在 Bun 下，`@cursor/sdk` 现在会解析成一个扁平的单文件包，因此 `bun build --compile` 无需改动 import 即可工作，也不会再出现 `Cannot find module './986.js'`。`@cursor/sdk/bundled` 和 `@cursor/sdk/bundled/sqlite` 将同一构建作为显式入口暴露出来，供 esbuild 等其他单文件打包器使用。仅 TypeScript。

## 1.0.27

- **限制 agent 的工具集。** `tools` 用允许列表限定提供给模型的内置工具（`[]` 表示仅文本），`disallowedTools` 则在保留其余工具的同时移除指定工具。两者都接受 `"read"` 这样的公开名称，或 `"shell"` 和 `"mcp"` 这样的能力分组，TypeScript 和 Python 都支持（`tools`、`disallowed_tools`）。目前仅限本地 agent，并且不会跨 `resume` 持久化。
- **在 TypeScript 中通过浏览器登录。** `Cursor.auth.login()` 会打开浏览器登录，签发一个 API 密钥，并存入 `~/.cursor/sdk/auth.json`；`Cursor.auth.status()` 和 `Cursor.auth.logout()` 补齐其余能力。登录之后，`Agent.create()` 和 `Cursor.*` 读取无需 `apiKey` 或 `CURSOR_API_KEY` 即可工作。
- **本地 agent 的用量和费用。** TypeScript 的 `agent.getUsage()` 和 Python 的 `agent.get_usage()` 现在对本地 agent 也同样可用，返回按回合拆分的明细。传入上一次结果中的 `runId`，可以把范围缩小到单个回合。
- **以 Cursor GitHub App 的身份打开 PR。** TypeScript 的 `cloud.openAsCursorGithubApp` 和 Python 的 `open_as_cursor_github_app` 控制 PR 的作者身份。服务账号密钥默认使用该 App；用户密钥默认使用密钥所有者。
- **多根的本地工作区。** 传入 `local.dirs`，从多个文件夹加载 Rules、Skills 和项目上下文；`cwd` 仍然是唯一的主工作目录。这取代了 `cwd` 的数组形式，那种形式始终只使用第一个条目。
- **更清晰的 Python 错误。** 以前只呈现为光秃秃的 “internal error” 的失败，现在会带上底层的消息和错误码。
- &#x20;**管理员命令拒绝列表适用于本地运行。** 与你的团队管理员拒绝列表匹配的 shell 命令会在执行前被拒绝，并附带一条策略消息，包括那些会跳过批准提示的路径。

## 1.0.26

- **在第一次 send 之前预热本地工作区。** `platform.prewarmLocalWorkspace(options)` 会提前解析 Rules、Skills、MCP 服务器和 ignore 文件，因此针对该工作区的第一次 `send()` 可以马上开始。它返回一个在关闭时调用的释放函数。
- **控制工作区扫描的缓存保留多久。** `configureCursorSdk({ local: { workspaceScanCacheTtlMs } })` 设置工作区扫描的缓存存活时间，环境变量 `CURSOR_RIPWALK_CACHE_TTL_MS` 为托管部署设置同一个值。稳定检出上长期运行的服务器现在可以跳过反复的重新扫描。
- **自定义工具运行时不再出现批准提示。** 通过 `customTools` 传入的、由宿主定义的工具，在沙箱或自动审查的本地运行上，不再会因交互式批准错误而失败。拒绝规则和沙箱限制仍然适用。
- **经过签名的 macOS 二进制文件。** `@cursor/sdk` 的 macOS 平台包现在附带经过代码签名的二进制文件，因此 Gatekeeper 和端点安全工具不再拦截它们。
- **更清晰的 Python 异常层次。** `PermissionDeniedError`、`BadRequestError` 和 `InternalServerError` 现在直接继承 `CursorSDKError`，而不再分别继承 `AuthenticationError`、`ConfigurationError` 和 `NetworkError`，因此 `except` 块捕获到的就是名称所表示的异常。
- **修复了 Python 中间歇出现的启动失败。** 大约每 64 次 agent 启动中有 1 次会在到达第一次 send 之前失败。现在启动是可靠的。

## 1.0.25

- **按需获取计费用量和费用。** TypeScript 的 `agent.getUsage()` 和 Python 的 `agent.get_usage()` 会返回 Cloud Agent 的 token 用量、计费费用以及按运行拆分的明细，而且 `Agent.getUsage(agentId)` 无需句柄也能调用。费用由服务器算出，包含折扣，并在运行结束后不久结算。目前仅限云端；本地运行会抛出带类型的配置错误。

## 1.0.24

- **TypeScript 和 Python 现在一起发布。** 从 1.0.24 起，npm 上的 `@cursor/sdk` 与 PyPI 上的 `cursor-sdk` 来自同一次发布，并共用一个版本号。Python 的发布不再落后于 TypeScript。
- **长时间运行的流更可靠。** 重负载运行上的流式响应不再会在中途断开。以前在长回合上，这会表现为 Python 客户端中的网络错误。

## 1.0.23

- **云端运行可以按每次 send 传入环境变量。** 传入 `send(prompt, { cloud: { envVars } })`，把环境变量限定在单次运行内，包括创建 agent 的第一次 send。`Agent.create({ cloud: { envVars } })` 仍然用来设置 agent 范围的默认值。
- **失败运行上的错误详情。** 失败的本地运行和云端运行现在会暴露带 `message` 和 `code` 字段的结构化错误，因此你不必解析日志就能知道哪里出了问题。`run.wait()` 的行为与以前相同。
- **Python 中的 token 用量。** 运行流会发出带每回合 token 计数的有类型 `usage` 消息，累计总量可以在 `run.usage` 和 `RunResult.usage` 上取得，与 1.0.22 起的 TypeScript 一致。
- **更稳固的本地运行历史。** 磁盘上的运行历史现在可以经受被中断的写入，从而修复一类失败：进程崩溃后留下无法恢复的运行。
- **修复了 Bun 上的流式停滞。** Bun 下的运行流不再会在长响应上停滞。

## 1.0.22

- **每次运行都有 token 用量。** 本地运行会发出每回合的 `usage` 事件，位于 `run.stream()` 上，累计总量位于 `run.wait()` 上。云端运行在自己的流和 `wait()` 结果上呈现同样的用量，总量会为已分离的本地句柄保留下来，因此重新连接的进程仍然能拿到这些总量。

## 1.0.21

- **在 Bun 下运行 agent。** `agent.send()` 现在可以在 Bun 下工作，行为与 Node 相同。这也修复了全新的 Node 安装可能缺少某个必需依赖的问题。
- **Python 中更友好的运行时名称。** 列表 API 和 `get_run` 接受 `runtime="cloud"`、`"local"` 和 `"auto"`，与文档中的取值一致。

## 1.0.20

- **SDK 在 Bun 下可以正常导入。** 在 Bun 下导入 `@cursor/sdk` 不再崩溃。在 Bun 下运行 agent 的能力随后在 1.0.21 中提供。


---

## 站点地图

[所有文档页面概览](/llms.txt)
