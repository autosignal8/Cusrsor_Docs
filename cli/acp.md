> 原文：https://cursor.com/docs/cli/acp

# ACP

## 概述

Cursor CLI 支持 **ACP (Agent Client Protocol)**，用于高级集成。你可以运行 `agent acp`，并通过 `stdio` 使用 JSON-RPC 连接自定义客户端。

更多信息见官方 [Agent Client Protocol 文档](https://agentclientprotocol.com/)。

ACP 用于构建自定义客户端和集成。日常终端工作流请使用交互式 CLI，命令为 `agent`。

## 启动 ACP 服务器

以 ACP 模式启动 Cursor CLI：

```bash
agent acp
```

## 传输与消息格式

- 传输：`stdio`
- 协议封装：JSON-RPC 2.0
- 分帧：换行分隔的 JSON（每行一条消息）
- 方向：
  - 客户端把请求/通知写入 `stdin`
  - Cursor CLI 把响应/通知写入 `stdout`
  - 日志可能写入 `stderr`

## 请求流程

典型的 ACP 会话流程：

1. `initialize`
2. 使用 `methodId: "cursor_login"` 进行 `authenticate`
3. `session/new`（或 `session/load`）
4. `session/prompt`
5. 在模型流式输出时处理 `session/update` 通知
6. 通过返回一个决定来处理 `session/request_permission`
7. 可选地发送 `session/cancel`

## 身份验证

Cursor CLI 将 `cursor_login` 宣告为 ACP 身份验证方法。实际使用中，你可以在启动前通过现有 CLI 身份验证路径预先完成身份验证：

- `agent login`
- `--api-key`（或 `CURSOR_API_KEY`）
- `--auth-token`（或 `CURSOR_AUTH_TOKEN`）

你也可以从根 CLI 命令传入端点和 TLS 选项：

```bash
agent --api-key "$CURSOR_API_KEY" acp
agent -e https://api2.cursor.sh acp
agent -k acp
```

## 会话、模式与权限

### 会话

- 用 `session/new` 创建会话
- 用 `session/load` 恢复已有对话

### 模式

ACP 会话支持与 CLI 相同的核心模式：

- `agent`（完整工具访问）
- `plan`（规划，只读行为）
- `ask`（问答/只读行为）

### 权限

当工具需要批准时，Cursor 会发送 `session/request_permission`。客户端应返回以下之一：

- `allow-once`
- `allow-always`
- `reject-once`

如果你的客户端不回应权限请求，工具执行可能会阻塞。

## MCP 服务器

ACP 支持项目级或用户级 `.cursor/mcp.json` 中定义的 [MCP 服务器](https://cursor.com/docs/mcp.md)。从项目目录启动 `agent`，并批准你想使用的服务器。

通过 Cursor 仪表板配置的团队级 MCP 服务器在 ACP 模式下不受支持。

## Cursor 扩展方法

Cursor 会发送 ACP 扩展方法，以提供更丰富的客户端体验。有两种类型：

- **阻塞方法**（`cursor/ask_question`、`cursor/create_plan`）：Agent 会等待响应后再继续。你的客户端必须回复一条 JSON-RPC 响应。
- **通知方法**（`cursor/update_todos`、`cursor/task`、`cursor/generate_image`）：Agent 将这些作为即发即忘通知发送。你的客户端可以展示它们，但不必响应。

| 方法                    | 类型 | 用途                         |
| :---------------------- | :--- | :--------------------------- |
| `cursor/ask_question`   | 阻塞 | 向用户提出选择题             |
| `cursor/create_plan`    | 阻塞 | 请求明确的计划批准           |
| `cursor/update_todos`   | 通知 | 通知客户端待办状态更新       |
| `cursor/task`           | 通知 | 通知客户端 Subagent 任务完成 |
| `cursor/generate_image` | 通知 | 通知客户端已生成的图像输出   |

### `cursor/ask_question`

向用户展示选择题。Agent 会阻塞，直到客户端响应。

**请求：**

```ts
interface CursorAskQuestionRequest {
  toolCallId: string;
  title?: string;
  questions: Array<{
    id: string;
    prompt: string;
    options: Array<{ id: string; label: string }>;
    allowMultiple?: boolean;
  }>;
}
```

**响应：**

```ts
interface CursorAskQuestionResponse {
  outcome:
    | {
        outcome: "answered";
        answers: Array<{
          questionId: string;
          selectedOptionIds: string[];
        }>;
      }
    | { outcome: "skipped"; reason?: string }
    | { outcome: "cancelled" };
}
```

**示例请求：**

```json
{
  "toolCallId": "call_123",
  "title": "Need input",
  "questions": [
    {
      "id": "q1",
      "prompt": "Which mode should I use?",
      "options": [
        { "id": "agent", "label": "Agent" },
        { "id": "plan", "label": "Plan" }
      ],
      "allowMultiple": false
    }
  ]
}
```

### `cursor/create_plan`

向用户请求计划批准。Agent 会阻塞，直到客户端接受或拒绝该计划。

**请求：**

```ts
interface CursorCreatePlanRequest {
  toolCallId: string;
  name?: string;
  overview?: string;
  plan: string;
  todos: Array<{
    id: string;
    content: string;
    status: "pending" | "in_progress" | "completed" | "cancelled";
  }>;
  isProject?: boolean;
  phases?: Array<{
    name: string;
    todos: Array<{
      id: string;
      content: string;
      status: "pending" | "in_progress" | "completed" | "cancelled";
    }>;
  }>;
}
```

- `plan`：描述完整计划的 markdown 字符串。
- `phases`：可选，把待办分组到命名阶段，用于更大的计划。

**响应：**

```ts
interface CursorCreatePlanResponse {
  outcome:
    | { outcome: "accepted"; planUri?: string }
    | { outcome: "rejected"; reason?: string }
    | { outcome: "cancelled" };
}
```

**示例请求：**

```json
{
  "toolCallId": "call_124",
  "name": "Refactor tabs layout",
  "overview": "Tighten layout behavior and preserve existing UX.",
  "plan": "1. Inspect current tab sizing logic.\n2. Update layout calculations.\n3. Verify editor behavior.",
  "todos": [
    { "id": "todo-1", "content": "Inspect current tab sizing logic", "status": "completed" },
    { "id": "todo-2", "content": "Update layout calculations", "status": "in_progress" },
    { "id": "todo-3", "content": "Verify editor behavior", "status": "pending" }
  ],
  "isProject": false
}
```

### `cursor/update_todos`

更新客户端的待办列表。以通知形式发送；无需响应。

**请求：**

```ts
interface CursorUpdateTodosRequest {
  toolCallId: string;
  todos: Array<{
    id: string;
    content: string;
    status: "pending" | "in_progress" | "completed" | "cancelled";
  }>;
  merge: boolean;
}
```

- `merge`：为 `true` 时，将这些待办合并进现有列表。为 `false` 时，替换整个列表。

**响应：**

```ts
interface CursorUpdateTodosResponse {
  outcome:
    | {
        outcome: "accepted";
        todos: Array<{
          id: string;
          content: string;
          status: "pending" | "in_progress" | "completed" | "cancelled";
        }>;
      }
    | { outcome: "rejected"; reason?: string }
    | { outcome: "cancelled" };
}
```

**示例请求：**

```json
{
  "toolCallId": "call_125",
  "todos": [
    { "id": "1", "content": "Set up project structure", "status": "completed" },
    { "id": "2", "content": "Add authentication", "status": "in_progress" },
    { "id": "3", "content": "Write unit tests", "status": "pending" }
  ],
  "merge": true
}
```

### `cursor/task`

通知客户端一项 Subagent 任务。以通知形式发送；无需响应。

**请求：**

```ts
interface CursorTaskRequest {
  toolCallId: string;
  description: string;
  prompt: string;
  subagentType:
    | "unspecified"
    | "computer_use"
    | "explore"
    | "video_review"
    | "browser_use"
    | "shell"
    | "vm_setup_helper"
    | { custom: string };
  model?: string;
  agentId?: string;
  durationMs?: number;
}
```

- `subagentType`：要运行的 Subagent 类型。自定义 Subagent 类型使用 `{ custom: "your_type" }`。
- `agentId`：设置为该值以恢复先前创建的 Subagent。
- `durationMs`：任务运行了多久，包含在响应中。

**响应：**

```ts
interface CursorTaskResponse {
  outcome:
    | { outcome: "completed"; agentId?: string; durationMs?: number }
    | { outcome: "rejected"; reason?: string }
    | { outcome: "cancelled" };
}
```

**示例请求：**

```json
{
  "toolCallId": "call_126",
  "description": "Explore codebase",
  "prompt": "Find where authentication is handled and report the file paths.",
  "subagentType": "explore"
}
```

### `cursor/generate_image`

通知客户端一张已生成的图像。以通知形式发送；无需响应。

**请求：**

```ts
interface CursorGenerateImageRequest {
  toolCallId: string;
  description: string;
  filePath?: string;
  referenceImagePaths?: string[];
}
```

- `filePath`：生成图像的建议文件路径。
- `referenceImagePaths`：用作输入的参考图像路径。

**响应：**

```ts
interface CursorGenerateImageResponse {
  outcome:
    | { outcome: "generated"; filePath: string; imageData?: string }
    | { outcome: "rejected"; reason?: string }
    | { outcome: "cancelled" };
}
```

**示例请求：**

```json
{
  "toolCallId": "call_127",
  "description": "Minimal flat app icon for a note-taking app",
  "filePath": "/tmp/icon.png",
  "referenceImagePaths": ["/tmp/reference.png"]
}
```

## 最小 Node.js 客户端

此示例展示自定义 ACP 客户端的最小控制流：

```js
import { spawn } from "node:child_process";
import readline from "node:readline";

const agent = spawn("agent", ["acp"], { stdio: ["pipe", "pipe", "inherit"] });

let nextId = 1;
const pending = new Map();

function send(method, params) {
  const id = nextId++;
  agent.stdin.write(JSON.stringify({ jsonrpc: "2.0", id, method, params }) + "\n");
  return new Promise((resolve, reject) => pending.set(id, { resolve, reject }));
}

function respond(id, result) {
  agent.stdin.write(JSON.stringify({ jsonrpc: "2.0", id, result }) + "\n");
}

const rl = readline.createInterface({ input: agent.stdout });
rl.on("line", line => {
  const msg = JSON.parse(line);

  if (msg.id && (msg.result || msg.error)) {
    const waiter = pending.get(msg.id);
    if (!waiter) return;
    pending.delete(msg.id);
    msg.error ? waiter.reject(msg.error) : waiter.resolve(msg.result);
    return;
  }

  if (msg.method === "session/update") {
    const update = msg.params?.update;
    if (update?.sessionUpdate === "agent_message_chunk" && update.content?.text) {
      process.stdout.write(update.content.text);
    }
    return;
  }

  if (msg.method === "session/request_permission") {
    respond(msg.id, { outcome: { outcome: "selected", optionId: "allow-once" } });
  }
});

const init = async () => {
  await send("initialize", {
    protocolVersion: 1,
    clientCapabilities: { fs: { readTextFile: false, writeTextFile: false }, terminal: false },
    clientInfo: { name: "acp-minimal-client", version: "0.1.0" }
  });

  await send("authenticate", { methodId: "cursor_login" });
  const { sessionId } = await send("session/new", { cwd: process.cwd(), mcpServers: [] });
  const result = await send("session/prompt", {
    sessionId,
    prompt: [{ type: "text", text: "Say hello in one sentence." }]
  });

  console.log(`\n\n[stopReason=${result.stopReason}]`);
};

init().finally(() => {
  agent.stdin.end();
  agent.kill();
});
```

## IDE 集成

ACP 让 Cursor 的 AI Agent 可以在 Cursor 桌面应用之外的编辑器中工作。为你偏好的开发环境构建或使用第三方集成。

### 示例用例

- **JetBrains IDEs** — 将 IntelliJ IDEA、WebStorm、PyCharm 或其他 JetBrains IDE 连接到 Cursor 的 Agent。设置说明见 [JetBrains 集成指南](https://cursor.com/docs/integrations/jetbrains.md)。

- **Neovim (avante.nvim)** — 使用 [avante.nvim](https://github.com/yetone/avante.nvim)，通过 ACP 将 Neovim 连接到 Cursor 的 Agent。见下方的 [Neovim 设置](https://cursor.com/docs/cli/acp.md#neovim-avantenvim)。

- **Zed** — 通过启动 `agent acp` 并经由 stdio 通信，与 Zed 的现代编辑器集成。Zed 扩展可以实现 ACP 客户端协议，把 AI 请求路由到 Cursor。

- **自定义编辑器** — 任何支持扩展的编辑器都可以实现 ACP 客户端。启动 agent 进程，通过 stdio 发送 JSON-RPC 消息，并在编辑器 UI 中处理响应。

### Neovim (avante.nvim)

[avante.nvim](https://github.com/yetone/avante.nvim) 是一个 Neovim 插件，提供 AI 编程助手。它支持 ACP，因此你可以把它连接到 Cursor 的 Agent，在 Neovim 内进行 Agent 式编码。

把以下内容加入你的 lazy.nvim 插件配置（例如 `~/.config/nvim/lua/plugins/avante.lua`）：

```lua
return {
  {
    "yetone/avante.nvim",
    event = "VeryLazy",
    version = false,
    build = "make",
    opts = {
      provider = "cursor",
      mode = "agentic",
      acp_providers = {
        cursor = {
          command = os.getenv("HOME") .. "/.local/bin/agent",
          args = { "acp" },
          auth_method = "cursor_login",
          env = {
            HOME = os.getenv("HOME"),
            PATH = os.getenv("PATH"),
          },
        },
      },
    },
    dependencies = {
      "nvim-lua/plenary.nvim",
      "MunifTanjim/nui.nvim",
      "nvim-tree/nvim-web-devicons",
      {
        "MeanderingProgrammer/render-markdown.nvim",
        opts = {
          file_types = { "markdown", "Avante" },
        },
        ft = { "markdown", "Avante" },
      },
    },
  },
}
```

关键设置：

- **`provider`**：设为 `"cursor"`，通过 Cursor 的 Agent 路由请求。
- **`mode`**：设为 `"agentic"` 以获得完整工具访问（文件编辑、终端命令）。仅聊天模式使用 `"normal"`。
- **`command`**：指向 `agent` 二进制文件。默认安装路径是 `~/.local/bin/agent`。如果安装在其他位置，请相应调整。
- **`auth_method`**：使用 `"cursor_login"`。先在终端中运行 `agent login` 完成身份验证。

### 构建集成

1. 将 `agent acp` 作为子进程启动
2. 使用 JSON-RPC 通过 stdin/stdout 通信
3. 处理 `session/update` 通知以显示流式响应
4. 当工具需要批准时响应 `session/request_permission`
5. 可选地实现 Cursor 扩展方法，以获得更丰富的体验

上方的[最小 Node.js 客户端](https://cursor.com/docs/cli/acp.md#minimal-nodejs-client)是一份可运行的参考实现。

## 相关

### CLI 中的 MCP

从 Cursor CLI 管理并使用 MCP 服务器

### MCP 概述

了解 MCP 传输、配置和服务器设置

---

## 站点地图

[所有文档页面概览](/llms.txt)
