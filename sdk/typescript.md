> 原文：https://cursor.com/docs/sdk/typescript

# Cursor TypeScript SDK

`@cursor/sdk` 包让你从自己的代码调用 Cursor 的 agent。在 Cursor IDE、CLI 和网页应用里运行的同一个 agent，现在可以从 TypeScript 编写脚本。在 Cursor 里运行 `/sdk` Skill 即可开始。

### Cookbook

端到端示例在 [Cursor Cookbook](https://github.com/cursor/cookbook)：一份 [SDK 快速开始](https://github.com/cursor/cookbook/tree/main/sdk/quickstart)、一个[应用构建原型工具](https://github.com/cursor/cookbook/tree/main/sdk/app-builder)、一块[面向 Cloud Agent 的看板](https://github.com/cursor/cookbook/tree/main/sdk/agent-kanban)，以及一个[编程 agent CLI](https://github.com/cursor/cookbook/tree/main/sdk/coding-agent-cli)。它们是 CI 自动修复 bot、bug 分拣工作者、代码审查流程、嵌入产品内的 agent，以及编排器的好起点。

## 概览

SDK 把本地和云端运行时包在同一套接口后面。无论 agent 在哪里运行，你写的代码都一样。

| 运行时 | 作用 | 何时使用 |
| :------------------------ | :--------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------- |
| **本地** | 在你的 Node 进程里内联运行 agent 循环。文件来自磁盘。 | 针对工作树的开发脚本和 CI 检查。 |
| **云端（Cursor 托管）** | 在隔离的 VM 中运行，并把你的仓库克隆进去。VM 由 Cursor 运行。 | 调用方没有该仓库、你希望多个 agent 并行，或者运行需要在调用方断开后继续时。 |

### 本地指的是本地 agent 循环，不是本地模型

「本地」描述的是 agent 循环和文件系统访问在哪里运行，不是模型在哪里运行。两种模式下，所有推理都经过 Cursor 托管的模型。本地模式把文件留在你的机器上；云端模式在 Cursor 环境中运行。无论哪种情况，模型本身都是托管的。

运行时由你传给 `Agent.create()` 的键决定（`local` 或 `cloud`）。两种运行时使用同一个 `CURSOR_API_KEY`。

REST API 见 [Cloud Agents API](https://cursor.com/docs/cloud-agent/api/endpoints.md)。其他语言见 [SDK Bridge](https://cursor.com/docs/sdk/bridge.md)。

## 认证

创建 agent 之前设置 `CURSOR_API_KEY`（或传入 `apiKey`）。对于没有预置密钥的交互式宿主，[`Cursor.auth.login()`](https://cursor.com/docs/sdk/typescript.md#cursorauth) 会通过浏览器登录签发并保存一个密钥。

SDK 对本地和云端运行都接受用户 API 密钥和服务账号 API 密钥。Team Admin API 密钥尚不支持。

- **用户 API 密钥**，来自 [Cursor Dashboard → API Keys](https://cursor.com/dashboard/api)
- **服务账号 API 密钥**，来自 [Team settings](https://cursor.com/dashboard/team-settings)。参见[服务账号](https://cursor.com/docs/account/enterprise/service-accounts.md)

```bash
export CURSOR_API_KEY="your-key"
```

## 用量与计费

SDK 运行遵循与 IDE 和 Cloud Agents 中的运行相同的定价、请求池和 Privacy Mode 规则。花费会显示在你团队的[用量仪表板](https://cursor.com/dashboard/usage)的 SDK 标签下。

服务账号 API 密钥记到拥有该服务账号的团队账上。用户 API 密钥记到该用户的方案上。

要在代码里读取每次运行的 token 计数，见 [Token 用量](https://cursor.com/docs/sdk/typescript.md#token-usage)。要获取某次 agent 运行的计费用量和美元费用，见 [`Agent.getUsage()`](https://cursor.com/docs/sdk/typescript.md#agentgetusage)。

## 核心概念

| 概念 | 说明 |
| :------------- | :----------------------------------------------------------------------------------------------------------------- |
| **Agent** | 持久容器，保存对话状态、工作区配置和设置。可以跨多次提示词存续。 |
| **Run** | 一次提示词提交。拥有自己的流、状态、结果和取消。 |
| **SDKMessage** | 运行期间发出的规范化流事件。所有运行时的形态相同。 |

## 安装

```bash
npm install @cursor/sdk
```

包名以 `@` 开头。npm 上不存在不带作用域的 `cursor/sdk`。

### 运行时支持

SDK 需要 Node.js 22.13 或更高版本。它为沙箱和 ripgrep 附带按平台区分的 `@cursor/sdk-<os>-<arch>` 二进制文件，因此它首先是一个 Node 包。

导入 `@cursor/sdk` 不会急切加载本地 agent 栈。本地执行器在第一次本地 `acquire` 时才加载，因此只用云端和只用类型的调用方不用承担本地导入的成本。一个进程里的第一个本地 agent 会付出一次性的导入成本，然后该模块会一直留在缓存里。

`@cursor/sdk` 发布自包含的 `.d.ts` 文件，因此类型解析不需要拉入未发布的工作区包。升级之后，重新跑一遍类型检查。`TurnEndedUpdate` 这类流类型会解析成真实类型，而不是 `any`。

### 单文件包和编译后的可执行文件

默认构建会在运行时惰性加载自身的一部分。单文件打包器跟不上这些加载，因此编译后的应用会在第一次 `Agent.create()` 时失败，错误类似 `Cannot find module './986.js'`。SDK 另外提供一份扁平的单文件构建，公开 API 相同。它把一切放进一个文件，因此你的打包器会预先嵌入整个 SDK。

在 Bun 下，`@cursor/sdk` 会自行解析到这份扁平构建。照常导入并编译：

```typescript
import { Agent } from "@cursor/sdk";

const agent = await Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2.5" },
  local: { cwd: process.cwd() },
});
```

```bash
bun build --compile main.ts --outfile my-agent
```

对于 esbuild 等其他单文件打包器，或要显式固定这份扁平构建，改为导入 bundled 入口：

| 入口 | 内容 |
| :--------------------------- | :------------------------------------------------------ |
| `@cursor/sdk/bundled` | `@cursor/sdk` 导出的全部内容。 |
| `@cursor/sdk/bundled/sqlite` | `SqliteLocalAgentStore`，与 `@cursor/sdk/sqlite` 对应。 |

有几件事需要知道：

- **扁平构建可以在 Bun 上运行**，包括 `bun build --compile` 产出的可执行文件。它在 Node 上也能加载，但那里没有 SQLite 存储，因此请配置 `JsonlLocalAgentStore`（通过 [`local.store`](https://cursor.com/docs/sdk/typescript.md#local-agent-stores)），否则 `Agent.create()` 会抛出 `ConfigurationError`。不以单文件形式发布的 Node 应用继续导入 `@cursor/sdk`。
- **`zod`、`@bufbuild/protobuf` 和 `@connectrpc/*` 包从你自己的安装中解析。** 它们随 `@cursor/sdk` 一起提供，你的打包器会嵌入一份共享副本，因此你传给[自定义工具](https://cursor.com/docs/sdk/typescript.md#custom-tools)的 Zod schema 仍然可用。
- **原生二进制文件不能放进 JavaScript 包里。** 沙箱和内置 ripgrep 位于按平台区分的 `@cursor/sdk-<os>-<arch>` 包中。把 `node_modules/@cursor/sdk-<os>-<arch>/` 放在编译后的可执行文件旁边，SDK 会在那里找到它。没有它时，搜索会回退到 `rg`（在 `PATH` 上），而启用 [`sandboxOptions`](https://cursor.com/docs/sdk/typescript.md#sandbox-options) 会抛出 `ConfigurationError`。

bundled 入口的类型解析方式与 `@cursor/sdk` 相同。不需要改 TypeScript 配置。

## 快速开始

最快的入门方式：针对你当前的工作树运行一个本地 agent，并在事件到来时流式接收。云端设置见下面的[创建 agent](https://cursor.com/docs/sdk/typescript.md#creating-agents)。

```typescript
import { Agent } from "@cursor/sdk";

const agent = await Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2.5" },
  local: { cwd: process.cwd() },
});

const run = await agent.send("Summarize what this repository does");

for await (const event of run.stream()) {
  console.log(event);
}
```

每个事件都是一个可区分的 `SDKMessage`。[流式传输](https://cursor.com/docs/sdk/typescript.md#streaming)说明如何提取助手文本、处理工具调用，以及用 `await using` 做清理。一次性提示词（创建、运行、释放）见 [Agent.prompt()](https://cursor.com/docs/sdk/typescript.md#agentprompt)。

### 快速开始会自动批准工具调用

默认的本地 agent 会直接运行工具调用（shell、编辑、写入等），不再征求批准；无头模式里没有人在回路中的提示。要拦住工具调用，配置 [Hooks](https://cursor.com/docs/sdk/typescript.md#hooks)（例如 `beforeShellExecution` 或 `preToolUse`），或者以 [`local.sandboxOptions.enabled:
  true`](https://cursor.com/docs/sdk/typescript.md#sandbox-options) 运行。

## 创建 agent

```typescript
function Agent.create(options: AgentOptions): Promise<SDKAgent>;
```

`Agent.create()` 会校验选项并立刻返回一个句柄。传入 `local` 或 `cloud` 来选择运行时。

```typescript
// Local agent
const agent = await Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2.5" },
  local: { cwd: "/path/to/repo" },
});

// Cloud agent
const agent = await Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2.5" },
  cloud: {
    repos: [{ url: "https://github.com/your-org/your-repo", startingRef: "main" }],
    autoCreatePR: true,
  },
});
```

`agent.agentId` 会立刻被填上。本地 agent 得到 `agent-<uuid>` 形式的 ID；云端 agent 得到 `bc-<uuid>` 形式的 ID。

SDK 启动的 Cloud Agent 会被默认的 agent 列表过滤掉。要在 Cursor Web 或一个 Cursor 窗口里查看它们，点击 **Filter > Source > SDK**。

### 无仓库的 Cloud Agent

Cloud Agent 可以在没有仓库的空 VM 上运行。传入 `cloud` 并带一个空的 `repos` 列表，或者完全省略 `repos`。省略 `cloud` 则会改选本地运行时。

```typescript
const agent = await Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  cloud: { repos: [] },
});

const run = await agent.send(
  "Research the top 3 TypeScript testing frameworks and summarize."
);
console.log((await run.wait()).result);
```

无仓库 agent 必须先为你的账号或团队启用。仓库范围的 API 密钥无法创建它们；请改用不受限的服务账号密钥或用户 API 密钥。

### 会话环境变量

对于 Cloud Agent，当运行需要短期凭据或其他只应随该 agent 存在的值时，传入 `cloud.envVars`。

```typescript
const agent = await Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  cloud: {
    repos: [{ url: "https://github.com/your-org/your-repo" }],
    envVars: {
      STAGING_API_TOKEN: process.env.STAGING_API_TOKEN!,
    },
  },
});
```

这些值会静态加密，注入到 Cloud Agent 的 shell 中，并随该 agent 一起删除。`envVars` 不能与调用方提供的 `agentId` 一起使用；请省略 `agentId`，并从 `agent.agentId` 读取服务器签发的 ID，时间是第一次 `send()` 之后。变量名不能以 `CURSOR_` 开头。

只应在单次运行期间存在的值，改为在 `agent.send()` 上传入。见[每次运行的环境变量](https://cursor.com/docs/sdk/typescript.md#per-run-environment-variables)。

### Agent 元数据

用 `cloud.metadata` 给 Cloud Agent 附上你自己的字符串标签。这些标签会随 agent 持久保存，并在 `SDKAgentInfo.metadata` 上从 `Agent.get()` 和 `Agent.list()` 返回。这些标签不是 VM 内的 [agent 元数据](https://cursor.com/docs/cloud-agent/metadata.md) API。那个 API 从 VM 内部暴露当前运行的 id、所有者、回合和工作区。

```typescript
const agent = await Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  cloud: {
    repos: [{ url: "https://github.com/your-org/your-repo" }],
    metadata: {
      end_user_id: "user-123",
      ticket_id: "ENG-456",
    },
  },
});
```

### 模型参数

用 `model.params` 传入按模型区分的选项，例如推理强度。参数 id 和取值因模型而异。用 [`Cursor.models.list()`](https://cursor.com/docs/sdk/typescript.md#cursormodelslist) 查看你的账号支持的参数和预设变体。

在旧版按请求计费的方案上，当所选模型需要时，Cursor 会自动启用 [Max Mode](https://cursor.com/help/ai-features/max-mode.md)。

### Composer 2 会改路由到 Composer 2.5

Composer 2 已退役。仍然传入 `composer-2` 或 `composer-2-fast` 的 SDK 请求会在认证时改路由到 Composer 2.5，因此已有脚本仍能工作。如果你依赖 `composer-2-fast` 变体，请确认快速行为仍然符合预期。

```typescript
const agent = await Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  model: {
    id: "composer-2.5",
    params: [{ id: "fast", value: "true" }],
  },
  local: { cwd: process.cwd() },
});
```

### Cursor Router

[Cursor Router](https://cursor.com/docs/cursor-router.md) 会为每个 Auto 请求选择一个模型。在 SDK 中，Router 就是 `auto-smart` 模型，并带有 `optimize_for` 参数。它在 Teams 和 Enterprise 上可用。Enterprise 管理员必须先为团队启用 Router，`auto-smart` 才会出现在目录中。

Cursor SDK 是 agent SDK，不是独立的模型推理或 chat-completions API。Router 为 Cursor agent 的运行挑选模型。这些运行可以基于工作区推理、调用工具、运行命令并编辑文件。Cursor 目前没有为任意模型调用记录一个原始的 Router 端点。

#### 选择 Cost、Balance 或 Intelligence

传入 `auto-smart`，并显式设置 `optimize_for`：

| 产品标签 | SDK 值 |
| :------------ | :------------- |
| Cost | `cost` |
| Balance | `balanced` |
| Intelligence | `intelligence` |

产品文案中使用 **Balance**。`balanced` 只作为 SDK 的线路值。

```typescript
import { Agent } from "@cursor/sdk";

await using agent = await Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  model: {
    id: "auto-smart",
    params: [{ id: "optimize_for", value: "balanced" }],
  },
  local: { cwd: process.cwd() },
});

