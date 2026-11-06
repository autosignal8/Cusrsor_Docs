> 原文：https://cursor.com/docs/cli/reference/output-format

# 输出格式

Cursor Agent CLI 在与 `--print` 结合时，通过 `--output-format` 选项提供多种输出格式。这些格式包括供程序使用的结构化格式（`json`、`stream-json`），以及供人阅读的简化文本格式（`text`）。

默认的 `--output-format` 是 `text`。此选项仅在打印时（`--print`），或在推断为打印模式时（非 TTY 的 stdout，或管道传入的 stdin）有效。

## JSON 格式

`json` 输出格式在运行成功完成时发出单个 JSON 对象（后跟换行）。不会发出增量和工具事件；文本会聚合到最终结果中。

失败时，进程以非零代码退出，并把错误消息写入 stderr。失败情况下不会发出格式良好的 JSON 对象。

### 成功响应

成功时，CLI 输出具有以下结构的 JSON 对象：

```json
{
  "type": "result",
  "subtype": "success",
  "is_error": false,
  "duration_ms": 1234,
  "duration_api_ms": 1234,
  "result": "<full assistant text>",
  "session_id": "<uuid>",
  "request_id": "<optional request id>"
}
```

| 字段              | 说明                                                                |
| ----------------- | ------------------------------------------------------------------- |
| `type`            | 终端结果始终为 `"result"`                                           |
| `subtype`         | 成功完成时始终为 `"success"`                                        |
| `is_error`        | 成功响应始终为 `false`                                              |
| `duration_ms`     | 总执行时间，单位为毫秒                                              |
| `duration_api_ms` | API 请求时间，单位为毫秒（当前等于 `duration_ms`）                  |
| `result`          | 完整的助手响应文本（所有文本增量的拼接）                            |
| `session_id`      | 唯一会话标识符                                                      |
| `request_id`      | 可选的请求标识符（可能省略）                                        |

## Stream JSON 格式

`stream-json` 输出格式发出换行分隔的 JSON（NDJSON）。每一行包含一个 JSON 对象，表示执行期间的一个事件。此格式会聚合文本增量，并**为每条助手消息输出一行**（两次工具调用之间的完整消息）。

流在成功时以终端 `result` 事件结束。失败时，进程以非零代码退出，流可能提前结束而没有终端事件；错误消息会写入 stderr。

**流式部分输出：** 要获得实时的字符级流式输出，将 `--stream-partial-output` 与 `--output-format stream-json` 一起使用。这会在文本生成时以小块发出，每条消息对应多个 `assistant` 事件。

使用 `--stream-partial-output` 时，CLI 会发出三种 `assistant` 事件。只有第一种包含新文本：

| `timestamp_ms` | `model_call_id` | 含义                                   | 处理方式                                      |
| -------------- | --------------- | -------------------------------------- | --------------------------------------------- |
| 存在           | 不存在         | 带有新文本的流式增量                   | **使用** — 追加 `message.content[].text`      |
| 存在           | 存在           | 工具调用前的缓冲刷新（重复）           | **跳过**                                      |
| 不存在         | 不存在         | 轮次结束时的最终刷新（重复）           | **跳过**                                      |

如果你不需要实时流式输出，只要完成的答案，跳过所有 `assistant` 事件，并从终端 `result` 事件读取 `result` 字段。

### 事件类型

#### 系统初始化

每个会话开始时发出一次：

```json
{
  "type": "system",
  "subtype": "init",
  "apiKeySource": "env|flag|login",
  "cwd": "/absolute/path",
  "session_id": "<uuid>",
  "model": "<model display name>",
  "permissionMode": "default"
}
```

将来可能会向此事件添加 `tools` 和 `mcp_servers` 等字段。

#### 用户消息

包含用户输入的提示词：

```json
{
  "type": "user",
  "message": {
    "role": "user",
    "content": [{ "type": "text", "text": "<prompt>" }]
  },
  "session_id": "<uuid>"
}
```

#### 助手消息

每条完整的助手消息发出一次（位于工具调用之间）。每个事件包含该消息片段的完整文本：

```json
{
  "type": "assistant",
  "message": {
    "role": "assistant",
    "content": [{ "type": "text", "text": "<complete message text>" }]
  },
  "session_id": "<uuid>"
}
```

启用 `--stream-partial-output` 时，助手事件可能包含两个额外字段：

| 字段            | 说明                                                                                                         |
| --------------- | ------------------------------------------------------------------------------------------------------------ |
| `timestamp_ms`  | 出现在流式增量和工具调用前的刷新中。轮次结束时的最终刷新中不存在。                                          |
| `model_call_id` | 仅出现在工具调用前发出的缓冲刷新中。用它识别并跳过重复文本。                                                 |

