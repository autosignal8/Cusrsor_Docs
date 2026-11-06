> 原文：https://cursor.com/docs/cloud-agent/api/endpoints

# Cloud Agents API

### 公开 Beta

Cloud Agents API v1 处于公开 Beta。API 在正式发布前可能发生变化。

Cloud Agents API 让你以编程方式启动并管理在你的仓库上工作的 Cloud Agent，以及它们所运行的[环境](https://cursor.com/docs/cloud-agent/api/endpoints.md#environments)。

- Cloud Agents API 同时接受 [Basic 与 Bearer 身份验证](https://cursor.com/docs/api.md#authentication)。从 [Cursor Dashboard → API Keys](https://cursor.com/dashboard/api) 生成用户 API key，或使用[服务账号 API key](https://cursor.com/docs/account/enterprise/service-accounts.md)。
- 关于身份验证方式、速率限制和最佳实践，参见 [API 概览](https://cursor.com/docs/api.md)。
- 查看完整的 [OpenAPI 规范](/docs-static/cloud-agents-openapi.yaml)，以获取详细 schema 与示例。
- Webhook 即将推出。旧版 [v0 API](https://cursor.com/docs/cloud-agent/api/v0.md) 仍然支持它们，参见 [Webhooks](https://cursor.com/docs/cloud-agent/api/webhooks.md)。

### 从 v0 迁移？

此 API 把工作拆成一个持久 Agent，再加上按提示词划分的 run，以取代更扁平的 v0 接口。旧版 [v0 参考](https://cursor.com/docs/cloud-agent/api/v0.md) 仍然可用。

下面的 15 MB 图片限制适用于 API 的图片输入。[cursor.com/agents](https://cursor.com/agents) 上的网页附件使用各自的限制。参见 [Cloud Agent 网页附件限制](https://cursor.com/help/ai-features/cloud-agents.md#what-are-the-attachment-limits-for-cloud-agents-on-the-web)。

## 端点

### 创建 Agent

POST

`/v1/agents`

创建一个 Cloud Agent，并立即将其初始 run 入队。响应会同时返回持久的 `agent` 和初始 `run`。

仓库可以来自 Cursor 支持的任意源代码托管提供方：GitHub（Cloud 与 Enterprise Server）、GitLab（Cloud 与 Self-Hosted）、Bitbucket Cloud 和 Azure DevOps。按该仓库在提供方上显示的 URL 传入，包括 `gitlab.example.com` 这样的自托管主机。连接提供方请参见 [Cloud Agents 设置](https://cursor.com/docs/cloud-agent/setup.md)。

#### 请求正文

`prompt` 对象（必填）

给 Agent 的任务提示词，可包含可选图片。

`prompt.text` 字符串（必填）

给 Agent 的指令文本。

`prompt.images` 数组（可选）

提示词的图片输入。每个条目必须包含 `data`（base64 编码的字节，且必须有 `mimeType`）或 `url`（由 Cursor 抓取的 http 或 https URL）二者之一。最多 5 张图片，每张 15 MB。支持的 MIME 类型：`image/png`、`image/jpeg`、`image/gif`、`image/webp`。

`model` 对象（可选）

模型选择。省略此字段即使用已配置的默认模型。省略时，Cursor 会依次解析你的用户默认模型、团队默认模型，然后是系统默认模型。

`model.id` 字符串（提供了 `model` 时必填）

`GET /v1/models` 返回的显式模型 ID（例如 `claude-4-sonnet-thinking`）。

`model.params` 数组（可选）

应用到该 run 的按模型参数，例如推理强度或上下文窗口大小。每一项都有 `id` 和 `value`。只使用所选模型支持的参数。调用 `GET /v1/models` 可查看有效的 `id`/`params` 组合。

`name` 字符串（可选）

Agent 的显示名称。最多 100 个字符。省略时，Cursor 会根据提示词自动推导名称。

`env` 对象（可选）

执行环境目标。使用具名的 `cloud` 环境，或路由到你自己托管的 `pool` 或 `machine`。在选择具名的 Cursor 托管环境时，与显式的 `repos` 互斥。

`env.type` 字符串（提供了 `env` 时必填）

执行环境类型。`cloud` 使用 Cursor 托管的虚拟机；`pool` 和 `machine` 路由到你自己的 worker。

`env.name` 字符串（可选）

具名的 Cursor 托管环境、pool 或机器名称。对于 `env.type: "pool"`，这是 pool 名称（省略时默认为 `default`）。未知的 pool 名称会返回 `400`，而不会一直排队。要在 `repos` 中发送多于一个条目，请指定一个[任意仓库 pool](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#any-repo-pools)。

`repos` 数组（可选）

仓库配置。与具名云环境互斥。同时省略 `repos` 和 `env` 可启动无仓库 Agent。当 `env.type` 为 `pool` 时，也可以省略 `repos`，以指向一个[任意仓库 pool](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#any-repo-pools)。最多 500 个仓库。

在自托管目标上，只有具名的任意仓库 pool 能接受多个仓库。`machine`、`default` pool 和绑定仓库的 pool 只接受一个；发送更多会返回 `400 validation_error`，消息为 "My Machines and repo-bound pools accept one repo in repos. To start an agent with several repos, set env.name to an any-repo pool."

`repos[0].url` 字符串（必填）

任意已连接源代码托管提供方上的仓库 URL（例如 `https://github.com/your-org/your-repo` 或 `https://gitlab.example.com/your-group/your-repo`）。每个仓库条目都必填，包括提供了 `prUrl` 时。

`repos[0].startingRef` 字符串（可选）

作为起点的分支名，或完整的 40 字符 commit SHA。提供了 `prUrl` 时会被忽略。

`repos[0].prUrl` 字符串（可选）

Pull request URL，在 GitLab 上称为 merge request URL。提供后，Agent 会在该请求的仓库和分支上工作；`startingRef` 会被忽略。同一条 `repos` 条目上仍必须设置 `url`。

`workOnCurrentBranch` 布尔值（可选，默认值：false）

为 `false`（默认值）时，Cursor 会根据 `repos[0].startingRef`（设置了 `prUrl` 时则为 PR 的 base ref）把 commit 推送到一个自动生成的新分支（`cursor/...`）。为 `true` 时，Cursor 会直接推送到该起始 ref：对于非 PR 的创建，这是你在 `startingRef` 中传入的分支；对于带 `prUrl` 的创建，这是 PR 的 head 分支。Agent 推送到的分支会出现在该 Agent 的 `git.branches[]` 中。

`autoCreatePR` 布尔值（可选）

run 完成时 Cursor 是否应打开一个 pull request。

`skipReviewerRequest` 布尔值（可选）

Cursor 打开 PR 时，是否跳过把用户请求为审查者。仅当 `autoCreatePR` 为 `true` 时适用。

`envVars` 对象（可选）

Cloud Agent 的会话范围环境变量。值会静态加密，注入到 Agent 的 shell，并随 Agent 一起删除。最多 50 项。每个名称是 1 到 255 个字符的 ASCII 字母、数字和下划线，不能以数字开头，不能以 `CURSOR_` 开头，并且在任何字母大小写下都不能包含 `CURSOR_SANDBOX`。每个值非空，且最多 4096 个 UTF-8 字节。不能与客户端提供的 `agentId` 一起使用。

在自托管目标上，`envVars` 只会到达在团队管理员开启 **Secret sync** 时以 `--sync-dashboard-secrets` 运行的 pool worker。`machine` 目标和其他 pool worker 运行时不会带上它们。参见[自托管机器上的环境](https://cursor.com/docs/cloud-agent/self-hosted.md#environments-on-self-hosted-machines)。

**Beta：** `envVars` 正在逐步推出。如果尚未对你的账号启用，创建时会静默忽略该字段，而不会使请求失败。在生产环境依赖这些值之前，请在首次运行时检查 Agent 的 shell，确认值确实存在。

`mcpServers` 数组（可选）

可供 Agent 使用的内联 MCP 服务器定义。最多 50 个服务器。远程服务器支持 `headers` 或 OAuth `auth`；stdio 服务器在云虚拟机内运行，并可以接收 `env`。服务器名称必须唯一。

`mcpServers[0].name` 字符串（必填）

暴露给 Agent 的 MCP 服务器名称。

`mcpServers[0].type` 字符串（可选）

传输类型：`http`、`sse` 或 `stdio`。带 `url` 的远程服务器默认为 `http`，带 `command` 的服务器默认为 `stdio`。

`mcpServers[0].url` 字符串（远程 MCP 必填）

远程 MCP 服务器的 HTTP 或 HTTPS URL。不允许带用户名或密码的 URL。

`mcpServers[0].command` 字符串（stdio MCP 必填）

在 Cloud Agent 虚拟机内启动 stdio MCP 服务器的命令。用 `args` 和 `env` 传递参数和运行时密钥。

`customSubagents` 数组（可选）

定义主 Agent 在本次 run 中可以委派的自定义 Subagent。最多 20 个 Subagent。每个条目需要 `name`、`description` 和 `prompt`，以及可选的 `model`（模型 ID 字符串、`ModelSelection` 对象或 `"inherit"`）。名称必须唯一，且不能与内置项冲突（`explore`、`debug`、`shell`、`computerUse` 等）。

`mode` 字符串（可选，默认值：agent）

Agent 首次 run 的初始对话模式。`plan` 会在编码前先探索并起草计划（[Plan Mode](https://cursor.com/help/ai-features/plan-mode.md)）；`agent` 会直接实现改动。

`agentId` 字符串（可选）

客户端提供的 Agent 标识符，形式为 `bc-<uuid>`。适用于幂等创建流程：用同一个 `agentId` 再次 POST 会返回 `409 agent_id_conflict`，而不会创建重复项。不能与 `envVars` 一起使用；需要会话密钥时请省略 `agentId`，让服务器生成一个。

```bash
curl --request POST \
  --url https://api.cursor.com/v1/agents \
  -u YOUR_API_KEY: \
  --header 'Content-Type: application/json' \
  --data '{
    "prompt": {
      "text": "Add a README with setup instructions"
    },
    "model": {
      "id": "composer-2",
      "params": [
        { "id": "fast", "value": "true" }
      ]
    },
    "repos": [
      {
        "url": "https://github.com/your-org/your-repo",
        "startingRef": "main"
      }
    ],
    "mcpServers": [
      {
        "name": "linear",
        "type": "http",
        "url": "https://mcp.linear.app/sse",
        "headers": {
          "Authorization": "Bearer YOUR_LINEAR_API_KEY"
        }
      },
      {
        "name": "github",
        "type": "stdio",
        "command": "npx",
        "args": ["-y", "@modelcontextprotocol/server-github"],
        "env": {
          "GITHUB_TOKEN": "YOUR_GITHUB_TOKEN"
        }
      }
    ],
    "autoCreatePR": true
  }'
```

自托管 GitLab 仓库：

```bash
curl --request POST \
  --url https://api.cursor.com/v1/agents \
  -u YOUR_API_KEY: \
  --header 'Content-Type: application/json' \
  --data '{
    "prompt": {
      "text": "Add a README with setup instructions"
    },
    "repos": [
      {
        "url": "https://gitlab.example.com/your-group/your-repo",
        "startingRef": "main"
      }
    ]
  }'
```

Worker pool（包括任意仓库）：

```bash
curl --request POST \
  --url https://api.cursor.com/v1/agents \
  -u YOUR_API_KEY: \
  --header 'Content-Type: application/json' \
  --data '{
    "prompt": {
      "text": "Clone the payments service and add a health check"
    },
    "env": {
      "type": "pool",
      "name": "sandbox"
    }
  }'
```

包含多个仓库的任意仓库 pool：

```bash
curl --request POST \
  --url https://api.cursor.com/v1/agents \
  -u YOUR_API_KEY: \
  --header 'Content-Type: application/json' \
  --data '{
    "prompt": {
      "text": "Update the API client and the web app together"
    },
    "env": {
      "type": "pool",
      "name": "my-pool"
    },
    "repos": [
      {
        "url": "https://github.com/your-org/your-api",
        "startingRef": "main"
      },
      {
        "url": "https://github.com/your-org/your-web-app",
        "startingRef": "main"
      }
    ]
  }'
```

**响应：**

```json
{
  "agent": {
    "id": "bc-00000000-0000-0000-0000-000000000001",
    "name": "Add README with setup instructions",
    "status": "ACTIVE",
    "env": {
      "type": "cloud"
    },
    "repos": [
      {
        "url": "https://github.com/your-org/your-repo",
        "startingRef": "main"
      }
    ],
    "workOnCurrentBranch": false,
    "autoCreatePR": true,
    "url": "https://cursor.com/agents/bc-00000000-0000-0000-0000-000000000001",
    "createdAt": "2026-04-13T18:30:00.000Z",
    "updatedAt": "2026-04-13T18:30:00.000Z",
    "latestRunId": "run-00000000-0000-0000-0000-000000000001"
  },
  "run": {
    "id": "run-00000000-0000-0000-0000-000000000001",
    "agentId": "bc-00000000-0000-0000-0000-000000000001",
    "status": "CREATING",
    "createdAt": "2026-04-13T18:30:00.000Z",
    "updatedAt": "2026-04-13T18:30:00.000Z"
  }
}
```

### 列出 Agent

GET

`/v1/agents`

列出已认证用户的 Agent，最新的在前。

#### 查询参数

`limit` 数字（可选）

要返回的 Agent 数量。默认值：20，最大值：100。

`cursor` 字符串（可选）

来自上一页响应中 `nextCursor` 的分页 cursor。

`prUrl` 字符串（可选）

按 pull request URL 筛选 Agent，在 GitLab 上称为 merge request URL。

`includeArchived` 布尔值（可选，默认值：true）

响应中是否包含已归档的 Agent。

列表项只包含持久身份字段。调用 `GET /v1/agents/{id}` 可加载完整记录（`repos`、`workOnCurrentBranch`、`autoCreatePR` 等）。

没有更多页时，响应会**省略** `nextCursor`，而不会把它返回为 `null`。将其缺失视为「没有更多结果」。

```bash
curl --request GET \
  --url 'https://api.cursor.com/v1/agents?limit=20' \
  -u YOUR_API_KEY:
```

**响应：**

```json
{
  "items": [
    {
      "id": "bc-00000000-0000-0000-0000-000000000001",
      "name": "Add README with setup instructions",
      "status": "ACTIVE",
      "env": {
        "type": "cloud"
      },
      "url": "https://cursor.com/agents/bc-00000000-0000-0000-0000-000000000001",
      "createdAt": "2026-04-13T18:30:00.000Z",
      "updatedAt": "2026-04-13T18:45:00.000Z",
      "latestRunId": "run-00000000-0000-0000-0000-000000000001"
    }
  ],
  "nextCursor": "bc-00000000-0000-0000-0000-000000000002"
}
```

### 获取 Agent

GET

`/v1/agents/{id}`

获取 Agent 的持久元数据。执行状态位于 run 上：取出 `latestRunId`，并调用[获取 Run](https://cursor.com/docs/cloud-agent/api/endpoints.md#get-a-run) 来读取 run 状态。

#### 路径参数

`id` 字符串

Agent 的唯一标识符（例如 `bc-00000000-0000-0000-0000-000000000001`）。

#### 响应字段

`status` 字符串

Agent 生命周期状态。控制器用它决定机器是否必须保持运行：

- `ACTIVE`：一个轮次正在运行、等待后台工作，或即将开始。保持该 Agent 的机器处于运行状态。
- `IDLE`：上一轮次已结束，可以接受后续消息。该 Agent 的机器可以被[休眠或创建快照](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#hibernation)。以可恢复错误结束的 run 也会报告 `IDLE`；run 级别的错误详情仍在[获取 Run](https://cursor.com/docs/cloud-agent/api/endpoints.md#get-a-run) 上。
- `ARCHIVED`：该 Agent 已归档或已过期。这是终态；认领会结束，工作区状态可以被删除。

```bash
curl --request GET \
  --url https://api.cursor.com/v1/agents/bc-00000000-0000-0000-0000-000000000001 \
  -u YOUR_API_KEY:
```

**响应：**

```json
{
  "id": "bc-00000000-0000-0000-0000-000000000001",
  "name": "Add README with setup instructions",
  "status": "ACTIVE",
  "env": {
    "type": "cloud"
  },
  "repos": [
    {
      "url": "https://github.com/your-org/your-repo",
      "startingRef": "main"
    }
  ],
  "workOnCurrentBranch": false,
  "autoCreatePR": true,
  "url": "https://cursor.com/agents/bc-00000000-0000-0000-0000-000000000001",
  "createdAt": "2026-04-13T18:30:00.000Z",
  "updatedAt": "2026-04-13T18:30:00.000Z",
  "latestRunId": "run-00000000-0000-0000-0000-000000000001"
}
```

### 发送后续消息或进行引导

POST

`/v1/agents/{id}/runs`

向一个已有的活跃 Agent 发送后续提示词。不带 `delivery` 时，该提示词会启动一次新 run，并使用该 Agent 当前的对话和工作区状态。

每个 Agent 同时只能有一个活跃 run。不带 `delivery` 时，如果另一个 run 处于 `CREATING`、`RUNNING` 或 `WAITING_FOR_BACKGROUND_WORK`，调用此端点会返回 `409 agent_busy`。若要把提示词加入正在运行的 Agent，请用 `delivery: "steer"` 对它进行[引导](https://cursor.com/docs/cloud-agent/api/endpoints.md#steering)。否则，请等待现有 run 结束，或取消它。

当该 Agent 是只有其父 Agent 才能驱动的 Subagent 时，此端点返回 `400 validation_error`，消息为 "Follow-ups and steers to subagents are not supported. Send them to the parent agent."。请改为向父 Agent 发送后续消息或引导。

#### 路径参数

`id` 字符串

Agent 的唯一标识符（例如 `bc-00000000-0000-0000-0000-000000000001`）。

#### 请求正文

`prompt` 对象（必填）

后续提示词，可包含可选图片。

`prompt.text` 字符串（必填）

后续指令文本。

`prompt.images` 数组（可选）

后续消息的图片输入。每个条目必须包含 `data`（base64 编码的字节，且必须有 `mimeType`）或 `url` 二者之一。最多 5 张图片，每张 15 MB。支持的 MIME 类型：`image/png`、`image/jpeg`、`image/gif`、`image/webp`。

`mcpServers` 数组（可选）

这次后续 run 的内联 MCP 服务器定义。提供后，它们会替换该 run 在创建时设置的内联 MCP 服务器。省略则保留 Agent 当前的 MCP 配置。

`mode` 字符串（可选）

这次后续 run 的对话模式覆盖：`agent` 或 `plan`。省略则保留对话在先前 run 中的当前模式。

`delivery` 字符串（可选）

`steer` 把提示词送入 Agent 正在进行的轮次，而不是启动新的 run。一次引导需要 `prompt` 和 `delivery: "steer"`，并且不接受其他顶层字段，因此同时发送 `model`、`mcpServers`、`envVars` 或 `mode` 会返回 `400 validation_error`。普通后续消息请省略 `delivery`。参见[引导](https://cursor.com/docs/cloud-agent/api/endpoints.md#steering)。

#### 引导

引导会在 Agent 工作期间向它追加一条提示词，而不会被 `409 agent_busy` 拒绝。无论是否有活跃 run，都可以发送一次引导。响应为 `202`，并带有 `delivery` 字段：

- `steered`：Agent 正在进行的轮次接受了该提示词。响应包含 `runId`，即接受这次引导的 run，也就是 API 收到引导时正在运行的那个 run。提示词会在该 run 的下一个安全步骤到达，除非该 run 先结束；在那种情况下，提示词会启动该 Agent 的下一次 run。当 Agent 没有活跃 run 时，会省略 `runId`。
- `queued`：没有正在运行的轮次可以接收该提示词，因此它会成为该 Agent 的下一次后续消息。这也包括恰好在 Agent 的 run 发生变化之后到达的引导。`queued` 响应从不包含 `runId`。

除 `prompt` 和 `delivery` 之外还带有任何顶层字段的引导正文，会返回 `400 validation_error`，错误消息会点名这些字段，例如 `"model" is not allowed with delivery "steer"` 或 `"model", "mode" are not allowed with delivery "steer"`。`delivery` 不是 `steer` 时也会返回 `400 validation_error`。Cursor 会先检查正文，因此即使你的账号无法使用引导，也会出现这些错误。Cursor 拒绝的引导同样返回 `400 validation_error`。如果你的账号无法使用引导，一次有效的引导会返回 `403 feature_unavailable`。

因服务器或网络错误而失败的引导，仍可能已经到达 Agent。Cursor 把重试视为一条新提示词，因此重试可能导致提示词被投递两次。

```bash
curl --request POST \
  --url https://api.cursor.com/v1/agents/bc-00000000-0000-0000-0000-000000000001/runs \
  -u YOUR_API_KEY: \
  --header 'Content-Type: application/json' \
  --data '{
    "prompt": {
      "text": "Also add troubleshooting steps"
    },
    "mcpServers": [
      {
        "name": "docs",
        "type": "http",
        "url": "https://example.com/mcp"
      }
    ]
  }'
```

**响应：**

```json
{
  "run": {
    "id": "run-00000000-0000-0000-0000-000000000002",
    "agentId": "bc-00000000-0000-0000-0000-000000000001",
    "status": "CREATING",
    "createdAt": "2026-04-13T18:50:00.000Z",
    "updatedAt": "2026-04-13T18:50:00.000Z"
  }
}
```

引导正在运行的 Agent：

```bash
curl --request POST \
  --url https://api.cursor.com/v1/agents/bc-00000000-0000-0000-0000-000000000001/runs \
  -u YOUR_API_KEY: \
  --header 'Content-Type: application/json' \
  --data '{
    "prompt": {
      "text": "Also cover the error path"
    },
    "delivery": "steer"
  }'
```

**响应：** `202`

```json
{
  "delivery": "steered",
  "runId": "run-00000000-0000-0000-0000-000000000002"
}
```

如果没有正在运行的轮次可以接收该提示词，同一请求会返回：

```json
{
  "delivery": "queued"
}
```

### 列出 Run

GET

`/v1/agents/{id}/runs`

列出某个 Agent 的 run，最新的在前。

#### 路径参数

`id` 字符串

Agent 的唯一标识符。

#### 查询参数

`limit` 数字（可选）

要返回的 run 数量。默认值：20，最大值：100。

`cursor` 字符串（可选）

来自上一页响应中 `nextCursor` 的分页 cursor。

```bash
curl --request GET \
  --url 'https://api.cursor.com/v1/agents/bc-00000000-0000-0000-0000-000000000001/runs?limit=20' \
  -u YOUR_API_KEY:
```

**响应：**

```json
{
  "items": [
    {
      "id": "run-00000000-0000-0000-0000-000000000002",
      "agentId": "bc-00000000-0000-0000-0000-000000000001",
      "status": "RUNNING",
      "createdAt": "2026-04-13T18:50:00.000Z",
      "updatedAt": "2026-04-13T18:51:00.000Z",
      "git": {
        "branches": [
          {
            "repoUrl": "github.com/your-org/your-repo",
            "branch": "cursor/add-readme-a1b2"
          }
        ]
      }
    }
  ]
}
```

### 获取 Run

GET

`/v1/agents/{id}/runs/{runId}`

获取某次 run 的状态、时间戳，以及（对于已结束的 run）最终结果、持续时长和已推送的分支。

#### 路径参数

`id` 字符串

Agent 的唯一标识符。

`runId` 字符串

run 的唯一标识符（例如 `run-00000000-0000-0000-0000-000000000001`）。

#### 响应字段

基础 run 字段（`id`、`agentId`、`status`、`createdAt`、`updatedAt`）始终存在。run 活跃时，`status` 为 `CREATING`、`RUNNING` 或 `WAITING_FOR_BACKGROUND_WORK`；结束后为 `FINISHED`、`ERROR`、`CANCELLED` 或 `EXPIRED`。以下字段会在数据可用时立即填充：

`durationMs` 整数（已结束的 run）

run 的实际经过时长，单位为毫秒，在 run 到达 `FINISHED`、`ERROR`、`CANCELLED` 或 `EXPIRED` 后计算。

`result` 字符串（已结束的 run）

已结束 run 的最终助手回复文本。

`git` 对象（已推送分支时）

Agent 当前已推送的分支和 pull request。`git.branches[]` 包含 `{ repoUrl, branch?, prUrl? }` 条目，Agent 推送的每个分支一条（堆叠 Agent 会产生多条）。

**这是每个 Agent 的状态，不是每次 run 的状态。** 同一 Agent 的每次 run 都返回相同的 `git` 快照。使用 Agent 的 `latestRunId` 或 SSE 流，把工作归因到某次特定 run。

返回的 `repoUrl` 不带协议（例如 `github.com/your-org/your-repo`），与请求中的 `repos[].url` 不同，后者保留 `https://` 前缀。

```bash
curl --request GET \
  --url https://api.cursor.com/v1/agents/bc-00000000-0000-0000-0000-000000000001/runs/run-00000000-0000-0000-0000-000000000001 \
  -u YOUR_API_KEY:
```

**响应：**

```json
{
  "id": "run-00000000-0000-0000-0000-000000000001",
  "agentId": "bc-00000000-0000-0000-0000-000000000001",
  "status": "FINISHED",
  "createdAt": "2026-04-13T18:30:00.000Z",
  "updatedAt": "2026-04-13T18:45:00.000Z",
  "durationMs": 12357,
  "result": "Added README.md with installation instructions and usage examples.",
  "git": {
    "branches": [
      {
        "repoUrl": "github.com/your-org/your-repo",
        "branch": "cursor/add-readme-a1b2",
        "prUrl": "https://github.com/your-org/your-repo/pull/123"
      }
    ]
  }
}
```

### 流式获取 Run

GET

`/v1/agents/{id}/runs/{runId}/stream`

为一次 run 流式传输 Server-Sent Events（SSE）。该流仅限于所请求的 run，不会重放先前的 run。

#### 事件类型

- `status`：run 状态更新。载荷：`{ runId, status }`。
- `assistant`：助手文本增量。载荷：`{ text }`。
- `thinking`：思考文本增量。载荷：`{ text }`。
- `tool_call`：工具调用状态更新。载荷：`{ callId, name, status, args?, result?, truncated? }`。
- `interaction_update`：可选的更丰富事件，与上面的简化事件一同发出。载荷与 [TypeScript SDK](https://cursor.com/docs/sdk/typescript.md) 所消费的 `InteractionUpdate` 形状一致，子类型包括 `text-delta`、`tool-call-started` / `tool-call-completed`、`step-started` / `step-completed` 和 `turn-ended`。如果只需要纯文本和工具调用，处理简化事件并忽略 `interaction_update`。如果需要完整的 SDK 形状流，处理 `interaction_update` 并忽略简化事件。
- `heartbeat`：保活事件。载荷：`{}`。
- `result`：run 的终态。载荷：`{ runId, status, text?, durationMs?, git? }`。`text` 是最终助手回复，`durationMs` 是 run 的实际经过时长（毫秒），`git` 与 `Run.git` 一致（Agent 当前已推送的分支，不只是这次 run 的）。
- `error`：流错误。载荷：`{ code, message }`。
- `done`：流结束。载荷：`{}`。

#### 工具调用载荷

`tool_call` 事件用稳定的信封包裹各工具自己的输入和输出：

```typescript
type JsonValue =
  | string
  | number
  | boolean
  | null
  | JsonValue[]
  | { [key: string]: JsonValue };

interface ToolCallEventData {
  callId: string;
  name: string;
  status: "running" | "completed";
  args?: JsonValue;
  result?: JsonValue;
  truncated?: {
    args?: true;
    result?: true;
  };
}
```

`callId` 在多次更新中标识同一次工具调用。`name` 是公开的工具名称，例如 `read_file`、`run_terminal_cmd` 或 `mcp`。`args` 和 `result` 是该工具自己的 JSON 值。如果 `args` 或 `result` 太大而无法放入流中，Cursor 会省略该字段，并设置对应的 `truncated` 标志。

#### 恢复流

大多数事件包含一行 `id`，这是一个不应解析的不透明字符串（当前格式类似 `1713033006000-0`，但请把它当作不透明值）。开头的 `status` 事件没有 `id`，它是一个粘性框架事件，每次重连时都会在最前面重新发送。

断开后要恢复，请重新连接，并把 `Last-Event-ID` 设为最近收到的事件 id。该事件 id 必须属于所请求的 run，否则请求返回 `400 invalid_last_event_id`。成功恢复后，在恢复区间开始之前，还会再收到一个 `status` 事件。

#### 保留期

流响应包含 `X-Cursor-Stream-Retention-Seconds` 头。保留窗口过后，此端点可能返回 `410 stream_expired`。请把它视为信号：改为通过[获取 Run](https://cursor.com/docs/cloud-agent/api/endpoints.md#get-a-run) 读取终态，而不是重试该流。

```bash
curl --request GET \
  --url https://api.cursor.com/v1/agents/bc-00000000-0000-0000-0000-000000000001/runs/run-00000000-0000-0000-0000-000000000001/stream \
  -u YOUR_API_KEY: \
  --header 'Accept: text/event-stream'
```

**示例流：**

```text
event: status
data: {"runId":"run-00000000-0000-0000-0000-000000000001","status":"RUNNING"}

id: 1713033000000-0
event: assistant
data: {"text":"I'll update the README now."}

id: 1713033005000-0
event: tool_call
data: {"callId":"call-1","name":"read_file","status":"running","args":{"path":"README.md"}}

id: 1713033006000-0
event: tool_call
data: {"callId":"call-1","name":"read_file","status":"completed","args":{"path":"README.md"},"result":{"success":{"content":"# Project","totalLines":1,"fileSize":9,"path":"README.md"}}}

id: 1713033010000-0
event: result
data: {"runId":"run-00000000-0000-0000-0000-000000000001","status":"FINISHED","text":"Added README.md with installation instructions.","durationMs":12357,"git":{"branches":[{"repoUrl":"github.com/your-org/your-repo","branch":"cursor/add-readme-a1b2"}]}}

id: 1713033010000-0
event: done
data: {}
```

### 取消 Run

POST

`/v1/agents/{id}/runs/{runId}/cancel`

取消某个 Agent 的活跃 run。取消是终态：该 run 会变为 `CANCELLED`，且不能恢复。要继续对话，请在同一 Agent 上创建新的 run。

取消已经处于终态、或从未活跃过的 run，会返回 `409 run_not_cancellable`。

#### 路径参数

`id` 字符串

Agent 的唯一标识符。

`runId` 字符串

要取消的 run 的唯一标识符。

```bash
curl --request POST \
  --url https://api.cursor.com/v1/agents/bc-00000000-0000-0000-0000-000000000001/runs/run-00000000-0000-0000-0000-000000000001/cancel \
  -u YOUR_API_KEY:
```

**响应：**

```json
{
  "id": "run-00000000-0000-0000-0000-000000000001"
}
```

### 获取 Agent 用量

GET

`/v1/agents/{id}/usage`

获取某个 Agent 的 token 用量，并按 run 拆分。响应会列出该 Agent 每次 run 的用量，以及该 Agent 的合计用量，包括其 Task Subagent 和侧边对话。token 用量与团队[用量事件](https://cursor.com/docs/account/teams/admin-api.md#get-usage-events-data)端点报告的 `tokenUsage` 一致。

#### 路径参数

`id` 字符串

Agent 的唯一标识符（例如 `bc-00000000-0000-0000-0000-000000000001`）。

#### 查询参数

`runId` 字符串（可选）

把响应限定为单次 run（例如 `run-00000000-0000-0000-0000-000000000001`）。省略则返回该 Agent 上每次 run 的用量。未知的 `runId` 返回 `404 run_not_found`。

#### 响应字段

`totalUsage` 对象

该请求的 token 用量。不带 `runId` 时，它还会计入该 Agent 的 Task Subagent、侧边对话，以及未绑定到某次 run 的用量，因此可能大于 `runs` 的合计。带 `runId` 时，它覆盖该 run，以及从该 run 开始到该 Agent 下一次 run 开始之间的 Task Subagent、侧边对话和其他未绑定到 run 的用量。最先开始的 run 还会计入它开始之前的这类用量，从未开始的 run 则不计。`runs` 中该 run 的条目包含相同的用量。所含字段与每次 run 的 `usage` 对象相同。

`runs` 数组

按 run 的用量，每次 run 一条（设置了 `runId` 时只有一条）。每个对象包含：

- `id` 字符串：run 标识符（例如 `run-00000000-0000-0000-0000-000000000001`）。
- `usageUuid` 字符串（可选）：该 run 的内部用量标识符。当 run 还没有记录用量时省略。
- `usage` 对象：这次 run 的 token 用量：
  - `inputTokens` 数字：消耗的输入 token。
  - `outputTokens` 数字：生成的输出 token。
  - `cacheWriteTokens` 数字：写入缓存的 token。
  - `cacheReadTokens` 数字：从缓存读取的 token。
  - `totalTokens` 数字：以上四项 token 计数之和。

没有任何已记录 token 用量的 run，所有字段都报告为零。尚未产生用量的 run 仍会出现在 `runs` 中，以便你随时间跟踪。

```bash
# All runs on the agent
curl --request GET \
  --url https://api.cursor.com/v1/agents/bc-00000000-0000-0000-0000-000000000001/usage \
  -u YOUR_API_KEY:

# A single run
curl --request GET \
  --url 'https://api.cursor.com/v1/agents/bc-00000000-0000-0000-0000-000000000001/usage?runId=run-00000000-0000-0000-0000-000000000001' \
  -u YOUR_API_KEY:
```

**响应：**

```json
{
  "totalUsage": {
    "inputTokens": 12480,
    "outputTokens": 3110,
    "cacheWriteTokens": 18200,
    "cacheReadTokens": 42600,
    "totalTokens": 76390
  },
  "runs": [
    {
      "id": "run-00000000-0000-0000-0000-000000000002",
      "usageUuid": "00000000-0000-0000-0000-000000000002",
      "usage": {
        "inputTokens": 6320,
        "outputTokens": 1450,
        "cacheWriteTokens": 7100,
        "cacheReadTokens": 21300,
        "totalTokens": 36170
      }
    },
    {
      "id": "run-00000000-0000-0000-0000-000000000001",
      "usageUuid": "00000000-0000-0000-0000-000000000001",
      "usage": {
        "inputTokens": 6160,
        "outputTokens": 1660,
        "cacheWriteTokens": 11100,
        "cacheReadTokens": 21300,
        "totalTokens": 40220
      }
    }
  ]
}
```

## 产物

产物以 Agent 为范围，因为工作区会在多次 run 之间保持。

### 列出产物

GET

`/v1/agents/{id}/artifacts`

列出某个 Agent 产生的产物。每个产物的 `path` 相对于工作区的 `artifacts/` 目录。

把这里返回的 `path` 值直接传给[下载产物](https://cursor.com/docs/cloud-agent/api/endpoints.md#download-an-artifact)。v1 路径是相对路径；不接受绝对的 v0 路径（`/opt/cursor/artifacts/...`）。

#### 路径参数

`id` 字符串

Agent 的唯一标识符。

```bash
curl --request GET \
  --url https://api.cursor.com/v1/agents/bc-00000000-0000-0000-0000-000000000001/artifacts \
  -u YOUR_API_KEY:
```

**响应：**

```json
{
  "items": [
    {
      "path": "artifacts/screenshot.png",
      "sizeBytes": 12345,
      "updatedAt": "2026-04-13T18:45:00.000Z"
    }
  ]
}
```

### 下载产物

GET

`/v1/agents/{id}/artifacts/download`

获取某个产物的临时预签名 S3 URL，有效期 15 分钟。

#### 路径参数

`id` 字符串

Agent 的唯一标识符。

#### 查询参数

`path` 字符串

[列出产物](https://cursor.com/docs/cloud-agent/api/endpoints.md#list-artifacts) 返回的相对产物路径（例如 `artifacts/screenshot.png`）。必须位于 `artifacts/` 之下。

```bash
curl --request GET \
  --url 'https://api.cursor.com/v1/agents/bc-00000000-0000-0000-0000-000000000001/artifacts/download?path=artifacts/screenshot.png' \
  -u YOUR_API_KEY:
```

**响应：**

```json
{
  "url": "https://cloud-agent-artifacts.s3.us-east-1.amazonaws.com/...",
  "expiresAt": "2026-04-13T19:00:00.000Z"
}
```

## Agent 生命周期

### 归档 Agent

POST

`/v1/agents/{id}/archive`

归档一个 Agent。已归档的 Agent 仍可读取，但在取消归档之前不能接受新的 run。用于可逆的「软删除」流程。

归档是幂等的：对已经归档的 Agent 再次归档会返回 `200`，且没有变化。调用前不需要检查当前状态。

#### 路径参数

`id` 字符串

Agent 的唯一标识符。

```bash
curl --request POST \
  --url https://api.cursor.com/v1/agents/bc-00000000-0000-0000-0000-000000000001/archive \
  -u YOUR_API_KEY:
```

**响应：**

```json
{
  "id": "bc-00000000-0000-0000-0000-000000000001"
}
```

### 取消归档 Agent

POST

`/v1/agents/{id}/unarchive`

取消归档一个 Agent，使它能再次接受新的 run。

取消归档是幂等的：对已经处于活跃状态的 Agent 调用会返回 `200`，且没有变化。

#### 路径参数

`id` 字符串

Agent 的唯一标识符。

```bash
curl --request POST \
  --url https://api.cursor.com/v1/agents/bc-00000000-0000-0000-0000-000000000001/unarchive \
  -u YOUR_API_KEY:
```

**响应：**

```json
{
  "id": "bc-00000000-0000-0000-0000-000000000001"
}
```

### 永久删除 Agent

DELETE

`/v1/agents/{id}`

永久删除一个 Agent。此操作不可逆。可逆的移除请使用[归档](https://cursor.com/docs/cloud-agent/api/endpoints.md#archive-an-agent)。

#### 路径参数

`id` 字符串

Agent 的唯一标识符。

```bash
curl --request DELETE \
  --url https://api.cursor.com/v1/agents/bc-00000000-0000-0000-0000-000000000001 \
  -u YOUR_API_KEY:
```

**响应：**

```json
{
  "id": "bc-00000000-0000-0000-0000-000000000001"
}
```

## 环境

环境是已保存的 [Cloud Agent 环境](https://cursor.com/docs/cloud-agent/setup.md)，Agent 在其中运行：也就是你在 [Cloud Agents 仪表盘](https://cursor.com/dashboard/cloud-agents#environments) 中管理的仓库和 `environment.json` 配置。使用这些端点，可以用脚本完成同样的工作。

### 创建环境

POST

`/v1/environments`

创建一个已保存的环境。响应为 `201`，并带有新环境。

如果所有者已经有同名 `name` 的环境，请求会返回 `409 environment_name_conflict`。错误中可以在 `environmentId` 里包含已有环境的 ID。

#### 请求正文

`owner` 字符串（必填）

`personal` 表示为该 API key 的用户创建环境，`team` 表示为团队创建环境。

`name` 字符串（必填）

显示名称，最多 255 个字符。它必须与该所有者的其他环境名称不同。

`repos` 数组（必填）

该环境的仓库。每个条目有一个 `url`（例如 `https://github.com/your-org/your-repo`）。最多 500 个仓库。发送空数组可创建没有仓库的环境。Cursor 无法通过你的源代码托管集成访问的仓库会返回 `400 repository_access`。

`environmentJson` 字符串（必填）

该环境的 `environment.json`，为 JSON 编码的字符串。它使用与 `.cursor/environment.json` 文件相同的 [schema](https://cursor.com/docs/cloud-agent/setup.md#configuration-in-code-with-environmentjson)。无效配置会返回 `400 validation_error`。

#### 响应字段

`id` 字符串

环境 ID。

`name` 字符串

显示名称。

`owner` 字符串

`personal` 表示属于单个用户的环境，`team` 表示与团队共享的环境。

`repos` 数组

环境中的仓库。每个条目有一个 `url`。

`createdAt`、`updatedAt` 字符串

环境的创建时间和最后更新时间（ISO 8601）。

```bash
curl --request POST \
  --url https://api.cursor.com/v1/environments \
  -u YOUR_API_KEY: \
  --header 'Content-Type: application/json' \
  --data '{
    "owner": "team",
    "name": "Web app",
    "repos": [
      { "url": "https://github.com/your-org/your-web-app" },
      { "url": "https://github.com/your-org/your-api" }
    ],
    "environmentJson": "{\"install\": \"pnpm install\", \"start\": \"sudo service docker start\"}"
  }'
```

**响应：**

```json
{
  "id": "8f14e45f-ceea-4e6b-9c3a-1d2e3f4a5b6c",
  "name": "Web app",
  "owner": "team",
  "repos": [
    { "url": "https://github.com/your-org/your-web-app" },
    { "url": "https://github.com/your-org/your-api" }
  ],
  "createdAt": "2026-09-30T21:40:00.000Z",
  "updatedAt": "2026-09-30T21:40:00.000Z"
}
```

### 列出环境

GET

`/v1/environments`

列出你的 API key 可以访问的已保存环境，最近更新的在前。不带 `userId` 时，列表包含你的个人环境和你所在团队的环境。团队管理员只有传入某位现任成员的 `userId`，才能列出该成员的个人环境。

团队管理员和服务账号 API key 可以看到每一个团队环境。其他调用方只有在能够访问某个团队环境的全部仓库时，才能看到该环境。限定于特定仓库的服务账号 API key，以及用它签发的用户范围 token，只会列出拥有仓库、且这些仓库全部落在该限制内的环境。不包含草稿和已删除的环境。

筛选条件会叠加，因此列表只保留与你发送的每一个筛选条件都匹配的环境。无效的筛选值会返回 `400 validation_error`。

#### 查询参数

`query` 字符串（可选）

只返回名称包含此文本的环境，忽略大小写。1 到 255 个字符。

`owner` 字符串（可选）

`personal` 或 `team`，只列出具有该 `owner` 的环境。

`named` 布尔值（可选）

`true` 表示只要有 `name` 的环境，`false` 表示只要没有名称的环境。

`repo` 字符串（可选，可重复）

只返回包含此仓库的环境，例如 `github.com/acme/api`。可重复，最多 100 次，以列出包含其中任一仓库的环境。

`userId` 字符串（可选）

只返回某一个用户的个人环境。传入 [API Key 信息](https://cursor.com/docs/cloud-agent/api/endpoints.md#api-key-info) 中的数字 `userId`，或 Admin API [团队成员列表](https://cursor.com/docs/account/teams/admin-api.md#get-team-members) 中的 `user_` ID。你自己的 ID 会列出你的个人环境。团队管理员的用户 API key 也可以传入一位现任团队成员的 ID，并得到该成员那些至少有一个仓库、且管理员都能读取的个人环境。对于你无权列出的用户，一个有效 ID 会返回 `items: []`，且没有 `nextCursor`，而不是错误。把 `userId` 与 `owner=team` 组合会返回 `400 validation_error`。

`limit` 数字（可选）

要返回的环境数量。默认值：20，最大值：100。

`cursor` 字符串（可选）

来自上一页响应中 `nextCursor` 的分页 cursor。每次带 `cursor` 时发送相同的筛选条件。

#### 响应字段

`items` 数组

环境，字段与[获取环境](https://cursor.com/docs/cloud-agent/api/endpoints.md#get-an-environment)相同，但不含 `environmentJson`、`versionId` 和 `buildSettings`。调用获取环境可加载某个环境的配置和 Build 设置。

`nextCursor` 字符串（可选）

下一页的 cursor。没有更多页时省略。

一页返回的条目可以少于 `limit`，当 `userId` 指向另一位成员时也可以为空。请持续请求后续页，直到 `nextCursor` 不存在。没有更多页时，它会从响应中被**省略**，而不是返回为 `null`。

团队管理员和服务账号 API key 以外的调用方，要等 Cursor 检查他们对仓库的访问权限之后，才能看到团队环境，而这些检查可能会超时。因此响应可能漏掉尚未验证的团队环境，或在列表结束之前停下。这些环境会出现在之后的请求中。

列表按最后更新时间排序，因此在遍历过程中发生变化的环境会移到最前面。后面的页就可能漏掉它，并重复另一个环境。

```bash
curl --request GET \
  --url 'https://api.cursor.com/v1/environments?repo=github.com/your-org/your-web-app&repo=github.com/your-org/your-api' \
  -u YOUR_API_KEY:
```

**响应：**

```json
{
  "items": [
    {
      "id": "8f14e45f-ceea-4e6b-9c3a-1d2e3f4a5b6c",
      "name": "Web app",
      "owner": "team",
      "repos": [
        { "url": "https://github.com/your-org/your-web-app" },
        { "url": "https://github.com/your-org/your-api" }
      ],
      "createdAt": "2026-09-01T16:20:00.000Z",
      "updatedAt": "2026-09-29T21:05:00.000Z"
    }
  ],
  "nextCursor": "MjA"
}
```

### 获取环境

GET

`/v1/environments/{id}`

获取一个已保存的环境、它最新保存的配置，以及它的 Build 设置。

#### 路径参数

`id` 字符串

环境 ID（例如 `8f14e45f-ceea-4e6b-9c3a-1d2e3f4a5b6c`）。

#### 响应字段

[创建环境](https://cursor.com/docs/cloud-agent/api/endpoints.md#create-an-environment) 返回的字段，再加上：

`repoFile` 对象（可选）

当环境从仓库中的某个文件读取配置时设置，并指向该文件：仓库 `url` 和文件 `path`。

`environmentJson` 字符串（可选）

环境最新保存的配置，即它的 `environment.json`，为 JSON 编码的字符串。

`versionId` 字符串（可选）

最新已保存环境版本的 ID。尚未保存任何版本时省略。

`buildSettings` 对象

该环境的 [Build](https://cursor.com/docs/cloud-agent/builds.md#how-builds-handle-git-state) 设置：

- `pullLatestCode` 字符串：在默认分支上启动的 Agent 是否拉取该分支的最新代码。`when_stale` 是默认值，当当前 Build 早于 `stalenessThresholdHours` 时会拉取。`never` 使用 Build 所记录的 commit。`always` 是遗留值，可以读取但不能设置。
- `stalenessThresholdHours` 整数或 null：当前 Build 被视为过期之前的小时数，范围 0 到 8,760。`0` 表示每次启动都拉取。`null` 表示默认值 24。
- `installTimeoutMinutes` 整数或 null：Build 的安装步骤可以运行的分钟数，范围 5 到 300。`null` 表示默认值 90。

```bash
curl --request GET \
  --url https://api.cursor.com/v1/environments/8f14e45f-ceea-4e6b-9c3a-1d2e3f4a5b6c \
  -u YOUR_API_KEY:
```

**响应：**

```json
{
  "id": "8f14e45f-ceea-4e6b-9c3a-1d2e3f4a5b6c",
  "name": "Web app",
  "owner": "team",
  "repos": [
    { "url": "https://github.com/your-org/your-web-app" },
    { "url": "https://github.com/your-org/your-api" }
  ],
  "environmentJson": "{\"install\": \"pnpm install\", \"start\": \"sudo service docker start\"}",
  "versionId": "c9f0f895-fb98-4b91-8f3e-2a1b0c9d8e7f",
  "buildSettings": {
    "pullLatestCode": "when_stale",
    "stalenessThresholdHours": null,
    "installTimeoutMinutes": 120
  },
  "createdAt": "2026-09-01T16:20:00.000Z",
  "updatedAt": "2026-09-29T21:05:00.000Z"
}
```

### 更新环境

PATCH

`/v1/environments/{id}`

重命名环境、替换其配置、替换其仓库，或任意组合。请求会把所发送的每个字段一起应用，因此要么全部更改，要么都不更改。请在单独的请求中更改 [Build 设置](https://cursor.com/docs/cloud-agent/api/endpoints.md#get-an-environment)。响应为 `204`，没有正文。

把环境重命名为所有者已经使用的名称，会返回 `409 environment_name_conflict`。

#### 路径参数

`id` 字符串

环境 ID。

#### 请求正文

`name` 字符串（可选）

新的显示名称，最多 255 个字符。它必须与该所有者的其他环境名称不同。

`environmentJson` 字符串（可选）

替换用的 `environment.json`，为 JSON 编码的字符串。它会替换整个配置，并使用与 `.cursor/environment.json` 文件相同的 [schema](https://cursor.com/docs/cloud-agent/setup.md#configuration-in-code-with-environmentjson)。无效配置会返回 `400 validation_error`。如果环境从它唯一仓库中的文件读取配置，请求会返回 `409 repo_file_environment_locked`。

`repos` 数组（可选）

该环境的完整仓库列表。它会替换当前列表，因此要保留的每个仓库都要包含在内。每个条目有一个 `url`，列表遵循与[创建环境](https://cursor.com/docs/cloud-agent/api/endpoints.md#create-an-environment)相同的规则。请同时发送 `environmentJson`：仓库变更会保存整个配置，因此没有 `environmentJson` 的 `repos` 会返回 `400 validation_error`。要保留当前配置，请发送[获取环境](https://cursor.com/docs/cloud-agent/api/endpoints.md#get-an-environment) 返回的 `environmentJson`。哪些环境可以更改仓库，参见[仓库变更](https://cursor.com/docs/cloud-agent/api/endpoints.md#repository-changes)。

`buildSettings` 对象（可选）

[获取环境](https://cursor.com/docs/cloud-agent/api/endpoints.md#get-an-environment) 所返回的任意 `buildSettings` 字段的新值。你省略的字段保持不变，`null` 会把字段重置为默认值：`when_stale`、24 或 90。请单独发送 `buildSettings`，因为同时发送 `name`、`environmentJson` 或 `repos` 的请求会返回 `400 validation_error`。把 `pullLatestCode` 设为 `always` 也会返回 `400 validation_error`。要让环境离开 `always`，请发送 `pullLatestCode: "when_stale"` 以及 `stalenessThresholdHours`。

#### 仓库变更

只有拥有两个或更多仓库的环境才能更改仓库，并且必须至少保留两个。原样发送当前列表不算变更。

- 没有仓库的环境不能添加任何仓库，单仓库环境也不能更改它的仓库。两者都返回 `409 environment_repo_edit_blocked`。要在不同仓库中工作，请创建新环境。
- 多仓库环境可以添加、移除和替换仓库。少于两个仓库的列表会返回 `409 environment_repo_edit_blocked`。

当环境从仓库中的文件读取配置时，[获取环境](https://cursor.com/docs/cloud-agent/api/endpoints.md#get-an-environment) 会在 `repoFile` 中返回该文件。更改其仓库会保留该文件作为配置来源，并有这些限制：

- 在 `repos` 中保留 `repoFile.url` 对应的仓库。不包含它的列表会返回 `409 repo_file_repo_removed`。Cursor 比较的是仓库，而不是精确字符串，因此末尾的 `.git` 或斜杠、缺少的 `https://`、开头的 `www.`，或 GitHub 上不同的字母大小写，仍然会匹配。如果列表以不同形式多次写出该仓库，Cursor 保留第一条并丢弃其余。对于 Bitbucket Server，请按获取环境所返回的 URL 发送。clone URL 不会匹配。
- 唯一仓库中存放配置文件的环境会被锁定到该文件。任何带 `repos` 的请求都会返回 `409 repo_file_environment_locked`，即使是当前列表也不例外。
- 当 Cursor 无法读取环境已存储的仓库时，带 `repos` 的请求会返回 `400 stored_repos_unreadable`。

```bash
curl --request PATCH \
  --url https://api.cursor.com/v1/environments/8f14e45f-ceea-4e6b-9c3a-1d2e3f4a5b6c \
  -u YOUR_API_KEY: \
  --header 'Content-Type: application/json' \
  --data '{
    "name": "Web app (staging)",
    "environmentJson": "{\"install\": \"pnpm install --frozen-lockfile\", \"start\": \"sudo service docker start\"}"
  }'
```

**响应：** `204 No Content`

替换仓库并保留当前配置：

```bash
curl --request PATCH \
  --url https://api.cursor.com/v1/environments/8f14e45f-ceea-4e6b-9c3a-1d2e3f4a5b6c \
  -u YOUR_API_KEY: \
  --header 'Content-Type: application/json' \
  --data '{
    "repos": [
      { "url": "https://github.com/your-org/your-web-app" },
      { "url": "https://github.com/your-org/your-api" },
      { "url": "https://github.com/your-org/your-design-system" }
    ],
    "environmentJson": "{\"install\": \"pnpm install\", \"start\": \"sudo service docker start\"}"
  }'
```

**响应：** `204 No Content`

更改 Build 设置：

```bash
curl --request PATCH \
  --url https://api.cursor.com/v1/environments/8f14e45f-ceea-4e6b-9c3a-1d2e3f4a5b6c \
  -u YOUR_API_KEY: \
  --header 'Content-Type: application/json' \
  --data '{
    "buildSettings": {
      "pullLatestCode": "when_stale",
      "stalenessThresholdHours": 12,
      "installTimeoutMinutes": null
    }
  }'
```

**响应：** `204 No Content`

### 删除环境

DELETE

`/v1/environments/{id}`

永久删除一个已保存的环境。此操作不可逆。

#### 路径参数

`id` 字符串

环境 ID。

```bash
curl --request DELETE \
  --url https://api.cursor.com/v1/environments/8f14e45f-ceea-4e6b-9c3a-1d2e3f4a5b6c \
  -u YOUR_API_KEY:
```

**响应：**

```json
{
  "id": "8f14e45f-ceea-4e6b-9c3a-1d2e3f4a5b6c"
}
```

### 列出环境历史

GET

`/v1/environments/{id}/history`

列出某个环境的变更，最新的在前。这些就是 [Cloud Agents 仪表盘](https://cursor.com/dashboard/cloud-agents#environments) 中 History 标签页所显示的事件。

#### 路径参数

`id` 字符串

环境 ID。

#### 查询参数

`limit` 数字（可选）

要返回的事件数量。默认值：20，最大值：100。

`cursor` 字符串（可选）

来自上一页响应中 `nextCursor` 的分页 cursor。

#### 响应字段

`items` 数组

历史事件，最新的在前，字段如下。

`nextCursor` 字符串（可选）

下一页的 cursor。没有更多事件时省略。

#### 事件字段

`id` 字符串

事件 ID。

`createdAt` 字符串

做出变更的时间（ISO 8601）。

`kind` 字符串

`created`、`updated`、`deleted`、`backfilled` 或 `changed`。

`title`、`description` 字符串

事件的摘要和详情，与 History 标签页所显示的一致。

`source` 字符串（可选）

变更来自何处：`dashboard`、`setup_flow`、`restore`、`api`、`agent_run`、`repo_file` 或 `request_override`。

`current` 布尔值

保存了该环境当前配置的事件为 `true`。

`environmentJson` 字符串（可选）

该事件所保存的 `environment.json`，为 JSON 编码的字符串。

```bash
curl --request GET \
  --url 'https://api.cursor.com/v1/environments/8f14e45f-ceea-4e6b-9c3a-1d2e3f4a5b6c/history?limit=2' \
  -u YOUR_API_KEY:
```

**响应：**

```json
{
  "items": [
    {
      "id": "3b7e1f2a-9c4d-4e8b-a1f6-5d2c8e9b0a17",
      "createdAt": "2026-09-29T21:05:00.000Z",
      "kind": "updated",
      "title": "Team environment updated",
      "description": "Install script changed.",
      "source": "api",
      "current": true,
      "environmentJson": "{\"install\": \"pnpm install\", \"start\": \"sudo service docker start\"}"
    },
    {
      "id": "e4a9c2d1-6b3f-4a7e-9d8c-1f0b2a3c4d5e",
      "createdAt": "2026-09-01T16:20:00.000Z",
      "kind": "created",
      "title": "Team environment created",
      "description": "Team environment created.",
      "source": "dashboard",
      "current": false,
      "environmentJson": "{\"install\": \"npm install\"}"
    }
  ],
  "nextCursor": "e4a9c2d1-6b3f-4a7e-9d8c-1f0b2a3c4d5e"
}
```

### 列出环境 Build

GET

`/v1/environments/{id}/builds`

列出某个环境的 [Build](https://cursor.com/docs/cloud-agent/builds.md)，最新的在前。可按状态或创建时间筛选。

#### 路径参数

`id` 字符串

环境 ID。

#### 查询参数

`status` 字符串（可选）

只返回具有此状态的 Build：`IN_PROGRESS`、`SUCCEEDED`、`FAILED`、`CANCELLED` 或 `SKIPPED`。

`createdAfter` 字符串（可选）

只返回在此时间或之后创建的 Build。使用带 `Z` 或 UTC 偏移、精度最多到毫秒的 ISO 8601 时间戳，例如 `2026-09-29T00:00:00Z`。

`createdBefore` 字符串（可选）

只返回在此时间之前创建的 Build，格式相同。它必须晚于 `createdAfter`。

`limit` 数字（可选）

要返回的 Build 数量。默认值：10，最大值：100。

`cursor` 字符串（可选）

来自上一页响应中 `nextCursor` 的分页 cursor。请随它发送相同的筛选条件。

#### 响应字段

`items` 数组

最多 `limit` 个 Build，最新的在前。每个 Build 具有[获取环境 Build](https://cursor.com/docs/cloud-agent/api/endpoints.md#get-an-environment-build) 所返回的字段。

`nextCursor` 字符串（可选）

下一页的 cursor。没有更多 Build 时省略。

```bash
curl --request GET \
  --url 'https://api.cursor.com/v1/environments/8f14e45f-ceea-4e6b-9c3a-1d2e3f4a5b6c/builds?createdAfter=2026-09-29T00:00:00Z&limit=2' \
  -u YOUR_API_KEY:
```

**响应：**

```json
{
  "items": [
    {
      "id": "bld-20260930-3c59dc04-8a1d-4b6e-9f2a-7e5d1c0b9a8f",
      "environmentId": "8f14e45f-ceea-4e6b-9c3a-1d2e3f4a5b6c",
      "status": "SUCCEEDED",
      "trigger": "RECURRING",
      "source": "SYSTEM",
      "draft": false,
      "requestedRefs": [],
      "repoCommits": [
        {
          "repoUrl": "https://github.com/your-org/your-web-app",
          "commitSha": "06b7d943cd0e7ea353ca932d5cdea8987b8e8105"
        },
        {
          "repoUrl": "https://github.com/your-org/your-api",
          "commitSha": "1dcf04d09d11d33343ae4758346ce1b61de676ed"
        }
      ],
      "snapshotState": "ACTIVE",
      "createdAt": "2026-09-30T09:00:00.000Z",
      "updatedAt": "2026-09-30T09:12:00.000Z",
      "completedAt": "2026-09-30T09:12:00.000Z"
    },
    {
      "id": "bld-20260929-a87ff679-a2f3-4e1c-8f5b-0c3d2e1f4a5b",
      "environmentId": "8f14e45f-ceea-4e6b-9c3a-1d2e3f4a5b6c",
      "status": "FAILED",
      "trigger": "CONFIG_CHANGE",
      "source": "SYSTEM",
      "draft": false,
      "failure": { "type": "TERMINAL_FAILURE", "code": "environment_json_invalid" },
      "requestedRefs": [],
      "repoCommits": [],
      "createdAt": "2026-09-29T21:05:00.000Z",
      "updatedAt": "2026-09-29T21:06:00.000Z",
      "completedAt": "2026-09-29T21:06:00.000Z"
    }
  ]
}
```

### 启动环境 Build

POST

`/v1/environments/{id}/builds`

启动某个环境的 [Build](https://cursor.com/docs/cloud-agent/builds.md)，与仪表盘中的 **Trigger build** 一样。响应为 `202`，并带有新 Build 的 `id`。立刻读取该 Build 可能短暂返回 `404`。

访问权限遵循[更新环境](https://cursor.com/docs/cloud-agent/api/endpoints.md#update-an-environment)，有两处不同：

- **团队环境上的草稿 Build 只需要读取权限。** 当团队把环境编辑限制为管理员时，成员仍可以启动草稿，但只有管理员可以启动非草稿 Build。其他成员会得到 `403 team_admin_required`。该设置不会限制服务账号 API key。
- **限定于某些仓库的 API key 不能启动 Build**，它签发的用户范围 token 也不能。当环境没有仓库，或有任何仓库落在限制之外时，它们会得到 `404 environment_not_found`；否则得到 `403 key_not_supported`。

没有仓库限制的团队服务账号 API key 可以启动其所属团队环境的 Build。该 key 需要 `agent` scope，没有该 scope 会得到 `401 unauthorized`。成员的个人环境或其他团队的环境会返回 `404 environment_not_found`。

请求过多，或该环境已有太多进行中的 Build，会返回 `429 rate_limit_exceeded`。`503 upstream_error` 表示 Cursor 无法确认 Build 是否已启动。不要自动重试，因为另一次请求可能启动第二个 Build。在决定怎么做之前，先查看[列出环境 Build](https://cursor.com/docs/cloud-agent/api/endpoints.md#list-environment-builds)。

#### 路径参数

`id` 字符串

环境 ID。

#### 请求正文

可选。不发送正文，或发送 `{}`，表示对每个仓库的默认分支做一次非草稿 Build。

`draft` 布尔值（可选）

`true` 表示草稿 Build，在激活之前 Agent 不会从它启动。默认值：`false`，即成功后 Agent 会从它启动的 Build。

`refs` 数组（可选）

要构建的分支，用来代替默认分支。每个条目有一个 `repoUrl`（该环境的某个仓库）和一个 `ref`（分支名）。没有条目的仓库会构建其默认分支，而指向默认分支的 `ref` 视为没有条目。任何其他分支都需要 `draft: true`。如果没有它，或者仓库不在该环境中，或者同一仓库有第二个 `ref`，请求会返回 `400 validation_error`。Cursor 不会检查分支是否存在，因此缺失的分支会使 Build 失败，而不是使请求失败。

#### 响应字段

`id` 字符串

新 Build 的 ID。

`environmentId` 字符串

环境 ID。

```bash
curl --request POST \
  --url https://api.cursor.com/v1/environments/8f14e45f-ceea-4e6b-9c3a-1d2e3f4a5b6c/builds \
  -u YOUR_API_KEY:
```

**响应：** `202`

```json
{
  "id": "bld-20261020-0f2a6c1e-3b7d-4e59-9a41-8c2d7e6f5a10",
  "environmentId": "8f14e45f-ceea-4e6b-9c3a-1d2e3f4a5b6c"
}
```

启动某个功能分支的草稿 Build：

```bash
curl --request POST \
  --url https://api.cursor.com/v1/environments/8f14e45f-ceea-4e6b-9c3a-1d2e3f4a5b6c/builds \
  -u YOUR_API_KEY: \
  --header 'Content-Type: application/json' \
  --data '{
    "draft": true,
    "refs": [
      { "repoUrl": "https://github.com/your-org/your-web-app", "ref": "feature/new-checkout" }
    ]
  }'
```

### 获取环境 Build

GET

`/v1/environments/{id}/builds/{buildId}`

获取某个环境的一个 Build。不属于该环境 Build 的 build ID 会返回 `404 build_not_found`。

#### 路径参数

`id` 字符串

环境 ID。

`buildId` 字符串

Build ID（例如 `bld-20260930-3c59dc04-8a1d-4b6e-9f2a-7e5d1c0b9a8f`）。

#### 响应字段

`id` 字符串

Build ID。

`environmentId` 字符串

该 Build 所属环境的 ID。

`status` 字符串

`IN_PROGRESS`、`SUCCEEDED`、`FAILED`、`CANCELLED` 或 `SKIPPED`。[被跳过的 Build](https://cursor.com/docs/cloud-agent/builds.md#skipped-builds) 表示没有需要重建的内容。

`trigger` 字符串

是什么启动了该 Build：`RECURRING` 表示计划中的 Build，`CONFIG_CHANGE` 表示配置或密钥变更之后，`MANUAL` 表示应请求启动。

`source` 字符串

Build 从何处启动：`SYSTEM`（Cursor 自行启动）、`WEBSITE`（仪表盘）、`API` 或 `AGENT`（一个 Cloud Agent）。

`draft` 布尔值

草稿 Build 为 `true`。在激活之前，Agent 不会从草稿 Build 启动。

`failure` 对象（可选）

失败的 Build 为何失败。`install` 命令失败时 `type` 为 `INSTALL_FAILED`，其他失败为 `TERMINAL_FAILURE`。存在 `code` 时，它是机器可读的原因，例如 `environment_json_invalid`。其他代码包括：

- `bitbucket_server_not_enabled`：该环境包含 Bitbucket Server 仓库，而此团队上的 Cloud Agents 不能使用它。
- `origin_not_enabled`：该环境包含 [Origin](https://cursor.com/docs/origin.md) 仓库，而此团队上的 Cloud Agents 不能使用它。
- `repo_url_invalid`：环境中的某个仓库 URL 不是受支持的格式。
- `snapshot_installation_missing`：Cursor 找不到授予该环境仓库访问权限的源代码托管应用安装。
- `dockerfile_contents_not_enabled`：该环境设置了 `build.dockerfileContents`，而你的团队不可用。

Cursor 可能会陆续增加代码。把你不认识的代码当作一般失败处理。

`requestedRefs` 数组

每个条目有仓库的 `repoUrl`，以及 Build 被要求使用的分支、标签或 commit（`ref`），用来代替其默认分支。没有条目的仓库使用其默认分支。

`repoCommits` 数组

该 Build 返回的仓库 commit（在可用时）。每个条目有 `repoUrl` 和 `commitSha`。在这两个数组中，限定于某些仓库的 API key 只能看到其限制之内的条目。

`snapshotState` 字符串（可选）

该 Build 快照的状态：`ACTIVE`、`REAP_REQUESTED` 或 `DELETED`。如果该 Build 没有创建快照则省略。

`createdAt`、`updatedAt` 字符串

Build 的创建时间和最后更新时间（ISO 8601）。

`completedAt` 字符串（可选）

Build 完成的时间（ISO 8601）。进行中时省略。

```bash
curl --request GET \
  --url https://api.cursor.com/v1/environments/8f14e45f-ceea-4e6b-9c3a-1d2e3f4a5b6c/builds/bld-20260930-3c59dc04-8a1d-4b6e-9f2a-7e5d1c0b9a8f \
  -u YOUR_API_KEY:
```

**响应：**

```json
{
  "id": "bld-20260930-3c59dc04-8a1d-4b6e-9f2a-7e5d1c0b9a8f",
  "environmentId": "8f14e45f-ceea-4e6b-9c3a-1d2e3f4a5b6c",
  "status": "SUCCEEDED",
  "trigger": "RECURRING",
  "source": "SYSTEM",
  "draft": false,
  "requestedRefs": [],
  "repoCommits": [
    {
      "repoUrl": "https://github.com/your-org/your-web-app",
      "commitSha": "06b7d943cd0e7ea353ca932d5cdea8987b8e8105"
    },
    {
      "repoUrl": "https://github.com/your-org/your-api",
      "commitSha": "1dcf04d09d11d33343ae4758346ce1b61de676ed"
    }
  ],
  "snapshotState": "ACTIVE",
  "createdAt": "2026-09-30T09:00:00.000Z",
  "updatedAt": "2026-09-30T09:12:00.000Z",
  "completedAt": "2026-09-30T09:12:00.000Z"
}
```

### 获取当前生效的 Build

GET

`/v1/environments/{id}/builds/active`

了解你在此环境上启动的 Agent 从什么启动：它的某个 Build，或 Cursor 的默认镜像。

#### 路径参数

`id` 字符串

环境 ID。

#### 响应字段

`type` 字符串

Agent 从某个 Build 启动时为 `build`，从 Cursor 的默认镜像启动时为 `universal_image`。

`buildId` 字符串（可选）

Agent 所从启动的 Build 的 ID。当 `type` 为 `build` 时设置。

```bash
curl --request GET \
  --url https://api.cursor.com/v1/environments/8f14e45f-ceea-4e6b-9c3a-1d2e3f4a5b6c/builds/active \
  -u YOUR_API_KEY:
```

**响应：**

```json
{
  "type": "build",
  "buildId": "bld-20260930-3c59dc04-8a1d-4b6e-9f2a-7e5d1c0b9a8f"
}
```

## 密钥

密钥是 Cursor 提供给 Cloud Agent 的环境变量，也就是你在 [Cloud Agents 仪表盘](https://cursor.com/dashboard/cloud-agents) 的 **Secrets** 标签页中管理的那些值。密钥端点覆盖属于某一个环境的密钥、属于你的团队的密钥，以及你的个人密钥。为密钥选择类型，请参见[密钥保护](https://cursor.com/docs/cloud-agent/security-network.md#secret-protection)。

- **响应从不包含值。** 它们列出名称、类型和仓库。
- **一个名称可以有面向不同仓库的多个版本。** 一个环境、一个团队或一个用户可以多次持有同一个名称，每个版本覆盖其他版本所不覆盖的仓库。每个版本单独列出，并有自己的 `id`。
- **使用用户 API key、Agent 范围的服务账号 API key，或用户范围的 token。** 每一种都在一个团队内工作：用户 API key 在其用户的默认团队中，用户范围的 token 在签发它的团队中，服务账号 API key 在它自己的团队中。用户范围的 token 来自[创建用户范围的 Worker Token](https://cursor.com/docs/cloud-agent/api/endpoints.md#create-a-user-scoped-worker-token)。不支持团队 API key 和自动化 webhook API key，以 `grok_` 开头的 API key 会得到 `403 key_not_supported`。

#### 密钥版本

每个密钥端点都用这些字段描述密钥的一个版本。任何响应都不包含密钥的值。

`id` 字符串

不透明的版本 ID，例如 `secv_3qKx9mT2bV7nR1cW5yZ8aQ`。版本在其值、类型或仓库发生变化后仍保持同一个 `id`。

`name` 字符串

Agent 看到的环境变量名，例如 `NPM_TOKEN`。

`type` 字符串

[运行时密钥](https://cursor.com/docs/cloud-agent/security-network.md#runtime-secrets) 为 `runtime_secret`，[环境变量](https://cursor.com/docs/cloud-agent/security-network.md#environment-variables) 为 `environment_variable`，[构建密钥](https://cursor.com/docs/cloud-agent/security-network.md#build-secrets) 为 `build_secret`。

`repos` 字符串数组

只有这些仓库会得到此版本，例如 `github.com/acme/api`；空数组表示每个仓库都会得到它。仓库按 Cursor 存储它们的方式列出：小写并排序，仓库 URL 被简化为 `host/owner/repo`。

`createdAt` 字符串

版本的创建时间，ISO 8601 格式。

#### Agent 获取哪个值

当同一个名称在多处设置时，Agent 会从最具体的位置取值：Agent 启动时传入的值，例如[创建 Agent](https://cursor.com/docs/cloud-agent/api/endpoints.md#create-an-agent) 上的 `envVars`，然后是环境的密钥，然后是用户的个人密钥，然后是团队的密钥。在某一层设置的名称会隐藏它下面每一层的同名项。

#### 更改密钥

- **变更会到达之后启动的 Agent。** 正在运行的 Agent 保持它们启动时的值。从 [Build](https://cursor.com/docs/cloud-agent/builds.md) 启动的 Agent，要等其环境更新的 Build 完成后才会得到该变更。
- **读取可能滞后于写入。** 变更刚完成时，请求可能看不到它。对同一个名称的写入之间留出片刻，并用 `?id=` 指定一个版本。

### 列出密钥

GET

`/v1/secrets`

在一个分页列表中列出你的 API key 能够列出的每一个 Cloud Agents 密钥。每一项都是一个[密钥版本](https://cursor.com/docs/cloud-agent/api/endpoints.md#secret-versions)，带有其所有者自己的列表所显示的 `id`，以及一个 `owner`。任何一项都不包含值。

不带 `scope` 或 `environmentId` 时，列表包含：

- **团队的密钥**，与[列出团队密钥](https://cursor.com/docs/cloud-agent/api/endpoints.md#list-team-secrets) 对没有仓库限制的 API key 所列出的相同。
- **你的个人密钥**，适用于没有仓库限制的用户 API key 或用户范围 token。
- **你的团队环境的密钥**，并且在使用用户 API key 或用户范围 token 时，还包括你的个人环境的密钥。限定于某些仓库的 API key 只会得到其仓库全部落在限制之内的环境。
- **其他成员的密钥，供团队管理员查看。** 团队管理员使用自己的用户 API key 且不带 `repo` 时，还会得到其他成员的个人密钥及其个人环境的密钥。

各项不按名称排序，其他成员的密钥排在其余项之后。一页的条目可以少于 `limit`，也可以为空，并且仍然有 `nextCursor`。请持续翻页，直到它为 `null`，并且每次带 `cursor` 时发送相同的筛选条件。

#### 查询参数

`scope` 字符串（可选）

`team`、`user`、`members` 或 `environment`。`team` 只列出团队的密钥，拒绝方式与[列出团队密钥](https://cursor.com/docs/cloud-agent/api/endpoints.md#list-team-secrets) 相同。`user` 只列出你的个人密钥：不代表用户行事的 key（例如服务账号 API key）会得到 `403 user_required`，限定于某些仓库的 key 会得到 `403 repository_access`。`members` 会列出你的个人密钥，并且对于使用自己的用户 API key 或会话的显式团队管理员，还会在其后列出每一位其他现任成员的个人密钥，但不包含他们个人环境的密钥。对其他人，它返回与 `user` 相同的响应或拒绝。`environment` 只列出环境密钥。

`environmentId` 字符串（可选）

只列出此环境的密钥，拒绝方式与[列出环境密钥](https://cursor.com/docs/cloud-agent/api/endpoints.md#list-environment-secrets) 相同。不能与 `scope=team`、`scope=user` 或 `scope=members` 组合；这些组合会返回 `400 validation_error`。

`name` 字符串（可选）

只返回具有此名称的版本，忽略大小写。

`repo` 字符串（可选）

只返回此仓库上的 Agent 会得到的版本，例如 `github.com/acme/api`：针对该仓库的版本，以及针对每个仓库的版本。把它与 `scope=members` 组合会返回 `400 validation_error`。

`limit` 数字（可选）

每页最多条目数，从 1 到 100。默认值为 100。

`cursor` 字符串（可选）

上一页的 `nextCursor`。

#### 响应字段

`items` 数组

[密钥版本](https://cursor.com/docs/cloud-agent/api/endpoints.md#secret-versions)，每一项都有 `owner`。每一项都有 `repos`，包括其他成员的版本，`[]` 表示每个仓库都会得到该版本。在另一位成员的版本上，`repos` 可以列出你无法访问的仓库。

`items[].owner` 对象

谁持有该版本。`type` 为 `team`、`user` 或 `environment`。`environment` 所有者包含 `environmentId`。`user` 所有者和个人环境包含 `user.email`，当用户没有存档邮箱时为 `null`。

`nextCursor` 字符串或 null

下一页的 cursor，最后一页为 `null`。

```bash
curl --request GET \
  --url 'https://api.cursor.com/v1/secrets?limit=100' \
  -u YOUR_API_KEY:
```

**响应：**

```json
{
  "items": [
    {
      "id": "secv_Hn2kT8wQ5vC1xZ4mB7rP0g",
      "name": "GITHUB_PACKAGES_TOKEN",
      "type": "runtime_secret",
      "repos": [],
      "createdAt": "2026-09-28T10:12:34.000Z",
      "owner": { "type": "team" }
    },
    {
      "id": "secv_3qKx9mT2bV7nR1cW5yZ8aQ",
      "name": "NPM_TOKEN",
      "type": "runtime_secret",
      "repos": ["github.com/acme/api"],
      "createdAt": "2026-10-01T09:15:42.000Z",
      "owner": {
        "type": "environment",
        "environmentId": "8f14e45f-ceea-4e6b-9c3a-1d2e3f4a5b6c"
      }
    },
    {
      "id": "secv_Tb4mK7qW2xN9pR5vC8sJ1e",
      "name": "OPENAI_API_KEY",
      "type": "runtime_secret",
      "repos": [],
      "createdAt": "2026-10-04T13:05:27.000Z",
      "owner": { "type": "user", "user": { "email": "ada@acme.com" } }
    }
  ],
  "nextCursor": null
}
```

### 列出环境密钥

GET

`/v1/environments/{id}/secrets`

列出某个环境的 Cloud Agents 密钥。每一项都是一个[密钥版本](https://cursor.com/docs/cloud-agent/api/endpoints.md#secret-versions)，任何一项都不包含值。各项按名称排序，因此同一个名称的各个版本会排在一起。

谁可以列出某个环境的密钥：

- **用户 API key 或用户范围的 token** 可以列出其用户的个人环境及其团队的环境。任何团队成员都可以列出团队环境的密钥。
- **服务账号 API key** 可以列出其团队的环境。
- **限定于某些仓库的 API key**，或它签发的用户范围 token，只能列出拥有仓库、且这些仓库全部落在其限制之内的环境。
- **其他人** 会得到 `404 environment_not_found`，包括查看成员个人环境的团队管理员。

列表尚未分页，因此 `nextCursor` 始终为 `null`。请跟随 `nextCursor` 直到它为 `null`，这样当分页出现时你的客户端仍能工作。

#### 路径参数

`id` 字符串

环境 ID。

#### 响应字段

`items` 数组

该环境的[密钥版本](https://cursor.com/docs/cloud-agent/api/endpoints.md#secret-versions)。一个名称若有面向不同仓库的版本，则每个版本出现一次。

`nextCursor` 字符串或 null

下一页的 cursor。目前始终为 `null`。

```bash
curl --request GET \
  --url https://api.cursor.com/v1/environments/8f14e45f-ceea-4e6b-9c3a-1d2e3f4a5b6c/secrets \
  -u YOUR_API_KEY:
```

**响应：**

```json
{
  "items": [
    {
      "id": "secv_Qm7pW2xK9cT4vB1nY6hR3w",
      "name": "DOCKER_TOKEN",
      "type": "build_secret",
      "repos": [],
      "createdAt": "2026-09-30T14:02:11.000Z"
    },
    {
      "id": "secv_3qKx9mT2bV7nR1cW5yZ8aQ",
      "name": "NPM_TOKEN",
      "type": "runtime_secret",
      "repos": ["github.com/acme/api"],
      "createdAt": "2026-10-01T09:15:42.000Z"
    },
    {
      "id": "secv_Lw0pE4sJ6hD9fG2kU8tY1g",
      "name": "NPM_TOKEN",
      "type": "runtime_secret",
      "repos": ["github.com/acme/web"],
      "createdAt": "2026-10-02T11:20:05.000Z"
    },
    {
      "id": "secv_Zr5cN8vM1qL4wX7tA2sD9g",
      "name": "SENTRY_ENVIRONMENT",
      "type": "environment_variable",
      "repos": [],
      "createdAt": "2026-10-03T16:45:00.000Z"
    }
  ],
  "nextCursor": null
}
```

### 设置环境密钥

PUT

`/v1/environments/{id}/secrets/{name}`

创建环境密钥、轮换其值，或更改其类型或仓库。每个请求都发送值，响应是不含该值的[密钥版本](https://cursor.com/docs/cloud-agent/api/endpoints.md#secret-versions)。

请求更改哪个版本：

- **带 `?id=`** 时，它更改具有该 `id` 的版本。不属于此环境中此名称的某个版本的 `id` 会返回 `404 secret_not_found`。
- **不带 `?id=`** 时，没有版本的名称会得到一个版本，响应为 `201`。只有一个版本的名称会就地更改，响应为 `200`。有多个版本的名称会返回 `409 secret_name_ambiguous`，列出每个版本的 `id` 和 `repos`，以便你把其中一个作为 `?id=` 传入。

`PUT` 从不会给已经有一个版本的名称再添加第二个版本。要让一个名称拥有面向其他仓库的版本，请使用仪表盘。

省略 `type` 或 `repos` 时，版本会保留当前的类型或仓库，因此轮换值从不会更改其类型或扩大其仓库。发送 `repos: []` 可让该版本覆盖每个仓库。与该名称的另一个版本重叠的新 `repos` 会返回 `409 secret_scope_conflict`。一个环境最多持有 1,000 个密钥，再创建一个会返回 `409 secret_limit_reached`。

构建密钥还不能通过 API 设置：`type: "build_secret"` 会返回 `400 validation_error`，更改已有的构建密钥也一样。`DELETE` 仍然可以移除一个。

谁可以设置某个环境的密钥：

- **用户 API key 或用户范围的 token** 可以在其用户的个人环境及其团队的环境上设置密钥。
- **服务账号 API key** 可以在其团队的环境上设置密钥。
- **限定于某些仓库的 API key**，或它签发的用户范围 token，只能在拥有仓库、且这些仓库全部落在其限制之内的环境上设置密钥。
- **当团队只允许管理员更改密钥时**，只有团队管理员可以设置团队环境的密钥。其他人，包括服务账号 API key，会得到 `403 team_admin_required`。
- **其他人** 会得到 `404 environment_not_found`。

每次变更都会记录为一条 [`cloud_agent_secret` 事件](https://cursor.com/docs/enterprise/compliance-and-monitoring.md#event-types)，包含密钥的名称和仓库，但不包含其值，与仪表盘中的同一变更一样。

#### 路径参数

`id` 字符串

环境 ID。

`name` 字符串

密钥名称，即 Agent 所看到的名称：字母、数字和下划线，不能以数字开头，最多 255 个字符。包含 `CURSOR_SANDBOX` 的名称是保留的，`HTTP_PROXY`、`HTTPS_PROXY` 和 `ALL_PROXY` 在任何字母大小写下也是保留的。

只在字母大小写上不同的名称算作同一个名称。当 `NPM_TOKEN` 已存在时创建 `npm_token` 会返回 `409 secret_name_conflict`。

#### 查询参数

`id` 字符串（可选）

要更改的版本的 `id`，来自该环境的密钥列表（`GET /v1/environments/{id}/secrets`）或 `409 secret_name_ambiguous` 响应。

#### 请求正文

`value` 字符串（必填）

密钥的值，1 到 4,096 字节的 UTF-8 文本。

`type` 字符串（可选）

`runtime_secret` 或 `environment_variable`。新版本默认为 `runtime_secret`。不带 `type` 的更改会保留该版本的类型。

`repos` 字符串数组（可选）

只有这些仓库会得到此版本，最多 100 个。空数组，或在新版本上省略 `repos`，会让它覆盖每个仓库。不带 `repos` 的更改会保留该版本的仓库。

每个条目命名一个仓库：`acme/api`、`https://github.com/acme/api.git` 或 `git@github.com:acme/api.git` 这样的 URL，或其他主机的路径，例如 `gitlab.com/group/subgroup/project`。

#### 响应字段

请求所创建或更改的[密钥版本](https://cursor.com/docs/cloud-agent/api/endpoints.md#secret-versions)。当新版本尚不可读时，`201` 会省略 `id` 和 `createdAt`。

```bash
curl --request PUT \
  --url https://api.cursor.com/v1/environments/8f14e45f-ceea-4e6b-9c3a-1d2e3f4a5b6c/secrets/NPM_TOKEN \
  -u YOUR_API_KEY: \
  --header 'Content-Type: application/json' \
  --data '{
    "value": "npm_example_token",
    "repos": ["github.com/acme/api"]
  }'
```

**响应：** `201`

```json
{
  "id": "secv_3qKx9mT2bV7nR1cW5yZ8aQ",
  "name": "NPM_TOKEN",
  "type": "runtime_secret",
  "repos": ["github.com/acme/api"],
  "createdAt": "2026-10-04T14:02:11.000Z"
}
```

轮换该值。响应为 `200`，`id`、`type`、`repos` 和 `createdAt` 保持不变：

```bash
curl --request PUT \
  --url https://api.cursor.com/v1/environments/8f14e45f-ceea-4e6b-9c3a-1d2e3f4a5b6c/secrets/NPM_TOKEN \
  -u YOUR_API_KEY: \
  --header 'Content-Type: application/json' \
  --data '{"value": "npm_rotated_token"}'
```

### 删除环境密钥

DELETE

`/v1/environments/{id}/secrets/{name}`

删除环境密钥的一个版本。此操作不可逆。每种类型都可以删除，包括构建密钥。

请求删除哪个版本：

- **带 `?id=`** 时，它删除具有该 `id` 的版本。不属于此环境中此名称的某个版本的 `id` 会返回 `404 secret_not_found`。
- **不带 `?id=`** 时，只有一个版本的名称会被删除。没有版本的名称会返回 `404 secret_not_found`，有多个版本的名称会返回 `409 secret_name_ambiguous`，列出每个版本的 `id` 和 `repos`，并且什么都不删除。

`{name}` 必须与列出的名称精确匹配，包括字母大小写，因此请求 `NPM_TOKEN` 从不会删除 `npm_token`。对路径无法承载的字符进行 URL 编码。

谁可以删除某个环境的密钥，规则与设置它们相同：用户 API key 或用户范围的 token 针对其用户的个人环境及其团队的环境，服务账号 API key 针对其团队的环境，限定于某些仓库的 API key 只针对拥有仓库、且这些仓库全部落在其限制之内的环境。其他人会得到 `404 environment_not_found`。当团队只允许管理员更改密钥时，除团队管理员外的所有人都会得到 `403 team_admin_required`，包括服务账号 API key。

每次删除都会记录为一条 [`cloud_agent_secret` 事件](https://cursor.com/docs/enterprise/compliance-and-monitoring.md#event-types)，动作为 `delete`，与仪表盘中的删除一样。之后启动的 Agent 不再获得此版本。

#### 路径参数

`id` 字符串

环境 ID。

`name` 字符串

密钥名称，与该环境的密钥列表（`GET /v1/environments/{id}/secrets`）所显示的完全一致。

#### 查询参数

`id` 字符串（可选）

要删除的版本的 `id`，来自该环境的密钥列表或 `409 secret_name_ambiguous` 响应。

#### 响应字段

`name` 字符串

已删除密钥的名称。

```bash
curl --request DELETE \
  --url https://api.cursor.com/v1/environments/8f14e45f-ceea-4e6b-9c3a-1d2e3f4a5b6c/secrets/NPM_TOKEN \
  -u YOUR_API_KEY:
```

**响应：**

```json
{
  "name": "NPM_TOKEN"
}
```

删除一个拥有多个版本的名称中的某一个版本：

```bash
curl --request DELETE \
  --url 'https://api.cursor.com/v1/environments/8f14e45f-ceea-4e6b-9c3a-1d2e3f4a5b6c/secrets/NPM_TOKEN?id=secv_Lw0pE4sJ6hD9fG2kU8tY1g' \
  -u YOUR_API_KEY:
```

### 列出团队密钥

GET

`/v1/team/secrets`

列出你的 API key 所在团队的 Cloud Agents 密钥。每一项都是一个[密钥版本](https://cursor.com/docs/cloud-agent/api/endpoints.md#secret-versions)，任何一项都不包含值。不包含个人密钥、环境密钥和其他团队的密钥。

谁可以列出团队密钥：

- **团队的任何成员** 都可以用用户 API key 或用户范围的 token 列出它们，即使团队只允许管理员更改密钥。
- **服务账号 API key** 列出它自己的团队。
- **限定于某些仓库的 API key**，或它签发的用户范围 token，会得到 `403 repository_access`，因为团队密钥会到达每个仓库。
- **不在某个团队中工作的 API key**，或在该团队没有席位的调用方，会得到 `403 team_membership_required`。

不带 `limit` 或 `cursor` 时，响应包含每一个团队密钥版本，按名称、然后按仓库、然后按创建时间排序，因此同一个名称的各个版本会排在一起，并且 `nextCursor` 为 `null`。发送任一参数即可按密钥版本的创建顺序分页。把每一页的 `nextCursor` 作为 `cursor` 传回，直到它为 `null`。页面是实时读取的，而不是来自快照，因此遍历期间创建或删除的密钥会改变后面各页所显示的内容。

cursor 使用与[列出密钥](https://cursor.com/docs/cloud-agent/api/endpoints.md#list-secrets) 相同的格式，因此对同一个团队，来自 `GET /v1/secrets?scope=team` 的 cursor 可以在这里继续，反之亦然。只把 `nextCursor` 传回同一个团队的列表。cursor 不绑定到团队、调用方、scope 或筛选条件，因此另一个列表的 cursor 可能静默跳过版本，或提前结束列表。来自团队管理员的列出密钥遍历中、列出其他成员密钥那一部分的 cursor，在这里会返回 `400 validation_error`。

不是 1 到 100 的整数的 `limit`、重复的 `limit` 或 `cursor`、API 没有返回过的 cursor，或任何其他查询参数，都会返回 `400 validation_error`。

#### 查询参数

`limit` 数字（可选）

每页最多条目数，从 1 到 100。只发送 `cursor` 时默认值为 100。

`cursor` 字符串（可选）

上一页的 `nextCursor`。

#### 响应字段

`items` 数组

此页上团队的[密钥版本](https://cursor.com/docs/cloud-agent/api/endpoints.md#secret-versions)。一个名称若有面向不同仓库的版本，则每个版本出现一次。

`nextCursor` 字符串或 null

下一页的 cursor，最后一页之后为 `null`。不带 `limit` 或 `cursor` 时始终为 `null`。

```bash
curl --request GET \
  --url https://api.cursor.com/v1/team/secrets \
  -u YOUR_API_KEY:
```

**响应：**

```json
{
  "items": [
    {
      "id": "secv_Hn2kT8wQ5vC1xZ4mB7rP0g",
      "name": "GITHUB_PACKAGES_TOKEN",
      "type": "runtime_secret",
      "repos": [],
      "createdAt": "2026-09-28T10:12:34.000Z"
    },
    {
      "id": "secv_Ye6sF3jL9pV2nK5tD8uW1A",
      "name": "SENTRY_DSN",
      "type": "environment_variable",
      "repos": ["github.com/acme/web"],
      "createdAt": "2026-10-02T08:30:00.000Z"
    }
  ],
  "nextCursor": null
}
```

逐页遍历列表，每次两个版本：

```bash
curl --request GET \
  --url 'https://api.cursor.com/v1/team/secrets?limit=2' \
  -u YOUR_API_KEY:
```

**响应：**

```json
{
  "items": [
    {
      "id": "secv_4Fq9Lm2Xc7Rt1Bn8Kd3Vh0",
      "name": "NPM_TOKEN",
      "type": "runtime_secret",
      "repos": [],
      "createdAt": "2026-09-30T17:02:11.000Z"
    },
    {
      "id": "secv_Pz6Wy1Ja5Qe9Ns3Gu7Ck2M",
      "name": "DATABASE_URL",
      "type": "environment_variable",
      "repos": ["github.com/acme/api"],
      "createdAt": "2026-10-01T09:14:52.000Z"
    }
  ],
  "nextCursor": "Tg9xR2pLbW5vcFFyc3R1dnd4eXo0NTY3ODkwYWJjZGVmZ2hp"
}
```

然后用该 cursor 请求下一页：

```bash
curl --request GET \
  --url 'https://api.cursor.com/v1/team/secrets?limit=2&cursor=Tg9xR2pLbW5vcFFyc3R1dnd4eXo0NTY3ODkwYWJjZGVmZ2hp' \
  -u YOUR_API_KEY:
```

### 设置团队密钥

PUT

`/v1/team/secrets/{name}`

为你的 API key 所在团队创建密钥、轮换其值，或更改其类型或仓库。每个请求都发送值，响应是不含该值的[密钥版本](https://cursor.com/docs/cloud-agent/api/endpoints.md#secret-versions)。团队密钥会到达该团队上的每个 Agent，除非更具体的值把它们隐藏起来，如[Agent 获取哪个值](https://cursor.com/docs/cloud-agent/api/endpoints.md#which-value-an-agent-gets) 所述。

请求更改哪个版本：

- **带 `?id=`** 时，它更改具有该 `id` 的版本，该 `id` 来自团队的密钥列表（`GET /v1/team/secrets`）。不属于你的团队中此名称的某个版本的 `id` 会返回 `404 secret_not_found`。
- **不带 `?id=`** 时，没有版本的名称会得到一个版本，响应为 `201`。只有一个版本的名称会就地更改，响应为 `200`。有多个版本的名称会返回 `409 secret_name_ambiguous`，列出每个版本的 `id` 和 `repos`，以便你把其中一个作为 `?id=` 传入。

`PUT` 从不会给已经有一个版本的名称再添加第二个版本。要让一个名称拥有面向其他仓库的版本，请使用仪表盘。

省略 `type` 或 `repos` 时，版本会保留当前的类型或仓库，因此轮换值从不会更改其类型或扩大其仓库。发送 `repos: []` 可让该版本覆盖每个仓库。与该名称的另一个版本重叠的新 `repos` 会返回 `409 secret_scope_conflict`；当该名称还有其他版本时，`repos: []` 也会如此。一个团队最多持有 1,000 个密钥，再创建一个会返回 `409 secret_limit_reached`。

构建密钥还不能通过 API 设置：`type: "build_secret"` 会返回 `400 validation_error`，更改已有的构建密钥也一样。

谁可以设置团队密钥：

- **团队的任何成员** 都可以用用户 API key 或用户范围的 token 设置，除非团队只允许管理员更改密钥。那时除团队管理员外的所有人都会得到 `403 team_admin_required`，包括服务账号 API key。
- **服务账号 API key** 设置它自己团队的密钥。
- **限定于某些仓库的 API key**，或它签发的用户范围 token，会得到 `403 repository_access`，因为团队密钥会到达每个仓库。
- **不在某个团队中工作的 API key**，或在该团队没有席位的调用方，会得到 `403 team_membership_required`。

每次变更都会记入你的团队审计日志，作为一条 [`cloud_agent_secret` 事件](https://cursor.com/docs/enterprise/compliance-and-monitoring.md#event-types)，包含密钥的名称和仓库，但不包含其值，与仪表盘中的同一变更一样。

#### 路径参数

`name` 字符串

密钥名称，即 Agent 所看到的名称：字母、数字和下划线，不能以数字开头，最多 255 个字符。包含 `CURSOR_SANDBOX` 的名称是保留的，`HTTP_PROXY`、`HTTPS_PROXY` 和 `ALL_PROXY` 在任何字母大小写下也是保留的。

只在字母大小写上不同的名称算作同一个名称。当团队已有 `NPM_TOKEN` 时创建 `npm_token` 会返回 `409 secret_name_conflict`。

#### 查询参数

`id` 字符串（可选）

要更改的版本的 `id`，来自团队的密钥列表或 `409 secret_name_ambiguous` 响应。

#### 请求正文

`value` 字符串（必填）

密钥的值，1 到 4,096 字节的 UTF-8 文本。

`type` 字符串（可选）

`runtime_secret` 或 `environment_variable`。新版本默认为 `runtime_secret`。不带 `type` 的更改会保留该版本的类型。

`repos` 字符串数组（可选）

只有这些仓库会得到此版本，最多 100 个。空数组，或在新版本上省略 `repos`，会让它覆盖每个仓库。不带 `repos` 的更改会保留该版本的仓库。

每个条目命名一个仓库：`acme/api`、`https://github.com/acme/api.git` 或 `git@github.com:acme/api.git` 这样的 URL，或其他主机的路径，例如 `gitlab.com/group/subgroup/project`。

#### 响应字段

请求所创建或更改的[密钥版本](https://cursor.com/docs/cloud-agent/api/endpoints.md#secret-versions)。当新版本尚不可读时，`201` 会省略 `id` 和 `createdAt`。

```bash
curl --request PUT \
  --url https://api.cursor.com/v1/team/secrets/NPM_TOKEN \
  -u YOUR_API_KEY: \
  --header 'Content-Type: application/json' \
  --data '{
    "value": "npm_example_token",
    "repos": ["github.com/acme/api"]
  }'
```

**响应：** `201`

```json
{
  "id": "secv_Ub4rJ7nD2kX9qM5wL1sE8Q",
  "name": "NPM_TOKEN",
  "type": "runtime_secret",
  "repos": ["github.com/acme/api"],
  "createdAt": "2026-10-04T14:02:11.000Z"
}
```

更改一个拥有多个版本的名称中的某一个版本，使用团队密钥列表中的 `id`：

```bash
curl --request PUT \
  --url 'https://api.cursor.com/v1/team/secrets/NPM_TOKEN?id=secv_Ub4rJ7nD2kX9qM5wL1sE8Q' \
  -u YOUR_API_KEY: \
  --header 'Content-Type: application/json' \
  --data '{"value": "npm_rotated_token"}'
```

### 删除团队密钥

DELETE

`/v1/team/secrets/{name}`

从你的 API key 所在团队删除密钥的一个版本。此操作不可逆。每种类型都可以删除，包括构建密钥。

请求删除哪个版本：

- **带 `?id=`** 时，它删除具有该 `id` 的版本，该 `id` 来自团队的密钥列表（`GET /v1/team/secrets`）。不属于你的团队中此名称的某个版本的 `id` 会返回 `404 secret_not_found`。
- **不带 `?id=`** 时，只有一个版本的名称会被删除。没有版本的名称会返回 `404 secret_not_found`，有多个版本的名称会返回 `409 secret_name_ambiguous`，列出每个版本的 `id` 和 `repos`，并且什么都不删除。

`{name}` 必须与列出的名称精确匹配，包括字母大小写，因此请求 `NPM_TOKEN` 从不会删除 `npm_token`。对路径无法承载的字符进行 URL 编码。

谁可以删除团队密钥，规则与设置它们相同：团队的任何成员使用用户 API key 或用户范围的 token，以及该团队的服务账号 API key。当团队只允许管理员更改密钥时，除团队管理员外的所有人都会得到 `403 team_admin_required`，包括服务账号 API key。限定于某些仓库的 API key 会得到 `403 repository_access`；不在某个团队中工作的 API key，或在该团队没有席位的调用方，会得到 `403 team_membership_required`。

每次删除都会记入你的团队审计日志，作为一条 [`cloud_agent_secret` 事件](https://cursor.com/docs/enterprise/compliance-and-monitoring.md#event-types)，动作为 `delete`，与仪表盘中的删除一样。之后启动的 Agent 不再获得此版本。

#### 路径参数

`name` 字符串

密钥名称，与团队密钥列表所显示的完全一致。

#### 查询参数

`id` 字符串（可选）

要删除的版本的 `id`，来自团队的密钥列表或 `409 secret_name_ambiguous` 响应。

#### 响应字段

`name` 字符串

已删除密钥的名称。

```bash
curl --request DELETE \
  --url https://api.cursor.com/v1/team/secrets/NPM_TOKEN \
  -u YOUR_API_KEY:
```

**响应：**

```json
{
  "name": "NPM_TOKEN"
}
```

### 设置个人密钥

PUT

`/v1/me/secrets/{name}`

为你的 API key 所代表的用户创建个人密钥、轮换其值，或更改其类型或仓库。每个请求都发送值，响应是不含该值的[密钥版本](https://cursor.com/docs/cloud-agent/api/endpoints.md#secret-versions)。

请求更改哪个版本：

- **带 `?id=`** 时，它更改具有该 `id` 的版本，该 `id` 来自你的个人密钥列表（`GET /v1/secrets?scope=user`）。不属于你的个人密钥中此名称的某个版本的 `id` 会返回 `404 secret_not_found`。
- **不带 `?id=`** 时，没有版本的名称会得到一个版本，响应为 `201`。只有一个版本的名称会就地更改，响应为 `200`。有多个版本的名称会返回 `409 secret_name_ambiguous`，列出每个版本的 `id` 和 `repos`，以便你把其中一个作为 `?id=` 传入。

`PUT` 从不会给已经有一个版本的名称再添加第二个版本。要让一个名称拥有面向其他仓库的版本，请使用仪表盘。

省略 `type` 或 `repos` 时，版本会保留当前的类型或仓库，因此轮换值从不会更改其类型或扩大其仓库。发送 `repos: []` 可让该版本覆盖每个仓库。与该名称的另一个版本重叠的新 `repos` 会返回 `409 secret_scope_conflict`；当该名称还有其他版本时，`repos: []` 也会如此。一个用户最多持有 1,000 个个人密钥，再创建一个会返回 `409 secret_limit_reached`。

构建密钥不能设置或更改；两者都会返回 `400 validation_error`。`DELETE` 可以移除一个。

谁可以设置个人密钥：

- **用户 API key 或用户范围的 token** 设置其自身用户的个人密钥。团队管理员不能设置其他成员的个人密钥。
- **服务账号 API key** 会得到 `403 user_required`，因为它不代表用户行事。
- **由限定于某些仓库的 API key 签发的用户范围 token** 会得到 `403 repository_access`，因为个人密钥会到达每个仓库。
- **当你的 API key 所在团队已关闭个人密钥时**，设置一个会返回 `403 personal_secrets_disabled`。[删除一个](https://cursor.com/docs/cloud-agent/api/endpoints.md#delete-a-personal-secret) 仍然有效。

#### 路径参数

`name` 字符串

密钥名称，即 Agent 所看到的名称：字母、数字和下划线，不能以数字开头，最多 255 个字符。包含 `CURSOR_SANDBOX` 的名称是保留的，`HTTP_PROXY`、`HTTPS_PROXY` 和 `ALL_PROXY` 在任何字母大小写下也是保留的。

只在字母大小写上不同的名称算作同一个名称。当你已有 `NPM_TOKEN` 时创建 `npm_token` 会返回 `409 secret_name_conflict`。

#### 查询参数

`id` 字符串（可选）

要更改的版本的 `id`，来自你的个人密钥列表或 `409 secret_name_ambiguous` 响应。

#### 请求正文

`value` 字符串（必填）

密钥的值，1 到 4,096 字节的 UTF-8 文本。

`type` 字符串（可选）

`runtime_secret` 或 `environment_variable`。新版本默认为 `runtime_secret`。不带 `type` 的更改会保留该版本的类型。

`repos` 字符串数组（可选）

只有这些仓库会得到此版本，最多 100 个。空数组，或在新版本上省略 `repos`，会让它覆盖每个仓库。不带 `repos` 的更改会保留该版本的仓库。

每个条目命名一个仓库：`acme/api`、`https://github.com/acme/api.git` 或 `git@github.com:acme/api.git` 这样的 URL，或其他主机的路径，例如 `gitlab.com/group/subgroup/project`。

#### 响应字段

请求所创建或更改的[密钥版本](https://cursor.com/docs/cloud-agent/api/endpoints.md#secret-versions)。`201` 可以省略 `id` 和 `createdAt`。

```bash
curl --request PUT \
  --url https://api.cursor.com/v1/me/secrets/NPM_TOKEN \
  -u YOUR_API_KEY: \
  --header 'Content-Type: application/json' \
  --data '{"value": "npm_example_token"}'
```

**响应：** `201`

```json
{
  "id": "secv_Pk6nV3yH8rT1wQ4zM9cX2b",
  "name": "NPM_TOKEN",
  "type": "runtime_secret",
  "repos": [],
  "createdAt": "2026-10-04T14:02:11.000Z"
}
```

更改一个拥有多个版本的名称中的某一个版本，使用你的个人密钥列表中的 `id`：

```bash
curl --request PUT \
  --url 'https://api.cursor.com/v1/me/secrets/NPM_TOKEN?id=secv_Pk6nV3yH8rT1wQ4zM9cX2b' \
  -u YOUR_API_KEY: \
  --header 'Content-Type: application/json' \
  --data '{"value": "npm_rotated_token"}'
```

### 删除个人密钥

DELETE

`/v1/me/secrets/{name}`

删除你的 API key 所代表用户的个人密钥的一个版本。此操作不可逆。之后启动的 Agent 不再获得此版本。

请求删除哪个版本：

- **带 `?id=`** 时，它删除具有该 `id` 的版本，该 `id` 来自你的个人密钥列表（`GET /v1/secrets?scope=user`）。不属于你的个人密钥中此名称的某个版本的 `id` 会返回 `404 secret_not_found`。
- **不带 `?id=`** 时，只有一个版本的名称会被删除。没有版本的名称会返回 `404 secret_not_found`，有多个版本的名称会返回 `409 secret_name_ambiguous`，列出每个版本的 `id` 和 `repos`，并且什么都不删除。

`{name}` 必须与列出的名称精确匹配，包括字母大小写，因此请求 `NPM_TOKEN` 从不会删除 `npm_token`。

谁可以删除个人密钥，规则与设置它们相同：用户 API key 或用户范围的 token 只删除其自身用户的个人密钥。服务账号 API key 会得到 `403 user_required`，由限定于某些仓库的 API key 签发的用户范围 token 会得到 `403 repository_access`。

#### 路径参数

`name` 字符串

密钥名称，与你的个人密钥列表所显示的完全一致。

#### 查询参数

`id` 字符串（可选）

要删除的版本的 `id`，来自你的个人密钥列表或 `409 secret_name_ambiguous` 响应。

#### 响应字段

`name` 字符串

已删除密钥的名称。

```bash
curl --request DELETE \
  --url https://api.cursor.com/v1/me/secrets/NPM_TOKEN \
  -u YOUR_API_KEY:
```

**响应：**

```json
{
  "name": "NPM_TOKEN"
}
```

## Worker Token

### 创建用户范围的 Worker Token

POST

`/v1/sub-tokens`

创建一个有效期一小时的用户范围 token，供 worker 以一位活跃团队成员的身份运行。

需要一个 Agent 范围的团队服务账号 API key。用户范围的 token 不能再签发其他用户范围的 token。

返回的 token 在 1 小时后过期，并且不能自行刷新。需要刷新正在运行的 worker 时，用服务账号 API key 签发一个新 token。

#### 请求正文

指定以下其中恰好一项来标识目标用户：

`forUserEmail` 字符串（可选）

活跃团队成员的邮箱。不区分大小写。

`forUserId` 整数（可选）

活跃团队成员的数字 Cursor 用户 ID。

按邮箱：

```bash
curl --request POST \
  --url https://api.cursor.com/v1/sub-tokens \
  --header "Authorization: Bearer $CURSOR_SERVICE_ACCOUNT_API_KEY" \
  --header "Content-Type: application/json" \
  --data '{
    "forUserEmail": "alice@company.com"
  }'
```

按用户 ID：

```bash
curl --request POST \
  --url https://api.cursor.com/v1/sub-tokens \
  --header "Authorization: Bearer $CURSOR_SERVICE_ACCOUNT_API_KEY" \
  --header "Content-Type: application/json" \
  --data '{
    "forUserId": 42
  }'
```

**响应：**

```json
{
  "accessToken": "eyJ...",
  "expiresAt": "2026-04-24T19:00:00.000Z",
  "userId": 42,
  "teamId": 456
}
```

## Worker 与 Pool

监控 worker 利用率，并为你的 pool 构建自动扩缩容。持久 pool 在最后一个 worker 断开后仍保持注册，因此你可以缩容到零，并在出现[待处理请求](https://cursor.com/docs/cloud-agent/api/endpoints.md#list-pending-pool-requests) 时把容量加回来。

端点路径保留了较早的 `private-workers` 名称；它们指的是同一批 [Self-Hosted Machines worker](https://cursor.com/docs/cloud-agent/self-hosted.md)。

使用该 pool 的服务账号 API key，通过 Basic 身份验证或 Bearer token 进行身份验证。其他类型的 API key 会被拒绝。

### 列出 Worker

GET

`/v0/private-workers`

列出已认证服务账号所属团队的 pool worker，最新的在前。

#### 查询参数

`status` 字符串（可选，默认值：`all`）

按 worker 状态筛选。取 `all`、`in_use` 或 `idle` 之一。

`scope` 字符串（可选，默认值：`all`）

按 worker 范围筛选。取 `all`、`team_pool` 或 `personal` 之一。

`limit` 整数（可选，默认值：50）

每页结果数。范围：1 到 100。

`pageToken` 字符串（可选）

分页 cursor。传入上一页响应中的 `nextPageToken`。

#### 响应字段

`workers` 数组

已连接的 worker。每个条目包含：

- `workerId` 字符串：worker 的唯一标识符。自动生成的 id 是 UUID；以 `CURSOR_AGENT_WORKER_ID` 启动的 worker 会改为报告该自定义 id。
- `isInUse` 布尔值：该 worker 当前是否有已分配的 Agent。
- `repoOwner`、`repoName` 字符串：worker 注册了 git remote 时的主仓库元数据。任意仓库 worker 为空字符串。
- `repoUrl` 字符串（可选）：主仓库 URL。任意仓库 worker 会省略。
- `workspaceRootPath` 字符串：worker 上的主工作区路径。
- `connectedAtMs` 整数：连接时间，Unix 毫秒。
- `userId` 整数：所属用户 id。使用服务账号 key 认证的 worker 为 `0`。
- `teamId` 整数（可选）：团队 pool worker 的团队 id。
- `serviceAccountId` 字符串（可选）：认证该 worker 的服务账号。
- `activeBcId` 字符串（可选）：当前正在该 worker 上运行的 Agent 的 id（使用中时）。
- `name` 字符串（可选）：worker 显示名称（`--name`，默认为机器主机名）。

`totalCount` 整数

匹配筛选条件的 worker 总数，跨所有页。

`nextPageToken` 字符串（可选）

供 `pageToken` 使用的分页 cursor。没有更多页时省略。

```bash
curl --request GET \
  --url "https://api.cursor.com/v0/private-workers?status=idle&scope=team_pool&limit=50" \
  -u "$CURSOR_API_KEY:"
```

**响应：**

```json
{
  "workers": [
    {
      "workerId": "a8574fe8-248e-424a-a078-7584a2b93724",
      "repoOwner": "acme",
      "repoName": "payments-service",
      "repoUrl": "https://github.com/acme/payments-service",
      "workspaceRootPath": "/home/agent/payments-service",
      "connectedAtMs": 1737306880000,
      "userId": 0,
      "teamId": 456,
      "serviceAccountId": "sa_abc123",
      "isInUse": false,
      "name": "gpu-worker-1"
    }
  ],
  "totalCount": 1
}
```

### 获取 Worker 摘要

GET

`/v0/private-workers/summary`

返回已认证用户及其团队的已连接 worker 数和正在使用的 worker 数。利用率高时，用它来触发扩缩容决策。

```bash
curl --request GET \
  --url "https://api.cursor.com/v0/private-workers/summary" \
  -u "$CURSOR_API_KEY:"
```

**示例扩缩容检查：**

```typescript
const summary = await response.json();
const team = summary.teamSummary;
if (team && team.totalConnected > 0) {
  const utilization = team.inUse / team.totalConnected;
  if (utilization >= 0.9) {
    // Scale up: provision additional workers
  }
}
```

### 按 ID 获取 Worker

GET

`/v0/private-workers/{id}`

按 ID 获取单个 pool worker。

#### 路径参数

`id` 字符串

worker 的唯一标识符（例如 `pw_123`）。

```bash
curl --request GET \
  --url "https://api.cursor.com/v0/private-workers/pw_123" \
  -u "$CURSOR_API_KEY:"
```

### 列出 Pool

GET

`/v0/private-workers/pools`

列出已认证服务账号所属团队的持久 pool。最后一个 worker 断开后 pool 仍保持注册，因此你可以监控缩容到零的机群，并决定何时预配容量。

#### 查询参数

`scope` 字符串（可选）

按 pool 列表范围筛选。取 `all`、`team_pool` 或 `personal` 之一。

`includeStale` 布尔值（可选，默认值：false）

为 `true` 时，包含因长期不活跃而被标记为过期的 pool。

#### 响应字段

`pools` 数组

已注册的 pool。每个条目包含：

- `scope` 字符串：pool 的所有权范围（`user` 或 `team`）。
- `ownerId` 整数：该范围的所属用户或团队 id。
- `poolName` 字符串：pool 名称（例如 `default` 或 `gpu`）。
- `connectedWorkerCount` 整数：当前连接到此 pool 的 worker 数。
- `inUseWorkerCount` 整数：当前已连接且有已分配 Agent 的 worker 数。空闲容量为 `connectedWorkerCount - inUseWorkerCount`。
- `firstSeenAtMs`、`lastSeenAtMs` 整数：首次和最后一次观察到的时间，Unix 毫秒。
- `isStale` 布尔值：该 pool 是否因长期不活跃而被标记为过期。
- `repoOwner`、`repoName`、`repoUrl` 字符串（可选）：pool 绑定到某个仓库时的仓库元数据。对于[任意仓库 pool](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#any-repo-pools) 会省略。
- `workerReadyTimeoutSeconds` 整数：已认领的请求等待此 pool 的离线 worker 重新连接、直到认领过期的秒数。`0` 表示针对离线 worker 的后续消息会立即从 pool 重新获取。

```bash
curl --request GET \
  --url "https://api.cursor.com/v0/private-workers/pools?scope=team_pool&includeStale=false" \
  -u "$CURSOR_API_KEY:"
```

**响应：**

```json
{
  "pools": [
    {
      "scope": "team",
      "ownerId": 456,
      "poolName": "gpu",
      "repoOwner": "acme",
      "repoName": "payments-service",
      "repoUrl": "https://github.com/acme/payments-service",
      "connectedWorkerCount": 2,
      "inUseWorkerCount": 1,
      "firstSeenAtMs": 1737000000000,
      "lastSeenAtMs": 1737306880000,
      "isStale": false,
      "workerReadyTimeoutSeconds": 900
    },
    {
      "scope": "team",
      "ownerId": 456,
      "poolName": "sandbox",
      "connectedWorkerCount": 0,
      "inUseWorkerCount": 0,
      "firstSeenAtMs": 1737100000000,
      "lastSeenAtMs": 1737200000000,
      "isStale": false,
      "workerReadyTimeoutSeconds": 0
    }
  ]
}
```

`sandbox` 条目是任意仓库：仓库字段被省略，并且在已连接 worker 为零时该 pool 仍可被选择。

### 注册 Pool

POST

`/v0/private-workers/pools`

在不启动 worker 的情况下注册一个持久 pool。用它在任何 worker 连接之前让 pool 可被选择，例如控制器按需预配容量时。用 `--pool` 启动 worker 会隐式注册该 pool；只有需要预先创建 pool 时才用此端点。

#### 请求正文

`scope` 字符串（必填）

pool 的所有权范围。取 `user` 或 `team` 之一。

`poolName` 字符串（必填）

要注册的 pool 名称（例如 `gpu`）。

`repoOwner`、`repoName` 字符串（可选）

pool 绑定到某个仓库时的仓库元数据。两者一起提供，或都省略以创建任意仓库 pool。

`repoUrl` 字符串（可选）

用于显示的仓库 URL。需要 `repoOwner` 和 `repoName`。

`workerReadyTimeoutSeconds` 整数（可选，默认值：0）

已认领的请求等待此 pool 的离线 worker 重新连接、直到认领过期并把请求放回队列的秒数。当机器会[在轮次之间休眠](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#hibernation) 并可以被唤醒时设置此项。为 `0` 时，针对离线 worker 的后续消息会立即从 pool 重新获取。必须是非负整数。

#### 响应字段

`registered` 布尔值

该 pool 是否已注册。

```bash
curl --request POST \
  --url "https://api.cursor.com/v0/private-workers/pools" \
  -u "$CURSOR_API_KEY:" \
  --header 'Content-Type: application/json' \
  --data '{
    "scope": "team",
    "poolName": "payments-pool",
    "repoOwner": "acme",
    "repoName": "payments-service",
    "repoUrl": "https://github.com/acme/payments-service"
  }'
```

**响应：**

```json
{
  "registered": true
}
```

### 注销 Pool

DELETE

`/v0/private-workers/pools`

注销（软删除）一个持久 pool，使它不再出现在 pool 选择器或[列出 Pool](https://cursor.com/docs/cloud-agent/api/endpoints.md#list-pools) 中。当前连接到该 pool 的 worker 不受影响。团队 pool 需要团队管理员；用户 pool 需要其所有者。

#### 查询参数

`scope` 字符串（必填）

pool 的所有权范围。取 `user` 或 `team` 之一。

`pool_name` 字符串（必填）

要注销的 pool 名称。

`repo_owner` 字符串（可选）

注销按仓库限定的 pool 记录时的仓库所有者。

`repo_name` 字符串（可选）

注销按仓库限定的 pool 记录时的仓库名称。请同时提供 `repo_owner` 和 `repo_name`，或都省略以针对任意仓库 pool。

```bash
curl --request DELETE \
  --url "https://api.cursor.com/v0/private-workers/pools?scope=team&pool_name=sandbox" \
  -u "$CURSOR_API_KEY:"
```

**响应：**

```json
{
  "deregistered": true
}
```

### 列出待处理的 Pool 请求

GET

`/v0/private-workers/pending-requests`

列出尚未分配给 worker 的 pool 请求。当用户正在等待可用的 pool worker 时，用此端点来扩容；也可以在启动临时 worker 之前，把它与[认领待处理请求](https://cursor.com/docs/cloud-agent/api/endpoints.md#claim-a-pending-request) 搭配使用。

对于配置了 `workerReadyTimeoutSeconds` 的 pool，列表还会显示已认领但离线的条目：其已认领 worker 处于离线状态、而重连窗口仍开着的请求。这些条目带有 `claimedWorkerId` 和 `wakeTimeoutMs`，以便控制器可以[唤醒机器](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#hibernation)。

此端点需要服务账号 API key。它返回该 key 所属团队的请求，并排除 My Machines 请求。如果该 key 限定于特定仓库，请传入 `repository`；该仓库必须在该 key 允许的范围内。

响应包含 `streamCursor`。把它传给[监视待处理的 Pool 请求](https://cursor.com/docs/cloud-agent/api/endpoints.md#watch-pending-pool-requests)，以便在此快照之后实时跟随队列变化。

#### 查询参数

`limit` 数字（可选）

要返回的待处理请求数量。默认值：50，最大值：100。

`pageToken` 字符串（可选）

来自上一页响应的分页 cursor。页面 token 绑定到签发它们的 `repository` 和 `pool` 筛选条件。

`repository` 字符串（可选）

按仓库 URL 筛选。按仓库限定的服务账号 API key 必填。省略则针对任意仓库的待处理请求。

`pool` 字符串（可选）

按 pool 名称筛选。与请求的 `pool` 标签精确、区分大小写地匹配。省略则列出该团队上每个 pool 的请求。

#### 响应字段

`requests` 数组

待处理请求。每个条目包含：

- `id` 字符串：待处理请求 / Agent id（作为 `id` 传给[认领](https://cursor.com/docs/cloud-agent/api/endpoints.md#claim-a-pending-request) 或[释放认领](https://cursor.com/docs/cloud-agent/api/endpoints.md#release-a-claim)）。
- `userId` 整数：创建该请求的 Cursor 用户 id。
- `userEmail` 字符串（可选）：请求用户的邮箱（在可用时）。用它选择与用户亲和的容量，而无需另一次查询。
- `serviceAccountId` 字符串（可选）：与该请求关联的服务账号（存在时）。
- `repoOwner`、`repoName`、`repoUrl` 字符串（可选）：请求针对某个仓库时的仓库元数据。任意仓库 pool 请求会省略。
- `labels` 数组：请求标签，为 `{ key, value }` 对（设置了时包含 `repo=` 和 `pool=`）。
- `createdAtMs` 整数：请求创建时间，Unix 毫秒。
- `claimedWorkerId` 字符串（可选）：出现在已认领但离线的条目上：该请求已被此 worker 认领，而此 worker 当前离线。用此 id（`CURSOR_AGENT_WORKER_ID`）启动一个 worker，即可在其机器上恢复该 Agent。
- `wakeTimeoutMs` 整数（可选）：已认领但离线的条目剩余的重连窗口毫秒数。窗口过后，认领会过期，该请求会作为未认领条目重新公布。

`nextPageToken` 字符串（可选）

分页 cursor。没有更多页时省略。要测量队列深度，请分页到结束并清点请求数。

`streamCursor` 字符串

供[监视待处理的 Pool 请求](https://cursor.com/docs/cloud-agent/api/endpoints.md#watch-pending-pool-requests) 使用的不透明恢复位置。同一次逻辑列表的每一页都会重复同一个 `streamCursor`；分页结束后再从它打开监视。它在签发它的那次列表之后五分钟过期。

```bash
curl --request GET \
  --url "https://api.cursor.com/v0/private-workers/pending-requests?limit=50&repository=https%3A%2F%2Fgithub.com%2Facme%2Fpayments-service" \
  -u "$CURSOR_API_KEY:"
```

**响应：**

```json
{
  "requests": [
    {
      "id": "bc-00000000-0000-0000-0000-000000000002",
      "userId": 321,
      "userEmail": "owner@acme.example",
      "serviceAccountId": "sa_abc123",
      "repoOwner": "acme",
      "repoName": "payments-service",
      "repoUrl": "https://github.com/acme/payments-service",
      "labels": [
        { "key": "repo", "value": "acme/payments-service" },
        { "key": "pool", "value": "gpu" },
        { "key": "env", "value": "production" }
      ],
      "createdAtMs": 1737306880000
    }
  ],
  "nextPageToken": "eyJjcmVhdGVkQXRNcyI6MTczNzMwNjg4MDAwMH0=",
  "streamCursor": "djQuZXhhbXBsZS1vcGFxdWUtY3Vyc29y"
}
```

当原始仓库 URL 包含 userinfo 时，`repoUrl` 会省略嵌入的凭据。

### 监视待处理的 Pool 请求

GET

`/v0/private-workers/pending-requests/stream`

通过 Server-Sent Events（SSE）流式传输待处理请求的生命周期事件，使控制器无需轮询即可对队列变化作出反应。

此端点需要服务账号 API key。控制器应先列出再监视：调用[列出待处理的 Pool 请求](https://cursor.com/docs/cloud-agent/api/endpoints.md#list-pending-pool-requests) 来建立你对队列的视图，保留响应中的 `streamCursor`，然后从该确切位置打开监视。列表和监视使用相同的 `repository` 和 `pool` 筛选条件；cursor 绑定到签发它们的筛选条件。

#### 查询参数

`cursor` 字符串（必填）

列表响应中的 `streamCursor`，或你处理的最后一个事件的 SSE `id:`。重新连接时，原生 `EventSource` 会把该 id 作为 `Last-Event-ID` 头重新发送，它优先于查询参数。

`repository` 字符串（可选）

语义与[列出待处理的 Pool 请求](https://cursor.com/docs/cloud-agent/api/endpoints.md#list-pending-pool-requests) 相同。按仓库限定的服务账号 API key 必填。流不接受分页参数。

`pool` 字符串（可选）

只监视此 pool 的事件。与请求的 `pool` 标签精确、区分大小写地匹配。必须与签发该 cursor 的列表所用的筛选条件一致。省略则监视该团队上的每个 pool。

#### 事件

监视会先重放该 cursor 之后保留的转换，然后跟随实时事件。每个事件的 SSE `id:` 都是连接断开时用来恢复的 cursor。

- `created` 事件：一个请求进入队列，包括重连窗口已过、认领已过期的已认领但离线的请求。载荷：与[列出待处理的 Pool 请求](https://cursor.com/docs/cloud-agent/api/endpoints.md#list-pending-pool-requests) 相同的请求对象。
- `claimed` 事件：一个 worker 认领了该请求，或一个离线 worker 重新连接并恢复了它已认领的请求。载荷：`{ id }`。
- `claimed_offline` 事件：一条后续消息到达某个已认领 worker 处于离线状态的请求。载荷：与[列出待处理的 Pool 请求](https://cursor.com/docs/cloud-agent/api/endpoints.md#list-pending-pool-requests) 相同的请求对象，包括 `claimedWorkerId` 和 `wakeTimeoutMs`。请在窗口过期前[唤醒机器](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#hibernation)，否则认领会过期，该请求会以新的 `created` 事件重新公布。
- `expired` 事件：该请求在未被认领的情况下离开队列。载荷：`{ id }`。
- `heartbeat` 事件：Cursor 检查点，没有状态变化，在安静的流上大约每 20 秒发送一次。载荷：`{}`。心跳会推进空闲监视的恢复位置，但不会延长 cursor 的有效期。

#### cursor 有效期

监视链中的每个 cursor 都在**签发它的那次列表之后五分钟**过期。心跳和重连不会延长它。当 cursor 过期，或保留的事件窗口不再覆盖它时，端点返回 HTTP `410 Gone`，正文为 `{"code": "cursor_expired"}`：重新列出，并从新的 `streamCursor` 开始监视。这是常规情况，不是错误路径。请按五分钟计时器主动重新列出，并加入抖动，而不是等到 `410`，这样一组控制器就不会把列表调用同步到同一时刻。

#### 投递保证

投递是尽力而为的，列表才是事实来源。事件在每次转换提交后发布，并带有重试，但罕见的失败可能丢掉一个事件，丢掉的事件从不会重新投递。在两次重新列出之间，把事件当作低延迟提示：以幂等方式应用它们（对 `created` 和 `claimed_offline` 请求做插入或更新，按 `id` 移除 `claimed` 和 `expired` 请求），并让下一次列表纠正任何偏差。针对你从未见过的请求的 `claimed` 事件是空操作。无论你的本地视图如何，认领在服务端都保持原子。

不要持久化 cursor。一个服务账号最多可以持有四条并发流；每个控制器使用一条流，并在本地扇出。

```bash
curl --request GET --no-buffer \
  --url "https://api.cursor.com/v0/private-workers/pending-requests/stream?cursor=$STREAM_CURSOR" \
  --header 'Accept: text/event-stream' \
  -u "$CURSOR_API_KEY:"
```

**示例流：**

```
: connected

event: heartbeat
id: djQuY3Vyc29yLWNoZWNrcG9pbnQ
data: {}

event: created
id: djQuY3Vyc29yLWFmdGVyLWNyZWF0ZWQ
data: {"id":"bc-00000000-0000-0000-0000-000000000002","userId":321,"userEmail":"owner@acme.example","repoOwner":"acme","repoName":"payments-service","repoUrl":"https://github.com/acme/payments-service","labels":[{"key":"pool","value":"gpu"}],"createdAtMs":1737306880000}

event: claimed
id: djQuY3Vyc29yLWFmdGVyLWNsYWltZWQ
data: {"id":"bc-00000000-0000-0000-0000-000000000002"}
```

**控制器循环：**

1. [列出待处理请求](https://cursor.com/docs/cloud-agent/api/endpoints.md#list-pending-pool-requests) 直到结束，并用结果替换你的本地视图。保留响应中的 `streamCursor`。
2. 用 `?cursor=<streamCursor>` 打开监视，并把事件应用到你的本地视图。跟踪你处理的最新事件 `id:`。
3. 断开时，用最新的事件 id 作为 `?cursor=` 重新连接，或依赖原生 `EventSource`，它会自动把该 id 作为 `Last-Event-ID` 重新发送。
4. 遇到 HTTP `410 Gone` 时，回到第 1 步并重新列出。

### 认领待处理请求

POST

`/v0/private-workers/claim`

在某个 worker 启动之前，为它预留一个待处理的 pool 请求。控制器用它在副本之间原子地分配工作：读取[待处理请求](https://cursor.com/docs/cloud-agent/api/endpoints.md#list-pending-pool-requests)，认领一个，然后用与该认领匹配的稳定 worker id 启动 worker。

已有有效认领时，第二次认领会被拒绝。请先[释放认领](https://cursor.com/docs/cloud-agent/api/endpoints.md#release-a-claim)，再认领一个新的 `workerId`。

此端点需要服务账号 API key。

#### 请求正文

`id` 字符串（必填）

待处理请求 id。与[列出待处理的 Pool 请求](https://cursor.com/docs/cloud-agent/api/endpoints.md#list-pending-pool-requests) 中的 `id` 相同。

`workerId` 字符串（必填）

要为该请求预留的 worker id。用相同的 id 通过 `CURSOR_AGENT_WORKER_ID`（或隐藏的 `--worker-id` 标志）启动 worker，以便 bridge 注册已认领的身份。

`sessionToken` 布尔值（可选，默认值：`false`）

同时为这次认领签发一个[会话 token](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#session-tokens)，使 worker 可以在没有服务账号 key 的情况下启动。如果 token 无法签发，认领会失败，请求保持未认领。

`identityToken` 对象（可选）

同时返回一个[认领 token](https://cursor.com/docs/cloud-agent/identity.md#claim-tokens)：一个已签名的 OIDC JWT，标明已认领的 Agent、其所有者和 `workerId`。如果 token 无法签发，认领会失败，请求保持未认领。

`identityToken.audience` 字符串（提供了 `identityToken` 时必填）

你的验证方所检查的 audience，设置为 JWT 的 `aud` 声明。可打印 ASCII，最多 512 个字符。Cursor 不会对 audience 做允许列表限制。

`identityToken.nonce` 字符串（可选）

回显到 JWT `nonce` 声明中的值。可打印 ASCII，最多 512 个字符。每次认领都发送一个新值。

#### 响应字段

`id`、`workerId` 字符串

已认领的请求和 worker id。

`token` 字符串（可选）

只服务这次认领的会话 token。请求发送了 `sessionToken: true` 时出现。认领被释放，或签发它的 API key 被删除或过期时，它停止生效。

`expiresAt` 字符串（可选）

ISO 8601 时间戳，为签发后 7 天。与 `token` 一同出现。

`identityToken.token` 字符串（可选）

已签名的认领 token。请求发送了 `identityToken` 时出现。在据其采取行动之前请[验证它](https://cursor.com/docs/cloud-agent/identity.md#verify-a-claim-token)。

`identityToken.expiresAt` 字符串（可选）

ISO 8601 时间戳，为签发后 5 分钟。与 JWT 的 `exp` 声明一致。

```bash
curl --request POST \
  --url "https://api.cursor.com/v0/private-workers/claim" \
  -u "$CURSOR_API_KEY:" \
  --header 'Content-Type: application/json' \
  --data '{
    "id": "bc-00000000-0000-0000-0000-000000000002",
    "workerId": "pw_123"
  }'
```

**响应：**

```json
{
  "id": "bc-00000000-0000-0000-0000-000000000002",
  "workerId": "pw_123"
}
```

认领成功后，用预留的 id 启动 worker：

```bash
export CURSOR_API_KEY="your-service-account-api-key"
export CURSOR_AGENT_WORKER_ID="pw_123"
agent worker --pool gpu --worker-dir /workspace start
```

如果 worker 无法启动，请[使认领失败](https://cursor.com/docs/cloud-agent/api/endpoints.md#fail-a-claim)，让该 Agent 的轮次以你的原因结束，而不是继续等待。

使用会话 token：

```bash
curl --request POST \
  --url "https://api.cursor.com/v0/private-workers/claim" \
  -u "$CURSOR_API_KEY:" \
  --header 'Content-Type: application/json' \
  --data '{
    "id": "bc-00000000-0000-0000-0000-000000000002",
    "workerId": "pw_123",
    "sessionToken": true
  }'
```

**响应：**

```json
{
  "id": "bc-00000000-0000-0000-0000-000000000002",
  "workerId": "pw_123",
  "token": "eyJ...",
  "expiresAt": "2026-10-02T21:00:00.000Z"
}
```

用该 token 而不是 key 来启动 worker：

```bash
printf '%s' "$TOKEN" > /run/cursor/token
export CURSOR_AGENT_WORKER_ID="pw_123"
agent worker --pool gpu --worker-dir /workspace --auth-token-file /run/cursor/token start
```

使用认领 token：

```bash
curl --request POST \
  --url "https://api.cursor.com/v0/private-workers/claim" \
  -u "$CURSOR_API_KEY:" \
  --header 'Content-Type: application/json' \
  --data '{
    "id": "bc-00000000-0000-0000-0000-000000000002",
    "workerId": "pw_123",
    "identityToken": {
      "audience": "https://provisioner.example.com",
      "nonce": "3f9c1e7a-claim-pw_123"
    }
  }'
```

**响应：**

```json
{
  "id": "bc-00000000-0000-0000-0000-000000000002",
  "workerId": "pw_123",
  "identityToken": {
    "token": "eyJ...",
    "expiresAt": "2026-10-02T21:05:00.000Z"
  }
}
```

当请求发送了 `identityToken` 而 Cursor 无法签发该 token 时，什么都不会被认领。HTTP `403` 表示此 Agent 不允许签发 token；不要重试。HTTP `429` 表示该 Agent 超过了每分钟 30 个认领 token 的预算；在 `Retry-After` 头之后重试。HTTP `503` 表示 token 签名不可用；请退避后重试。

HTTP `409` 不返回 token，也没有任何内容被签名。当另一个 Agent 持有 `workerId`，或这次认领的较早一次尝试已经提交但其响应从未到达时，Cursor 会返回它。响应丢失后，请[释放该认领](https://cursor.com/docs/cloud-agent/api/endpoints.md#release-a-claim)，然后再次认领。

### 创建会话 Token

POST

`/v0/private-workers/tokens`

为你的团队已经持有的认领签发一个[会话 token](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#session-tokens)。当 worker 重新连接到已有认领时使用它，例如被唤醒的休眠机器，或某次 run 的寿命超过了它的 token。`agent worker controller --session-token` 在唤醒休眠机器时会替你调用此端点。

该 token 只服务这次认领。认领被释放，或签发它的 API key 被删除或过期时，它停止生效。

此端点需要来自持有该认领的团队的、Agent 范围的服务账号 API key。按仓库限定的 key 只能为范围内仓库上的 Agent 签发 token。

#### 请求正文

`id` 字符串（必填）

该认领所针对的 Agent id。与[认领待处理请求](https://cursor.com/docs/cloud-agent/api/endpoints.md#claim-a-pending-request) 上的 `id` 相同。

`workerId` 字符串（必填）

该认领绑定到该 Agent 的 worker id。

```bash
curl --request POST \
  --url "https://api.cursor.com/v0/private-workers/tokens" \
  -u "$CURSOR_API_KEY:" \
  --header 'Content-Type: application/json' \
  --data '{
    "id": "bc-00000000-0000-0000-0000-000000000002",
    "workerId": "pw_123"
  }'
```

**响应：**

```json
{
  "id": "bc-00000000-0000-0000-0000-000000000002",
  "workerId": "pw_123",
  "token": "eyJ...",
  "expiresAt": "2026-10-02T21:00:00.000Z"
}
```

HTTP `404` 表示你的团队没有把该 worker 绑定到该 Agent 的认领。

### 释放认领

POST

`/v0/private-workers/claims/{id}/release`

释放把 Agent 绑定到其自托管 worker 的认领，使该 worker 可以服务另一个 Agent。

当没有轮次在使用该 worker 时，释放会立即把它空出来。Cursor 会一起清除该认领和该 Agent 的 worker 分配，把等待中的后续消息作为未认领请求放回 pool 队列，并让 worker CLI 退出。该 Agent 的下一轮次会在另一个 worker 上运行。替换 worker 可以在释放返回后立即认领该 Agent。

当有轮次正在使用该 worker 时，释放返回 HTTP `400`，并且什么都不改变。有轮次正在使用该 worker，是指该 Agent 的[状态](https://cursor.com/docs/cloud-agent/api/endpoints.md#get-an-agent) 为 `ACTIVE`、该 worker 已连接，并且该认领不是在等待一台[休眠](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#hibernation) 的机器唤醒。如果 Cursor 无法读取 worker 的连接状态，它会把该 worker 计为已连接。请在轮次结束后重试，或[取消活跃 run](https://cursor.com/docs/cloud-agent/api/endpoints.md#cancel-a-run) 后再释放。

已有有效认领时，第二次[认领待处理请求](https://cursor.com/docs/cloud-agent/api/endpoints.md#claim-a-pending-request) 会被拒绝。请先释放，再认领一个新的 `workerId`。

`--idle-release-timeout`（环境变量 `CURSOR_WORKER_IDLE_RELEASE_TIMEOUT`）会让 worker CLI 在空闲后自行退出。

此端点需要服务账号 API key。

#### 路径参数

`id` 字符串

待处理请求 / Agent id。与[认领待处理请求](https://cursor.com/docs/cloud-agent/api/endpoints.md#claim-a-pending-request) 上的 `id` 相同。没有请求正文。

```bash
curl --request POST \
  --url "https://api.cursor.com/v0/private-workers/claims/bc-00000000-0000-0000-0000-000000000002/release" \
  -u "$CURSOR_API_KEY:"
```

**响应：**

```json
{
  "id": "bc-00000000-0000-0000-0000-000000000002",
  "workerId": "pw_123"
}
```

HTTP `400` 表示有轮次正在使用该 worker。什么都没有改变。请在轮次结束后重试：

```json
{
  "error": "Worker in use: The agent is mid-turn on this worker; retry after the turn ends, or stop the agent to free the worker now"
}
```

HTTP `404` 表示没有有效认领：已经释放、已过期，或已被接管。不要重试 404。

### 使认领失败

POST

`/v0/private-workers/claims/{id}/fail`

报告你无法启动为某个 Agent 认领的 worker，例如因为基础设施配额用尽或缺少权限。该 Agent 的轮次下次检查 worker 时，会以你的原因作为错误结束，而不是继续等待一个不会连接的 worker。

启动该 Agent 的人会看到 `Your team's self-hosted pool couldn't start a worker for this agent:`，后面跟着你的消息。当 Agent 从 Slack 运行时，Cursor 会在该会话中发布相同文本，并带有 **Try Again** 按钮。

轮次结束后，Cursor 会释放该认领。该 Agent 的下一轮次，包括 Try Again，会作为任何 worker 都可以[认领](https://cursor.com/docs/cloud-agent/api/endpoints.md#claim-a-pending-request) 的未认领请求回到 pool 队列。

只有当有轮次正在等待该认领时，你才能使它失败：该 Agent 的[状态](https://cursor.com/docs/cloud-agent/api/endpoints.md#get-an-agent) 为 `ACTIVE`，且已认领的 worker 未连接。否则端点返回 HTTP `400`，并且什么都不改变。如果 Cursor 无法读取 worker 的连接状态，它会把该 worker 计为已连接。当没有轮次在等待时要释放认领，请使用[释放认领](https://cursor.com/docs/cloud-agent/api/endpoints.md#release-a-claim)。

如果已认领的 worker 已连接，或认领已结束，而轮次还没取走你的报告，Cursor 会丢弃该报告。

此端点需要来自持有该认领的团队的、Agent 范围的服务账号 API key。按仓库限定的 key 只能使范围内仓库上的 Agent 的认领失败。

#### 路径参数

`id` 字符串

该认领所针对的 Agent id。与[认领待处理请求](https://cursor.com/docs/cloud-agent/api/endpoints.md#claim-a-pending-request) 上的 `id` 相同。

#### 请求正文

`message` 字符串（必填）

展示给该 Agent 用户的纯文本原因。Cursor 会移除换行和制表符以外的控制字符，并去掉两端空白。结果必须是 1 到 500 个字符。其他正文字段会被拒绝。

#### 响应字段

`id`、`workerId` 字符串

该失败认领所绑定的 Agent 和 worker。

```bash
curl --request POST \
  --url "https://api.cursor.com/v0/private-workers/claims/bc-00000000-0000-0000-0000-000000000002/fail" \
  -u "$CURSOR_API_KEY:" \
  --header 'Content-Type: application/json' \
  --data '{
    "message": "The gpu pool has reached its limit of 20 machines. Try again in a few minutes."
  }'
```

**响应：**

```json
{
  "id": "bc-00000000-0000-0000-0000-000000000002",
  "workerId": "pw_123"
}
```

带有 `No turn waiting` 的 HTTP `400` 表示该 Agent 不是 `ACTIVE`。什么都没有改变。请改为释放该认领：

```json
{
  "error": "No turn waiting: The agent is not running, so no turn is waiting on this claim; release the claim instead"
}
```

带有 `Worker connected` 的 HTTP `400` 表示已认领的 worker 已连接并正在服务该轮次。什么都没有改变：

```json
{
  "error": "Worker connected: The claimed worker is connected and serving the agent; stop the agent or release the claim after the turn ends"
}
```

不带这两个标题的 HTTP `400` 表示正文无效：`message` 缺失、不是字符串、为空，或长于 500 个字符，或者正文还有其他字段。

HTTP `404` 表示该 Agent 不是你的团队上的自托管 Agent，或它没有认领。不要重试 400 或 404。请重试 5xx；再次报告同一个认领是安全的。

## 元数据端点

### API Key 信息

GET

`/v1/me`

获取用于身份验证的 API key 的信息。

#### 响应字段

`apiKeyName` 字符串

API key 的显示名称。

`createdAt` 字符串

API key 的创建时间（ISO 8601）。

`userId` 整数（用户范围的 key）

API key 所有者的数字 Cursor 用户 ID。服务账号 / 团队 API key 会省略，因为它们不绑定到特定用户。

`userEmail` 字符串（用户范围的 key）

API key 所有者的邮箱地址。

`userFirstName`、`userLastName` 字符串（用户范围的 key）

API key 所有者的名和姓（已填写时）。

```bash
curl --request GET \
  --url https://api.cursor.com/v1/me \
  -u YOUR_API_KEY:
```

**响应（用户范围的 key）：**

```json
{
  "apiKeyName": "Production API Key",
  "userId": 42,
  "createdAt": "2026-04-13T18:30:00.000Z",
  "userEmail": "developer@example.com",
  "userFirstName": "Alex",
  "userLastName": "Rivera"
}
```

**响应（服务账号 key）：**

```json
{
  "apiKeyName": "Production Service Account",
  "createdAt": "2026-04-13T18:30:00.000Z"
}
```

### 列出模型

GET

`/v1/models`

返回你可以传给[创建 Agent](https://cursor.com/docs/cloud-agent/api/endpoints.md#create-an-agent) 上 `model.id` 字段的推荐模型，以及每个模型接受的参数和变体。模型参数使用与 [TypeScript SDK ModelSelection](https://cursor.com/docs/sdk/typescript.md#modelselection) 相同的 `model.params` 形状。

要使用已配置的默认模型，请从请求正文中完全省略 `model`。Cursor 会依次解析你的用户默认模型、团队默认模型，然后是系统默认模型。

#### 响应字段

`items` 中的每一项描述一个模型：

`id` 字符串

创建 Agent 时把此值作为 `model.id` 传入。

`displayName` 字符串

Cursor UI 中显示的人类可读名称。

`description` 字符串（可选）

该模型的简短说明。

`aliases` 数组（可选）

解析到同一模型的备用 ID（例如 `composer-latest`）。

`parameters` 数组（可选）

按模型的参数定义。每个条目有一个 `id`、可选的 `displayName`，以及允许的 `{ value, displayName? }` 条目组成的 `values` 数组。用这些来填充创建请求上的 `model.params`。

`variants` 数组（可选）

该模型接受的具体 `id`+`params` 组合。每个条目有一个 `params` 数组（可以为空）、一个 `displayName`、可选的 `description`，以及可选的 `isDefault` 标志。

```bash
curl --request GET \
  --url https://api.cursor.com/v1/models \
  -u YOUR_API_KEY:
```

**响应：**

```json
{
  "items": [
    {
      "id": "composer-2",
      "displayName": "Composer 2",
      "aliases": ["composer-latest", "composer"],
      "parameters": [
        {
          "id": "fast",
          "displayName": "Fast",
          "values": [
            { "value": "false" },
            { "value": "true", "displayName": "Fast" }
          ]
        }
      ],
      "variants": [
        {
          "params": [{ "id": "fast", "value": "true" }],
          "displayName": "Composer 2",
          "isDefault": true
        },
        {
          "params": [{ "id": "fast", "value": "false" }],
          "displayName": "Composer 2"
        }
      ]
    },
    {
      "id": "claude-4.6-sonnet-thinking",
      "displayName": "Claude 4.6 Sonnet (Thinking)",
      "variants": [
        {
          "params": [],
          "displayName": "Claude 4.6 Sonnet (Thinking)",
          "isDefault": true
        }
      ]
    }
  ]
}
```

### 列出 GitHub 仓库

GET

`/v1/repositories`

列出已认证用户通过 Cursor 的 GitHub App 安装可以访问的 GitHub 仓库。

此端点只返回 GitHub 仓库。GitLab、Bitbucket Cloud 和 Azure DevOps 上的仓库不会列在这里，尽管你可以用[创建 Agent](https://cursor.com/docs/cloud-agent/api/endpoints.md#create-an-agent) 针对它们创建 Agent。

GitHub Enterprise Server 仓库也不会列出。服务账号 API key 始终得到空列表，因为此端点读取的是调用方的个人 GitHub 连接。这两种情况下，都用仓库 URL 创建 Agent。

**此端点的速率限制非常严格。**

请把请求限制为**每用户每分钟 1 次**，以及**每用户每小时 30 次**。

对于可以访问很多仓库的用户，此请求可能需要数十秒才响应。

请妥善处理此信息不可用的情况。

```bash
curl --request GET \
  --url https://api.cursor.com/v1/repositories \
  -u YOUR_API_KEY:
```

**响应：**

```json
{
  "items": [
    {
      "url": "https://github.com/your-org/your-repo"
    }
  ]
}
```


---

## 站点地图

[所有文档页面概览](/llms.txt)