const run = await agent.send("Find and fix the failing authentication test");
const result = await run.wait();

console.log(result.status);
console.log(result.requestId);
```

始终传入 `optimize_for`。不要省略它，也不要发送旧的 `default` 值；通过目录发现才是受支持的契约。

#### 在模型目录中发现 Router

`Cursor.models.list()` 返回当前 API 密钥所属账号和团队可用的模型、参数定义和预设变体。Router 可用时，Cursor Router 会显示为 `auto-smart`。团队管理员可以禁用 Router，或限制成员可以选择哪些优化模式。

在把某个选择硬编码之前，把目录当作事实来源：

```typescript
import { Cursor, type ModelSelection } from "@cursor/sdk";

const models = await Cursor.models.list();
const router = models.find((model) => model.id === "auto-smart");
const optimizeFor = router?.parameters?.find(
  (parameter) => parameter.id === "optimize_for",
);

if (!router || !optimizeFor) {
  throw new Error(
    "Cursor Router is not available for this API key. Verify that Router is enabled for the key's team.",
  );
}

const requestedMode = "balanced";
const allowedValues = new Set(
  optimizeFor.values.map(({ value }) => value),
);

if (!allowedValues.has(requestedMode)) {
  throw new Error(
    `Router mode "${requestedMode}" is not enabled for this team.`,
  );
}

const model: ModelSelection = {
  id: router.id,
  params: [{ id: optimizeFor.id, value: requestedMode }],
};
```

#### 按次运行切换模式

在 `agent.send()` 上覆盖模型，以改变某次运行的 Router 模式：

```typescript
const run = await agent.send("Handle this complex migration", {
  model: {
    id: "auto-smart",
    params: [{ id: "optimize_for", value: "intelligence" }],
  },
});
```

按次运行的模型覆盖会保持生效。之后没有覆盖的 send 会继续使用新的选择。见[按次运行的模型覆盖](https://cursor.com/docs/sdk/typescript.md#per-run-model-override)。

#### 模型 id：`auto-smart`、`auto` 和 `default`

| 选择 | 含义 |
| :-------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| `auto-smart` 加 `optimize_for` | Cursor Router。想要 Cost、Balance 或 Intelligence 时用这个。 |
| `{ id: "auto" }` | 当目录里缺少某个具体模型时，由服务器选择的 Auto 回退。需要明确的 Router 模式时，优先使用 `auto-smart`。 |
| 省略 `optimize_for`，或发送 `default` | 不是受支持的 Router 契约。始终先发现允许的值，再传入 `cost`、`balanced` 或 `intelligence`。 |

#### 计费与路由池

- 所有 Auto 模式都按每个请求被路由到的模型的标价计费。
- 底层模型可能在请求之间变化。需要可复现的对比时，优先使用固定的模型 id。
- Enterprise 的模型允许列表会塑造路由池。拦住必需的模型可能会禁用 Router。

当前费率和路由池见 [Cursor Router](https://cursor.com/docs/cursor-router.md) 和 [Auto 模式](https://cursor.com/docs/models-and-pricing.md#auto-modes)。

#### Router 缺失时的排查

如果缺少 `auto-smart`，或某个优化模式被拒绝：

1. 调用 `Cursor.models.list()`。
2. 确认结果中有 `auto-smart`。
3. 确认 `optimize_for` 包含你想要的值（`cost`、`balanced` 或 `intelligence`）。
4. 确认与该 API 密钥关联的团队已启用 Router。
5. 如果你属于多个团队，确认该密钥正在预期的团队上下文中工作。
6. 如果 Router 不可用，或无法选出一个有效的底层模型，检查团队的模型访问策略。

### 替换系统提示词

`systemPrompt` 用你自己的文本替换主 agent 循环里 Cursor 内置的系统提示词。模型会失去编程助手身份、工具使用协议和沟通指引，因此把 agent 仍然需要的内容重新写上。工具 schema、Rules 和 Skills 仍会加载，Subagent 保留自己的提示词。

```typescript
const agent = await Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2.5" },
  systemPrompt:
    "You are a release manager. Only edit CHANGELOG.md, and answer in one paragraph.",
  local: { cwd: process.cwd() },
});
```

- 仅本地 agent。把 `systemPrompt` 和 `cloud` 一起用会抛出 `ConfigurationError`。
- 必须非空。只有空白的文本会抛出 `ConfigurationError`，时机是 `Agent.create()` 或 `Agent.resume()`。
- 不会持久保存在 agent 上。再次传入 `systemPrompt`（位于 `Agent.resume()`），以便后续运行继续使用它。
- 访问按账号启用。没有启用时，第一次 `send()` 会失败，错误里会点名 `--system-prompt`。

### SDKAgent

由 `Agent.create()` 和 `Agent.resume()` 返回的句柄。

```typescript
interface SDKAgent {
  readonly agentId: string;
  readonly model: ModelSelection | undefined;

  send(message: string | SDKUserMessage, options?: SendOptions): Promise<Run>;
  close(): void;
  reload(): Promise<void>;
  [Symbol.asyncDispose](): Promise<void>;

  listArtifacts(): Promise<SDKArtifact[]>;
  downloadArtifact(path: string): Promise<Buffer>;
  getUsage(options?: GetUsageOptions): Promise<AgentUsage>;
}
```

| 成员 | 说明 |
| :---------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `agentId` | 稳定的 agent 标识。本地为 `agent-<uuid>`，云端为 `bc-<uuid>`。 |
| `model` | 当前的模型选择。每次成功的 `send({ model })` 之后更新。在有东西设置它之前是 `undefined`（包括调用方没有传入 `model` 的已恢复 agent）。 |
| `send` | 用给定的提示词开始一次新运行。返回一个 `Run` 句柄。 |
| `close` | 开始释放，但不等待。即发即忘。 |
| `reload` | 重新读取文件系统配置（hooks、项目 MCP、subagent），而不释放资源。 |
| `[Symbol.asyncDispose]` | 异步释放。与 `await using` 搭配可自动清理。 |
| `listArtifacts` | 列出 agent 产出的文件（仅云端；本地返回空）。 |
| `downloadArtifact` | 按路径下载文件（仅云端；本地会抛出异常）。 |
| `getUsage` | 获取该 agent 的计费 token 用量和美元费用。本地 agent 需要按账号启用用量访问；见 [`Agent.getUsage()`](https://cursor.com/docs/sdk/typescript.md#agentgetusage)。 |

### Agent.prompt()

```typescript
function Agent.prompt(message: string, options?: AgentOptions): Promise<RunResult>;
```

一次性便捷方法：创建 agent，发送一条提示词，等待运行结束，然后释放。

```typescript
const result = await Agent.prompt("What does the auth middleware do?", {
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2.5" },
  local: { cwd: process.cwd() },
});
```

## 发送消息

每次 `agent.send()` 都返回一个 `Run`。agent 会在各次运行之间保留对话上下文；run 是一条提示词的工作单元。

### Run

```typescript
type RunStatus = "running" | "finished" | "error" | "cancelled";
type RunOperation = "stream" | "wait" | "cancel" | "conversation";
type SteerAckOutcome = "complete_delivered" | "revert_to_followup";

interface Run {
  readonly id: string;
  readonly requestId?: string;
  readonly agentId: string;
  readonly status: RunStatus;
  readonly result?: string;
  readonly error?: RunError;
  readonly model?: ModelSelection;
  readonly durationMs?: number;
  readonly usage?: TokenUsage;
  readonly git?: RunGitInfo;
  readonly createdAt?: number;

  stream(): AsyncGenerator<SDKMessage, void>;
  wait(): Promise<RunResult>;
  cancel(): Promise<void>;
  steer?(text: string): Promise<SteerAckOutcome>;
  conversation(): Promise<ConversationTurn[]>;

  supports(operation: RunOperation): boolean;
  unsupportedReason(operation: RunOperation): string | undefined;
  onDidChangeStatus(listener: (status: RunStatus) => void): () => void;
}

interface RunGitInfo {
  branches: Array<{ repoUrl: string; branch?: string; prUrl?: string }>;
}

interface RunError {
  message: string;
  code?: string;
}

interface TokenUsage {
  inputTokens: number;
  outputTokens: number;
  cacheReadTokens: number;
  cacheWriteTokens: number;
  totalTokens: number;
  reasoningTokens?: number;
}

interface RunResult {
  id: string;
  requestId?: string;
  status: "finished" | "error" | "cancelled";
  result?: string;
  error?: RunError;
  model?: ModelSelection;
  durationMs?: number;
  usage?: TokenUsage;
  git?: RunGitInfo;
}
```

### 流式传输

```typescript
const run = await agent.send("Find the bug in src/auth.ts");

for await (const event of run.stream()) {
  switch (event.type) {
    case "assistant":
      for (const block of event.message.content) {
        if (block.type === "text") process.stdout.write(block.text);
      }
      break;
    case "thinking":
      process.stdout.write(event.text);
      break;
    case "tool_call":
      console.log(`[tool] ${event.name}: ${event.status}`);
      break;
    case "status":
      console.log(`[status] ${event.status}`);
      break;
  }
}

// Follow-up on the same agent. Conversation state from the previous
// run is loaded automatically.
const run2 = await agent.send("Fix it and add a regression test");
await run2.wait();
```

要连同文本一起发送图像：

```typescript
const run = await agent.send({
  text: "What's in this screenshot?",
  images: [{ data: base64Png, mimeType: "image/png" }],
});
```

### 不等待流、直接等待

```typescript
const result = await run.wait();

console.log(result.status);      // "finished" | "error" | "cancelled"
console.log(result.result);      // final assistant text, if any
console.log(result.error);       // { message, code? } when the run failed
console.log(result.model);       // resolved ModelSelection used for this run
console.log(result.durationMs);
console.log(result.usage);       // cumulative TokenUsage, or undefined if unavailable
console.log(result.git);         // { branches: [{ repoUrl, branch?, prUrl? }] } on cloud
```

最终的助手文本在 `result.result` 上，是一个字符串。没有 `text`、`message`、`messages` 或 `content` 字段需要再挖。如果你要的是逐步转录，调用 `run.conversation()`，得到结构化的 `ConversationTurn[]` 视图：

```typescript
const result = await run.wait();
const finalText = result.result ?? "";

const turns = await run.conversation();
const lastAssistant = turns
  .flatMap((t) => (t.type === "agentConversationTurn" ? t.turn.steps : []))
  .filter((s) => s.type === "assistantMessage")
  .at(-1);

console.log(lastAssistant?.message.text);
```

### 取消一次运行

```typescript
await run.cancel();
```

取消这次运行。状态变为 `"cancelled"`，实时流中止，进行中的工具调用停止，`run.wait()` 以 `status: "cancelled"` 结束等待。已经写出的部分输出（助手文本）会留在 Run 对象上。

取消在正在进行的本地和云端运行上都支持；如果运行已经结束，取消是空操作。

### 在运行过程中引导

`run.steer(text)` 把一条消息注入已经在跑的回合，而不是等它结束。这个 promise 会在该回合确认是否追加了这段文本之后落定。`complete_delivered` 表示回合已经拿到这条消息，所以不要重发。`revert_to_followup` 表示回合没有接受它，因此在运行结束之后用 `agent.send()` 把它送出去。

```typescript
const run = await agent.send("Migrate the auth module to the new session API");

const outcome = await run.steer?.("Skip the admin routes for now");
if (outcome !== "complete_delivered") {
  await run.wait();
  await agent.send("Skip the admin routes for now");
}
```

- 仅本地运行。云端运行和已分离的本地句柄暴露了 `steer`，但总会解析为 `revert_to_followup`。
- 前台 Subagent 正在运行时也可以引导。该 Subagent 会转到后台并继续运行，这样父回合才能接住这条消息。
- `steer` 在 `Run` 上是可选的，并且不是 `RunOperation`。调用之前检查 `run.steer`，而不是用 `run.supports()`。

### 读取运行状态

```typescript
console.log(run.status);  // "running" | "finished" | "error" | "cancelled"

const stop = run.onDidChangeStatus((status) => {
  console.log(`status changed to ${status}`);
});
// Call `stop()` to remove the listener.