如何过滤这些事件，见上方的[流式部分输出说明](https://cursor.com/docs/cli/reference/output-format.md#stream-json-format)。

#### 工具调用事件

工具调用通过开始和完成事件跟踪：

**工具调用已开始：**

```json
{
  "type": "tool_call",
  "subtype": "started",
  "call_id": "<string id>",
  "tool_call": {
    "readToolCall": {
      "args": { "path": "file.txt" }
    }
  },
  "session_id": "<uuid>"
}
```

**工具调用已完成：**

```json
{
  "type": "tool_call",
  "subtype": "completed",
  "call_id": "<string id>",
  "tool_call": {
    "readToolCall": {
      "args": { "path": "file.txt" },
      "result": {
        "success": {
          "content": "file contents...",
          "isEmpty": false,
          "exceededLimit": false,
          "totalLines": 54,
          "totalChars": 1254
        }
      }
    }
  },
  "session_id": "<uuid>"
}
```

#### 工具调用类型

**读取文件工具：**

- **已开始**：`tool_call.readToolCall.args` 包含 `{ "path": "file.txt" }`
- **已完成**：`tool_call.readToolCall.result.success` 包含文件元数据和内容

**写入文件工具：**

- **已开始**：`tool_call.writeToolCall.args` 包含 `{ "path": "file.txt", "fileText": "content...", "toolCallId": "id" }`
- **已完成**：`tool_call.writeToolCall.result.success` 包含 `{ "path": "/absolute/path", "linesCreated": 19, "fileSize": 942 }`

**其他工具：**

- 可能使用 `tool_call.function` 结构，包含 `{ "name": "tool_name", "arguments": "..." }`

#### 终端结果

成功完成时发出的最终事件：

```json
{
  "type": "result",
  "subtype": "success",
  "duration_ms": 1234,
  "duration_api_ms": 1234,
  "is_error": false,
  "result": "<full assistant text>",
  "session_id": "<uuid>",
  "request_id": "<optional request id>"
}
```

### 示例序列

下面是一个有代表性的 NDJSON 序列，展示事件的典型流程：

```json
{"type":"system","subtype":"init","apiKeySource":"login","cwd":"/Users/user/project","session_id":"c6b62c6f-7ead-4fd6-9922-e952131177ff","model":"Claude 4 Sonnet","permissionMode":"default"}
{"type":"user","message":{"role":"user","content":[{"type":"text","text":"Read README.md and create a summary"}]},"session_id":"c6b62c6f-7ead-4fd6-9922-e952131177ff"}
{"type":"assistant","message":{"role":"assistant","content":[{"type":"text","text":"I'll read the README.md file"}]},"session_id":"c6b62c6f-7ead-4fd6-9922-e952131177ff"}
{"type":"tool_call","subtype":"started","call_id":"toolu_vrtx_01NnjaR886UcE8whekg2MGJd","tool_call":{"readToolCall":{"args":{"path":"README.md"}}},"session_id":"c6b62c6f-7ead-4fd6-9922-e952131177ff"}
{"type":"tool_call","subtype":"completed","call_id":"toolu_vrtx_01NnjaR886UcE8whekg2MGJd","tool_call":{"readToolCall":{"args":{"path":"README.md"},"result":{"success":{"content":"# Project\n\nThis is a sample project...","isEmpty":false,"exceededLimit":false,"totalLines":54,"totalChars":1254}}}},"session_id":"c6b62c6f-7ead-4fd6-9922-e952131177ff"}
{"type":"assistant","message":{"role":"assistant","content":[{"type":"text","text":"Based on the README, I'll create a summary"}]},"session_id":"c6b62c6f-7ead-4fd6-9922-e952131177ff"}
{"type":"tool_call","subtype":"started","call_id":"toolu_vrtx_01Q3VHVnWFSKygaRPT7WDxrv","tool_call":{"writeToolCall":{"args":{"path":"summary.txt","fileText":"# README Summary\n\nThis project contains...","toolCallId":"toolu_vrtx_01Q3VHVnWFSKygaRPT7WDxrv"}}},"session_id":"c6b62c6f-7ead-4fd6-9922-e952131177ff"}
{"type":"tool_call","subtype":"completed","call_id":"toolu_vrtx_01Q3VHVnWFSKygaRPT7WDxrv","tool_call":{"writeToolCall":{"args":{"path":"summary.txt","fileText":"# README Summary\n\nThis project contains...","toolCallId":"toolu_vrtx_01Q3VHVnWFSKygaRPT7WDxrv"},"result":{"success":{"path":"/Users/user/project/summary.txt","linesCreated":19,"fileSize":942}}}},"session_id":"c6b62c6f-7ead-4fd6-9922-e952131177ff"}
{"type":"assistant","message":{"role":"assistant","content":[{"type":"text","text":"Done! I've created the summary in summary.txt"}]},"session_id":"c6b62c6f-7ead-4fd6-9922-e952131177ff"}
{"type":"result","subtype":"success","duration_ms":5234,"duration_api_ms":5234,"is_error":false,"result":"I'll read the README.md fileBased on the README, I'll create a summaryDone! I've created the summary in summary.txt","session_id":"c6b62c6f-7ead-4fd6-9922-e952131177ff","request_id":"10e11780-df2f-45dc-a1ff-4540af32e9c0"}
```

## 文本格式

`text` 输出格式只提供最终的助手消息，没有任何中间进度更新或工具调用摘要。对于只需要 Agent 最终响应的脚本，这是最干净的输出格式。

当你只想要 Agent 的答案或最终消息，而不需要进度指示或工具执行细节时，此格式很合适。

### 示例输出

```
The command to move this branch onto main is `git rebase --onto main HEAD~3`.
```

只输出最终的助手消息（最后一次工具调用之后），没有工具调用摘要或中间文本。

## 说明

- 每个事件作为以 `\n` 结尾的单行发出
- `thinking` 事件在打印模式中被抑制，不会出现在任何输出格式中
- 字段可能会以向后兼容的方式随时间增加（消费者应忽略未知字段）
- `json` 格式会等到完成后再输出结果
- `stream-json` 格式输出完整的 Agent 消息
- `--stream-partial-output` 标志提供实时文本增量，用于字符级流式输出（仅适用于 `stream-json` 格式）
- 工具调用 ID 可用于关联开始/完成事件
- 会话 ID 在单次 Agent 执行中保持一致

---

## 站点地图

[所有文档页面概览](/llms.txt)
