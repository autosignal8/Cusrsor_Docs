> 原文：https://cursor.com/docs/grok-bot/inference-hooks

# Grok Bot inference hooks

Inference hooks 把每一个受管辖的 Grok Bot 模型请求发送到你的组织自己运行、或通过安全供应商使用的 AI 安全服务器。Cursor 为每个请求签名，等待允许或拒绝的裁决，并在拒绝时停止该请求，因此你的策略会在模型看到之前检查提示词以及 Grok Bot 收集的内容。打开 [Validate Tool Calls](https://cursor.com/docs/grok-bot/inference-hooks.md#validate-tool-calls) 后，你的服务器还会在每次响应中的工具调用运行之前检查它们。Grok Bot 的其他控制见 [Grok Bot 安全](https://cursor.com/docs/grok-bot/security.md)。

### Beta

Inference hooks 处于 beta，所有 Enterprise 团队都可用。团队管理员可以从仪表盘设置它们。它们只覆盖 Grok Bot。见[覆盖范围](https://cursor.com/docs/grok-bot/inference-hooks.md#coverage)。

## 工作原理

- **每个受管辖的模型请求都会被检查。** 在 Grok Bot 调用模型之前，Cursor 向你的端点发送一个已签名的 `prompt` 帧，其中包含到目前为止的对话，并扣住该请求，直到裁决到达或超时。一个回合如果多次调用模型，例如在工具调用之间，则每次调用发送一个帧。
- **由你的服务器决定。** `allow` 让请求发往模型。`deny` 会停止它、结束该回合，发送该消息的成员会看到一条[阻止通知](https://cursor.com/docs/grok-bot/inference-hooks.md#what-members-see)。
- **失败遵循你的模式。** 当你的端点超时、返回错误，或回答的不是裁决时，由[模式](https://cursor.com/docs/grok-bot/inference-hooks.md#modes)决定请求是继续还是被阻止。失败永远不算作拒绝。
- **只有打开 Validate Tool Calls 时，工具调用才会在运行之前被检查。** `prompt` 帧携带即将发给模型的内容，因此工具调用及其结果只有在工具已经运行之后才会出现在那里。[Validate Tool Calls](https://cursor.com/docs/grok-bot/inference-hooks.md#validate-tool-calls) 增加一个 `tool_call` 帧，你的服务器在调用运行之前评判它。Grok Bot 给成员的回复在发送之前从不检查。拒绝永远不会撤销已经发生的操作。

## 设置 inference hooks

你需要是 Enterprise 团队上的团队管理员。

### 准备你的安全服务器

在安全供应商的控制台中创建 inference hooks 集成，或托管一台满足[端点要求](https://cursor.com/docs/grok-bot/inference-hooks.md#endpoint-requirements)的自己的服务器。设置对话框的 **Security Vendor** 列表包含带设置清单的供应商预设。

### 打开 Inference Hooks

在 Cursor 仪表盘中选择团队，然后从 Grok Bot 页面的 **Security & Compliance** 分区打开 [Inference Hooks](https://cursor.com/dashboard/bot/inference-hooks)。

### 添加端点

点击 **Set Up Endpoint**，选择你的供应商或 **Custom Endpoint**，并输入 **Endpoint URL** 以及服务器需要的任何 **Custom Headers**，例如它的 API 密钥。保存时 Cursor 会检查该 URL。

### 复制签名机密

第一次保存并设置端点时，会生成一个以 `whsec_` 开头的签名机密，并且只显示一次。把它粘贴到供应商的 webhook 机密字段，或你服务器的配置中，以便它能验证每个请求。

### 测试连接

点击 **Test Connection**。Cursor 会发送一个合成请求，并显示裁决或检查失败的原因。这里的拒绝意味着你的策略会阻止测试提示词，因此在强制执行之前先修正它。

### 从 Shadow mode 开始

新配置从 **Shadow Mode** 开始。打开 **Enforce Verdicts** 以开始发送。你的端点会收到每一个被覆盖的请求，拒绝会记录到审计日志，但没有任何东西被阻止。如果你的团队有工具调用检查，新配置还会以打开 **Validate Tool Calls** 开始。然后工具调用会等待每个裁决，即使在 Shadow mode 中也是如此。一旦裁决看起来正确，把 **Failure Handling** 下的 **Mode** 设为 **Allow the Request** 或 **Block the Request**。

设置更改大约在一分钟内生效，已经在途的请求会在旧设置下完成。

当端点已保存、**Enforce Verdicts** 已打开，并且 Grok Bot 被覆盖时，在任何模式和任何推出百分比下，Grok Bot 都会跳过一些它本来会在回合之前提前做的模型调用，因此回复开始得可能更慢。

### 端点要求

- **使用 443 端口上的 `https://` URL。** 主机必须可公网路由，并出示来自公共证书颁发机构的证书。Cursor 拒绝私有、环回和运营商级 NAT 地址。
- **重定向和隧道不起作用。** Cursor 不跟随重定向，并把重定向视为失败的检查。反向隧道主机不被接受。
- **不要把机密放进 URL。** URL 不能携带凭证、查询字符串或片段，因为管理员会以纯文本看到它。把凭证放在自定义标头中，它们会被加密存储。URL 限制为 2,048 个字符。
- **用裁决回答每个请求。** 返回 HTTP 200 和一份 JSON 裁决，包括对 Test Connection。见[裁决](https://cursor.com/docs/grok-bot/inference-hooks.md#verdicts)。

### 自定义标头

- **你最多可以添加 16 个标头。** 名称使用字母、数字和连字符，不含下划线，最多 128 个字符。值是可打印 ASCII，最多 8,192 个字符。
- **值是只写的。** 保存之后，页面只显示标头名称。更改任何一个标头都意味着重新输入每一个值。
- **新的 URL 会清除已保存的值。** 标头值永远不会发往它们不是为之保存的端点。更改 URL 之后，在 **Edit** 中重新输入它们。在你这样做之前，请求发出时不带这些标头。
- **有些标头归 Cursor 所有。** 你不能添加 `Host`、`Cookie`、`User-Agent`、`Accept-Encoding`、`Transfer-Encoding` 这类连接标头、`X-Real-IP` 这类客户端地址标头，或以 `Content-`、`Proxy-`、`X-Forwarded-`、`Webhook-` 或 `X-Cursor-` 开头的任何名称。

### 签名机密

每个请求都携带 [Standard Webhooks](https://www.standardwebhooks.com) 标头，以便你的服务器可以拒绝任何不是 Cursor 发送的内容：

| 标头 | 值 |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `webhook-id` | 该帧的 `request_id` |
| `webhook-timestamp` | 以秒为单位的 Unix 时间 |
| `webhook-signature` | `v1,` 然后是 `{webhook-id}.{webhook-timestamp}.{raw body}` 的 base64 HMAC-SHA256，密钥是机密中 `whsec_` 之后经 base64 解码的部分 |

每个请求恰好携带一个签名。要轮换机密，在 **Request Signing** 下点击 **Rotate Secret**。新机密只显示一次，Cursor 大约在一分钟内开始用它签名，因此在此之前继续接受先前的机密。轮换对[断路器](https://cursor.com/docs/grok-bot/inference-hooks.md#circuit-breaker)而言算作一次设置更改。

### Test Connection

Test Connection 发送一个已签名的 `prompt` 帧，`source.application` 设为 `config-test`，一条不含用户内容的固定消息，并且没有成员、会话或模型。它用你保存的机密签名，并等待你保存的裁决超时。从端点对话框运行时，它使用表单中的 URL 和标头；从页面运行时，使用已保存的那些。

打开 **Validate Tool Calls** 后，Test Connection 还会发送一个 `tool_call` 帧，其中有一个名为 `config_test` 的合成调用。它显示两个结果：**Prompt request** 和 **Tool call request**。只要提示词请求得到裁决，就会出现工具调用结果。那里的允许意味着你的服务器做出了回答，而不是它检查了该调用。

| 结果 | 含义 |
| ----------------------- | ------------------------------------------------------------------------- |
| Allow 或 deny | 你的服务器返回了裁决。在强制执行之前先解决拒绝 |
| URL rejected | URL 不满足端点要求 |
| Private or internal IP | 主机不是可公网路由的地址 |
| Timeout | 在裁决超时内没有裁决到达 |
| Transport error | DNS、连接或 TLS 失败 |
| Non-200 status | 你的服务器用 200 以外的状态回答，包括重定向 |
| Unparseable response | 正文不是 JSON 裁决、被压缩了，或大于 64 KiB |
| Signing secret required | 先保存配置。保存会生成机密 |

## 模式

**Enforce Verdicts** 打开或关闭发送。关闭时，Cursor 什么也不发送，并保留你的设置。打开时，**Mode** 决定检查无法完成时会发生什么：

| 模式 | 拒绝时 | 检查失败时 |
| ------------------------- | ------------------------ | ------------------------------------------------- |
| **Shadow Mode**（默认） | 放行该请求 | 放行该请求 |
| **Allow the Request** | 阻止该请求 | 未经检查即放行该请求（失败时放行） |
| **Block the Request** | 阻止该请求 | 阻止该请求（失败时关闭） |

在 Allow 和 Block 模式中，模型请求会等待裁决。在 Shadow mode 中，Cursor 在相同的时刻发送相同的 `prompt` 帧，但是在后台：模型请求不等待，成员什么也看不到，拒绝和失败会记录到审计日志。负载很重时，Cursor 可以跳过一次 Shadow `prompt` 检查，被跳过的检查不写审计行。[工具调用](https://cursor.com/docs/grok-bot/inference-hooks.md#validate-tool-calls)在每一种模式中都等待裁决，包括 Shadow。

当服务器超时、用 200 以外的状态回答、返回的正文不是 JSON 裁决或大于 64 KiB、返回的 `action` 不是 `allow` 或 `deny`，或无法到达时，检查失败。大于 64 MiB 的帧也算作失败。

一旦 Cursor 有了你的设置，Block mode 会阻止每一个无法完成的检查，包括原因在 Cursor 这一侧时。如果 Cursor 完全无法加载你团队的 inference hook 设置，它无法判断你选择了哪种模式，因此请求会继续，审计日志把它记录为失败时放行，原因为 `config_unavailable`。工具调用也会继续，但 Cursor 只在你的设置加载并显示这些调用本应被检查之后才记录它们。

### 裁决超时

**Verdict Timeout (ms)** 接受 1 到 10,000 ms，默认 5,000。它覆盖整个交换：连接、TLS 握手、请求和响应。如果连接失败，Cursor 会在同一超时内重试一次，使用相同的 `webhook-id` 和签名，因此你可以按 `webhook-id` 去重。你的服务器响应之后，Cursor 从不重试。它自己的处理在超时之上最多再增加大约半秒。

### 推出

**Requests Inspected (%)** 在你把它接入时，只把被覆盖请求的一部分发送到你的端点。这个选择是粘性的，因此一个回合背后的所有请求，包括 subagent 和工具调用，要么全部检查，要么全部跳过。份额之外的请求即使在 Block mode 中也会未经检查地继续，0% 会关闭检查。

### 断路器

你的服务器负责的持续失败，例如超时、错误状态、TLS 错误和格式错误的裁决，会触发断路器。DNS 和连接错误、Cursor 拒绝的 URL，以及超过 64 MiB 的帧算作失败，但永远不会触发它。

- **触发期间，Cursor 停止发送。** 你的模式适用于每一个被覆盖的请求：Block mode 全部阻止，Allow 和 Shadow 未经检查地放行。审计日志每次触发记录一个 `inference_hooks_circuit_breaker_tripped` 事件，触发期间没有按请求的事件。
- **恢复是自动的。** 从触发后 10 分钟开始，Cursor 定期向你的服务器发送 Test Connection 请求。允许或拒绝都会重置断路器。
- **任何设置更改都会停止自动恢复。** 这包括轮换机密。端点修好之后，点击 **Reset**，或把 **Enforce Verdicts** 关掉再打开。

### Cursor 暂停

Cursor 可以在紧急情况下为一个团队或所有人暂停 inference hooks。暂停期间，Cursor 不发送帧，被覆盖的请求和工具调用继续进行，审计日志把其中大多数记录为失败时放行，原因为 `kill_switch`。Test Connection 显示 “Inference hooks are temporarily paused by Cursor.”

## Validate Tool Calls

**Validate Tool Calls** 在每次调用工具的模型响应之后，向你的端点发送一个 `tool_call` 帧。该响应中的每一次调用都等待裁决。开关位于 **Enforce Verdicts** 之下，在 Enforce Verdicts 关闭时不起作用，并大约在一分钟内生效。

- **它是可选的。** 当 Cursor 为你的团队打开工具调用检查时，开关会出现，按请求的审计事件开始标明它们的帧类型。其他什么都不变。在此之前保存的配置会保持 Validate Tool Calls 关闭，直到管理员打开它。在此之后第一次保存的配置会以打开它开始。
- **一个帧和一个裁决覆盖一次响应。** 帧按模型发出的顺序列出该响应中的每一次调用，从不入帧的工具除外，例如回复。`allow` 会运行它们全部。你不能允许一些调用而拒绝另一些。
- **拒绝会拒绝每一次调用。** 这包括被留在帧外的调用，例如回复。每个被拒绝的调用得到结果 “Blocked by your organization's policy before it ran”，该回合以与被拒绝的提示词相同的[阻止通知](https://cursor.com/docs/grok-bot/inference-hooks.md#what-members-see)结束。
- **它共享你的设置。** 工具调用帧使用与提示词帧相同的端点、签名机密、模式、裁决超时、推出、断路器和 Blocked Message。没有单独的模式，因此为工具调用选择 **Block the Request** 也会阻止检查失败的提示词。
- **工具调用在每一种模式中都等待。** 每次带有入帧调用的响应都会给你的端点增加一次往返。在 Shadow mode 中，调用会等待裁决，最长为裁决超时，然后无论允许还是拒绝都会运行。Shadow 失败计入断路器。
- **Subagent 和计算机操作也会被检查。** Subagent 响应得到相同的检查。计算机操作期间，每个浏览器动作单独检查，拒绝会停止该次计算机操作运行。鼠标和键盘动作不入帧。

只有在你的安全服务器能处理 `tool_call` 帧之后，才打开 Validate Tool Calls。协议告诉服务器对它们不认识的帧类型回答 `allow`，因此不支持的服务器会让每一次调用都运行，而检查看起来像是已强制执行。在这些帧上出错的服务器会在 Block mode 中停止每一次工具调用，这些失败计入断路器，与提示词帧共用。

## 成员看到什么

当你的服务器拒绝一个请求时，Grok Bot 桌面和移动应用会向发送被阻止消息的成员，在他们自己与 Bot 的对话中显示一条阻止通知。在移动端，通知位于消息框上方。它显示：

- 标题 **Blocked by your organization's policy**。
- 你的服务器的 `deny_reason`，后面是你的 **Blocked Message**。
- 带有 **Edit** 按钮和 **Reset** 按钮的指导。**Edit** 把被阻止的消息从对话中取出，并把文本放回消息框，除非成员那里已经有草稿，这样他们可以发送不同的内容。**Reset** 清除对话。移动应用和较新版本的桌面应用会先请成员确认。当 Cursor 无法把对话回滚到被阻止消息之前的位置时，只出现 **Reset**。

**Blocked Message** 最多接受 500 个字符，默认为 “Your organization's security policy blocked this request.” 关闭它则只显示你的服务器的 `deny_reason`。用它告诉成员下一步做什么，例如联系谁。

重置之后，Bot 不再看到该对话中更早的消息，并停止它为此运行的后台助手。它已经开始的其他工作，例如 cloud agents 和后台命令，会继续，并且可以回来报告。它的记忆、文件、例程和设置会保留。Edit 把对话回滚到被阻止消息之前的位置。两者都不会撤销 Bot 已经采取的操作，例如它写入的文件。

如果重置之后对话立刻再次被阻止，通知没有按钮。它说明被阻止的内容可能在 Bot 的记忆或文件中，并请成员在那里移除，或向管理员求助。

当检查在 Block mode 中失败时，桌面应用显示 **Your organization's policy check is unavailable** 以及 “Your organization requires a policy check before the model responds, and the check didn't complete. Try again later, or contact your admin if this keeps happening.” 移动应用显示 “Your organization's policy check is unavailable. Try again later, or contact your admin if this keeps happening.” 不会显示你的 Blocked Message，也没有 Edit 或 Reset。

其他地方显示得更少：

- **共享 Bot 只通知发送者。** 在共享 Bot 中，通知及其按钮只给发送被阻止消息的队友，在他们自己与 Bot 的聊天中，Edit 或 Reset 只改变该聊天。Bot 的所有者看不到拒绝原因或你的 Blocked Message。房间和群聊没有 Edit 或 Reset 按钮。
- **Slack 在对话中回复。** Bot 回答 “Your organization's policy blocked this conversation, so I can't reply,”，并在一对一私信中引用拒绝原因和你的 Blocked Message，频道讨论串和群组私信从不显示这些。回复提供 **Remove blocked message**（作用类似 Edit）和 **Reset**（会先请求确认），或者当 Cursor 无法回滚对话时只有 **Reset**。任何可以使用该 Bot 并已关联 Cursor 账户的人都可以点击它们。被移除的消息在 Slack 中仍然可见，但 Bot 不再看到它。重置之后立刻再次阻止时没有按钮，Block mode 中失败的检查得到的是 Bot 的通用错误回复，而不是策略通知。
- **在其他地方，移动应用显示普通失败。** 在群聊以及不是该成员自己的对话中，它显示 “Bot failed to respond”，并带有 “Blocked by your organization's policy. Send a different message, or contact your admin if this keeps happening,”，没有拒绝原因、你的 Blocked Message 或按钮。
- **其他 Cursor 应用只显示标题。**
- **较旧的桌面版本没有按钮。** 视版本而定，通知显示拒绝原因和你的 Blocked Message，并带有指导 “Send a different message, or start a new chat if this keeps happening,”，或者只有 “Blocked by your organization's policy. Send a different message, or contact your admin if this keeps happening.”
- **后台助手停止时没有通知。** 当 Grok Bot 启动的助手被阻止时，Bot 得知是你组织的策略停止了它，但没有拒绝原因。
- **例程停止并记录这次阻止。** Cursor 仪表盘中的运行历史显示 **Blocked by your organization's policy**，或在 Block mode 中检查失败时显示 **Your organization's policy check is unavailable**，带有固定说明，没有拒绝原因。Grok Bot 应用的例程列表只显示该次运行失败。
- **电子邮件、Linear 和其他已连接渠道得不到回复。**

被阻止的消息会留在对话中，直到有人把它取回或重置对话。在此之前，Grok Bot 会把它随以后的每个请求一起发送，因此你的服务器每次都会再次评判它。扫描整份记录的策略会持续拒绝该对话。评判最新用户消息的策略，会在成员发送不同内容后让它继续。

请求被允许时，或你处于 Shadow mode 时，成员什么也看不到。Shadow mode 也会把他们的对话发送到你的端点，因此在打开 **Enforce Verdicts** 之前，告诉成员你的组织会检查 Grok Bot 请求。

## 覆盖范围

**被覆盖：** 你的团队成员的 Grok Bot 对话背后的模型请求。这包括回合的每一步、subagent、Grok Bot 为长对话所写的摘要，以及例程。共享 Bot 或房间遵循其团队的 inference hook。个人 Bot 遵循其所有者所选团队的 hook。每个请求都有自己的帧，包含到目前为止的对话。

**不被覆盖：**

- **模型响应不被检查。** 打开 [Validate Tool Calls](https://cursor.com/docs/grok-bot/inference-hooks.md#validate-tool-calls) 后，其中的工具调用会被检查，从不入帧的工具除外。见[工作原理](https://cursor.com/docs/grok-bot/inference-hooks.md#how-it-works)。
- **请求中不属于对话的部分不进入。** 系统提示词、工具定义和模型的隐藏推理永远不会发往你的端点，Cursor 也会省略它为模型添加的大部分文本，例如提醒和时间戳。
- **辅助模型调用不会发送。** 这些调用是支撑对话而不是延续对话，例如撰写聊天标题、Auto Review，以及保存记忆。
- **其他 Cursor 功能不被覆盖。** 这包括 Cloud Agents、编辑器中的 Agent、CLI、Agent SDK、Tab、嵌入和本地模型。
- **工具一旦运行之后做了什么不被检查。** Validate Tool Calls 在每个入帧调用运行之前评判它，而不是评判随后发生的事，例如脚本运行的命令、已连接应用的副作用，或成员的 Grok Bot 计算机发出的网络流量。工具的输出只有作为以后某个 `prompt` 帧中的工具结果才会到达你的端点。用[网络策略](https://cursor.com/docs/grok-bot/security.md#network-policy)和 [Auto Review](https://cursor.com/docs/grok-bot/security.md#approvals-and-auto-review) 控制操作。
- **语音通话不被覆盖。** 它们的提示词和工具调用都不会到达你的端点。

## 请求格式

Cursor 把每个帧作为带有紧凑 JSON 正文的 HTTPS `POST` 发送。标头名称是小写的。

| 标头 | 值 |
| ------------------------------------------------------ | ---------------------------------------------------------------------------------------- |
| `content-type` | `application/json` |
| `user-agent` | `cursor-inference-hooks/1` |
| `accept-encoding` | `identity` |
| `webhook-id`、`webhook-timestamp`、`webhook-signature` | 见[签名机密](https://cursor.com/docs/grok-bot/inference-hooks.md#signing-secret) |
| 你的自定义标头 | 按已保存的内容 |

`host` 和 `content-length` 这类标准标头也会随每个请求一起到来。

```json
{
  "type": "prompt",
  "request_id": "req_6b1f0c2e-4d7a-4f1e-9a53-2c8d7e41b9a0",
  "tenant_id": "team_4mQvT8yLcN2pR6",
  "actor": {
    "type": "user",
    "id": "user_8hJw3KzP5sXq1V7nB2cF9mD4tR",
    "email_address": null
  },
  "source": { "application": "grok-bot" },
  "session_id": "3f2c9a71-58e4-4b0d-9c6a-0d1e7b25a4f8",
  "model": "<Cursor model identifier>",
  "messages": [
    {
      "role": "user",
      "content": [
        {
          "type": "text",
          "text": "Check the Q3 renewals sheet for accounts at risk and draft a note to the account team."
        },
        {
          "type": "attachment",
          "file_name": null,
          "media_type": "image/png",
          "size_bytes": 184320,
          "text": null
        }
      ]
    }
  ],
  "metadata": {}
}
```

### 帧字段

| 字段 | 值 |
| --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| `type` | `prompt`，或用于[工具调用帧](https://cursor.com/docs/grok-bot/inference-hooks.md#tool-call-frames)的 `tool_call` |
| `request_id` | 该请求的唯一 ID。它与 `webhook-id` 以及审计日志的 `request_id` 匹配 |
| `tenant_id` | 你的团队的不透明 ID |
| `actor.type` | 始终为 `user` |
| `actor.id` | 发送该消息的成员的不透明 ID，或团队服务账户的不透明 ID。尚未关联 Cursor 账户的 Slack 发送者为 null |
| `actor.email_address` | 始终为 null |
| `source.application` | `grok-bot`，或用于 Test Connection 和断路器恢复检查的 `config-test` |
| `session_id` | 该 Bot 的不透明 ID，在它的各个聊天和成员之间共享，因此它不标识某一个对话 |
| `model` | 一个 Cursor 模型标识符。用于报告，不用于允许列表 |
| `messages` | 到目前为止的对话，或工具调用帧中的最新响应。见[消息](https://cursor.com/docs/grok-bot/inference-hooks.md#messages) |
| `metadata` | 始终为空 |

### 消息

- **角色是 `user` 和 `assistant`。** 工具结果位于 `user` 之下。
- **块是 `text`、`tool_use`、`tool_result` 和 `attachment`。** `tool_use` 块指名工具并持有其 `input`，匹配的 `tool_result` 持有输出。Grok Bot 给模型的上下文，例如规则、共享记忆，以及它可以使用的已连接应用，通常到达 `attachment` 块的 `text` 中。图像是一个带有媒体类型和大小的 `attachment`，但不含字节或 URL。
- **每个 `prompt` 帧都持有到目前为止的整段对话。** 帧不是增量，因此每一个都会再次携带更早的消息。在长对话中，Grok Bot 所写的摘要会取代更早的消息。
- **给成员的回复是工具调用。** Grok Bot 通过一个工具发送成员阅读的内容，因此它的回复出现在 `tool_use` 的 input 中，而不是 assistant 的 `text` 中。
- **Cursor 从不添加机密值。** 上下文可以指名并描述 Bot 可以使用的机密，但从不包含它们的值。

### 工具调用帧

`tool_call` 帧与 `prompt` 帧有相同的标头和字段，`type` 设为 `tool_call`，有自己的 `request_id`，以及该回合 `prompt` 帧的 `session_id`。它的 `messages` 数组持有该响应的 `assistant` 消息，模型的文本在 `text` 块中，每个入帧调用有一个 `tool_use` 块。读取 `messages` 的最后一项，因为协议允许在它之前有更早的消息。

| `tool_use` 字段 | 值 |
| ---------------- | ------------------------------------------------------------------------------------- |
| `id` | 模型给该调用的 ID。以后的 `prompt` 帧使用同一个 ID |
| `tool_name` | 运行的工具 |
| `input` | 模型所写的参数，当它们不是有效 JSON 时为 null |
| `tool_info` | 该工具是什么、在哪里运行。从不为 null。可选字段会被省略，而不是 null |

| `tool_info.type` | Grok Bot 在这些情况下发送它 | `toolset_name` |
| ---------------- | ---------------------------------------------------------------------------------------------------- | ----------------------------------------------- |
| `client` | Bot 计算机上的 `Shell`、`Read` 和 `AwaitShell` | `grok-bot-computer` |
| `client` | 成员计算机上的同样工具，以及两台计算机之间的文件复制 | `user-computer` |
| `client` | 手机和设备工具 | `user-phone`、`user-device` 或 `user-computer` |
| `client` | 浏览器工具 | `browser` |
| `client` | 计算机操作 | `computer-use` |
| `client` | 在 Bot 计算机上运行的 Model Context Protocol（MCP）服务器 | 该服务器的名称 |
| `third_party` | 通过 HTTP 到达的 MCP 服务器，以及 Slack 和 Microsoft Teams 工具 | 该服务器或应用 |
| `application` | 在 Cursor 后端运行的工具，例如网络搜索、电子邮件、subagent，以及发给其他 Bot 的消息 | 该功能，如果已知 |

- **`tool_name` 指名运行的是什么。** `bash` 调用显示为 `Shell`。MCP 调用保留模型使用的工具名 `CallMcpTool` 或 `CallDynamicTool`，服务器在 `toolset_name` 中，参数在 `input` 中。
- **当 Cursor 知道时，`origin` 会随 `third_party` 调用一起到来。** 它是该服务的 scheme、主机和端口，并且 `verified_origin: true`，因为 Cursor 自己连接到该服务。缺少 `origin` 意味着未知，而不是安全。Grok Bot 不发送 `platform`。
- **输入不会被涂黑。** `input` 携带模型放进调用中的任何内容，例如粘贴进命令的 token，或 `AddMcpServer` 调用中的凭证。以后的 `prompt` 帧携带同样的输入。Cursor 唯一会裁剪的值是它作为 `origin` 添加的服务器 URL，它会裁到 scheme、主机和端口。
- **`text` 块是旁白。** 它们持有模型在调用之间的话，Grok Bot 不会把它们交给任何人。成员从 `SendToUser` 调用阅读回复，工具调用帧从不包含这些调用。

```json
{
  "type": "tool_call",
  "request_id": "req_3b1f8c2e-9a4d-4e7b-8f20-6c5d1a9e7b42",
  "tenant_id": "team_4mQvT8yLcN2pR6",
  "actor": {
    "type": "user",
    "id": "user_8hJw3KzP5sXq1V7nB2cF9mD4tR",
    "email_address": null
  },
  "source": { "application": "grok-bot" },
  "session_id": "3f2c9a71-58e4-4b0d-9c6a-0d1e7b25a4f8",
  "model": "<Cursor model identifier>",
  "messages": [
    {
      "role": "assistant",
      "content": [
        { "type": "text", "text": "I'll check the build log, then file the bug." },
        {
          "type": "tool_use",
          "id": "call_01",
          "tool_name": "Shell",
          "input": { "command": "tail -n 50 build.log" },
          "tool_info": { "type": "client", "toolset_name": "grok-bot-computer" }
        },
        {
          "type": "tool_use",
          "id": "call_02",
          "tool_name": "CallMcpTool",
          "input": {
            "server": "tracker",
            "toolName": "create_issue",
            "arguments": { "title": "Nightly build fails at the link step" }
          },
          "tool_info": {
            "type": "third_party",
            "toolset_name": "tracker",
            "origin": "https://mcp.tracker.example",
            "verified_origin": true
          }
        }
      ]
    }
  ],
  "metadata": {}
}
```

用与 `prompt` 帧相同的[裁决](https://cursor.com/docs/grok-bot/inference-hooks.md#verdicts)回答 `tool_call` 帧。这个例子中的拒绝会拒绝两次调用。

### 为这些帧编写策略

- **验证签名。** 不要依赖 User-Agent 或源 IP。如果 Inference Hooks 页面在 **Request Signing** 下列出 **Source IP Ranges**，你也可以把它们加入允许列表。
- **把 ID 当作不透明字符串。** 不要解析 `tenant_id` 或 `actor.id`。按 `actor.id` 映射成员，因为帧不携带电子邮件。
- **把 `source.application` 当作开放集合。** 随着覆盖范围扩大，Cursor 会增加值。它是参考性的，不是安全控制。
- **扫描每一种块类型，而不只是 `text`。** 上下文通常到达附件的 `text` 中，给成员的回复和已连接应用的调用到达 `tool_use` 的 input 中。
- **不要依赖 `tool_result.is_error`。** 对大多数失败的工具调用它也是 `false`，包括你的服务器拒绝的调用。
- **按 `tool_info` 评判工具调用，而不是按名称。** 用 `tool_info.type` 和 `origin` 决定调用去向哪里，并接受你不认识的 `type` 值。

### 裁决

用 HTTP 200 和一个 JSON 对象回答：

```json
{
  "action": "deny",
  "deny_reason": "This message contains a customer account number. Remove it and try again.",
  "reference_id": "policy-7f3c21"
}
```

- **`action`** 是 `allow` 或 `deny`。其他任何值都是失败的检查。
- **`deny_reason`** 会在阻止通知中显示给发送该消息的成员。Cursor 把它截断为 500 个字符。
- **`reference_id`** 是你为该决定所设的标识符，记录在 Cursor 审计日志中。最多 50 个字符，来自 `A-Z`、`a-z`、`0-9`，以及 `.`、`_`、`:`、`/`、`-`。格式错误的值会被丢弃，拒绝仍然生效。
- **未知字段会被忽略。** Cursor 最多读取 64 KiB 的未压缩内容，并忽略 cookie。

对来自 Test Connection 和断路器恢复的 `config-test` 帧也用裁决回答，并把它们排除在你关于成员流量的报告之外。

## 审计日志

Inference hook 活动出现在你团队的[审计日志](https://cursor.com/docs/enterprise/compliance-and-monitoring.md#audit-logs)中。载荷携带标识符和代码，从不携带提示词内容、拒绝原因、签名机密或标头值。被允许的请求不写事件。

| `event_type` | 何时触发 | 字段 |
| ------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `inference_hooks_config_updated` | 管理员保存设置或重置断路器，或自动恢复重置了它 | `action`（`update`、`reset_circuit_breaker`、`circuit_breaker_recovered`）、`changed_fields`、`enforce_verdicts`、`mode`、`endpoint_host`、`verdict_timeout_ms`、`rollout_percent`、`enabled_surfaces`、`config_version`、`validate_tool_calls` |
| `inference_hooks_signing_secret_generated` | 第一次保存并设置端点时生成签名机密，或管理员轮换了它 | `action`（`generate_signing_secret`、`rotate_signing_secret`）、`changed_fields`（始终为空）、`enforce_verdicts`、`mode`、`endpoint_host`、`verdict_timeout_ms`、`rollout_percent`、`enabled_surfaces`、`config_version`、`validate_tool_calls` |
| `inference_hooks_request_denied` | 你的服务器拒绝了一个请求 | `request_id`、`reference_id`、`source_application`、`session_id`、`model`、`actor_id`、`mode`、`enforced`（Shadow mode 中为 `false`）、载荷 `event_type` |
| `inference_hooks_request_failed_open` | 请求在没有裁决的情况下继续：Allow 或 Shadow mode 中失败的检查、Cursor 暂停，或 Cursor 无法加载的设置 | `request_id`、`reason`、`source_application`、`session_id`、`model`、`actor_id`、`mode`、`status_code`（没有响应到达时为 `0`）、载荷 `event_type` |
| `inference_hooks_request_failed_closed` | 检查在 Block mode 中失败，因此请求被阻止 | `request_id`、`reason`、`source_application`、`session_id`、`model`、`actor_id`、`mode`、`status_code`（没有响应到达时为 `0`）、载荷 `event_type` |
| `inference_hooks_circuit_breaker_tripped` | 持续失败触发了断路器 | `config_version`、`mode`、`failure_count`、`window_seconds`、`last_failure_kind` |

有两个字段共用名称 `event_type`。表的第一列是审计事件自身的类型。被拒绝、失败时放行和失败时关闭事件的载荷 `event_type` 指名被检查的帧，`prompt` 或 `tool_call`。它在 Cursor 为你的团队打开工具调用检查时出现，两个设置事件上的 `validate_tool_calls` 也是如此。没有载荷 `event_type` 的被拒绝或失败事件来自提示词检查。

在 `inference_hooks_signing_secret_generated` 上，设置字段显示更改之后的状态。轮换会增加 `config_version`。第一次生成的机密与同一次保存产生的 `inference_hooks_config_updated` 事件共用它。

Cursor 在每一种模式中都记录请求失败：Allow 和 Shadow 中为失败时放行，Block 中为失败时关闭，并且它记录自动的断路器恢复。失败时放行和失败时关闭事件上的 `reason` 是一种失败（`timeout`、`non_200_status`、`unparseable_response`、`oversized_response`、`invalid_action`、`transport_error`、`tls_error`、`endpoint_rejected`、`frame_too_large`）、一种 Cursor 侧原因（`endpoint_not_allowlisted`、`config_unavailable`），或者仅对失败时放行而言，一种暂停（`kill_switch`）。基于这些事件名称和 `reason` 值构建 SIEM 规则。

被拒绝和失败事件上的 `request_id` 与帧的 `request_id` 和 `webhook-id` 匹配，因此你可以把供应商的记录与审计日志关联起来。

Inference Hooks 页面还显示端点健康状况：状态、裁决延迟、每分钟失败次数、24 小时内的阻止率，以及带有类型和状态码的近期错误。打开 Validate Tool Calls 后，它把这些数字拆成提示词和工具调用。它的数字并不覆盖全部流量，因此可能低于供应商的计数。

## 隐私与 Privacy Mode

- **Cursor 不保留它发给你的 hook 的内容。** Cursor 通过 HTTPS 把每个帧发送到你的端点，并在裁决到达后丢弃它。帧内容不会写入 Cursor 的数据库、日志、指标或追踪。
- **你的端点，你的保留。** 端点属于你的组织或你的安全供应商。它存储什么、存在哪里、存多久，取决于你的配置以及你与该供应商的协议。Cursor 不控制这些数据。
- **Privacy Mode 不变。** 模型请求本身的处理与没有 hook 时完全一样，受你团队的 Privacy Mode 设置约束，hook 不会额外复制一份。无论 Privacy Mode 如何，inference hooks 都会为每个被覆盖的成员运行，因为你的管理员指示 Cursor 把这些请求发送到你自己的端点。
- **帧字段只用不透明 ID 标识人。** `actor.email_address` 始终为 null。

Cursor 只保留运行该功能并向你展示发生了什么所需的内容：

- **你的设置，机密已加密。** 标头值和签名机密加密存储。
- **只有标识符和代码的审计日志事件。** 它们包括你的服务器的 `reference_id`，如[审计日志](https://cursor.com/docs/grok-bot/inference-hooks.md#audit-log)所列。
- **不含内容的健康数字。** 它们持有计数、延迟，以及近期错误类型和状态码。
- **阻止通知，与被阻止的回合一起加密。** 你的服务器的 `deny_reason` 和你的 Blocked Message 会加密存储，与 Grok Bot 对被阻止回合的记录放在一起。

## 故障排除

### 为什么我在 Grok Bot 页面上看不到 Inference Hooks？

该页面向 Enterprise 团队上的团队管理员出现，并且显示仪表盘中选中的团队。检查是否选中了正确的团队。如果页面显示 “Inference hooks aren't available yet,”，请联系你的客户经理。

### 为什么我的端点 URL 被拒绝？

检查[端点要求](https://cursor.com/docs/grok-bot/inference-hooks.md#endpoint-requirements)。常见原因是端口不是 443、URL 中有查询字符串或凭证、隧道主机，或解析到私有地址的主机。

### 为什么 Test Connection 说需要签名机密？

Cursor 在第一次保存并设置端点时生成机密。保存端点，把机密复制到供应商的控制台，然后再测试。

### 为什么我的服务器拒绝 Cursor 的请求？

大多数拒绝是签名或凭证不匹配。把当前的签名机密粘贴到你的服务器，如果你的验证器需要 `whsec_` 前缀，也要包括它，并检查它是否把机密按标准 base64 解码。确认你的 API 密钥标头已保存。如果你更改了端点 URL，重新输入标头值。轮换之后，Cursor 大约在一分钟内切换到新机密。

### 为什么 Test Connection 返回拒绝？

你的服务器的策略拒绝了 Cursor 的合成测试提示词。在 Allow 或 Block mode 中，这样的策略也会阻止成员。在打开强制执行之前，调整该策略或其默认动作。

### 为什么请求会超时？

你的服务器花费的时间超过了裁决超时。在健康面板和供应商的报告中检查裁决延迟。更轻的扫描配置回答得更快。你可以把 **Verdict Timeout (ms)** 提高到 10,000。

### 为什么我的端点收不到请求？

检查端点已保存、**Enforce Verdicts** 已打开、**Coverage** 包含 Grok Bot、**Requests Inspected (%)** 大于 0，并且断路器没有触发。只有你团队成员的 Grok Bot 对话被覆盖。如果 Test Connection 报告 inference hooks 已暂停，则是 Cursor 暂停了它们。

### 为什么成员看到策略检查不可用？

你处于 Block mode 并且检查失败，或断路器已触发。在 Inference Hooks 页面上检查 **Recent Errors**。要在修复端点期间让成员继续工作，切换到 **Allow the Request**。

### 为什么打开 Validate Tool Calls 之后工具调用停止运行？

在 Block mode 中，当你的服务器无法回答 `tool_call` 帧时，工具调用会停止。在 Inference Hooks 页面的 **Recent Errors** 中检查 **Tool Calls** 下的错误，并确认你的供应商支持工具调用帧。在它支持之前，关闭 **Validate Tool Calls**。

### 断路器触发了。我如何恢复？

修复端点，运行 Test Connection，然后点击 **Reset**。任何设置更改之后自动恢复都会停止，因此如果你在触发之后编辑过任何内容，不要等它自动恢复。

### 为什么一次阻止之后，对话中的每条消息都被阻止？

被阻止的消息留在对话中，并随以后的每个请求发往你的服务器，因此扫描整份记录的策略会拒绝其中每一个。在桌面或移动应用的阻止通知上选择 **Edit**，或在 Slack 中 Bot 的回复上选择 **Remove blocked message**，把该消息取回并发送不同的内容。**Reset** 则改为清除对话。如果重置之后对话立刻再次被阻止，你的策略匹配到的内容很可能在 Bot 的记忆或文件中。在那里移除它。评判最新用户消息的策略，会在成员发送不同内容后让对话继续。

### 为什么审计日志筛选器中不出现 inference hook 事件？

事件类型只在你的团队启用了 inference hooks 时才出现在筛选器中。已经记录的事件仍会显示在日志中。

## 相关页面

- [Grok Bot 安全](https://cursor.com/docs/grok-bot/security.md)
- [面向 Teams 与 Enterprise 的 Grok Bot](https://cursor.com/docs/grok-bot/teams.md)
- [合规与监控](https://cursor.com/docs/enterprise/compliance-and-monitoring.md)
- [隐私与数据治理](https://cursor.com/docs/enterprise/privacy-and-data-governance.md)


---

## 站点地图

[所有文档页面概览](/llms.txt)