// Structured per-turn view of the conversation accumulated in this run
const turns = await run.conversation();
```

`run.conversation()` 返回这次运行的 `ConversationTurn[]`（带步骤的 agent 回合，或带命令和输出的 shell 回合）。用它来渲染或持久化这次运行的结构化历史，而无需订阅实时流。

### Token 用量

运行时提供用量时，运行会报告 token 用量。从 `run.usage` 读取累计总量（运行进行中），或从 `result.usage` 读取（在 `run.wait()` 之后）。两者都持有一个 `TokenUsage`，它是所有报告过用量的回合之和；如果没有任何回合报告用量，两者都是 `undefined`（一次被取消、从未完成回合的运行，或一个不呈现用量的运行时）。

```typescript
interface TokenUsage {
  inputTokens: number;
  outputTokens: number;
  cacheReadTokens: number;
  cacheWriteTokens: number;
  totalTokens: number;
  reasoningTokens?: number;
}
```

| 字段 | 说明 |
| :----------------- | :------------------------------------------------------------------------------------------------ |
| `inputTokens` | 发给模型的提示词 token。 |
| `outputTokens` | 模型生成的 token。 |
| `cacheReadTokens` | 从提示词缓存提供的 token。 |
| `cacheWriteTokens` | 写入提示词缓存的 token。 |
| `totalTokens` | `inputTokens + outputTokens + cacheReadTokens + cacheWriteTokens`。不含 `reasoningTokens`。 |
| `reasoningTokens` | 推理 token，是 `outputTokens` 的子集。模型或运行时没有报告它时省略。 |

```typescript
const result = await run.wait();

if (result.usage) {
  console.log(`total: ${result.usage.totalTokens}`);
  console.log(`in: ${result.usage.inputTokens}, out: ${result.usage.outputTokens}`);
  console.log(
    `cache read/write: ${result.usage.cacheReadTokens}/${result.usage.cacheWriteTokens}`
  );
} else {
  console.log("no usage reported for this run");
}
```

`reasoningTokens` 已经计在 `outputTokens` 里面，因此 `totalTokens` 把它排除在外，以免重复计算。

要取得流式传输过程中的每回合数字，处理 `usage` [流事件](https://cursor.com/docs/sdk/typescript.md#stream-events)（`SDKUsageMessage`）。它在每个报告了用量的回合结束时触发一次，并带上该回合的 `TokenUsage`。`run.usage` 和 `result.usage` 在整次运行上保持累计。

```typescript
for await (const event of run.stream()) {
  if (event.type === "usage") {
    console.log(`turn used ${event.usage.totalTokens} tokens`);
  }
}
```

token 计数是运行时报告的数字；它们不说明费用。要取得计费用量和某次 agent 运行的美元费用，调用 [`agent.getUsage()`](https://cursor.com/docs/sdk/typescript.md#agentgetusage)。

### 用 requestId 关联一次运行

每次 `agent.send()` 都会得到一个平台生成的 UUID，作为 `requestId` 暴露在 `Run` 和 `RunResult` 上。用它把一段脚本或 CI 运行对应到后端日志、分析和支持会话，而不是只凭 `agentId` 去猜。

```typescript
const run = await agent.send("Audit the auth middleware");
console.log(run.requestId); // e.g. "6e0d261c-86a2-4383-89f0-9162c1c10662"

const result = await run.wait();
logger.info({ requestId: result.requestId }, "run finished");
```

`requestId` 会随这次运行持久保存，因此它会在内存、SQLite 和 JSONL [本地存储](https://cursor.com/docs/sdk/typescript.md#local-agent-stores) 之间往返，并在后端返回时设到云端运行上。把它和[错误](https://cursor.com/docs/sdk/typescript.md#errors)里的 `error.requestId` 一起记下，这样同一个标识就能同时覆盖成功和失败路径。

### 按次运行的模型覆盖

`model`（传给 `agent.send()`）会覆盖该 agent 在这次运行上的选择，然后保持下去：之后没有覆盖的 send 会继续使用新模型。要切回去，再传一次 `model` 覆盖，或从 `agent.model` 读取当前选择。

```typescript
const run = await agent.send("Plan the refactor", {
  model: { id: "composer-2.5", params: [{ id: "fast", value: "true" }] },
});

console.log(agent.model);  // updated to the override after the send succeeds
```

`run.model` 和 `result.model` 反映这次具体运行实际使用的选择，运行一开始就不可变。

### 每次运行的环境变量

Cloud Agent 也可以为单次运行接受环境变量。传入 `cloud.envVars`（位于 `agent.send()`），这些值只注入到该 agent 这次运行的 shell 中。运行结束时，它们会从 VM 上移除，下一次运行看不到它们。这适合在回合之间轮换的凭据，比如你在让 agent 使用之前刚刚签发的短期部署 token。

```typescript
const run = await agent.send("Deploy the preview environment", {
  cloud: {
    envVars: {
      DEPLOY_TOKEN: await mintShortLivedToken(),
    },
  },
});
```

如果某个运行范围的变量与 [`cloud.envVars`（位于 `Agent.create()`）](https://cursor.com/docs/sdk/typescript.md#session-environment-variables) 里的 agent 范围变量同名，这次运行采用运行范围的值，下一次运行再回到 agent 范围的值。

每次运行的变量在第一次 send 上也有效。SDK 会把它们连同 agent 创建一起传递，范围限定在初始运行，因此它们不会持久保存在 agent 上。和 agent 范围的变量一样，它们会静态加密，而且名称不能以 `CURSOR_` 开头。

每次运行的环境变量仅适用于 Cloud Agent，而且对针对公开仓库运行的 agent 不可用。对于本地 agent，agent 进程继承你自己的环境，因此在调用 `send()` 之前把变量设到进程上。

### 对话模式

传入 `mode: "plan"` 或 `mode: "agent"`，控制一次运行是先探索并规划，还是直接实施改动。Plan Mode 在产品中的作用见 [Plan Mode](https://cursor.com/help/ai-features/plan-mode.md)。

设置 `mode`（位于 `Agent.create()`），为第一次运行定下模式。在后续的 `agent.send()` 调用上，省略 `mode` 以保持对话的当前模式，或传入 `mode` 只为这次运行切换。

```typescript
const agent = await Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2.5" },
  mode: "plan",
  cloud: {
    repos: [{ url: "https://github.com/your-org/your-repo" }],
  },
});

await (await agent.send("Design the auth refactor")).wait();
await (await agent.send("Looks good, start building", { mode: "agent" })).wait();
```

### 流式传输原始增量

`run.stream()` 产出规范化的 `SDKMessage` 事件。要取得更低层的更新（逐 token 的文本、流入中的工具调用参数、thinking 增量、嵌套任务更新、步骤边界），把 `onDelta` 和 `onStep` 回调传给 `send()`：

```typescript
const run = await agent.send("Refactor the utils module", {
  onDelta: ({ update }) => {
    if (update.type === "text-delta") process.stdout.write(update.text);
    if (update.type === "thinking-delta") process.stdout.write(update.text);
  },
  onStep: ({ step }) => {
    console.log(`[step] ${step.type}`);
  },
});
```

回调会在处理下一个更新之前被 await，因此你可以施加背压。`InteractionUpdate` 覆盖 `text-delta`、`thinking-delta`、`thinking-completed`、`tool-call-started`、`tool-call-completed`、`tool-call-delta`、`partial-tool-call`、`token-delta`、`step-started`、`step-completed`、`turn-ended`，以及少量总结和 shell 输出增量。

### 按次 send 的选项

| 属性 | 类型 | 说明 |
| :------------------ | :-------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `model` | `ModelSelection` | 按次 send 的模型覆盖。省略时使用 `agent.model`。会保持：成功的 send 会更新 `agent.model`。 |
| `mode` | `"agent" \| "plan"` | 按次 send 的对话模式覆盖。后续调用省略时，保持对话的当前模式。 |
| `mcpServers` | `Record<string, McpServerConfig>` | 内联 MCP 服务器定义。这次运行会完全替换创建时的服务器。 |
| `onStep` | `(args: { step }) => void \| Promise<void>` | 每个已完成的对话步骤（文本、thinking 或工具批次）之后的回调。 |
| `onDelta` | `(args: { update }) => void \| Promise<void>` | 每个原始 `InteractionUpdate` 的回调。 |
| `idempotencyKey` | `string` | 这次 send 可选的、由客户端生成的幂等键。 |
| `cloud.envVars` | `Record<string, string>` | 仅 Cloud Agent。为这次运行注入、并在结束时移除的[每次运行的环境变量](https://cursor.com/docs/sdk/typescript.md#per-run-environment-variables)。仅这次运行按名称覆盖 agent 范围的 `cloud.envVars`。 |
| `local.force` | `boolean` | 仅本地 agent。默认为 `false`。在开始这条消息之前让卡住的活动运行过期。云端会在服务器侧返回 `409 agent_busy`，因此不需要对应项。 |
| `local.customTools` | `Record<string, SDKCustomTool>` | 仅本地 agent。这次运行的[自定义工具](https://cursor.com/docs/sdk/typescript.md#custom-tools)。这次运行会替换 agent 创建时的 `local.customTools`。 |

***

接下来三节是 `SDKMessage`、`InteractionUpdate` 和 `ConversationTurn` 的详细参考。第一遍可以略读或跳过；[恢复 agent](https://cursor.com/docs/sdk/typescript.md#resuming-agents) 会接上正文。

## 流事件

来自 `run.stream()` 的事件。按 `type` 区分。所有事件都包含 `agent_id` 和 `run_id`。

```typescript
type SDKMessage =
  | SDKSystemMessage
  | SDKUserMessageEvent
  | SDKAssistantMessage
  | SDKThinkingMessage
  | SDKToolUseMessage
  | SDKStatusMessage
  | SDKTaskMessage
  | {
      type: "request";
      agent_id: string;
      run_id: string;
      request_id: string;
    }
  | SDKUsageMessage;
```

| `type` | 说明 | 关键字段 |
| :------------ | :----------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------ |
| `"system"` | 初始化元数据。一次运行开始时发出一次。 | `subtype?`（`"init"`）、`model?`、`tools?` |
| `"user"` | 这次运行的用户提示词回显。 | `message.content: TextBlock[]` |
| `"assistant"` | 模型的文本输出。 | `message.content: (TextBlock \| ToolUseBlock)[]` |
| `"thinking"` | 推理内容。 | `text`、`thinking_duration_ms?` |
| `"tool_call"` | 工具调用的生命周期。开始时带 `args` 发出，完成时再带 `result` 发出一次。 | `call_id`、`name`、`status`、`args?`、`result?`、`truncated?` |
| `"status"` | 云端运行的生命周期转换。 | `status`、`message?` |
| `"task"` | 任务级里程碑和总结。 | `status?`、`text?` |
| `"request"` | 正在等待用户输入或批准。 | `request_id` |
| `"usage"` | 每回合 token 用量，运行时报告了它时在回合结束发出一次。 | `usage`（[`TokenUsage`](https://cursor.com/docs/sdk/typescript.md#token-usage)） |

结果数据（最终文本、模型、耗时、累计 token 用量、git 元数据）在流完成后位于 `Run` 对象上。用 `run.wait()` 读取它。

> **工具调用的 schema 不稳定。** `args` 和 `result` 载荷（位于 `tool_call` 事件上）反映每个工具的内部形态，会随着工具演进而变化。工具名称也可能被重命名或替换。把 `args` 和 `result` 当作 `unknown`，防御性地解析。事件信封（`type`、`call_id`、`name`、`status`）是稳定的。

### 消息类型

```typescript
interface SDKSystemMessage {
  type: "system";
  subtype?: "init";
  agent_id: string;
  run_id: string;
  model?: ModelSelection;
  tools?: string[];
}

interface SDKUserMessageEvent {
  type: "user";
  agent_id: string;
  run_id: string;
  message: { role: "user"; content: TextBlock[] };
}

interface SDKAssistantMessage {
  type: "assistant";
  agent_id: string;
  run_id: string;
  message: {
    role: "assistant";
    content: Array<TextBlock | ToolUseBlock>;
  };
}

interface SDKThinkingMessage {
  type: "thinking";
  agent_id: string;
  run_id: string;
  text: string;
  thinking_duration_ms?: number;
}

interface SDKToolUseMessage {
  type: "tool_call";
  agent_id: string;
  run_id: string;
  call_id: string;
  name: string;
  status: "running" | "completed" | "error";
  args?: unknown;
  result?: unknown;
  truncated?: { args?: boolean; result?: boolean };
}

interface SDKStatusMessage {
  type: "status";
  agent_id: string;
  run_id: string;
  status: "CREATING" | "RUNNING" | "FINISHED" | "ERROR" | "CANCELLED" | "EXPIRED";
  message?: string;
}

interface SDKTaskMessage {
  type: "task";
  agent_id: string;
  run_id: string;
  status?: string;
  text?: string;
}

interface SDKUsageMessage {
  type: "usage";
  agent_id: string;
  run_id: string;
  usage: TokenUsage;
}

interface TextBlock {
  type: "text";
  text: string;
}

interface ToolUseBlock {
  type: "tool_use";
  id: string;
  name: string;
  input: unknown;
}
```

大多数工具调用会发出两次 `SDKToolUseMessage`：第一次 `status: "running"` 且填好了 `args`，完成时再发一次，`status: "completed"`（或 `"error"`）且填好了 `result`。`truncated` 标记 SDK 是否因为载荷过大而截断了 `args` 或 `result`。

`SDKStatusMessage` 覆盖云端一侧的生命周期转换。`CREATING` 覆盖 VM 供应和仓库克隆；`RUNNING` 是 agent 在干活；其余都是终态。

每个报告了 token 用量的回合结束时会发出一次 `SDKUsageMessage`，带上该回合的 [`TokenUsage`](https://cursor.com/docs/sdk/typescript.md#token-usage)。跨回合的累计总量留在 `run.usage` 和 `result.usage` 上。见 [Token 用量](https://cursor.com/docs/sdk/typescript.md#token-usage)。

## 交互更新

`InteractionUpdate` 是传给 `onDelta` 回调（位于 `agent.send()`）的原始增量类型。更新比 `SDKMessage` 事件更细：文本逐 token 流入，工具调用会在参数累积时报告部分状态，thinking 随发生而到达。

```typescript
type InteractionUpdate =
  | TextDeltaUpdate
  | ThinkingDeltaUpdate
  | ThinkingCompletedUpdate
  | ToolCallStartedUpdate
  | ToolCallCompletedUpdate
  | ToolCallDeltaUpdate
  | PartialToolCallUpdate
  | TokenDeltaUpdate
  | StepStartedUpdate
  | StepCompletedUpdate
  | TurnEndedUpdate
  | UserMessageAppendedUpdate
  | SummaryUpdate
  | SummaryStartedUpdate
  | SummaryCompletedUpdate
  | ShellOutputDeltaUpdate;
```

### 更新类型

```typescript
interface TextDeltaUpdate {
  type: "text-delta";
  text: string;
}

interface ThinkingDeltaUpdate {
  type: "thinking-delta";
  text: string;
}

interface ThinkingCompletedUpdate {
  type: "thinking-completed";
  thinkingDurationMs: number;
}

interface ToolCallStartedUpdate {
  type: "tool-call-started";
  callId: string;
  toolCall: ToolCall;
  modelCallId: string;
}

interface PartialToolCallUpdate {
  type: "partial-tool-call";
  callId: string;
  toolCall: ToolCall;
  modelCallId: string;
}

interface ToolCallCompletedUpdate {
  type: "tool-call-completed";
  callId: string;
  toolCall: ToolCall;
  modelCallId: string;
}

interface ToolCallDeltaUpdate {
  type: "tool-call-delta";
  callId: string;
  modelCallId: string;
  taskUpdate: NestedTaskUpdate;
}

type NestedTaskUpdate =
  | TextDeltaUpdate
  | ToolCallStartedUpdate
  | ToolCallCompletedUpdate
  | ThinkingDeltaUpdate
  | ThinkingCompletedUpdate
  | PartialToolCallUpdate
  | StepStartedUpdate
  | StepCompletedUpdate;

interface TokenDeltaUpdate {
  type: "token-delta";
  tokens: number;
}

interface StepStartedUpdate {
  type: "step-started";
  stepId: number;
}

interface StepCompletedUpdate {
  type: "step-completed";
  stepId: number;
  stepDurationMs: number;
}

interface TurnEndedUpdate {
  type: "turn-ended";
  usage?: {
    inputTokens: number;
    outputTokens: number;
    cacheReadTokens: number;
    cacheWriteTokens: number;
    reasoningTokens?: number;
  };
}

interface UserMessageAppendedUpdate {
  type: "user-message-appended";
  userMessage: UserMessage;
}

interface SummaryUpdate {
  type: "summary";
  summary: string;
}

interface SummaryStartedUpdate {
  type: "summary-started";
}

interface SummaryCompletedUpdate {
  type: "summary-completed";
}

interface ShellOutputDeltaUpdate {
  type: "shell-output-delta";
  event: Record<string, unknown>;
}
```

`ToolCallDeltaUpdate` 携带来自任务或 Subagent 工具调用的一层嵌套交互更新。模型在提交之前把参数流进一次工具调用时，会发出 `PartialToolCallUpdate`。适用于 `SDKToolUseMessage.args` 的同一条稳定性说明在这里同样适用。

## 对话类型

一次运行的结构化逐回合视图，由 `run.conversation()` 返回，也用在 `onStep` 回调的参数里。

```typescript
type ConversationTurn =
  | { type: "agentConversationTurn"; turn: AgentConversationTurn }
  | { type: "shellConversationTurn"; turn: ShellConversationTurn };

interface AgentConversationTurn {
  userMessage?: UserMessage;
  steps: ConversationStep[];
}

interface ShellConversationTurn {
  shellCommand?: ShellCommand;
  shellOutput?: ShellOutput;
}

type ConversationStep =
  | { type: "assistantMessage"; message: AssistantMessage }
  | { type: "toolCall"; message: ToolCall }
  | { type: "thinkingMessage"; message: ThinkingMessage };

interface AssistantMessage {
  text: string;
}

interface ThinkingMessage {
  text: string;
  thinkingDurationMs?: number;
}

interface UserMessage {
  text: string;
}

interface ShellCommand {
  command: string;
  workingDirectory?: string;
}

interface ShellOutput {
  stdout: string;
  stderr: string;
  exitCode: number;
}
```

`ToolCall` 是覆盖每个内置工具的可区分联合（shell、edit、read、write、glob、grep、ls、semSearch、mcp、task 以及其他）。它的形态面向内部；见流事件下的[稳定性说明](https://cursor.com/docs/sdk/typescript.md#stream-events)。

## 恢复 agent

```typescript
function Agent.resume(agentId: string, options?: Partial<AgentOptions>): Promise<SDKAgent>;
```

用 `Agent.resume()` 按 ID 重新连上一个已有 agent。常见流程：重新连接早先启动的长时间运行的 Cloud Agent，或在本地进程重启后继续对话。运行时根据 ID 前缀自动检测（`bc-` 是云端，其他都是本地）。

```typescript
await using agent = await Agent.resume("bc-abc123", {
  apiKey: process.env.CURSOR_API_KEY!,
});

const run = await agent.send("Also update the changelog");
await run.wait();
```

恢复之后，`agent.model` 为 `undefined`，除非你再次传入 `model`。内联 `mcpServers` 不会跨 resume 持久化，它们常常带有机密，并且只存在于内存中。在 resume 时再次传入它们，或对应当存活下来的服务器使用基于文件的 MCP 配置（`.cursor/mcp.json` + `local.settingSources`）。

## 查看 agent 和运行

列出、获取并重新加载过去的 agent。列表端点返回 `{ items, nextCursor? }`，用于基于游标的分页。

### Agent.list()

```typescript
function Agent.list(options?: ListAgentsOptions): Promise<ListResult<SDKAgentInfo>>;

type ListAgentsOptions = {
  limit?: number;
  cursor?: string;
} & (
  | { runtime?: undefined }
  | { runtime: "local"; cwd?: string; store?: LocalAgentStore }
  | {
      runtime: "cloud";
      prUrl?: string;
      includeArchived?: boolean;
      apiKey?: string;
    }
);
```

```typescript
const { items, nextCursor } = await Agent.list({
  runtime: "local",
  cwd: process.cwd(),
});
```

### Agent.get()

```typescript
function Agent.get(agentId: string, options?: GetAgentOptions): Promise<SDKAgentInfo>;

interface GetAgentOptions {
  cwd?: string;       // local routing
  apiKey?: string;    // cloud routing
  store?: LocalAgentStore;
}
```

运行时根据 agent ID 前缀自动检测（`bc-` → 云端，否则为本地）。

### Agent.listRuns()

```typescript
function Agent.listRuns(agentId: string, options?: ListRunsOptions): Promise<ListResult<Run>>;

type ListRunsOptions = {
  limit?: number;
  cursor?: string;
} & (
  | { runtime?: "local"; cwd?: string; store?: LocalAgentStore }
  | { runtime: "cloud"; apiKey?: string }
);
```

### Agent.getRun()

```typescript
function Agent.getRun(runId: string, options?: GetRunOptions): Promise<Run>;

type GetRunOptions =
  | { runtime?: "local"; cwd?: string; store?: LocalAgentStore }
  | { runtime: "cloud"; agentId: string; apiKey?: string };
```

云端 `getRun` 需要父级 `agentId`。

### Agent.cancelRun()

```typescript
function Agent.cancelRun(runId: string, options?: GetRunOptions): Promise<void>;
```

当你有运行 ID、但没有 `Run` 句柄时，取消这次运行。

### Agent.messages.list()

```typescript
Agent.messages.list(
  agentId: string,
  options?: GetAgentMessagesOptions
): Promise<AgentMessage[]>;

interface GetAgentMessagesOptions {
  limit?: number;
  offset?: number;
  runtime?: "local";
  cwd?: string;
  store?: LocalAgentStore;
}

interface AgentMessage {
  type: "user" | "assistant";
  uuid: string;
  agent_id: string;
  message: unknown;
}
```

返回一个本地 agent 已存储的用户消息和助手消息。

### Agent.getUsage()

获取某次 agent 运行的计费 token 用量和美元费用。在句柄上调用它，或者在没有句柄时按 ID 静态调用。Cloud Agent 返回按运行拆分的明细；本地 agent 返回按回合拆分的明细。传入 `runId` 把结果限制为一条：对 Cloud Agent 是 `run-<uuid>` 形式的运行 ID，对本地 agent 是先前 `getUsage().runs[].runId` 中的一个 ID。

本地 agent 的用量正在逐步推出，并且按账号启用。在你的 API 密钥所属账号启用之前，对本地 agent 调用 `getUsage()` 会返回 `403 feature_unavailable`，SDK 会把它作为 `UnknownAgentError` 抛出，且 `error.code === "feature_unavailable"`。Cloud Agent 不受此门控。在此期间，从 `run.usage` 或 `result.usage` 读取每次运行的 token 计数（见 [Token 用量](https://cursor.com/docs/sdk/typescript.md#token-usage)），并从 SDK 标签下的[用量仪表板](https://cursor.com/dashboard/usage)读取计费费用。

```typescript
agent.getUsage(options?: GetUsageOptions): Promise<AgentUsage>;
function Agent.getUsage(
  agentId: string,
  options?: GetUsageOptions & { apiKey?: string }
): Promise<AgentUsage>;

interface GetUsageOptions {
  runId?: string;
}

interface AgentUsage {
  usage: TokenUsage;   // total for the agent
  cost?: UsageCost;    // total for the agent
  runs: RunUsage[];
}

interface RunUsage {
  runId: string;
  usage: TokenUsage;
  cost?: UsageCost;
}

interface UsageCost {
  rawCostCents: number;   // undiscounted model token cost; 0 for request-priced usage
  chargedCents: number;   // amount charged, discounts and the Cursor Token Fee included
}
```

```typescript
const { usage, cost, runs } = await agent.getUsage();

console.log(`tokens: ${usage.totalTokens}`);
if (cost) {
  console.log(`charged: $${(cost.chargedCents / 100).toFixed(2)}`);
}
for (const run of runs) {
  console.log(run.runId, run.usage.totalTokens, run.cost?.chargedCents);
}
```

对 Cloud Agent 调用时如果不带 `runId`，`usage` 和 `cost` 也会计入该 agent 的 Task Subagent 和侧边聊天，因此它们可能大于 `runs` 的总和。带上 `runId` 时，`usage`、`cost` 以及该运行的条目也会计入从这次运行开始、直到该 agent 的下一次运行开始之间的 Subagent 和侧边聊天用量，如[获取 Agent 用量](https://cursor.com/docs/cloud-agent/api/endpoints.md#get-agent-usage)所述。

费用包含折扣，并且可能在运行结束后过一会儿才结算；在此之前 `cost` 不存在。对于方案内包含的用量、BYOK 用量和赠送额度用量，`chargedCents` 为 `0`。

这与 [Token 用量](https://cursor.com/docs/sdk/typescript.md#token-usage) 是不同的视图：`run.usage` 是单次运行的实时 token 计数，而 `getUsage()` 是该 agent 各次运行的计费记录。

### Cloud Agent 生命周期

Cloud Agent 会留在你团队的工作区里，直到你归档或删除它们。`Agent.list({ runtime: "cloud" })` 默认隐藏已归档的 agent；传入 `includeArchived: true` 即可看到它们。按 `prUrl` 过滤，找到打开了某个 pull request 的 agent。

```typescript
function Agent.archive(agentId: string, options?: AgentOperationOptions): Promise<void>;
function Agent.unarchive(agentId: string, options?: AgentOperationOptions): Promise<void>;
function Agent.delete(agentId: string, options?: AgentOperationOptions): Promise<void>;

interface AgentOperationOptions {
  cwd?: string;
  apiKey?: string;
  store?: LocalAgentStore;
}
```

```typescript
await Agent.archive(agentId);     // soft-delete; transcript stays readable
await Agent.unarchive(agentId);   // restore an archived agent
await Agent.delete(agentId);      // permanent; subsequent reads return 404
```

### SDKAgentInfo

`Agent.list()` 和 `Agent.get()` 返回的元数据形态。

```typescript
type SDKAgentInfo = {
  agentId: string;
  name: string;
  summary: string;
  lastModified: number;
  status?: "running" | "finished" | "error";
  createdAt?: number;
  archived?: boolean;
} & (
  | { runtime?: undefined }
  | { runtime: "local"; cwd?: string }
  | {
      runtime: "cloud";
      env?: { type: "cloud" | "pool" | "machine"; name?: string };
      repos?: string[];
      metadata?: Record<string, string>;
    }
);
```

## Cursor 命名空间

账号级读取、目录读取，以及进程范围的 SDK 配置。读取方法接受可选的 `{ apiKey }`，否则回退到 `CURSOR_API_KEY`，然后再到已保存的[浏览器登录](https://cursor.com/docs/sdk/typescript.md#cursorauth)。

### Cursor.auth

给没有预置 API 密钥的宿主用的交互式登录。`Cursor.auth.login()` 会在浏览器里打开 Cursor 网站的登录页，等待完成，并签发一个用户 API 密钥（默认 90 天），存入 `~/.cursor/sdk/auth.json`。登录之后，`Agent.create()`、`Cursor.me()` 以及其他读取无需 `apiKey` 或 `CURSOR_API_KEY` 即可工作。

```typescript
import { Cursor } from "@cursor/sdk";

await Cursor.auth.login();

const status = await Cursor.auth.status();
// { status: "logged-in", backendUrl, email?, apiKeyExpiresAtMs? }
// | { status: "logged-out" }

await Cursor.auth.logout();
```

| 选项 | 说明 |
| :------------ | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `backendUrl` | API 基址。默认先用 `CURSOR_BACKEND_URL`，然后是生产环境。 |
| `websiteUrl` | 浏览器登录的基址。默认先用 `CURSOR_WEBSITE_URL`，然后是生产环境。 |
| `openBrowser` | `true`（默认）会在很可能能打开时打开系统浏览器；`false` 从不打开；函数则是自定义打开方式。在 SSH 会话中，或设置了 `NO_OPEN_BROWSER` 时会跳过。 |
| `onLoginUrl` | 在等待之前带着登录 URL 被调用，这样宿主可以把它显示出来。省略且没有打开浏览器时，URL 会写到 stderr。 |
| `signal` | 用来取消等待的 `AbortSignal`；然后 `login` 会抛出 `AuthenticationError`。 |
| `store` | 凭据存到哪里。默认是 `~/.cursor/sdk/auth.json`；传入 `null` 则只在结果里收到密钥。 |
| `apiKeyName` | 签发的密钥在仪表板 API 密钥列表中的显示名称。 |
| `apiKeyTtlMs` | 签发的密钥的寿命，单位是毫秒。默认 90 天。 |

`Cursor.auth.login()` 返回 `{ apiKey, email?, apiKeyExpiresAtMs }`。用 `FileCredentialStore` 或 `InMemoryCredentialStore` 提供自定义的 `store`，传给 `login()`、`status()` 或 `logout()`。

整个 SDK 里的凭据解析顺序：显式的 `apiKey`，然后是 `CURSOR_API_KEY`，然后是已保存的登录。已保存的登录不会从本地 Cursor 应用安装里读取凭据；它只保存 `Cursor.auth.login()` 签发的密钥。

### Cursor.configure()

```typescript
function Cursor.configure(options: CursorConfigureOptions): void;

interface CursorConfigureOptions {
  local?: {
    store?: LocalAgentStore | null;
    useHttp1ForAgent?: boolean | null;
    workspaceScanCacheTtlMs?: number | null;
  };
}
```

为之后的 `Agent.*` 调用设置本地 agent 的默认值。单次调用上的字段会覆盖这些值；传入 `null` 可以清掉先前的默认值。

| 选项 | 说明 |
| :------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `local.store` | 调用省略 `local.store` 时使用的默认[本地 agent 存储](https://cursor.com/docs/sdk/typescript.md#local-agent-stores)。SDK 通过 `node:sqlite` 使用磁盘上的 SQLite；当该模块不可用时，`Agent.create()` 会抛出 `ConfigurationError`，除非你在这里配置 `JsonlLocalAgentStore` 或其他存储。 |
| `local.useHttp1ForAgent` | 强制本地 agent 的后端流使用带 SSE 的 HTTP/1.1，而不是 HTTP/2。在代理后面，或在不支持 HTTP/2 的 fetch 栈上有用。由于上游的 HTTP/2 兼容问题，Bun 默认使用 HTTP/1.1。 |
| `local.workspaceScanCacheTtlMs` | SDK 复用一次工作区扫描（Rules、Skills、`AGENTS.md`、ignore 文件）的时长，单位是毫秒。默认 20 秒。在只在部署时才变化的检出上，长期运行的宿主可以提高它；代价是新鲜度，因为进程启动之后才加的规则在这段时间里可能看不到。环境变量 `CURSOR_RIPWALK_CACHE_TTL_MS` 设置同一个值。 |

```typescript
import { Cursor, JsonlLocalAgentStore } from "@cursor/sdk";

Cursor.configure({
  local: {
    store: new JsonlLocalAgentStore("/var/lib/cursor-agents"),
    useHttp1ForAgent: true,
  },
});
```

### Cursor.me()

```typescript
function Cursor.me(options?: CursorRequestOptions): Promise<SDKUser>;

interface CursorRequestOptions {
  apiKey?: string;
}

interface SDKUser {
  apiKeyName: string;
  userId?: number;
  userEmail?: string;
  userFirstName?: string;
  userLastName?: string;
  createdAt: string;
}
```

### Cursor.models.list()

```typescript
function Cursor.models.list(options?: CursorRequestOptions): Promise<SDKModel[]>;

type SDKModel = ModelListItem;

interface ModelListItem {
  id: string;
  displayName: string;
  description?: string;
  aliases?: string[];
  parameters?: ModelParameterDefinition[];
  variants?: ModelVariant[];
}

interface ModelParameterDefinition {
  id: string;
  displayName?: string;
  values: Array<{ value: string; displayName?: string }>;
}

interface ModelVariant {
  params: ModelParameterValue[];
  displayName: string;
  description?: string;
  isDefault?: boolean;
}
```

用 `Cursor.models.list()` 查看有效的 `model` id 和按模型区分的 `params`，然后再调用 `Agent.create()` 或 `agent.send()`。参数因模型而异。常见例子包括推理强度，以及 Cursor Router 的 `optimize_for`（位于 `auto-smart`）。

目录是账号和团队专属的。只有当 Router 对该 API 密钥的团队可用时，Cursor Router 才会显示为 `auto-smart`。见 [Cursor Router](https://cursor.com/docs/sdk/typescript.md#cursor-router)。

```typescript
const models = await Cursor.models.list();
const composer = models.find((model) => model.id === "composer-2.5");

console.log(composer?.parameters);
// [
//   {
//     id: "fast",
//     displayName: "Fast",
//     values: [
//       { value: "false" },
//       { value: "true", displayName: "Fast" },
//     ],
//   },
// ]
```

通过 `model.params` 传入选中的参数值。预设 `variants` 已经包含有效的 `params`，因此你可以把它们抄进一个模型选择。

```typescript
const agent = await Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  model: {
    id: "composer-2.5",
    params: [{ id: "fast", value: "true" }],
  },
  local: { cwd: process.cwd() },
});
```

#### 最佳实践

- **先发现，不要硬编码。** 在启动时（或每个进程一次）调用 `Cursor.models.list()` 并缓存结果。模型 id 和参数形态会随着新模型发布而变化。
- **模型期望参数时就显式传入。** `parameters` 数组非空的模型是参数化模型。把你想要的参数发出去；否则这次运行会使用每个参数的第一个允许值，这可能不是你想要的。对于 Cursor Router，始终显式传入 `optimize_for`。
- **按能力解析，而不是按 id。** 如果你要的是「当前默认的快速模式」，而不是某个具体模型，就去查它：```typescript
  const models = await Cursor.models.list();
  const composer = models.find((m) => m.id === "composer-2.5");
  const fast = composer?.parameters?.find((p) => p.id === "fast");
  const fastValue = fast?.values.find((v) => v.value === "true")?.value;

  const model = composer
    ? {
        id: composer.id,
        params: fastValue ? [{ id: "fast", value: fastValue }] : undefined,
      }
    : {
        id: "auto-smart",
        params: [{ id: "optimize_for", value: "balanced" }],
      };
  ```

当目标模型缺失时，优先使用明确的 Router 选择（`auto-smart` + `optimize_for`）。只有在你想要由服务器选择的 Auto、且不挑选 Cost、Balance 或 Intelligence 时，才回退到 `{ id: "auto" }`。

### Cursor.repositories.list()

```typescript
function Cursor.repositories.list(options?: CursorRequestOptions): Promise<SDKRepository[]>;

interface SDKRepository {
  url: string;
}
```

返回调用用户的团队已连接的 GitHub 仓库。仅云端。

## 配置来源一览

MCP 服务器、Subagent 和 Hooks 都从内联选项和磁盘配置的混合中解析。三者的优先级形态相同：按次 send 的内联 > 创建时的内联 > 项目文件 > 用户文件 > 团队 / 仪表板配置。

| 功能 | 内联选项 | 本地文件（项目） | 本地文件（用户） | 云端 / 仪表板 | 优先级 |
| :------------------- | :---------------------------------------------------------- | :------------------------------------------------------------------------- | :--------------------------------------- | :------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------ |
| **MCP 服务器** | `mcpServers`（位于 `Agent.create()` 和 `agent.send()`） | `.cursor/mcp.json`（由 `local.settingSources` 包含 `"project"` 来控制） | `~/.cursor/mcp.json`（由 `"user"` 控制） | 在 [cursor.com/agents](https://cursor.com/agents) 配置的服务器（仅云端） | 本地：Send > create > plugins > project > user；云端：Send > create > dashboard |
| **Subagents** | `agents`（位于 `Agent.create()`） | `.cursor/agents/*.md`（frontmatter：`name`、`description`、`model?`） | 不适用 | 当 agent 针对克隆下来的仓库运行时，云端会加载同样的项目文件 | 同名时内联覆盖基于文件的定义 |
| **Hooks** | 无，只能基于文件 | `.cursor/hooks.json`（加脚本） | `~/.cursor/hooks.json` | 云端运行项目 Hooks。在 Enterprise 方案上，还有团队 Hooks 和由 Enterprise 管理的 Hooks。 | 基于文件；项目与用户 / 团队 / Enterprise 按 [Hooks](https://cursor.com/docs/hooks.md) 分层 |
| **设置来源** | `local.settingSources` 选择要加载哪些磁盘上的层 | `.cursor/` | `~/.cursor/` | 不适用 | 云端始终加载 `project` / `team` / `plugins`，并忽略 `local.settingSources`。 |

内联值适合绝不该落到磁盘上的机密（每次运行的 API 密钥、租户范围的 token）。基于文件的配置适合策略：Hooks 尤其是项目边界，不是按次运行的开关。

## MCP 服务器

agent 可以从多个来源加载 MCP 服务器。`Agent.create()` 或 `agent.send()` 里的内联定义是最常见的路径。也支持基于文件和由仪表板管理的配置。

### 会加载什么

**本地 agent** 最多从五个来源加载服务器，名称冲突时先匹配者优先：

1. `mcpServers`（位于 `agent.send()`）。这次运行会完全替换创建时的服务器（不是合并）。
2. `mcpServers`（位于 `Agent.create()`）。没有按次 send 的覆盖时使用。
3. Plugin 服务器，如果 `local.settingSources` 包含 `"plugins"`。
4. 来自 `.cursor/mcp.json` 的项目服务器，如果 `local.settingSources` 包含 `"project"`。
5. 来自 `~/.cursor/mcp.json` 的用户服务器，如果 `local.settingSources` 包含 `"user"`。

没有 `local.settingSources` 时，只加载内联服务器。如果某个本地 MCP 服务器需要 OAuth 登录，SDK 不能提示你登录。只有你已经从 Cursor 应用登录过该服务器时它才能工作，此时 SDK 会复用那份已保存的登录。

**Cloud Agent** 从这些来源加载服务器：

1. `mcpServers`（位于 `agent.send()`）。这次运行会完全替换创建时的服务器（不是合并）。
2. `mcpServers`（位于 `Agent.create()`）。没有按次 send 的覆盖时使用。
3. 你在 [cursor.com/agents](https://cursor.com/agents) 上的用户和团队 MCP 服务器。

如果内联服务器没有包含 `auth` 或 `headers`，而你此前已经在 cursor.com/agents 上授权过该服务器 URL，那么用个人 API token 认证的运行会自动复用那些 OAuth token。服务账号 API 密钥不能回退到用户认证，因为它们不关联某个用户。

`local.settingSources` 不适用于 Cloud Agent。

### 本地

```typescript
const agent = await Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "auto" },
  local: { cwd: process.cwd() },
  mcpServers: {
    docs: {
      type: "http",
      url: "https://example.com/mcp",
      auth: {
        CLIENT_ID: "client-id",
        scopes: ["read", "write"],
      },
    },
    filesystem: {
      type: "stdio",
      command: "npx",
      args: ["-y", "@modelcontextprotocol/server-filesystem", process.cwd()],
      cwd: process.cwd(),
    },
  },
});
```

### 云端

Cloud Agent 也可以内联接收已认证的 MCP 配置。当 Cursor 应通过后端代理一个远程 MCP 时，使用 HTTP 认证。当服务器在云端 VM 内运行、并从环境变量读取凭据时，使用 stdio `env`。

```typescript
const agent = await Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2.5" },
  cloud: {
    repos: [{ url: "https://github.com/your-org/your-repo", startingRef: "main" }],
  },
  mcpServers: {
    linear: {
      type: "http",
      url: "https://mcp.linear.app/sse",
      headers: {
        Authorization: `Bearer ${process.env.LINEAR_API_KEY!}`,
      },
    },
    figma: {
      type: "http",
      url: "https://api.figma.com/mcp",
      auth: {
        CLIENT_ID: process.env.FIGMA_CLIENT_ID!,
        CLIENT_SECRET: process.env.FIGMA_CLIENT_SECRET!,
        scopes: ["file_content:read"],
      },
    },
    github: {
      type: "stdio",
      command: "npx",
      args: ["-y", "@modelcontextprotocol/server-github"],
      env: {
        GITHUB_TOKEN: process.env.GITHUB_TOKEN!,
      },
    },
  },
});
```

静态 API 密钥或 Bearer token 使用 `headers`，Cursor 会在每个请求上把它们传过去。受 OAuth 保护的服务器使用 `auth`。对于云端，Cursor 在服务器侧跑一次 OAuth 流程，并在各次运行之间复用该 token。在本地，SDK 不能打开浏览器让你登录；它只复用你已经通过 Cursor 应用登录而取得的 token。

- HTTP 的 `headers` 和 `auth` 由 Cursor 的后端处理。敏感字段会被脱敏，不会进入 VM。
- Stdio 的 `env` 值会传入 VM，因为服务器在那里运行。把它们当作任何其他运行时机密来对待。
- 在 cursor.com/agents 上配置的 MCP 服务器的 OAuth 始终按用户隔离，即便是团队级服务器。

完整配置格式见 [MCP](https://cursor.com/docs/mcp.md)，云端特有行为见 [Cloud Agent 能力](https://cursor.com/docs/cloud-agent/capabilities.md#mcp-tools)。

## Subagents

定义具名 Subagent，主 agent 通过 `Agent` 工具启动它们。内联传入：

```typescript
const agent = await Agent.create({
  model: { id: "composer-2.5" },
  apiKey: process.env.CURSOR_API_KEY!,
  local: { cwd: process.cwd() },
  agents: {
    "code-reviewer": {
      description: "Expert code reviewer for quality and security.",
      prompt: "Review code for bugs, security issues, and proven approaches.",
      model: "inherit",
    },
    "test-writer": {
      description: "Writes tests for code changes.",
      prompt: "Write comprehensive tests for the given code.",
    },
  },
});
```

提交在仓库 `.cursor/agents/*.md` 里的 Subagent（frontmatter 含 `name`、`description`，以及可选的 `model`）也会被加载。同名时，内联定义覆盖基于文件的定义。

### 嵌套 Subagent

Subagent 可以在嵌套层数上限内启动自己的 Subagent。当一个 Subagent 使用 `Agent` 工具时，SDK 交给它的是父级所拥有的同一个 Subagent 执行器，因此父级可以委托给一个还会继续委托的 Subagent。每一层到达的是同一组具名 Subagent 和[自定义工具](https://cursor.com/docs/sdk/typescript.md#custom-tools)。顶层 agent 和它的直接 Subagent 可以启动 Subagent，但由另一个 Subagent 启动的 Subagent 不能再继续启动。

### 后台 Subagent

agent 在后台运行 Subagent 时，该 Subagent 的结果会作为同一次运行上的后续回合返回给父级，而不再在父回合结束时被丢掉。`run.stream()` 会持续产出这些回合的事件，`run.wait()` 会在它们之后返回，并把最后一回合的文本作为 `result`。仅本地 agent。

## 限制工具集

`tools` 用允许列表限定提供给模型的内置工具；`disallowedTools` 移除工具并保留其余工具，包括在你的 SDK 版本发布之后才加到平台上的工具。目前两者都仅限本地 agent，并且都不会持久保存在 agent 上：要让后续运行保持限制，在 `Agent.resume()` 上再次传入它们。

```typescript
// Read-only agent: only these tools are offered.
const reader = await Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2.5" },
  tools: ["read", "grep", "glob", "ls"],
  local: { cwd: process.cwd() },
});

// Everything except shell access.
const noShell = await Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2.5" },
  disallowedTools: ["shell"],
  local: { cwd: process.cwd() },
});
```

- `tools: undefined`（默认）提供所选模型的标准工具集；`tools: []` 不提供任何内置工具，因此模型只能用文本回应。
- 两个字段都接受 `ToolName` 联合：公开名称（`"read"`、`"edit"`、`"task"`、`"webSearch"`，……）、能力分组 `"shell"` 和 `"mcp"`，以及原始 proto 工具名。未知名称会抛出 `ConfigurationError`，时机是 `Agent.create()` / `Agent.resume()`。
- 拒绝优先：一个工具必须在 `tools` 中（当设置了它时），并且不在 `disallowedTools` 中，才会被提供。
- 禁用 `"mcp"` 也会移除[自定义工具](https://cursor.com/docs/sdk/typescript.md#custom-tools)。禁用 `"task"` 会阻止 [Subagents](https://cursor.com/docs/sdk/typescript.md#subagents)；否则 Subagent 保留各自精选的工具集。

## 自定义工具

自定义工具让你把自己的函数暴露给 agent，而不必另起一个独立的 MCP 服务器。在 `local.customTools` 上传入它们，SDK 会把它们注册成一个名为 `custom-user-tools` 的 MCP 服务器。agent 通过与其他服务器相同的 MCP 路径发现并调用它们。拒绝规则和[沙箱](https://cursor.com/docs/sdk/typescript.md#sandbox-options)限制仍然适用，但自定义工具会跳过交互式批准，因此[沙箱](https://cursor.com/docs/sdk/typescript.md#sandbox-options)运行和 [auto-review](https://cursor.com/docs/sdk/typescript.md#auto-review) 运行会直接调用它们，不再提示。自定义工具也会到达 [Subagents](https://cursor.com/docs/sdk/typescript.md#subagents)（包括嵌套的）。

自定义工具仅限本地 agent。Cloud Agent 在创建时忽略 `local.customTools`，并抛出 `ConfigurationError`，如果你在 `send()` 上传入它。

```typescript
const agent = await Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2.5" },
  local: {
    cwd: process.cwd(),
    customTools: {
      get_deployment_status: {
        description: "Look up the current deployment status for a service.",
        inputSchema: {
          type: "object",
          properties: {
            service: { type: "string", description: "Service name" },
          },
          required: ["service"],
        },
        async execute({ service }) {
          const res = await fetch(`https://deploys.internal/api/${service}`);
          const body = await res.json();
          return `Service ${service} is ${body.status} (build ${body.build}).`;
        },
      },
    },
  },
});

await agent.send("Is the checkout service deployed yet?").then((r) => r.wait());
```

在 `Agent.create()` 上设置一次自定义工具，让它们应用于每一次运行，或传入 `local.customTools`（位于单次 `agent.send()`），只为这次运行替换它们。

```typescript
await agent.send("Roll forward if the canary is healthy", {
  local: {
    customTools: {
      promote_canary: {
        description: "Promote the current canary build to production.",
        async execute() {
          await promoteCanary();
          return { content: [{ type: "text", text: "Promoted." }] };
        },
      },
    },
  },
});
```

### 工具定义

```typescript
interface SDKCustomTool {
  description?: string;
  inputSchema?: Record<string, SDKJsonValue>;
  outputSchema?: Record<string, SDKJsonValue>;
  annotations?: SDKToolAnnotations;
  execute: (
    args: Record<string, SDKJsonValue>,
    context: SDKCustomToolContext
  ) => SDKCustomToolResult | Promise<SDKCustomToolResult>;
}

interface SDKToolAnnotations {
  title?: string;
  readOnlyHint?: boolean;
  destructiveHint?: boolean;
  idempotentHint?: boolean;
  openWorldHint?: boolean;
}

interface SDKCustomToolContext {
  toolCallId?: string;
  sessionId?: string;
}
```

| 字段 | 说明 |
| :------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `description` | 展示给模型，让它知道何时调用这个工具。默认为空字符串。 |
| `inputSchema` | 参数的 JSON Schema。默认是一个接受任意属性的开放对象。 |
| `outputSchema` | 该工具结构化结果的 JSON Schema，作为 MCP 的 `Tool.outputSchema` 告知模型。结果不会对照它做校验。 |
| `annotations` | 传给模型的 MCP 工具注解（`title`、`readOnlyHint`、`destructiveHint`、`idempotentHint`、`openWorldHint`）。只是描述性提示；SDK 里没有任何东西强制执行它们。 |
| `execute` | 你的回调。收到解析后的 `args`，以及一个 `context`，其中有 `toolCallId` 和调用该工具的本地会话的 `sessionId`。每个 Subagent 有自己的 `sessionId`。它在你的进程里运行，因此能碰到你的代码能碰到的任何东西。 |

### 工具结果

`execute` 可以返回普通字符串、任意 JSON 值，或一个结构化信封。映射的键是模型调用的工具名。

```typescript
type SDKCustomToolResult =
  | string
  | SDKJsonValue
  | {
      content: SDKCustomToolContent[];
      isError?: boolean;
      structuredContent?: Record<string, SDKJsonValue>;
    };

type SDKCustomToolContent =
  | { type: "text"; text: string }
  | { type: "image"; data: string; mimeType?: string };
```

- 返回字符串，作为纯文本输出。
- 返回任意 JSON 值，会作为文本送回；对象还会填上 `structuredContent`。
- 返回信封以获得完全控制：混合文本和 base64 图像 `content`，设置 `isError: true` 来报告失败，或附上 `structuredContent` 供模型解析。从 `execute` 抛出也会作为工具错误回报给 agent。

## Hooks

Hooks 只能基于文件。没有可编程的 hook 回调。Hooks 是项目策略边界，不是按次运行的开关。

- **本地：** 把 `.cursor/hooks.json` 加到作为 `local.cwd` 传入的仓库，或添加 `~/.cursor/hooks.json` 作为用户级 Hooks。
- **云端：** 把 `.cursor/hooks.json` 及其脚本提交到 `cloud.repos` 里传入的仓库。SDK 创建的 Cloud Agent 会自动加载项目 Hooks。在 Enterprise 方案上，它们还会运行团队 Hooks 和由 Enterprise 管理的 Hooks。

配置格式见 [Hooks](https://cursor.com/docs/hooks.md)，云端行为见 [Cloud Agents 的 Hooks 支持](https://cursor.com/docs/cloud-agent.md#hooks-support)。

## 沙箱选项

本地 agent 默认以 `local.sandboxOptions.enabled: false` 运行。agent 可以读写工作目录、执行 shell 命令，并无限制地访问网络。无头 SDK 运行里没有人在回路中的批准流程，因此默认开启沙箱要么会静默挡住合法的工具调用，要么需要一个不适合脚本的回调。

启用沙箱后，SDK 会约束每一次 shell 工具调用以及由 shell 拉起的进程：

- **文件系统。** 写入限于工作目录（`local.cwd`）、临时目录，以及你在 [`sandbox.json`](https://cursor.com/docs/reference/sandbox.md) 里允许的路径。读取不限于工作区。
- **Shell。** 命令在平台沙箱里运行（Linux 上是 `bubblewrap`，macOS 上是 `seatbelt`，以及随包的 `@cursor/sdk-<os>-<arch>` 辅助程序）。特权操作会被拒绝。
- **网络。** 默认拒绝出站网络。要允许特定主机，在工作区放一个 `.cursor/sandbox.json`，列出允许的主机。如果存在，SDK 也会读取 `~/.cursor/sandbox.json` 上同一份按用户的策略。

```typescript
const agent = await Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2.5" },
  local: {
    cwd: process.cwd(),
    sandboxOptions: { enabled: true },
  },
});
```

如果宿主不支持沙箱（没有 `bubblewrap` 的较旧 Linux，或缺少辅助二进制文件），SDK 会抛出 `ConfigurationError`，消息里会点名缺少的依赖。关掉 `sandboxOptions.enabled`，或改在云端模式运行，即可恢复。

云端运行始终在隔离的 VM 里执行，因此 `sandboxOptions` 不适用。

## Auto-review

默认情况下，本地 agent 会不加限制地运行每一次工具调用，因为无头运行没有人来批准它们。设置 `local.autoReview: true`，把本地工具调用交给 [Auto-review](https://cursor.com/docs/agent/security/run-modes.md)。这是 IDE 用来根据安全性和每次调用与本次运行意图的匹配程度、允许或阻止 Shell、MCP 和 Fetch 调用的同一个分类器。

```typescript
const agent = await Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2.5" },
  local: {
    cwd: process.cwd(),
    autoReview: true,
  },
});
```

Auto-review 需要所连接的后端启用分类器；不可用时，运行会退回默认行为。因为无头运行里没有交互式批准，分类器阻止的调用会被拒绝而不是升级，agent 会拿到阻止原因，并可以换一种做法。用工作区里 `permissions.json` 的 `autoRun` 块来引导分类器，和在 IDE 里一样。格式见 [permissions.json](https://cursor.com/docs/reference/permissions.md)。

Auto-review 仅限本地 agent。云端运行已经在隔离的 VM 里执行。分类器是尽力而为的便利，不是安全边界；要严格控制，把它和 [`sandboxOptions`](https://cursor.com/docs/sdk/typescript.md#sandbox-options) 或一份[允许列表](https://cursor.com/docs/agent/security/run-modes.md) 一起用。

## 产物

列出并下载 agent 工作区中的文件。

```typescript
interface SDKArtifact {
  path: string;
  sizeBytes: number;
  updatedAt: string;
}
```

```typescript
const artifacts: SDKArtifact[] = await agent.listArtifacts();

for (const artifact of artifacts) {
  console.log(artifact.path, artifact.sizeBytes);
}

const buffer = await agent.downloadArtifact(artifacts[0].path);
```

产物支持取决于运行时。本地 SDK agent 目前不返回产物，并对 `downloadArtifact` 抛出异常。

## 资源管理

用完后始终释放 agent。最干净的写法是 `await using`：

```typescript
await using agent = await Agent.create({ /* ... */ });
// disposed automatically when the block exits
```

要显式释放：

```typescript
await agent[Symbol.asyncDispose]();
```

`agent.close()` 是开始释放、但不等待的文档化方式。`Symbol.asyncDispose` 也能用（`await using` 建立在它之上），但 `close()` 才是你在不使用 `await using` 语法的代码里应该走的路径。`agent.reload()` 会拾取文件系统配置的变化（hooks、项目 MCP、subagent），而不释放。

## Agent 生命周期

### 预热本地工作区

解析本地工作区（Rules、Skills、MCP 服务器、ignore 文件）是本地 agent 第一回合里最慢的部分，在大仓库上它可能占掉这一回合的大部分时间。默认情况下，这笔成本落在第一次 `send()` 里面。已经知道 agent 会在哪里运行的宿主，可以用 `prewarmLocalWorkspace()` 提前付掉它：

```typescript
import { createAgentPlatform } from "@cursor/sdk";

const platform = await createAgentPlatform();
const release = await platform.prewarmLocalWorkspace({
  apiKey: process.env.CURSOR_API_KEY!,
  local: { cwd: "/srv/checkout", settingSources: ["project"] },
});

// The first send() against this workspace starts immediately.

await release(); // on shutdown
```

传入你的 agent 将会使用的同一份 `AgentOptions`；预热只对工作区选项与之匹配的 send 有帮助。宿主关闭时调用返回的释放函数。

### 重新连上一个已有 agent

`Agent.resume(agentId)` 为一个已经存在的 agent 返回一个新句柄。运行时根据 ID 前缀自动检测（`bc-` 是云端，其他都是本地），对话状态从云端（云端）或本地检查点存储（本地）加载。这就是进程重启之后继续工作的方式，也是另一个 worker 接手别的进程启动的 agent 的方式。

```typescript
const agent = await Agent.resume("bc-abc123", {
  apiKey: process.env.CURSOR_API_KEY!,
});

const run = await agent.send("Apply the suggested fix");
const result = await run.wait();
```

如果你重新连上时这次运行已经在跑，`Agent.getRun(runId, { runtime: "cloud", agentId })`（或本地的等价写法）会返回一个 `Run`，你可以对其调用 `stream()`、`wait()` 或 `cancel()`。

### 对话上下文

本地 agent 把对话状态存在检查点存储里。默认是你主目录下磁盘上的 SQLite；可以用 [`local.store`](https://cursor.com/docs/sdk/typescript.md#local-agent-stores) 换成 JSONL 或自定义后端。每次 `agent.send()` 都会加载该 agent 的最新检查点并传给模型，因此后续消息看到的是上一次运行结束时的同一份上下文。存储能熬过进程重启，这意味着全新进程里的 `Agent.resume(agentId)` 会从上次停下的地方继续。

Cloud Agent 在服务器侧持久化状态。从任何地方重新连上都会得到同一段对话。

有几件事看起来像丢了上下文，其实不是：

- 新的 `Agent.create()` 总会以一个新的 `agentId` 开始一个全新的 agent。要继续已有对话，从第一次调用记下 `agent.agentId`，之后使用 `Agent.resume(agentId)`。
- `Agent.prompt()` 一次性完成创建、运行和释放。没有第二回合；这就是它的契约。
- 内联 `mcpServers` 不会跨 `Agent.resume()` 持久化，因为它们常常带有机密。在 resume 时再次传入它们，或使用基于文件的 MCP 配置。

### 分发器模式

分发器拥有一组 agent，并在工作到来时交给它们。形态很直接：维护一张 `agentId` 到长期存活的 `SDKAgent` 的映射，按某个键（用户、仓库、工单）路由进来的提示词，如果进程重启清掉了内存里的这张表，就从磁盘 `Agent.resume()`。

```typescript
import { Agent, type SDKAgent } from "@cursor/sdk";

const agents = new Map<string, SDKAgent>();

async function getAgent(key: string, savedId?: string): Promise<SDKAgent> {
  const existing = agents.get(key);
  if (existing) return existing;

  const agent = savedId
    ? await Agent.resume(savedId, {
        apiKey: process.env.CURSOR_API_KEY!,
      })
    : await Agent.create({
        apiKey: process.env.CURSOR_API_KEY!,
        model: { id: "composer-2.5" },
        local: { cwd: process.cwd() },
      });

  agents.set(key, agent);
  return agent;
}

async function handleMessage(key: string, prompt: string, savedId?: string) {
  const agent = await getAgent(key, savedId);
  const run = await agent.send(prompt);
  return run.wait();
}
```

云端 SSE 流会在运行开始后的一段窗口里保留积压，因此一个向许多订阅者推流的分发器可以从每个订阅者调用 `run.stream()`，而不会丢掉更早的事件。对于真正长时间运行的云端运行，分发器通常扇出到 `run.wait()`，并让订阅者在需要结构化转录时轮询 `run.conversation()`。

## 本地 agent 存储

本地 agent 把 agent 元数据、对话检查点、运行和运行事件持久化到磁盘，这样后续消息和 `Agent.resume()` 可以在进程重启后继续。默认情况下，SDK 通过 `node:sqlite` 使用磁盘上的 SQLite。当该模块不可用时，`Agent.create()` 会抛出 `ConfigurationError`，而不是回退。你可以用 `local.store` 换成另一个后端。

SDK 自带两种后端，也允许你带上自己的：

| 存储 | 导入 | 何时使用 |
| :----------------------- | :------------------- | :------------------------------------------------------------------------------------------------------------------- |
| `SqliteLocalAgentStore` | `@cursor/sdk/sqlite` | 工作区状态根目录下磁盘上的 SQLite。 |
| `JsonlLocalAgentStore` | `@cursor/sdk` | 你选定目录下的可移植换行分隔 JSON（NDJSON）文件。便于查看、复制和 diff。 |
| 自定义 `LocalAgentStore` | 你的代码 | 持久化到任何地方：内存、Redis、Postgres，或一个托管数据库。实现该接口，或组合子存储。 |

Cloud Agent 在服务器侧持久化，因此 `local.store` 只适用于本地 agent。

### JSONL 存储

`JsonlLocalAgentStore` 在你传入的目录下写四个 NDJSON 文件（`agents.ndjson`、`runs.ndjson`、`run_events.ndjson`、`checkpoints.ndjson`）。构造一个，并在 `local.store` 上传入它。

```typescript
import { Agent, JsonlLocalAgentStore } from "@cursor/sdk";

const store = new JsonlLocalAgentStore("/var/lib/cursor-agents");

const agent = await Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2.5" },
  local: { cwd: process.cwd(), store },
});
```

在 `Agent.resume()` 以及本地的列表和获取 API（`Agent.list`、`Agent.get`、`Agent.listRuns`、`Agent.getRun`）上传入同一个存储实例，这样它们读到的是同一份数据。

### 设置进程范围的默认值

为了避免把存储穿过每一次调用，用 [`Cursor.configure()`](https://cursor.com/docs/sdk/typescript.md#cursorconfigure) 设置一次默认值。当你传入按次调用的 `local.store` 时，它仍然优先。

```typescript
import { Cursor, JsonlLocalAgentStore } from "@cursor/sdk";

Cursor.configure({ local: { store: new JsonlLocalAgentStore("/var/lib/cursor-agents") } });

// Later calls use the configured store unless they pass their own.
const agent = await Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  model: { id: "composer-2.5" },
  local: { cwd: process.cwd() },
});
```

把 `store: null` 传给 `Cursor.configure({ local: { store: null } })`，以清掉先前的默认值，并回到 SDK 默认的本地存储选择。

### 自定义存储

要持久化到别处（共享的 Postgres、Redis，或测试用的内存映射），实现 `LocalAgentStore`。它是四个子存储，每个都是 SDK 会调用的一小块 CRUD 表面：

```typescript
interface LocalAgentStore {
  readonly agents: LocalAgentStoreAgents;         // agent metadata rows
  readonly checkpoints: LocalAgentStoreCheckpoints; // content-addressed conversation blobs
  readonly runs: LocalAgentStoreRuns;             // run rows
  readonly runEvents: LocalAgentStoreRunEvents;   // append-only run event log
}
```

直接实现该接口，或分别构建每个子存储，再用 `composeLocalAgentStore` 把它们组合起来：

```typescript
import { composeLocalAgentStore } from "@cursor/sdk";

const store = composeLocalAgentStore({
  agents: myAgentsTable,
  checkpoints: myCheckpointBlobs,
  runs: myRunsTable,
  runEvents: myRunEventLog,
});
```

子存储对应默认的 SQLite 表：`agents` 为每个 agent 保存一行（带一个精简的 `latestCheckpoint.rootBlobId` 指针），`checkpoints` 保存这些指针引用的、按内容寻址的对话 blob，`runs` 为每次运行保存一行，`runEvents` 是只追加的流日志。目录子存储用一个不透明的 `cursor` / `nextCursor` 分页；运行事件日志用排他的 `afterOffset` / `nextOffset` 续读。确切形态见导出的 `LocalAgentStore`、`LocalAgentDocument`、`LocalAgentRunDocument` 及相关类型。

## 配置参考

### AgentOptions

| 属性 | 类型 | 默认值 | 说明 |
| :---------------- | :-------------------------------- | :---------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `model`           | `ModelSelection`                  | 第一次本地 `send()` 之前必填；云端回退到服务器解析出的默认值 | 要使用的模型。见 [`ModelSelection`](https://cursor.com/docs/sdk/typescript.md#modelselection)。 |
| `apiKey`          | `string`                          | `CURSOR_API_KEY` 环境变量 | 用户 API 密钥或服务账号密钥。Team Admin 密钥尚不支持。 |
| `name`            | `string`                          | 自动生成 | 作为 `name` 在 `Agent.list()` / `Agent.get()` 里返回的人类可读 agent 名称。 |
| `local`           | `LocalAgentOptions`               |                                                                                           | 本地 agent 配置。见 [`LocalAgentOptions`](https://cursor.com/docs/sdk/typescript.md#localagentoptions)。 |
| `cloud`           | `CloudAgentOptions`               |                                                                                           | Cloud Agent 配置。 |
| `mcpServers`      | `Record<string, McpServerConfig>` |                                                                                           | 内联 MCP 服务器定义。 |
| `agents`          | `Record<string, AgentDefinition>` |                                                                                           | Subagent 定义。 |
| `tools`           | `ToolName[]`                      | 默认工具集 | [限制工具集](https://cursor.com/docs/sdk/typescript.md#restricting-the-toolset)：只把列出的内置工具提供给模型。`[]` 表示没有内置工具。仅本地 agent。 |
| `disallowedTools` | `ToolName[]`                      |                                                                                           | 从工具集中[移除工具](https://cursor.com/docs/sdk/typescript.md#restricting-the-toolset)；其余工具仍然可用。与 `tools` 一起使用时，拒绝优先。仅本地 agent。 |
| `systemPrompt`    | `string`                          | Cursor 的内置提示词 | [替换系统提示词](https://cursor.com/docs/sdk/typescript.md#replacing-the-system-prompt)，用于主 agent 循环。必须非空。仅本地 agent，并且不会跨 `Agent.resume()` 持久化。 |
| `agentId`         | `string`                          | 自动生成 | 持久的 agent ID。传入它，以便在多次调用之间保持稳定 ID。 |
| `idempotencyKey`  | `string`                          | 云端自动生成 | 可选的、由客户端生成的幂等键。 |
| `mode`            | `"agent" \| "plan"`               | `"agent"`                                                                                 | agent 第一次运行的初始对话模式。见[对话模式](https://cursor.com/docs/sdk/typescript.md#conversation-mode)。 |

### LocalAgentOptions

本地 agent 的配置，作为 `local` 传给 `Agent.create()`。也作为独立类型导出，供 `Partial<LocalAgentOptions>` 使用。

| 属性 | 类型 | 默认值 | 说明 |
| :------------------- | :------------------------------ | :------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `cwd`                | `string`                        |                      | 默认 shell 的主工作目录，也用于 agent 存储的范围。 |
| `dirs`               | `string[]`                      |                      | 多根设置下的额外工作区文件夹。与 `cwd` 合并（去掉重复），从而从每条路径加载 Rules、Skills 和工作区上下文。 |
| `settingSources`     | `SettingSource[]`               |                      | 要加载的环境设置层：`"project"`、`"user"`、`"team"`、`"mdm"`、`"plugins"` 或 `"all"`。 |
| `sandboxOptions`     | `{ enabled: boolean }`          | `{ enabled: false }` | [沙箱](https://cursor.com/docs/sdk/typescript.md#sandbox-options)配置。 |
| `autoReview`         | `boolean`                       | `false`              | 把本地工具调用交给 [Auto-review](https://cursor.com/docs/sdk/typescript.md#auto-review)。 |
| `customTools`        | `Record<string, SDKCustomTool>` |                      | 作为 `custom-user-tools` MCP 服务器暴露的[自定义工具](https://cursor.com/docs/sdk/typescript.md#custom-tools)。 |
| `store`              | `LocalAgentStore`               | SDK 默认存储 | 支撑持久化的[本地 agent 存储](https://cursor.com/docs/sdk/typescript.md#local-agent-stores)。 |
| `enableAgentRetries` | `boolean`                       | `true`               | 为本地 agent 运行启用传输和停滞的自动重试。设为 `false` 可在第一次失败时暴露传输错误。 |

### CloudAgentOptions

| 属性 | 类型 | 默认值 | 说明 |
| :---------------------- | :---------------------------------------------------------------------------------------------------------- | :----------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `env`                   | `{ type: "cloud"; name?: string } \| { type: "pool"; name?: string } \| { type: "machine"; name?: string }` | `{ type: "cloud" }`                                    | 执行环境目标。`cloud` 使用 Cursor 托管的 VM；设置 `name` 以使用已保存的 Cursor 托管环境。`pool` 和 `machine` 路由到你自己运行的自托管 worker。省略 `repos` 并把 `env` 留在默认值，即得到一个工作区为空的无仓库 agent。具名的 Cursor 托管环境与显式的 `repos` 互斥。 |
| `repos`                 | `Array<{ url: string; startingRef?: string; prUrl?: string }>`                                              |                                                        | 要克隆进 VM 的仓库。单仓库 agent 传一个条目，多仓库 agent 最多 20 个。自托管目标只接受一个条目，除非 `env.name` 指向一个[任意仓库池](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#any-repo-pools)。省略或传入 `[]`，即得到一个[无仓库 agent](https://cursor.com/docs/sdk/typescript.md#no-repo-cloud-agents)。对于 Cursor 托管环境，与具名的 `env.name` 互斥。传入 `prUrl` 把 agent 附到一个已有 PR。 |
| `workOnCurrentBranch`   | `boolean`                                                                                                   | `false`                                                | 把提交推送到已有分支，而不是新分支。 |
| `autoCreatePR`          | `boolean`                                                                                                   | `false`                                                | 运行结束时打开一个 PR。 |
| `openAsCursorGithubApp` | `boolean`                                                                                                   | `true` 用于服务账号密钥，`false` 用于用户密钥 | 以 Cursor GitHub App 的身份打开 PR，而不是以 API 密钥所有者的身份。解析后的值会在 create、get 和 list 上回显。 |
| `skipReviewerRequest`   | `boolean`                                                                                                   | `false`                                                | 跳过把调用用户请求为该 PR 的审查者。 |
| `envVars`               | `Record<string, string>`                                                                                    |                                                        | Cloud Agent 的会话范围环境变量。 |
| `metadata`              | `Record<string, string>`                                                                                    |                                                        | 持久保存在 Cloud Agent 上、由调用方拥有的字符串标签。见 [Agent 元数据](https://cursor.com/docs/sdk/typescript.md#agent-metadata)。 |

### AgentDefinition

| 属性 | 类型 | 默认值 | 说明 |
| :------------ | :------------------------------------------------- | :---------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `description` | `string`                                           | *必填* | 何时使用这个 Subagent。展示给父 agent，让它知道何时启动。 |
| `prompt`      | `string`                                           | *必填* | 该 Subagent 的系统提示词。 |
| `model`       | `ModelSelection \| "inherit"`                      | `"inherit"` | 模型覆盖。传入 `"inherit"` 以使用父级的选择。 |
| `mcpServers`  | `Array<string \| Record<string, McpServerConfig>>` |             | 为前向兼容而接受。字符串引用会被忽略，内联配置会抛出 `ConfigurationError`；Subagent 继承父级的 MCP 服务器。 |

### ModelSelection

```typescript
interface ModelSelection {
  id: string;
  params?: ModelParameterValue[];
}

interface ModelParameterValue {
  id: string;
  value: string;
}
```

`id` 是模型标识（例如 `"composer-2.5"` 或 `"auto-smart"`）。`params` 携带按模型区分的参数，例如推理强度或 Router 的 `optimize_for`。用 [`Cursor.models.list()`](https://cursor.com/docs/sdk/typescript.md#cursormodelslist) 查看你的账号可用的有效 id、参数定义和预设变体。Router 的选择契约见 [Cursor Router](https://cursor.com/docs/sdk/typescript.md#cursor-router)。

### McpServerConfig

```typescript
type McpServerConfig =
  // stdio
  | {
      type?: "stdio";
      command: string;
      args?: string[];
      env?: Record<string, string>;
      cwd?: string;       // local only; cloud rejects this field
    }
  // HTTP / SSE
  | {
      type?: "http" | "sse";
      url: string;
      headers?: Record<string, string>;   // passed through; Authorization here works
      auth?: {
        CLIENT_ID: string;
        CLIENT_SECRET?: string;
        scopes?: string[];
      };
    };
```

对于在云端运行的 HTTP 服务器，`headers` 和 `auth` 由 Cursor 的后端处理。敏感字段在 VM 看到之前会被脱敏。对于云端的 stdio 服务器，`env` 值会传入 VM（把它们当作任何运行时机密）。

### SDKUserMessage

```typescript
interface SDKUserMessage {
  text: string;
  images?: SDKImage[];
}
```

`agent.send()` 消息参数的结构化形式。用它连同文本一起发送图像。

### SDKImage

```typescript
type SDKImage =
  | { url: string; dimension?: SDKImageDimension }
  | { data: string; mimeType: string; dimension?: SDKImageDimension };

interface SDKImageDimension {
  width: number;
  height: number;
}
```

传入远程 `url`，或传入 base64 `data` 并带上 `mimeType`。

### SettingSource

```typescript
type SettingSource =
  | "project"
  | "user"
  | "team"
  | "mdm"
  | "plugins"
  | "all";
```

控制本地 agent 加载哪些磁盘上的设置层。Cloud Agent 始终加载 `project` / `team` / `plugins`，并忽略这个字段。

| 值 | 来源 |
| :---------- | :-------------------------------------- |
| `"project"` | 工作区中的 `.cursor/` |
| `"user"` | `~/.cursor/` |
| `"team"` | 从仪表板同步的 Team settings |
| `"mdm"` | 由 MDM 管理的 Enterprise 设置 |
| `"plugins"` | Plugin 提供的设置 |
| `"all"` | 以上全部的简写 |

### ListResult

```typescript
interface ListResult<T> {
  items: T[];
  nextCursor?: string;
}
```

由 `Agent.list()` 和 `Agent.listRuns()` 返回。没有更多页时 `nextCursor` 不存在。

## 错误

所有 SDK 错误都扩展 `CursorSdkError`（为了向后兼容，也重新导出为 `CursorAgentError`）。用 `isRetryable` 驱动重试逻辑，用 `code` / `status` / `requestId` 做诊断。

```typescript
class CursorSdkError extends Error {
  readonly isRetryable: boolean;
  readonly code?: string;       // stable SDK / backend code
  readonly status?: number;     // HTTP status if available
  readonly cause?: unknown;     // wrapped underlying error
  readonly endpoint?: string;
  readonly requestId?: string;
  readonly operation?: string;  // SDK operation that produced the error
}
```

| 错误类 | 典型消息 | 可能原因 | 建议的修复 |
| :----------------------------- | :------------------------------------------------------------ | :---------------------------------------- | :-- |
| `AuthenticationError` | "Invalid API key" | 缺少或错误的 `CURSOR_API_KEY`、过期的 token，或管理员禁用了该密钥。 | 从 [API Keys](https://cursor.com/dashboard/api)（用户）或 [Team settings](https://cursor.com/dashboard/team-settings)（服务账号）生成一个新密钥。确认该密钥有此操作的权限。 |
| `RateLimitError` | "Rate limit exceeded" 或 "Usage limit exceeded" | 突发限制或每月用量上限。 | 用指数延迟退避（SDK 对暂时情况报告 `isRetryable: true`）。每月上限则提高方案的[用量限制](https://cursor.com/help/models-and-usage/usage-limits.md)。 |
| `ConfigurationError` | "Bad model name"、"API key not supported"、"File unsupported" | 无效的 `model.id`、缺少必需的 `params`、工具调用里有不支持的文件，或管理员策略挡住了请求。 | 调用 `Cursor.models.list()` 确认 id 和参数。检查仓库 / 文件路径是否存在。 |
| `AgentBusyError` | "Agent is busy" | 同一个 Cloud Agent 已有一次处于 `CREATING` 或 `RUNNING` 状态的运行时发送后续消息。 | 等待活动运行结束，取消它，或在再次发送之前轮询 `Agent.listRuns()`。 |
| `IntegrationNotConnectedError` | "\[provider] integration is not connected" | 为一个 SCM 提供商尚未连接到你的 Cursor 团队的仓库创建 Cloud Agent。 | 打开 `error.helpUrl` 重新连接该提供商，然后重试。 |
| `NetworkError` | "Service unavailable"、"Timeout" | 暂时的后端问题、网络分区，或超过了截止时间。 | 带退避重试。如果要提交支持工单，查看 `error.requestId`。 |
| `UnsupportedRunOperationError` | "Operation "stream" is not supported on this runtime" | 调用了当前运行时无法满足的 `Run` 方法（例如对一次已经结束、重新取回的本地运行做流式传输）。 | 先用 `run.supports(operation)` / `run.unsupportedReason(operation)` 做保护。 |
| `AgentNotFoundError` | "Agent not found" | 请求的 agent 不存在，或在解析出的本地工作区下不可见。 | 检查 agent ID、`cwd` 和 `local.store`。 |
| `UnknownAgentError` | 服务器定义的消息 | 未归类的后端或运行时错误。 | 查看 `error.code` 和 `error.cause` 里的底层细节。 |

### 检查 error.helpUrl

有些错误带有一键解决链接。最常见的是 `IntegrationNotConnectedError`，但更多错误类型以后也可能加上 `helpUrl`。捕获错误时，如果存在 `error.helpUrl` 就把它记下来，并呈现给用户。

### IntegrationNotConnectedError

```typescript
class IntegrationNotConnectedError extends ConfigurationError {
  readonly provider: string;   // e.g. "github", "gitlab", "azure-devops"
  readonly helpUrl: string;    // dashboard link to reconnect
}
```

默认错误消息不包含 `helpUrl`，所以请显式把它记下来：

```typescript
import { Agent, IntegrationNotConnectedError } from "@cursor/sdk";

try {
  await Agent.create({
    apiKey: process.env.CURSOR_API_KEY!,
    cloud: {
      repos: [{ url: "https://github.com/your-org/private-repo" }],
    },
  });
} catch (err) {
  if (err instanceof IntegrationNotConnectedError) {
    console.error(err.provider, err.helpUrl);
  }
}
```

### AgentBusyError

```typescript
class AgentBusyError extends CursorAgentError {}
```

`isRetryable` 为 `false`（针对 `agent_busy`）。立刻重试会一直失败，直到活动运行到达终态，或你取消它。其他 `409` 响应，例如 `agent_archived`，会改为抛出 `ConfigurationError`。

等待活动运行结束，用 `run.cancel()` 取消它，或在再次发送之前轮询 `Agent.listRuns()`：

```typescript
import { Agent, AgentBusyError } from "@cursor/sdk";

const agent = await Agent.resume("bc-00000000-0000-0000-0000-000000000001");

try {
  await agent.send({ text: "Also add tests for the auth middleware." });
} catch (err) {
  if (err instanceof AgentBusyError) {
    const runs = await Agent.listRuns(agent.agentId, { runtime: "cloud", limit: 1 });
    const active = runs.items[0];
    if (active?.status === "running") {
      await active.cancel();
    }
    await agent.send({ text: "Also add tests for the auth middleware." });
    return;
  }
  throw err;
}
```

本地 agent 不会返回 `agent_busy`。使用 `send({ local: { force: true } })`，可以在开始新的一次运行之前让卡住的本地运行过期。

### UnsupportedRunOperationError

```typescript
class UnsupportedRunOperationError extends ConfigurationError {
  readonly operation: RunOperation;
}
```

当某个 `Run` 操作在当前运行时不可用时抛出。调用之前用 `run.supports(operation)` 和 `run.unsupportedReason(operation)` 检查。

## 已知限制

- 内联 `mcpServers` 不会跨 `Agent.resume()` 持久化。如果需要，在 resume 时再次传入它们。
- 自定义工具（`local.customTools`）、Auto-review（`local.autoReview`）、自定义存储（`local.store`）、工具集限制（`tools`、`disallowedTools`）和 `systemPrompt` 仅限本地 agent。Cloud Agent 会拒绝 `local.customTools`（位于 `send()`），并在服务器侧持久化。
- `tools`、`disallowedTools` 和 `systemPrompt` 不会持久保存在 agent 上。要保留它们，在 `Agent.resume()` 上再次传入。
- 本地 agent 尚未实现产物下载（`agent.listArtifacts()` 返回空列表，`agent.downloadArtifact()` 会抛出异常）。
- `local.settingSources`（以及它控制的基于文件的 MCP / Subagent 路径）不适用于 Cloud Agent。云端始终加载 `project` / `team` / `plugins`。
- Hooks 只能基于文件（`.cursor/hooks.json`）。没有可编程回调。
- SDK 不会从本地 Cursor 应用安装自动发现凭据。显式设置 `CURSOR_API_KEY`（或传入 `apiKey`），或用 [`Cursor.auth.login()`](https://cursor.com/docs/sdk/typescript.md#cursorauth) 签发一个密钥。
- 本地模式需要 Node.js 22.13 或更高版本，以及平台沙箱辅助程序支持。默认存储需要 `node:sqlite`；当它不可用时，`Agent.create()` 会抛出 `ConfigurationError`，直到你配置 `JsonlLocalAgentStore` 或其他存储。

---

## 站点地图

[所有文档页面概览](/llms.txt)
