> 原文：https://cursor.com/docs/sdk/python

# Cursor Python SDK

`cursor-sdk` 包让你从自己的 Python 代码调用 Cursor 的 agent。在 Cursor IDE、CLI 和网页应用里运行的同一个 agent，可以从 Python 用同步和异步客户端、带类型的 dataclass，以及对流和分页的普通迭代来编写脚本。在 Cursor 里运行 `/sdk` Skill 即可开始。

REST API 见 [Cloud Agents API](https://cursor.com/docs/cloud-agent/api/endpoints.md)。其他语言见 [SDK Bridge](https://cursor.com/docs/sdk/bridge.md)。

## 概览

SDK 把本地和云端运行时包在同一套接口后面。无论 agent 在哪里运行，你写的代码都一样。

| 运行时 | 作用 | 何时使用 |
| :------------------------ | :-------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------- |
| **本地** | 针对磁盘上的本地文件运行 agent。 | 针对工作树的开发脚本和 CI 检查。 |
| **云端（Cursor 托管）** | 在隔离的 VM 中运行，并把你的仓库克隆进去。VM 由 Cursor 运行。 | 调用方没有该仓库、你希望多个 agent 并行，或者运行需要在调用方断开后继续时。 |

通过传入 `local` 或 `cloud` 给 `Agent.create()` 来设置运行时。

## 认证

创建 agent 之前，设置 `CURSOR_API_KEY` 或传入 `api_key`。

SDK 对本地和云端运行都接受用户 API 密钥和服务账号 API 密钥。Team Admin API 密钥尚不支持。

- **用户 API 密钥**，来自 [Cursor Dashboard -> API Keys](https://cursor.com/dashboard/api)
- **服务账号 API 密钥**，来自 [Team settings](https://cursor.com/dashboard/team-settings)。参见[服务账号](https://cursor.com/docs/account/enterprise/service-accounts.md)

```bash
export CURSOR_API_KEY="your-key"
```

## 用量与计费

SDK 运行遵循与 IDE 和 Cloud Agents 中的运行相同的定价、请求池和 Privacy Mode 规则。花费会显示在你团队的[用量仪表板](https://cursor.com/dashboard/usage)的 SDK 标签下。

要在代码里读取每次运行的 token 计数，见[Token 用量](https://cursor.com/docs/sdk/python.md#token-usage)。要获取某次 agent 运行的计费用量和美元费用，见 [`agent.get_usage()`](https://cursor.com/docs/sdk/python.md#agentget_usage)。

## 核心概念

| 概念 | 说明 |
| :--------------- | :------------------------------------------------------------------------------------------------------------------------------- |
| **Agent** | 持久句柄，保存对话状态、工作区配置、模型选择和设置。可以跨多次提示词存续。 |
| **Run** | 一次提示词提交。拥有自己的流、状态、结果、对话和取消。 |
| **SDKMessage** | 运行期间产出的有类型流消息。本地和云端运行时的形态相同。 |
| **CursorClient** | 显式客户端，用于生命周期控制、自定义 HTTP 选项，或在一个进程里使用多个工作区。`Client` 是别名。 |
| **AsyncClient** | 异步对应客户端。所有异步操作都需要它。 |

## 安装

```bash
pip install cursor-sdk
```

需要 Python 3.10 或更高版本。

## 快速开始

```python
import os

from cursor_sdk import Agent, LocalAgentOptions

with Agent.create(
    model="composer-2.5",
    api_key="crsr_key",
    local=LocalAgentOptions(cwd=os.getcwd()),
) as agent:
    print(agent.send("Summarize what this repository does").text())
```

[流事件](https://cursor.com/docs/sdk/python.md#stream-events)说明如何提取助手文本、处理工具调用，以及读取运行状态。一次性提示词（创建、运行、结束）见 [`Agent.prompt()`](https://cursor.com/docs/sdk/python.md#agentprompt)。

### 云端快速开始

Python SDK 原生支持 Cursor 的 Cloud Agents。你可以列出已连接的仓库，针对其中一个启动 agent，等待运行，并查看最终结果。

```python
from cursor_sdk import Agent, CloudAgentOptions, CloudRepository

with Agent.create(
    model="composer-2.5",
    api_key="crsr_key",
    cloud=CloudAgentOptions(
        repos=[CloudRepository(url="https://github.com/your-org/your-repo", starting_ref="main")],
        auto_create_pr=True,
    ),
) as agent:
    print(agent.send("Add structured logging to the auth middleware").text())
```

SDK 启动的 Cloud Agent 会被默认的 agent 列表过滤掉。要在 Cursor Web 或 Agents Window 里查看它们，点击 **Filter > Source > SDK**。

## 异步用法

异步客户端镜像了同步接口，推荐用于服务器、bot 以及并发的 agent 编排。`AsyncAgent`、`AsyncClient`、`AsyncRun` 和 `AsyncCursor` 从 `cursor_sdk` 和 `cursor_sdk.asyncio` 两处导出。

```python
import asyncio
import os

from cursor_sdk import AsyncClient, LocalAgentOptions

async def main():
    async with await AsyncClient.launch_bridge(workspace=os.getcwd()) as client:
        async with await client.agents.create(
            model="composer-2.5",
            api_key="crsr_key",
            local=LocalAgentOptions(cwd=os.getcwd()),
        ) as agent:
            run = await agent.send("Summarize what this repository does")
            print(await run.text())

asyncio.run(main())
```

没有全局的异步默认客户端。请显式实例化 `AsyncClient`，或把 `AsyncClient.launch_bridge(...)` 用作异步上下文管理器，这样每个事件循环都拥有自己的客户端。不要在同一条代码路径里混用同步和异步客户端。

直接调用 `AsyncAgent` 的类方法时必须传入 `client=`。请使用
`await client.agents.create(...)` 或
`await AsyncAgent.create(..., client=client)`。

| 同步 | 异步 |
| :------------------------ | :---------------------------------- |
| `CursorClient` / `Client` | `AsyncClient` / `AsyncCursorClient` |
| `Agent` | `AsyncAgent` |
| `Run` | `AsyncRun` |
| `Cursor` | `AsyncCursor` |
| `ListResult` | `AsyncListResult` |
| `DefaultHttpxClient` | `DefaultAsyncHttpxClient` |

## 创建 agent

`Agent.create()` 会校验选项并立刻返回一个句柄。传入 `local` 或 `cloud` 来选择运行时。

```python
from cursor_sdk import Agent, CloudAgentOptions, CloudRepository, LocalAgentOptions

agent = Agent.create(
    model="composer-2.5",
    local=LocalAgentOptions(cwd="."),
)

cloud_agent = Agent.create(
    model="composer-2.5",
    cloud=CloudAgentOptions(
        repos=[CloudRepository(url="https://github.com/your-org/your-repo", starting_ref="main")],
        auto_create_pr=True,
    ),
)
```

`agent.agent_id` 会立刻被填上。本地 agent 得到 `agent-<uuid>` 形式的 ID；云端 agent 得到 `bc-<uuid>` 形式的 ID。`agent.model` 是带类型的 `ModelSelection`，因此 `agent.model.id` 和 `agent.model.params` 可以直接使用。

SDK 启动的 Cloud Agent 会被默认的 agent 列表过滤掉。要在 Cursor Web 或一个 Cursor agent 窗口里查看它们，点击 **Filter > Source > SDK**。

### 无仓库的 Cloud Agent

Cloud Agent 可以在没有仓库的空 VM 上运行。传入 `cloud` 并带一个空的 `repos` 列表，或者完全省略 `repos`。省略 `cloud` 则会改选本地运行时。

```python
from cursor_sdk import Agent, CloudAgentOptions

with Agent.create(cloud=CloudAgentOptions(repos=[])) as agent:
    run = agent.send("Research the top 3 Python testing frameworks and summarize.")
    print(run.wait().result)
```

无仓库 agent 必须先为你的账号或团队启用。仓库范围的 API 密钥无法创建它们；请改用不受限的服务账号密钥或用户 API 密钥。

### 会话环境变量

对于 Cloud Agent，当运行需要短期凭据或其他只应随该 agent 存在的值时，传入 `env_vars`。

```python
import os

agent = Agent.create(
    model="composer-2.5",
    cloud=CloudAgentOptions(
        repos=[CloudRepository(url="https://github.com/your-org/your-repo")],
        env_vars={
            "STAGING_API_TOKEN": os.environ["STAGING_API_TOKEN"],
        },
    ),
)
```

这些值会静态加密，注入到 Cloud Agent 的 shell 中，并随该 agent 一起删除。`env_vars` 不能与调用方提供的 `agent_id` 一起使用；请省略 `agent_id`，并从 `agent.agent_id` 读取服务器签发的 ID。变量名不能以 `CURSOR_` 开头。

只应在单次运行期间存在的值，改为在 `agent.send()` 上传入。见[每次运行的环境变量](https://cursor.com/docs/sdk/python.md#per-run-environment-variables)。

### Agent 元数据

创建 Cloud Agent 时附上你自己的标识。元数据可以把 agent 关联到你系统中的用户、租户、工作流或工单，并在 `SDKAgentInfo.metadata` 上读回，来源是 `client.agents.get()` 和 `client.agents.list()`。这些标签不是 VM 内的 [agent 元数据](https://cursor.com/docs/cloud-agent/metadata.md) API。那个 API 从 VM 内部暴露当前运行的 id、所有者、回合和工作区。

```python
from cursor_sdk import Agent, CloudAgentOptions, CloudRepository

with Agent.create(
    model="composer-2.5",
    cloud=CloudAgentOptions(
        repos=[CloudRepository(url="https://github.com/your-org/your-repo")],
        metadata={
            "end_user_id": "user-123",
            "ticket_id": "ENG-456",
        },
    ),
) as agent:
    print(agent.agent_id)
```

元数据在创建 Cloud Agent 时可用。你最多可以附加 50 个键值对。键必须非空，且不超过 255 个字符。值必须是不超过 4096 字节的字符串。允许空字符串值，空映射视为没有元数据。

如果该 API 密钥所属账号没有启用元数据，用非空映射创建 agent 会返回 `403 feature_unavailable`。

### 模型参数

用 `ModelSelection.params` 传入按模型区分的选项，例如推理强度，或 Cursor Router 的 `optimize_for`。参数 ID 和取值因模型而异。用 [`Cursor.models.list()`](https://cursor.com/docs/sdk/python.md#the-cursor-namespace) 查看你的账号支持的参数和预设变体。

```python
from cursor_sdk import Agent, LocalAgentOptions, ModelParameterValue, ModelSelection

agent = Agent.create(
    model=ModelSelection(
        id="composer-2.5",
        params=[ModelParameterValue(id="fast", value="true")],
    ),
    local=LocalAgentOptions(cwd="."),
)
```

用 [`Cursor.models.list()`](https://cursor.com/docs/sdk/python.md#the-cursor-namespace) 查看给定模型的参数 ID 和预设变体。`auto-smart` 的选择契约见 [Cursor Router](https://cursor.com/docs/sdk/python.md#cursor-router)。

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

```python
import os

from cursor_sdk import Agent, LocalAgentOptions, ModelParameterValue, ModelSelection

with Agent.create(
    model=ModelSelection(
        id="auto-smart",
        params=[ModelParameterValue(id="optimize_for", value="balanced")],
    ),
    local=LocalAgentOptions(cwd=os.getcwd()),
) as agent:
    run = agent.send("Find and fix the failing authentication test")
    result = run.wait()

    print(result.status)
```

始终传入 `optimize_for`。不要省略它，也不要发送旧的 `default` 值；通过目录发现才是受支持的契约。

#### 在模型目录中发现 Router

`Cursor.models.list()` 返回当前 API 密钥所属账号和团队可用的模型、参数定义和预设变体。Router 可用时，Cursor Router 会显示为 `auto-smart`。团队管理员可以禁用 Router，或限制成员可以选择哪些优化模式。

在把某个选择硬编码之前，把目录当作事实来源：

```python
from cursor_sdk import Cursor, ModelParameterValue, ModelSelection

models = Cursor.models.list()
router = next((model for model in models if model.id == "auto-smart"), None)
optimize_for = next(
    (
        parameter
        for parameter in (router.parameters if router else [])
        if parameter.id == "optimize_for"
    ),
    None,
)

if router is None or optimize_for is None:
    raise RuntimeError(
        "Cursor Router is not available for this API key. "
        "Verify that Router is enabled for the key's team."
    )

requested_mode = "balanced"
allowed_values = {entry.value for entry in optimize_for.values}

if requested_mode not in allowed_values:
    raise RuntimeError(
        f'Router mode "{requested_mode}" is not enabled for this team.'
    )

model = ModelSelection(
    id=router.id,
    params=[ModelParameterValue(id=optimize_for.id, value=requested_mode)],
)
```

#### 按次运行切换模式

在 `agent.send()` 上覆盖模型，以改变某次运行的 Router 模式：

```python
from cursor_sdk import ModelParameterValue, ModelSelection, SendOptions

run = agent.send(
    "Handle this complex migration",
    SendOptions(
        model=ModelSelection(
            id="auto-smart",
            params=[ModelParameterValue(id="optimize_for", value="intelligence")],
        ),
    ),
)
```

按次运行的模型覆盖会保持生效。之后没有覆盖的 send 会继续使用新的选择。见[按次运行的模型覆盖](https://cursor.com/docs/sdk/python.md#per-run-model-override)。

#### 模型 id：`auto-smart`、`auto` 和 `default`

| 选择 | 含义 |
| :-------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| `auto-smart` 加 `optimize_for` | Cursor Router。想要 Cost、Balance 或 Intelligence 时用这个。 |
| `ModelSelection(id="auto")` | 当目录里缺少某个具体模型时，由服务器选择的 Auto 回退。需要明确的 Router 模式时，优先使用 `auto-smart`。 |
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

### 原始字典

应用代码优先使用带类型的 dataclass，因为 IDE 自动补全和类型检查会更好用。SDK 也接受普通字典，方便短脚本或外部提供的 JSON。snake_case 键会被规范化。

```python
from cursor_sdk import Agent

with Agent.create(
    {
        "api_key": "crsr_key",
        "model": {"id": "composer-2.5"},
        "local": {"cwd": "."},
    }
) as agent:
    ...
```

## Agent

由 `Agent.create()`、`Agent.resume()`、`client.agents.create()` 和 `client.agents.resume()` 返回的句柄。

```python
class Agent:
    agent_id: str
    model: ModelSelection | None
    client: CursorClient

    def send(
        self,
        message: str | Mapping[str, Any] | UserMessage,
        options: SendOptions | Mapping[str, Any] | None = None,
        *,
        idempotency_key: str | None = None,
    ) -> Run: ...

    def reload(self) -> None: ...
    def close(self) -> None: ...

    def list_messages(
        self, options: Mapping[str, Any] | None = None
    ) -> list[AgentMessage]: ...
    def list_artifacts(self) -> list[SDKArtifact]: ...
    def download_artifact(self, path: str) -> bytes: ...
    def get_usage(self, *, run_id: str | None = None) -> AgentUsage: ...

    def archive(self, options: Mapping[str, Any] | None = None) -> None: ...
    def unarchive(self, options: Mapping[str, Any] | None = None) -> None: ...
    def delete(self, options: Mapping[str, Any] | None = None) -> None: ...
```

| 成员 | 说明 |
| :--------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `agent_id` | 稳定的 agent 标识。本地为 `agent-<uuid>`，云端为 `bc-<uuid>`。 |
| `model` | 当前带类型的模型选择。在带模型覆盖的 send 成功后更新。 |
| `send` | 用给定的提示词开始一次新运行。返回一个 `Run` 句柄。 |
| `reload` | 重新读取文件系统配置（hooks、项目 MCP、subagent），而不释放资源。 |
| `close` | 关闭 agent 并释放资源。 |
| `list_messages` | 列出该 agent 的消息历史。 |
| `list_artifacts` | 列出 agent 产出的文件（仅云端；本地返回空）。 |
| `download_artifact` | 按路径下载文件（仅云端；本地会抛出异常）。 |
| `get_usage` | 获取该 agent 的计费 token 用量和美元费用。本地 agent 需要按账号启用用量访问；见 [`agent.get_usage()`](https://cursor.com/docs/sdk/python.md#agentget_usage)。 |
| `archive` / `unarchive` / `delete` | 管理 Cloud Agent 的生命周期。 |

用上下文管理器做自动清理：

```python
with Agent.create(model="composer-2.5", local=LocalAgentOptions(cwd=".")) as agent:
    print(agent.send("Explain this repository").text())
```

使用同步的 `Agent.*` 或 `Cursor.*` 辅助方法且不传入 `client=` 时，SDK 会启动或复用一个模块级的默认客户端。它会在进程退出时自动关闭，你也可以显式关闭它：

```python
from cursor_sdk import close_default_client

close_default_client()
```

### Agent.prompt()

```python
Agent.prompt(
    message: str | Mapping[str, Any] | UserMessage,
    options: AgentOptions | Mapping[str, Any] | None = None,
    *,
    client: CursorClient | None = None,
) -> RunResult
```

一次性便捷方法：创建 agent，发送一条提示词，等待运行结束，然后释放。

```python
from cursor_sdk import Agent, AgentOptions, LocalAgentOptions

result = Agent.prompt(
    "What does the auth middleware do?",
    AgentOptions(model="composer-2.5", local=LocalAgentOptions(cwd=".")),
)
print(result.result)
```

异步对应写法（假定你已经打开了一个 `AsyncClient`）：

```python
from cursor_sdk import AgentOptions, AsyncAgent, LocalAgentOptions

result = await AsyncAgent.prompt(
    "What does the auth middleware do?",
    AgentOptions(model="composer-2.5", local=LocalAgentOptions(cwd=".")),
    client=client,
)
```

## CursorClient

当你需要显式的生命周期控制、自定义 bridge 端点、自定义 HTTP 选项，或在一个进程里使用多个工作区时，使用 `CursorClient`。`Client` 仍可作为别名使用。

```python
from cursor_sdk import CursorClient, LocalAgentOptions

with CursorClient.launch_bridge(workspace=".") as client:
    with client.agents.create(
        model="composer-2.5",
        api_key="crsr_key",
        local=LocalAgentOptions(cwd="."),
    ) as agent:
        print(agent.send("Summarize what this repository does").text())
```

如果你的平台已经在运行 `cursor-sdk-bridge`，用 `CursorClient.connect(base_url, auth_token)` 连上它，而不是再启动一个。`client.ping()` 和 `client.get_version()` 报告 bridge 的健康状况和版本。`AsyncClient.connect(...)` 与同步形式对应。

```python
from cursor_sdk import CursorClient

with CursorClient.connect("http://127.0.0.1:43210", auth_token=bridge_token) as client:
    print(client.ping(), client.get_version()["bridgeVersion"])
```

### 资源

显式客户端暴露资源命名空间：

| 资源 | 同步方法示例 | 异步方法示例 |
| :------------- | :------------------------------------------------------------------------------- | :----------------------------------------------------------------- |
| `agents` | `client.agents.create(...)`、`client.agents.list(...)`、`client.agents.get(...)` | `await client.agents.create(...)`、`await client.agents.list(...)` |
| `models` | `client.models.list()` | `await client.models.list()` |
| `repositories` | `client.repositories.list()` | `await client.repositories.list()` |

`client.create_agent(...)` 和 `client.list_agents(...)` 这类顶层方法仍然可用，但应用代码优先使用资源命名空间这种形态。

### 自定义 HTTP 客户端

同步和异步客户端都接受自定义的 `httpx` 客户端，用于代理、传输以及其他高级 HTTP 配置：

```python
from cursor_sdk import CursorClient, DefaultHttpxClient

with CursorClient.launch_bridge(
    workspace=".",
    http_client=DefaultHttpxClient(proxy="http://proxy.example.com"),
) as client:
    ...
```

```python
from cursor_sdk import AsyncClient, DefaultAsyncHttpxClient

async with await AsyncClient.launch_bridge(
    workspace=".",
    http_client=DefaultAsyncHttpxClient(proxy="http://proxy.example.com"),
) as client:
    ...
```

`DefaultHttpxClient` 和 `DefaultAsyncHttpxClient` 保留 SDK 的默认超时和重定向行为。普通的 `httpx.Client` 和 `httpx.AsyncClient` 则使用 httpx 自己的默认值。

### 配置超时和重试

两个客户端都暴露 `with_options(...)`，它返回一个浅拷贝，共享连接设置并覆盖默认值。用 `timeout` 作用于所有请求，或分别设置 `unary_timeout` 和 `stream_timeout`。`max_retries` 控制客户端重试：

```python
short = client.with_options(timeout=5.0, max_retries=2)
agent = short.agents.create(model="composer-2.5", local=LocalAgentOptions(cwd="."))
```

异步对应写法：

```python
short_async = async_client.with_options(timeout=5.0, max_retries=2)
agent = await short_async.agents.create(model="composer-2.5", local=LocalAgentOptions(cwd="."))
```

## 发送消息

每次 `agent.send()` 都返回一个 `Run`。每次 `await async_agent.send()` 都返回一个 `AsyncRun`。agent 会在各次运行之间保留对话上下文；run 是一条提示词的工作单元。

```python
print(agent.send("Find the bug in src/auth.py").text())

# Same agent, full conversation context is preserved.
print(agent.send("Fix it and add a regression test").text())
```

异步对应写法：

```python
run = await agent.send("Find the bug in src/auth.py")
print(await run.text())

run = await agent.send("Fix it and add a regression test")
print(await run.text())
```

要连同文本一起发送图像：

```python
run = agent.send(
    {
        "text": "What's in this screenshot?",
        "images": [{"data": base64_png, "mime_type": "image/png"}],
    }
)
```

也可以使用辅助 dataclass。`SDKImage.from_file(path)` 会从磁盘读取，并为你处理 base64 编码：

```python
from cursor_sdk import SDKImage, UserMessage

run = agent.send(
    UserMessage(
        text="What's in this screenshot?",
        images=[SDKImage.from_file("screenshot.png")],
    )
)
```

已经有编码后的字节或远程 URL 的调用方，还可以使用 `SDKImage.data_image(base64_data, mime_type)` 和 `SDKImage.url_image(url)`。

### Run

```python
class Run:
    id: str
    agent_id: str
    status: str  # "running" | "finished" | "error" | "cancelled" | "expired"
    result: str
    model: ModelSelection | None
    duration_ms: int
    git: RunGitInfo | None
    created_at: str | None
    usage: TokenUsage | None  # cumulative; property on the live handle

    def stream(self) -> Iterator[SDKMessage]: ...
    def messages(self) -> Iterator[SDKMessage]: ...
    def events(self) -> Iterator[RunStreamEvent]: ...
    def iter_text(self) -> Iterator[str]: ...
    def text(self) -> str: ...
    def wait(self) -> RunResult: ...
    def cancel(self) -> None: ...
    def conversation(self) -> list[ConversationTurn]: ...
    def conversation_json(self) -> str: ...
    def observe(self, *, after_offset: str | None = None) -> Iterator[RunStreamEvent]: ...

    def supports(self, operation: str) -> bool: ...
    def unsupported_reason(self, operation: str) -> str | None: ...
    def on_did_change_status(
        self, listener: Callable[[str], None]
    ) -> Callable[[], None]: ...
```

`run.stream()` 是 `run.messages()` 的别名。直接迭代 `run` 会产出 `RunStreamEvent` 信封，与 `run.events()` 相同。

`AsyncRun` 暴露同样的状态字段，包括 `usage`。做 I/O 的方法是异步的：`async for message in run.stream()`、`async for message in run.messages()`、`async for event in run.events()`、`async for text in run.iter_text()`、`await run.text()`、`await run.wait()`、`await run.cancel()`、`await run.conversation()`、`await run.conversation_json()`，以及 `async for event in run.observe()`。

### 流式传输

```python
run = agent.send("Find the bug in src/auth.py")

for message in run.messages():
    if message.type == "assistant":
        for block in message.message.content:
            if block.type == "text":
                print(block.text, end="")
    elif message.type == "thinking":
        print(message.text, end="")
    elif message.type == "tool_call":
        print(f"[tool] {message.name}: {message.status}")
    elif message.type == "status":
        print(f"[status] {message.status}")
    elif message.type == "usage":
        print(f"[usage] turn total={message.usage.total_tokens}")
```

运行流只能消费一次。`run.messages()`、`run.events()` 和 `run.iter_text()` 都从同一条底层流读取并推进它。流完成后，这次运行会持有终态结果（`run.result`、`run.status`、`run.usage`、`run.git`，……）。调用 `run.wait()` 可以排空剩余事件，并返回带类型的 `RunResult`。

### 不等待流、直接等待

```python
result = run.wait()

print(result.status)       # "finished" | "error" | "cancelled" | "expired"
print(result.result)       # final assistant text, if any
print(result.model)        # resolved ModelSelection used for this run
print(result.duration_ms)
print(result.usage)        # cumulative TokenUsage, or None if unavailable
print(result.git)          # RunGitInfo on cloud
```

异步对应写法：

```python
result = await run.wait()
```

### Token 用量

运行时提供用量时，运行会报告 token 用量。从实时句柄上的 `run.usage` 读取累计总量（流式传输期间，或在 `wait()` 之后），或读取 `result.usage`，它位于 `RunResult` 上，由 `run.wait()` 返回。两者都持有一个 `TokenUsage`，它是所有报告过用量的回合之和；如果没有任何回合报告用量，两者都是 `None`。例如一次被取消、从未完成回合的运行，一个不呈现用量的运行时，或一份尚未归并用量的分离云端快照。

```python
@dataclass(frozen=True)
class TokenUsage:
    input_tokens: int
    output_tokens: int
    cache_read_tokens: int
    cache_write_tokens: int
    total_tokens: int
    reasoning_tokens: int | None = None
```

| 字段 | 说明 |
| :------------------- | :---------------------------------------------------------------------------------------------------------------------- |
| `input_tokens` | 发给模型的提示词 token。 |
| `output_tokens` | 模型生成的 token。 |
| `cache_read_tokens` | 从提示词缓存提供的 token。 |
| `cache_write_tokens` | 写入提示词缓存的 token。 |
| `total_tokens` | `input_tokens + output_tokens`。缓存读取和写入已经包含在 `input_tokens` 里。不含 `reasoning_tokens`。 |
| `reasoning_tokens` | 推理 token，是 `output_tokens` 的子集。模型或运行时没有报告它时为 `None`。 |

```python
result = run.wait()

if result.usage is not None:
    print(f"total: {result.usage.total_tokens}")
    print(f"in: {result.usage.input_tokens}, out: {result.usage.output_tokens}")
    print(
        f"cache read/write: {result.usage.cache_read_tokens}/{result.usage.cache_write_tokens}"
    )
else:
    print("no usage reported for this run")
```

`reasoning_tokens` 已经计在 `output_tokens` 里面，因此 `total_tokens` 把它排除在外，以免重复计算。

要取得流式传输过程中的每回合数字，处理 `usage` [流事件](https://cursor.com/docs/sdk/python.md#stream-events)（`SDKUsageMessage`）。它在每个报告了用量的回合结束时触发一次，并带上该回合的 `TokenUsage`。`run.usage` 和 `result.usage` 在整次运行上保持累计。流式回合之后，句柄优先使用这些加总；否则，当 bridge 提供了用量时，它使用来自 `wait()` 或 `get_run` / `list_runs` 快照的用量。

```python
for message in run.messages():
    if message.type == "usage":
        print(f"turn used {message.usage.total_tokens} tokens")

# Or after wait / without consuming messages yourself:
result = run.wait()
print(run.usage, result.usage)
```

异步对应写法：`async for message in run.messages()` 和 `await run.wait()`。`run.usage` 在 `AsyncRun` 上仍然是同步属性。

`TokenUsage` 从 `cursor_sdk` 导出（进阶调用方还有 `to_token_usage` / `sum_token_usage`）。线路上的 JSON 是 camelCase（`inputTokens`，……）；Python dataclass 使用 snake\_case。

token 计数是运行时报告的数字；它们不说明费用。要取得计费用量和某次 agent 运行的美元费用，调用 [`agent.get_usage()`](https://cursor.com/docs/sdk/python.md#agentget_usage)。

### 读取文本输出

`iter_text()` 会在助手文本流式输出时逐段产出。`text()` 返回最终的终态文本；如果运行仍在进行，它会阻塞在 `wait()` 上。

```python
for chunk in run.iter_text():
    print(chunk, end="")

final_text = run.text()
```

异步对应写法：

```python
async for chunk in run.iter_text():
    print(chunk, end="")

final_text = await run.text()
```

### 取消一次运行

```python
run.cancel()
```

异步对应写法：

```python
await run.cancel()
```

`run.cancel()` 请求取消一次仍在进行的运行。状态变为 `"cancelled"`，实时流停止，进行中的工具调用停止，`run.wait()` 以 `status: "cancelled"` 结束等待。已经写出的部分输出（助手文本）会留在 `Run` 对象上。

取消一次已经处于终态的运行（`"finished"`、`"error"`、`"cancelled"`、`"expired"`）会抛出 `UnsupportedRunOperationError`。拿不准时用 `run.status` 做保护：

```python
if run.status == "running":
    run.cancel()
```

### 读取运行状态

```python
print(run.id)
print(run.status)  # "running" | "finished" | "error" | "cancelled" | "expired"

stop = run.on_did_change_status(lambda status: print(f"status changed to {status}"))
stop()  # remove the listener

turns = run.conversation()
```

`run.conversation()` 返回带类型的 `list[ConversationTurn]`。用它来渲染或持久化结构化历史，而无需订阅实时流。`run.conversation_json()` 返回原始 JSON 字符串。

异步运行使用 `await run.conversation()` 和 `await run.conversation_json()`。

### 按次运行的模型覆盖

`model`（传给 `agent.send()`）会覆盖该 agent 在这次运行上的选择，然后保持下去：之后没有覆盖的 send 会继续使用新模型。要切回去，再传一次 `model` 覆盖，或从 `agent.model` 读取当前选择。

```python
from cursor_sdk import ModelParameterValue, ModelSelection, SendOptions

run = agent.send(
    "Plan the refactor",
    SendOptions(
        model=ModelSelection(
            id="composer-2.5",
            params=[ModelParameterValue(id="fast", value="true")],
        ),
    ),
)
```

`run.model` 和 `result.model` 反映这次运行使用的选择，运行一开始就不可变。

### 每次运行的环境变量

Cloud Agent 也可以为单次运行接受环境变量。传入 `cloud.env_vars`（位于 `SendOptions`），这些值只注入到该 agent 这次运行的 shell 中。运行结束时，它们会从 VM 上移除，下一次运行看不到它们。这适合在回合之间轮换的凭据，比如你在让 agent 使用之前刚刚签发的短期部署 token。

```python
from cursor_sdk import CloudSendOptions, SendOptions

run = agent.send(
    "Deploy the preview environment",
    SendOptions(
        cloud=CloudSendOptions(env_vars={"DEPLOY_TOKEN": mint_short_lived_token()}),
    ),
)
```

如果某个运行范围的变量与 [`env_vars`（位于 `CloudAgentOptions`）](https://cursor.com/docs/sdk/python.md#session-environment-variables) 里的 agent 范围变量同名，这次运行采用运行范围的值，下一次运行再回到 agent 范围的值。

每次运行的变量在第一次 send 上也有效。SDK 会把它们连同 agent 创建一起传递，范围限定在初始运行，因此它们不会持久保存在 agent 上。和 agent 范围的变量一样，它们会静态加密，而且名称不能以 `CURSOR_` 开头。

每次运行的环境变量仅适用于 Cloud Agent，而且对针对公开仓库运行的 agent 不可用。对于本地 agent，agent 进程继承你自己的环境，因此在调用 `send()` 之前把变量设到进程上。

### 对话模式

传入 `mode="plan"` 或 `mode="agent"`，控制一次运行是先探索并规划，还是直接实施改动。Plan Mode 在产品中的作用见 [Plan Mode](https://cursor.com/help/ai-features/plan-mode.md)。

设置 `mode`（位于 `AgentOptions` 中，传给 `Agent.create()`），为第一次运行定下模式。在后续的 `agent.send()` 调用上，省略 `mode` 以保持对话的当前模式，或传入 `mode` 只为这次运行切换。

```python
from cursor_sdk import Agent, AgentOptions, CloudAgentOptions, CloudRepository, SendOptions

with Agent.create(
    AgentOptions(
        model="composer-2.5",
        mode="plan",
        cloud=CloudAgentOptions(
            repos=[CloudRepository(url="https://github.com/your-org/your-repo")],
        ),
    )
) as agent:
    agent.send("Design the auth refactor").wait()
    agent.send(
        "Looks good, start building",
        SendOptions(mode="agent"),
    ).wait()
```

### 流式传输原始增量

传入 `on_delta` 和 `on_step` 回调（位于 `SendOptions`），以取得更低层的更新。同步回调会被内联调用。异步回调可以是同步或异步的；可等待的返回值会在处理下一个事件之前被 await。

```python
from cursor_sdk import SendOptions

def on_delta(update):
    if update.type in ("text-delta", "thinking-delta"):
        print(update.text, end="")

run = agent.send(
    "Refactor the utils module",
    SendOptions(on_delta=on_delta, on_step=lambda step: print(f"[step] {step.type}")),
)
run.wait()
```

具体的 update 和 step 子类位于 `cursor_sdk.events`：

```python
from cursor_sdk.events import TextDeltaUpdate, ToolCallStartedUpdate

if isinstance(update, TextDeltaUpdate):
    print(update.text)
```

为了向后兼容，它们仍然可以从 `cursor_sdk` 导入，但新代码应从 `cursor_sdk.events` 导入。

### SendOptions

| 属性 | 类型 | 说明 |
| :---------------- | :------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `model` | `str \| ModelSelection \| Mapping[str, Any]` | 按次 send 的模型覆盖。省略时使用 `agent.model`。成功的 send 之后会保持。 |
| `mode` | `"agent" \| "plan"` | 按次 send 的对话模式覆盖。后续调用省略时，保持对话的当前模式。 |
| `mcp_servers` | `Mapping[str, McpServerConfig]` | 内联 MCP 服务器定义。这次运行会完全替换创建时的服务器。 |
| `cloud.env_vars` | `Mapping[str, str]` | 仅 Cloud Agent。为这次运行注入、并在结束时移除的[每次运行的环境变量](https://cursor.com/docs/sdk/python.md#per-run-environment-variables)。仅这次运行按名称覆盖 agent 范围的 `env_vars`。 |
| `local.force` | `bool` | 仅本地 agent。默认为 `None`（未设置）。设为 `True` 可在开始这条消息之前让卡住的活动运行过期。云端会在服务器侧返回 `409 agent_busy`，因此不需要对应项。 |
| `idempotency_key` | `str` | 这次 send 可选的、由客户端生成的幂等键。 |
| `on_step` | `Callable[[ConversationStep], Any]` | 每个已完成的对话步骤（文本、thinking 或工具批次）之后的回调。 |
| `on_delta` | `Callable[[InteractionUpdate], Any]` | 每个原始 `InteractionUpdate` 的回调。 |

***

接下来三节是 `SDKMessage`、`InteractionUpdate` 和 `ConversationTurn` 的详细参考。第一遍可以略读或跳过；[恢复 agent](https://cursor.com/docs/sdk/python.md#resuming-agents) 会接上正文。

## 流事件

`run.messages()` 产出带类型的 SDK 消息 dataclass。按 `message.type` 区分。运行时提供时，所有消息都包含 `agent_id` 和 `run_id`。

```python
SDKMessage = (
    SDKSystemMessage
    | SDKUserMessageEvent
    | SDKAssistantMessage
    | SDKThinkingMessage
    | SDKToolUseMessage
    | SDKStatusMessage
    | SDKTaskMessage
    | SDKRequestMessage
    | SDKSummaryMessage
    | SDKUsageMessage
    | Mapping[str, Any]
)
```

| `type` | Dataclass | 关键字段 |
| :------------ | :-------------------- | :-------------------------------------------------------------------------- |
| `"system"` | `SDKSystemMessage` | `subtype`、`model`、`tools` |
| `"user"` | `SDKUserMessageEvent` | `message.content` |
| `"assistant"` | `SDKAssistantMessage` | `message.content`，值为 `TextBlock` 和 `ToolUseBlock` |
| `"thinking"` | `SDKThinkingMessage` | `text`、`thinking_duration_ms` |
| `"tool_call"` | `SDKToolUseMessage` | `call_id`、`name`、`status`、`args`、`result`、`truncated` |
| `"status"` | `SDKStatusMessage` | `status`、`message` |
| `"task"` | `SDKTaskMessage` | `status`、`text` |
| `"request"` | `SDKRequestMessage` | `request_id` |
| `"summary"` | `SDKSummaryMessage` | `status`、`fallback` |
| `"usage"` | `SDKUsageMessage` | `usage`（[`TokenUsage`](https://cursor.com/docs/sdk/python.md#token-usage)） |

大多数工具调用会发出两次 `SDKToolUseMessage`：第一次 `status="running"` 且填好了 `args`，完成时再发一次，`status="completed"`（或 `"error"`）且填好了 `result`。`truncated` 标记 SDK 是否因为载荷过大而截断了 `args` 或 `result`。

当 agent 在接近上下文上限时总结对话，会发出 `SDKSummaryMessage`。总结开始时 `status` 为 `"running"`，然后是 `"completed"` 或 `"error"`。已完成消息上 `fallback=True` 表示模型没能写出总结，因此 agent 改为保存了一份裁剪后的转录。

每个报告了 token 用量的回合结束时会发出一次 `SDKUsageMessage`，带上该回合的 [`TokenUsage`](https://cursor.com/docs/sdk/python.md#token-usage)。跨回合的累计总量留在 `run.usage` 和 `result.usage` 上。见 [Token 用量](https://cursor.com/docs/sdk/python.md#token-usage)。

```python
@dataclass(frozen=True)
class SDKUsageMessage:
    type: Literal["usage"]
    agent_id: str
    run_id: str
    usage: TokenUsage
```

结果数据（最终文本、模型、耗时、累计 token 用量、git 元数据）在流完成后位于 `Run` 对象上。用 `run.wait()` 读取它，包括运行时报告了用量时的 `result.usage`。

> **工具调用的 schema 不稳定。** `args` 和 `result` 载荷（位于 `tool_call` 事件上）反映每个工具的内部形态，会随着工具演进而变化。工具名称也可能被重命名或替换。把 `args` 和 `result` 当作无类型数据，防御性地解析。事件信封（`type`、`call_id`、`name`、`status`）是稳定的。

`run.events()` 产出更低层的 `RunStreamEvent` 信封。当你需要偏移、终态结果信封或原始交互更新时使用它：

```python
for event in run.events():
    print(event.kind, event.offset)
```

## 交互更新

`InteractionUpdate` 是传给 `on_delta` 回调（位于 `agent.send()`）的原始增量类型。更新比 `SDKMessage` 事件更细：文本逐 token 流入，工具调用会在参数累积时报告部分状态。

```python
InteractionUpdate = (
    TextDeltaUpdate
    | ThinkingDeltaUpdate
    | ThinkingCompletedUpdate
    | ToolCallStartedUpdate
    | ToolCallCompletedUpdate
    | PartialToolCallUpdate
    | TokenDeltaUpdate
    | StepStartedUpdate
    | StepCompletedUpdate
    | TurnEndedUpdate
    | UserMessageAppendedUpdate
    | SummaryUpdate
    | SummaryStartedUpdate
    | SummaryCompletedUpdate
    | ShellOutputDeltaUpdate
    | UnknownInteractionUpdate
    | Mapping[str, Any]
)
```

模型在提交之前把参数流进一次工具调用时，会发出 `PartialToolCallUpdate`。适用于 `SDKToolUseMessage.args` 的同一条稳定性说明在这里同样适用。

`SummaryCompletedUpdate` 带有 `failed` 和 `fallback`：前者在总结以错误结束时设置，后者在 agent 保存的是一份裁剪后的转录而不是总结时设置。

## 对话类型

一次运行的结构化逐回合视图，由 `run.conversation()` 返回。每一项都是一个包装，旁边带有回合的 `type` 判别字段，带类型的载荷在 `turn` 里。

```python
@dataclass(frozen=True)
class ConversationTurn:
    type: str  # "agentConversationTurn" | "shellConversationTurn"
    turn: AgentConversationTurn | ShellConversationTurn | Mapping[str, Any]

@dataclass(frozen=True)
class AgentConversationTurn:
    user_message: Mapping[str, Any] | None = None
    steps: Sequence[ConversationStep] = ()

@dataclass(frozen=True)
class ShellConversationTurn:
    shell_command: ShellCommand | None = None
    shell_output: ShellOutput | None = None

ConversationStep = (
    AssistantConversationStep
    | ToolCallConversationStep
    | ThinkingConversationStep
    | Mapping[str, Any]
)
```

按 `turn.type` 区分，并通过 `turn.turn` 读取载荷：

```python
for turn in run.conversation():
    if turn.type == "agentConversationTurn":
        for step in turn.turn.steps:
            print(step.type)
    elif turn.type == "shellConversationTurn":
        print(turn.turn.shell_command, turn.turn.shell_output)
```

`run.conversation()` 在 `on_step` 回调里按 `ConversationStep` 触发，而不是按回合。工具调用对话步骤带有 `Mapping[str, Any]` 载荷。把工具调用载荷的细节当作无类型数据；见流事件下的[稳定性说明](https://cursor.com/docs/sdk/python.md#stream-events)。

## 恢复 agent

```python
Agent.resume(
    agent_id: str,
    options: AgentOptions | Mapping[str, Any] | None = None,
    *,
    client: CursorClient | None = None,
) -> Agent
```

用 `Agent.resume()` 或 `client.agents.resume()` 按 ID 重新连上一个已有 agent。常见流程：重新连接早先启动的长时间运行的 Cloud Agent，或在本地进程重启后继续对话。运行时根据 ID 前缀自动检测（`bc-` 是云端，其他都是本地）。

```python
agent = Agent.resume("bc-abc123")
run = agent.send("Also update the changelog")
run.wait()
```

异步对应写法：

```python
agent = await client.agents.resume("bc-abc123")
run = await agent.send("Also update the changelog")
await run.wait()
```

恢复之后，`agent.model` 为 `None`，除非你再次传入 `model`。内联 MCP 服务器不会跨 resume 持久化；它们常常带有机密，并且只存在于内存中。在 resume 时再次传入它们，或对应当存活下来的服务器使用基于文件的 MCP 配置（`.cursor/mcp.json` 加上 `local.setting_sources`）。

### 本地持久化

本地 agent 通过 bridge 持久化对话状态和运行元数据，因此后续消息和 `Agent.resume()` 可以在进程重启后继续。bridge 默认把这些数据放在磁盘上每个工作区一个的状态根目录下。Cloud Agent 在服务器侧持久化，因此从任何地方恢复一个 Cloud Agent 都会得到同一段对话。

本地持久化以工作区为范围。当 bridge 作为长期运行的 sidecar 或子进程运行时，给它与 agent 相同的工作区，这样本地的 list、get 和 resume 调用才能解析到正确的 agent。在客户端上设置一次，并把 `cwd` 传给本地的 list 和 get 调用：

```python
from cursor_sdk import CursorClient

with CursorClient.launch_bridge(workspace="/path/to/repo") as client:
    agents = client.agents.list(runtime="local", cwd="/path/to/repo")
    info = client.agents.get(agents.items[0].agent_id, cwd="/path/to/repo")
```

## 查看 agent 和运行

列表、获取和分页 API 使用 `CursorClient`。

```python
from cursor_sdk import CursorClient

with CursorClient.launch_bridge(workspace=".") as client:
    agents = client.agents.list(runtime="local", cwd=".")

    for agent_info in agents.auto_paging_iter():
        print(agent_info.agent_id)

    info = client.agents.get(agents.items[0].agent_id)
    runs = client.agents.list_runs(info.agent_id)
    run = client.agents.get_run(runs.items[0].id)
```

异步对应写法：

```python
agents = await client.agents.list(runtime="local", cwd=".")

async for agent_info in agents.auto_paging_iter():
    print(agent_info.agent_id)

info = await client.agents.get(agents.items[0].agent_id)
runs = await client.agents.list_runs(info.agent_id)
run = await client.agents.get_run(runs.items[0].id)
```

在 agent 句柄上用 `agent.list_messages()` 读取消息历史。当你只有一个 ID 时，`Agent.messages.list(agent_id)` 是同一次调用的类型化属性便捷写法。

用 `Agent.get_run(run_id)` 或 `client.agents.get_run(run_id)` 在没有 agent 句柄时获取一次运行。用
`Agent.cancel_run(run_id, agent_id=...)` 或
`client.agents.cancel_run(run_id, agent_id=...)` 取消它。异步客户端方法是可等待的，参数相同。

`AgentMessage` 与流式的 `SDKMessage` 不同：

```python
@dataclass(frozen=True)
class AgentMessage:
    type: str
    uuid: str
    agent_id: str
    message: Any = None
```

列表端点返回 `ListResult[T]`。直接使用 `.items` 和 `.next_cursor`，用 `for item in page` 迭代当前页，或用 `.auto_paging_iter()` 迭代所有页。异步列表端点返回 `AsyncListResult[T]`；`async for item in page` 遍历当前页，`async for item in page.auto_paging_iter()` 遍历结果集中的每一页。

### SDKAgentInfo

`Agent.list()`、`Agent.get()`、`client.agents.list()` 和 `client.agents.get()` 返回的元数据形态。

```python
@dataclass(frozen=True)
class SDKAgentInfo:
    agent_id: str
    name: str
    summary: str
    last_modified: str | None = None
    status: str | None = None  # "running" | "finished" | "error"
    created_at: str | None = None
    archived: bool = False
    runtime: Literal["local", "cloud"] | None = None
    cwd: str = ""
    env: CloudEnvironment | None = None
    repos: Sequence[str] = ()
    metadata: Mapping[str, str] = {}  # from CloudAgentOptions.metadata; empty for local agents
```

### Cloud Agent 生命周期

Cloud Agent 会留在你团队的工作区里，直到你归档或删除它们。`client.agents.list(runtime="cloud")` 默认隐藏已归档的 agent；传入 `include_archived=True` 即可看到它们。按 `pr_url` 过滤，找到打开了某个 pull request 的 agent。

```python
# By ID, no agent handle required:
Agent.archive(agent_id)
Agent.unarchive(agent_id)
Agent.delete(agent_id)

# Through an explicit client:
client.agents.archive(agent_id)
client.agents.unarchive(agent_id)
client.agents.delete(agent_id)

# On an existing agent handle:
agent.archive()
agent.unarchive()
agent.delete()
```

`archive` 会软删除 agent，使转录仍可阅读。`unarchive` 会恢复它。`delete` 是永久的；之后的读取返回 `NotFoundError`。

异步生命周期方法使用相同的名称，并且可等待。

### agent.get\_usage()

获取某次 agent 运行的计费 token 用量和美元费用。Cloud Agent 返回按运行拆分的明细；本地 agent 返回按回合拆分的明细。传入 `run_id` 把结果限制为一条：对 Cloud Agent 是 `run-<uuid>` 形式的运行 ID，对本地 agent 是先前 `get_usage().runs[].run_id` 中的一个 ID。

本地 agent 的用量正在逐步推出，并且按账号启用。在你的 API 密钥所属账号启用之前，对本地 agent 调用 `get_usage()` 会返回 `403 feature_unavailable`，Python SDK 会把它作为 `InternalServerError` 抛出，消息中包含 `feature_unavailable`。Cloud Agent 不受此门控。在此期间，从 `run.usage` 或 `result.usage` 读取每次运行的 token 计数（见 [Token 用量](https://cursor.com/docs/sdk/python.md#token-usage)），并从 SDK 标签下的[用量仪表板](https://cursor.com/dashboard/usage)读取计费费用。

```python
usage = agent.get_usage()

print(f"tokens: {usage.usage.total_tokens}")
if usage.cost is not None:
    print(f"charged: ${usage.cost.charged_cents / 100:.2f}")
for run in usage.runs:
    print(run.run_id, run.usage.total_tokens)
```

```python
@dataclass(frozen=True)
class AgentUsage:
    usage: TokenUsage              # total for the agent
    runs: Sequence[RunUsage] = ()
    cost: UsageCost | None = None  # total for the agent

@dataclass(frozen=True)
class RunUsage:
    run_id: str
    usage: TokenUsage
    cost: UsageCost | None = None

@dataclass(frozen=True)
class UsageCost:
    raw_cost_cents: float  # undiscounted model token cost; 0 for request-priced usage
    charged_cents: float   # amount charged, discounts and the Cursor Token Fee included
```

对 Cloud Agent 调用时如果不带 `run_id`，`usage` 和 `cost` 也会计入该 agent 的 Task Subagent 和侧边聊天，因此它们可能大于 `runs` 的总和。带上 `run_id` 时，`usage`、`cost` 以及该运行的条目也会计入从这次运行开始、直到该 agent 的下一次运行开始之间的 Subagent 和侧边聊天用量，如[获取 Agent 用量](https://cursor.com/docs/cloud-agent/api/endpoints.md#get-agent-usage)所述。

费用包含折扣，并且可能在运行结束后过一会儿才结算；在此之前 `cost` 为 `None`。对于方案内包含的用量、BYOK 用量和赠送额度用量，`charged_cents` 为 `0.0`。

这与 [Token 用量](https://cursor.com/docs/sdk/python.md#token-usage) 是不同的视图：`run.usage` 是单次运行的实时 token 计数，而 `get_usage()` 是该 agent 各次运行的计费记录。在 `get_usage()` 的结果里，`total_tokens` 等于 `input_tokens + output_tokens + cache_read_tokens + cache_write_tokens`。在异步 agent 上，`await agent.get_usage()` 与此一致。`AgentUsage`、`RunUsage` 和 `UsageCost` 从 `cursor_sdk` 导出。

## Cursor 命名空间

账号级和目录读取。同步方法接受可选的 `api_key`，否则回退到 `CURSOR_API_KEY`。

```python
from cursor_sdk import Cursor

me = Cursor.me()
models = Cursor.models.list()
repositories = Cursor.repositories.list()
```

显式客户端的对应写法：

```python
me = client.me()
models = client.models.list()
repositories = client.repositories.list()
```

异步对应写法：

```python
from cursor_sdk import AsyncCursor

me = await AsyncCursor.me(client=client)
models = await AsyncCursor.models.list(client=client)
repositories = await AsyncCursor.repositories.list(client=client)
```

`Cursor.me()` 返回一个 `SDKUser`，带有 `api_key_name`、`created_at`，以及可选的 `user_id`、`user_email`、`user_first_name` 和 `user_last_name` 字段。

用 `Cursor.models.list()` 在调用 `Agent.create()` 或 `agent.send()` 之前查看有效的模型 ID 和按模型区分的参数。参数因模型而异。常见例子是推理强度，以及 Cursor Router 的 `optimize_for`（位于 `auto-smart`）。

目录是账号和团队专属的。只有当 Router 对该 API 密钥的团队可用时，Cursor Router 才会显示为 `auto-smart`。见 [Cursor Router](https://cursor.com/docs/sdk/python.md#cursor-router)。

```python
models = Cursor.models.list()
composer = next((model for model in models if model.id == "composer-2.5"), None)

print(composer.parameters if composer else [])
# [
#   ModelParameterDefinition(
#       id="fast",
#       display_name="Fast",
#       values=(
#           ModelParameterDefinitionValue(value="false"),
#           ModelParameterDefinitionValue(value="true", display_name="Fast"),
#       ),
#   ),
# ]
```

预设 `variants`（位于每个 `SDKModel` 上）已经包含有效的 `params`，因此你可以把它们抄进一个 `ModelSelection`。

当目标模型缺失，而你想要 Cost、Balance 或 Intelligence 时，优先使用明确的 Router 选择（`auto-smart` + `optimize_for`）。只有在你想要由服务器选择的 Auto、且不挑选 Router 模式时，才回退到 `ModelSelection(id="auto")`。对于 Cursor Router，始终显式传入 `optimize_for`。

`Cursor.repositories.list()` 返回调用账号或团队上可供 Cloud Agent 使用的 SCM 仓库（GitHub、GitLab、Bitbucket、Azure DevOps，取决于已连接的是哪一种）。每一项都暴露一个 `url`。用它们来填写 `CloudAgentOptions.repos`。

## MCP 服务器

agent 可以根据运行时，从内联定义、项目/用户设置、Plugins，以及仪表板管理的配置中加载 MCP 服务器。

```python
from cursor_sdk import (
    Agent,
    AgentOptions,
    HttpMcpServerConfig,
    LocalAgentOptions,
    McpAuth,
    StdioMcpServerConfig,
)

agent = Agent.create(
    AgentOptions(
        model="composer-2.5",
        local=LocalAgentOptions(cwd="."),
        mcp_servers={
            "docs": HttpMcpServerConfig(
                url="https://example.com/mcp",
                auth=McpAuth(client_id="client-id", scopes=["read", "write"]),
            ),
            "filesystem": StdioMcpServerConfig(
                command="npx",
                args=["-y", "@modelcontextprotocol/server-filesystem", "."],
            ),
        },
    )
)
```

扁平字典（`{"type": "http", "url": ...}` 和 `{"type": "stdio", "command": ...}`）也可以接受，作为短脚本的便利写法。

### 会加载什么

**本地 agent** 最多从五个来源加载服务器，名称冲突时先匹配者优先：

1. `mcp_servers`（位于 `agent.send()`）。这次运行会完全替换创建时的服务器（不是合并）。
2. `mcp_servers`（位于 `Agent.create()`）。没有按次 send 的覆盖时使用。
3. Plugin 服务器，如果 `local.setting_sources` 包含 `"plugins"`。
4. 来自 `.cursor/mcp.json` 的项目服务器，如果 `local.setting_sources` 包含 `"project"`。
5. 来自 `~/.cursor/mcp.json` 的用户服务器，如果 `local.setting_sources` 包含 `"user"`。

没有 `local.setting_sources` 时，只加载内联服务器。如果某个本地 MCP 服务器需要 OAuth 登录，SDK 可以复用 Cursor 应用里已保存的登录，但不能打开浏览器让你登录。

**Cloud Agent** 从这些来源加载服务器：

1. `mcp_servers`（位于 `agent.send()`）。这次运行会完全替换创建时的服务器（不是合并）。
2. `mcp_servers`（位于 `Agent.create()`）。没有按次 send 的覆盖时使用。
3. 你在 [cursor.com/agents](https://cursor.com/agents) 上的用户和团队 MCP 服务器。

如果内联服务器没有包含 `auth` 或 `headers`，而你此前已经在 cursor.com/agents 上授权过该服务器 URL，那么用个人 API token 认证的运行会自动复用那些 OAuth token。服务账号 API 密钥不能回退到用户认证，因为它们不关联某个用户。

`local.setting_sources` 不适用于 Cloud Agent。

### 云端

Cloud Agent 也接受内联的、已认证的 MCP 配置。云端 MCP 支持 HTTP 和 stdio 传输。静态 API 密钥或 Bearer token 使用 HTTP `headers`。受 OAuth 保护的服务器使用 HTTP `auth`。当服务器在云端 VM 内运行、并从环境变量读取凭据时，使用 stdio `env`。

```python
from cursor_sdk import (
    Agent,
    AgentOptions,
    CloudAgentOptions,
    CloudRepository,
    HttpMcpServerConfig,
    StdioMcpServerConfig,
)

agent = Agent.create(
    AgentOptions(
        model="composer-2.5",
        cloud=CloudAgentOptions(
            repos=[CloudRepository(url="https://github.com/your-org/your-repo")],
        ),
        mcp_servers={
            "linear": HttpMcpServerConfig(
                url="https://mcp.linear.app/mcp",
                headers={"Authorization": "Bearer linear_pat_xxx"},
            ),
            "github": StdioMcpServerConfig(
                command="npx",
                args=["-y", "@modelcontextprotocol/server-github"],
                env={"GITHUB_TOKEN": "ghp_xxx"},
            ),
        },
    )
)
```

- HTTP 的 `headers` 和 `auth` 由 Cursor 的后端处理。敏感字段会被脱敏，不会进入 VM。
- Stdio 的 `env` 值会传入 VM，因为服务器在那里运行。把它们当作任何其他运行时机密来对待。
- 在 cursor.com/agents 上配置的 MCP 服务器的 OAuth 始终按用户隔离，即便是团队级服务器。

完整配置格式见 [MCP](https://cursor.com/docs/mcp.md)，云端特有行为见 [Cloud Agent 能力](https://cursor.com/docs/cloud-agent/capabilities.md#mcp-tools)。

## Subagents

定义具名 Subagent，主 agent 可以通过 `Agent` 工具启动它们。内联传入：

```python
from cursor_sdk import Agent, AgentDefinition, AgentOptions, LocalAgentOptions

agent = Agent.create(
    AgentOptions(
        model="composer-2.5",
        local=LocalAgentOptions(cwd="."),
        agents={
            "code-reviewer": AgentDefinition(
                description="Expert code reviewer for quality and security.",
                prompt="Review code for bugs, security issues, and proven approaches.",
                model="inherit",
            ),
            "test-writer": AgentDefinition(
                description="Writes tests for code changes.",
                prompt="Write comprehensive tests for the given code.",
            ),
        },
    )
)
```

提交在仓库 `.cursor/agents/*.md` 里的 Subagent（frontmatter 含 `name`、`description`，以及可选的 `model`）也会被加载。同名时，内联定义覆盖基于文件的定义。

### 嵌套 Subagent

Subagent 可以在嵌套层数上限内启动自己的 Subagent。当一个 Subagent 使用 `Agent` 工具时，它到达的是父级所拥有的同一个 Subagent 执行器，因此父级可以委托给一个还会继续委托的 Subagent。每一层看到的具名 Subagent 集合相同。顶层 agent 和它的直接 Subagent 可以启动 Subagent，但由另一个 Subagent 启动的 Subagent 不能再继续启动。

### 后台 Subagent

agent 在后台运行 Subagent 时，该 Subagent 的结果会作为同一次运行上的后续回合返回给父级，而不再在父回合结束时被丢掉。`run.messages()` 会持续产出这些回合，`run.wait()` 会在它们之后返回。仅本地 agent。

## 限制工具集

`tools` 用允许列表限定提供给模型的内置工具；`disallowed_tools` 移除工具并保留其余工具，包括在你的 SDK 版本发布之后才加到平台上的工具。目前两者都仅限本地 agent，并且都不会持久保存在 agent 上：要保持限制，在 resume 时再次传入它们。

```python
from cursor_sdk import Agent, AgentOptions, LocalAgentOptions

# Read-only agent: only these tools are offered.
reader = Agent.create(
    AgentOptions(
        model="composer-2.5",
        tools=["read", "grep", "glob", "ls"],
        local=LocalAgentOptions(cwd="."),
    )
)

# Everything except shell access.
no_shell = Agent.create(
    AgentOptions(
        model="composer-2.5",
        disallowed_tools=["shell"],
        local=LocalAgentOptions(cwd="."),
    )
)
```

- 省略 `tools` 时，提供所选模型的标准工具集；`tools=[]` 不提供任何内置工具，因此模型只能用文本回应。
- 两个字段都接受公开名称（`"read"`、`"edit"`、`"task"`、`"webSearch"`，……）以及能力分组 `"shell"` 和 `"mcp"`。未知名称会在创建时抛出 `BadRequestError`。
- 拒绝优先：一个工具必须在 `tools` 中（当设置了它时），并且不在 `disallowed_tools` 中，才会被提供。
- 禁用 `"mcp"` 也会移除[自定义工具](https://cursor.com/docs/sdk/python.md#custom-tools)。禁用 `"task"` 会阻止 [Subagents](https://cursor.com/docs/sdk/python.md#subagents)；否则 Subagent 保留各自精选的工具集。

## 自定义工具

自定义工具让你把 Python 函数暴露给本地 agent，而不必另起一个独立的 MCP 服务器。在 `LocalAgentOptions.custom_tools` 上传入它们。

```python
from cursor_sdk import Agent, CustomTool, CustomToolContext, LocalAgentOptions

def get_deployment_status(args, context: CustomToolContext):
    service = args["service"]
    return f"Service {service} is healthy."

with Agent.create(
    model="composer-2.5",
    local=LocalAgentOptions(
        cwd=".",
        custom_tools={
            "get_deployment_status": CustomTool(
                description="Look up the current deployment status for a service.",
                input_schema={
                    "type": "object",
                    "properties": {
                        "service": {"type": "string", "description": "Service name"},
                    },
                    "required": ["service"],
                },
                execute=get_deployment_status,
            ),
        },
    ),
) as agent:
    agent.send("Is the checkout service healthy?").wait()
```

`execute` 收到解析后的参数，以及一个 `CustomToolContext`，其中在可用时带有 `tool_call_id`。它可以返回字符串、JSON 兼容的值，或一个带 `content` 列表的映射。`output_schema` 为该工具的结构化结果声明一份 JSON Schema，并作为该工具的 MCP `outputSchema` 告知模型；结果不会对照它做校验。自定义工具仅限本地 agent。

## Hooks

Hooks 只能基于文件。没有可编程的 hook 回调。Hooks 是项目策略边界，不是按次运行的开关。

- **本地：** 把 `.cursor/hooks.json` 加到作为 `local.cwd` 传入的仓库，或添加 `~/.cursor/hooks.json` 作为用户级 Hooks。
- **云端：** 把 `.cursor/hooks.json` 及其脚本提交到 `cloud.repos` 里传入的仓库。SDK 创建的 Cloud Agent 会自动加载项目 Hooks。在 Enterprise 方案上，它们还会运行团队 Hooks 和由 Enterprise 管理的 Hooks。

配置格式见 [Hooks](https://cursor.com/docs/hooks.md)，云端行为见 [Cloud Agents 的 Hooks 支持](https://cursor.com/docs/cloud-agent.md#hooks-support)。

## 产物

列出并下载 agent 工作区中的文件。

```python
@dataclass(frozen=True)
class SDKArtifact:
    path: str
    size_bytes: int = 0
    updated_at: str = ""
```

```python
from pathlib import Path

artifacts = agent.list_artifacts()

for artifact in artifacts:
    print(artifact.path, artifact.size_bytes)

# Download a single artifact to disk.
content = agent.download_artifact(artifacts[0].path)
Path("review.md").write_bytes(content)
```

异步 agent 暴露 `await agent.list_artifacts()` 和 `await agent.download_artifact(path)`。

产物支持取决于运行时。本地 SDK agent 的 `list_artifacts()` 返回空列表，`download_artifact()` 会抛出异常。

## 资源管理

用完后始终关闭 agent。最干净的同步写法是上下文管理器：

```python
from cursor_sdk import Agent, LocalAgentOptions

with Agent.create(model="composer-2.5", local=LocalAgentOptions(cwd=".")) as agent:
    agent.send("Summarize the repository").wait()
```

要显式释放：

```python
agent.close()
```

异步 agent 和客户端支持异步上下文管理器以及 `await` 清理：

```python
from cursor_sdk import AsyncClient, LocalAgentOptions

async with await AsyncClient.launch_bridge(workspace=".") as client:
    async with await client.agents.create(
        model="composer-2.5",
        local=LocalAgentOptions(cwd="."),
    ) as agent:
        run = await agent.send("Summarize the repository")
        await run.wait()
```

要显式释放：

```python
await agent.close()
await client.aclose()
```

模块级的同步默认客户端会在进程退出时自动关闭。长时间运行的进程可以显式关闭并重置它：

```python
from cursor_sdk import close_default_client

close_default_client()
```

## 配置参考

Python SDK 接受辅助 dataclass 和原始字典。dataclass 使用 Python 的 `snake_case` 字段，应用代码优先使用它们。

### AgentOptions

| 属性 | 类型 | 默认值 | 说明 |
| :----------------- | :--------------------------------------------------- | :------------------------------------------------------------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `model`            | `str \| ModelSelection \| Mapping[str, Any]`         | 本地必填；云端回退到服务器解析出的默认值 | 要使用的模型。见 [`ModelSelection`](https://cursor.com/docs/sdk/python.md#modelselection)。 |
| `api_key`          | `str`                                                | `CURSOR_API_KEY` 环境变量 | 用户 API 密钥或服务账号密钥。Team Admin 密钥尚不支持。 |
| `name`             | `str`                                                | 自动生成 | 显示在 `client.agents.list()` / `client.agents.get()` 中的人类可读 agent 名称。 |
| `local`            | `LocalAgentOptions \| Mapping[str, Any]`             | `None`                                                              | 本地 agent 配置。传入它来创建本地 agent。 |
| `cloud`            | `CloudAgentOptions \| Mapping[str, Any]`             | `None`                                                              | Cloud Agent 配置。传入它来创建 Cloud Agent。 |
| `mcp_servers`      | `Mapping[str, McpServerConfig]`                      | `None`                                                              | 内联 MCP 服务器定义。 |
| `agents`           | `Mapping[str, AgentDefinition \| Mapping[str, Any]]` | `None`                                                              | Subagent 定义。 |
| `tools`            | `Sequence[str]`                                      | 默认工具集 | 只把列出的内置工具提供给模型。`[]` 表示没有内置工具；模型只能用文本回应。仅本地 agent。 |
| `disallowed_tools` | `Sequence[str]`                                      | `None`                                                              | 移除列出的内置工具；其余工具仍然可用。与 `tools` 一起使用时，拒绝优先。仅本地 agent。 |
| `agent_id`         | `str`                                                | 自动生成 | 持久的 agent ID。传入它，以便在多次调用之间保持稳定 ID。 |
| `idempotency_key`  | `str`                                                | 云端自动生成 | 可选的、由客户端生成的幂等键。仅云端。 |
| `mode`             | `"agent" \| "plan"`                                  | `None`                                                              | agent 第一次运行的初始对话模式。省略时，服务器以 agent 模式开始。见[对话模式](https://cursor.com/docs/sdk/python.md#conversation-mode)。 |

### LocalAgentOptions

| 属性 | 类型 | 默认值 | 说明 |
| :---------------- | :---------------------------------------------- | :------ | :---------------------------------------------------------------------------------------------------------------------------------- |
| `cwd`             | `str \| os.PathLike`                            | `None`  | 主工作目录。多条目列表会被拒绝；多根请使用 `dirs`。 |
| `dirs`            | `Sequence[str \| os.PathLike]`                  | `None`  | 多根设置下的额外工作区文件夹。与 `cwd` 合并，从而从每条路径加载 Rules、Skills 和工作区上下文。 |
| `setting_sources` | `Sequence[SettingSource]`                       | `None`  | 一并加载的设置层：`"project"`、`"user"`、`"team"`、`"mdm"`、`"plugins"` 或 `"all"`。 |
| `sandbox_options` | `SandboxOptions \| Mapping[str, Any]`           | `None`  | 本地沙箱选项。 |
| `store`           | `LocalAgentStoreConfig \| Mapping[str, Any]`    | `None`  | 传给 bridge 的本地存储配置。 |
| `auto_review`     | `bool`                                          | `None`  | 当所连接的后端支持时，把本地工具调用交给 Auto-review。 |
| `custom_tools`    | `Mapping[str, CustomTool \| Mapping[str, Any]]` | `None`  | 暴露给本地 agent 的[自定义工具](https://cursor.com/docs/sdk/python.md#custom-tools)。 |

### CloudAgentOptions

| 属性 | 类型 | 默认值 | 说明 |
| :-------------------------- | :----------------------------------------------- | :----------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `env`                       | `CloudEnvironment \| Mapping[str, Any]`          | `None`                                                 | 执行环境。省略时，服务器使用 Cursor 托管的云端 VM。`pool` 和 `machine` 指向你自己运行的自托管 worker。 |
| `repos`                     | `Sequence[CloudRepository \| Mapping[str, Any]]` | `None`                                                 | 要克隆进 VM 的仓库。省略或传入 `[]`，即得到一个工作区为空的[无仓库 agent](https://cursor.com/docs/sdk/python.md#no-repo-cloud-agents)。在某个仓库上传入 `pr_url`，把 agent 附到一个已有 PR。 |
| `work_on_current_branch`    | `bool`                                           | `None`                                                 | 把提交推送到已有分支，而不是新分支。服务器把省略的值当作 `False`。 |
| `auto_create_pr`            | `bool`                                           | `None`                                                 | 运行结束时打开一个 PR。服务器把省略的值当作 `False`。 |
| `open_as_cursor_github_app` | `bool`                                           | `True` 用于服务账号密钥，`False` 用于用户密钥 | 以 Cursor GitHub App 的身份打开 PR，而不是以 API 密钥所有者的身份。解析后的值会在 create、get 和 list 上回显。 |
| `skip_reviewer_request`     | `bool`                                           | `None`                                                 | 跳过把调用用户请求为该 PR 的审查者。服务器把省略的值当作 `False`。 |
| `env_vars`                  | `Mapping[str, str]`                              | `None`                                                 | Cloud Agent 的会话范围环境变量。 |
| `metadata`                  | `Mapping[str, str]`                              | `None`                                                 | 持久保存在 Cloud Agent 上、由调用方拥有的字符串标签。见 [Agent 元数据](https://cursor.com/docs/sdk/python.md#agent-metadata)。 |

### AgentDefinition

| 属性 | 类型 | 默认值 | 说明 |
| :------------ | :--------------------------------------------------------------- | :--------- | :----------------------------------------------------------------------------------------------- |
| `description` | `str`                                                            | *必填* | 何时使用这个 Subagent。展示给父 agent，让它知道何时启动。 |
| `prompt`      | `str`                                                            | *必填* | 该 Subagent 的系统提示词。 |
| `model`       | `str \| ModelSelection \| Mapping[str, Any] \| "inherit"`        | `None`     | 模型覆盖。`None` 和 `"inherit"` 都使用父级的选择。 |
| `mcp_servers` | `Sequence[str \| AgentDefinitionMcpServer \| Mapping[str, Any]]` | `None`     | 这个 Subagent 可用的 MCP 服务器。名称引用父级 `mcp_servers` 中的服务器。 |

### CustomTool

```python
@dataclass
class CustomTool:
    execute: Callable[[Mapping[str, Any], CustomToolContext], Any]
    description: str | None = None
    input_schema: Mapping[str, Any] | None = None
    output_schema: Mapping[str, Any] | None = None

class CustomToolContext:
    tool_call_id: str | None = None
```

### ModelSelection

```python
@dataclass(frozen=True)
class ModelSelection:
    id: str
    params: Sequence[ModelParameterValue] = ()

@dataclass(frozen=True)
class ModelParameterValue:
    id: str
    value: str
```

`id` 是模型标识（例如 `"composer-2.5"` 或 `"auto-smart"`）。`params` 携带按模型区分的参数，例如推理强度或 Router 的 `optimize_for`。用 `Cursor.models.list()` 查看你的账号可用的有效 ID、参数定义和预设变体。Router 的选择契约见 [Cursor Router](https://cursor.com/docs/sdk/python.md#cursor-router)。

### McpServerConfig

```python
from cursor_sdk.types import McpServerConfig

@dataclass(frozen=True)
class HttpMcpServerConfig:
    url: str
    type: Literal["http", "sse"] | str = "http"
    headers: Mapping[str, str] | None = None
    auth: McpAuth | Mapping[str, Any] | None = None

@dataclass(frozen=True)
class SseMcpServerConfig(HttpMcpServerConfig):
    type: Literal["sse"] = "sse"

@dataclass(frozen=True)
class StdioMcpServerConfig:
    command: str
    args: Sequence[str] | None = None
    env: Mapping[str, str] | None = None
    cwd: str | os.PathLike | None = None  # local only; cloud rejects this field

@dataclass(frozen=True)
class McpAuth:
    client_id: str
    client_secret: str | None = None
    scopes: Sequence[str] = ()
```

对于在云端运行的 HTTP 服务器，`headers` 和 `auth` 由 Cursor 的后端处理。敏感字段在 VM 看到之前会被脱敏。对于云端的 stdio 服务器，`env` 值会传入 VM（把它们当作任何运行时机密）。

### UserMessage

```python
@dataclass(frozen=True)
class UserMessage:
    text: str
    images: Sequence[SDKImage | Mapping[str, Any]] | None = None
```

`agent.send()` 消息参数的结构化形式。用它连同文本一起发送图像。

### SDKImage

```python
@dataclass(frozen=True)
class SDKImage:
    url: str | None = None
    data: str | None = None
    mime_type: str | None = None
    dimension: SDKImageDimension | Mapping[str, Any] | None = None

    @classmethod
    def from_url(cls, url: str, dimension=None) -> SDKImage: ...

    @classmethod
    def from_data(cls, data: bytes | str, mime_type: str, dimension=None) -> SDKImage: ...

    @classmethod
    def url_image(cls, url: str, dimension=None) -> SDKImage: ...

    @classmethod
    def data_image(cls, data: str, mime_type: str, dimension=None) -> SDKImage: ...

    @classmethod
    def from_file(cls, path, *, mime_type=None, dimension=None) -> SDKImage: ...
```

传入远程 `url`，或传入 base64 `data` 并带上 `mime_type`。`from_data()` 接受字节或 base64 字符串。`from_file()` 从磁盘读取文件并做 base64 编码。

### SettingSource

`SettingSource` 可以从 `cursor_sdk.types` 取得。

```python
from cursor_sdk.types import SettingSource
```

控制本地 agent 加载哪些磁盘上的设置层。Cloud Agent 始终加载 `project`、`team` 和 `plugins`，并忽略这个字段。

| 值 | 来源 |
| :---------- | :-------------------------------------- |
| `"project"` | 工作区中的 `.cursor/` |
| `"user"` | `~/.cursor/` |
| `"team"` | 从仪表板同步的 Team settings |
| `"mdm"` | 由 MDM 管理的 Enterprise 设置 |
| `"plugins"` | Plugin 提供的设置 |
| `"all"` | 以上全部的简写 |

### ListResult

```python
@dataclass(frozen=True)
class ListResult(Generic[T]):
    items: list[T]
    next_cursor: str = ""

    def to_dict(self) -> dict[str, Any]: ...
    def has_next_page(self) -> bool: ...
    def next_page_info(self) -> dict[str, str]: ...
    def get_next_page(self) -> ListResult[T]: ...
    def auto_paging_iter(self) -> Iterator[T]: ...
```

由 `client.agents.list()`、`client.agents.list_runs()` 和 `Agent.list()` 返回。没有更多页时 `next_cursor` 为空。异步列表端点返回 `AsyncListResult[T]`，并带有可等待的对应方法。

## 错误

所有 SDK 错误都扩展 `CursorAgentError`。具体错误是 `CursorSDKError` 的子类，而它本身是 `CursorAgentError`，因此两者都可以作为总捕获类型。用 `is_retryable` 和 `retry_after` 来驱动重试逻辑。

```python
class CursorAgentError(Exception):
    message: str
    code: str | None
    status: int | None
    status_code: int | None
    details: list[Mapping[str, Any]]
    is_retryable: bool
    cause: BaseException | None
    proto_error_code: str | None
    request_id: str | None
    headers: Mapping[str, str]
    retry_after: str | None
```

| 错误 | 何时 |
| :----------------------------- | :---------------------------------------------------------------------------------------------------------------------- |
| `AuthenticationError` | API 密钥无效，或尚未登录。 |
| `PermissionDeniedError` | 已认证的调用方没有所请求操作的权限。 |
| `RateLimitError` | 请求过多，或超出了用量限制。 |
| `ConfigurationError` | 模型无效、缺少必需配置，或请求参数有误。 |
| `AgentBusyError` | 在 agent 已有一次处于 `CREATING` 或 `RUNNING` 状态的运行时发送后续消息（HTTP `409`，代码 `agent_busy`）。 |
| `BadRequestError` | 请求格式不正确。 |
| `IntegrationNotConnectedError` | 为一个 SCM 提供商尚未连接的仓库创建 Cloud Agent。 |
| `NetworkError` | 服务不可用或网络失败。 |
| `APITimeoutError` | 请求超时。 |
| `InternalServerError` | Cursor 服务返回了服务器错误。 |
| `NotFoundError` | 请求的资源不存在。 |
| `AgentNotFoundError` | Agent 不存在，或在当前工作目录下不可见。 |
| `UnsupportedRunOperationError` | 当前运行状态不支持该运行操作。 |

### 带退避的重试

`is_retryable` 和 `retry_after` 驱动调用方一侧的重试逻辑。`retry_after` 是服务器在设置了它时提供的 HTTP 风格字符串（秒数，或一个 HTTP 日期）。

```python
import time

from cursor_sdk import Agent, AgentOptions, CursorAgentError, LocalAgentOptions, RateLimitError

for attempt in range(3):
    try:
        result = Agent.prompt(
            "Audit the auth middleware for missing input validation",
            AgentOptions(model="composer-2.5", local=LocalAgentOptions(cwd=".")),
        )
        break
    except RateLimitError as err:
        time.sleep(float(err.retry_after) if err.retry_after else 2**attempt)
    except CursorAgentError as err:
        if not err.is_retryable:
            raise
        time.sleep(2**attempt)
```

每个 `CursorAgentError` 在服务器返回了 `request_id` 时都会带上它。每当你把错误呈现出来时都记下它，这样支持人员才能定位这次失败。

### IntegrationNotConnectedError

```python
class IntegrationNotConnectedError(ConfigurationError):
    provider: str   # e.g. "github", "gitlab", "azure-devops"
    help_url: str   # dashboard link to reconnect
```

用 `help_url` 把用户指向正确的重新连接流程。新的提供商可能会在不发布 SDK 的情况下加入。

### AgentBusyError

Cloud Agent 一次只允许一个活动运行。`AgentBusyError` 会在你调用 `agent.send()`（或以其他方式创建一次运行）时抛出，此时同一个 agent 上另一次运行仍处于 `CREATING` 或 `RUNNING`。

`is_retryable` 为 `False`。立刻重试会一直失败，直到活动运行到达终态，或你取消它。其他 `409` 响应，例如 `agent_archived`，会改为抛出 `ConfigurationError`。

等待活动运行结束，用 `run.cancel()` 取消它，或在再次发送之前轮询 `Agent.list_runs()`：

```python
from cursor_sdk import Agent, AgentBusyError

agent = Agent.resume("bc-00000000-0000-0000-0000-000000000001")

try:
    agent.send("Also add tests for the auth middleware.")
except AgentBusyError:
    runs = Agent.list_runs(agent.agent_id, {"runtime": "cloud", "limit": 1})
    active = runs.items[0] if runs.items else None
    if active is not None and active.status == "running":
        active.cancel()
    agent.send("Also add tests for the auth middleware.")
```

本地 agent 不会抛出 `AgentBusyError`。传入 `local={"force": True}`（位于 `send()`），以便在开始新的一次运行之前让卡住的本地运行过期。

### UnsupportedRunOperationError

```python
class UnsupportedRunOperationError(ConfigurationError):
    operation: str
```

当当前运行不允许某个 `Run` 操作时抛出。最常见的情况是对已经处于终态的运行调用 `run.cancel()`。

`run.supports(operation)` 和 `run.unsupported_reason(operation)` 报告某个操作名（`"stream"`、`"wait"`、`"cancel"`、`"conversation"`）在 SDK 层面的能力，并且不检查运行状态。读取 `run.status` 来保护对状态敏感的调用。

## 排查

设置 `CURSOR_SDK_LOG=debug`（或 `info`），为 SDK 自己的 logger 接上一个 stderr 处理器。SDK 只配置它自己的 `cursor_sdk` logger，因此这不会干扰宿主应用的日志设置。

```bash
CURSOR_SDK_LOG=debug python my_script.py
```

随包附带的 bridge 二进制文件会作为 `cursor-sdk-bridge` 安装到 PATH 上。直接运行它，以确认你的 wheel 里带的是哪一个构建：

```bash
cursor-sdk-bridge --help
```

## 已知限制

- 工具调用载荷的 schema 有意不做强类型。
- 内联 MCP 服务器不会跨 `Agent.resume()` 持久化。如果需要，在 resume 时再次传入它们。
- 自定义工具（`local.custom_tools`）和工具集限制（`tools`、`disallowed_tools`）仅限本地 agent。这些限制不会持久保存在 agent 上；在 resume 时再次传入它们。
- 本地 agent 尚未实现产物下载。
- `local.setting_sources`（以及它控制的基于文件的 MCP 和 Subagent 路径）不适用于 Cloud Agent。云端始终加载 `project`、`team` 和 `plugins`。
- Hooks 只能基于文件（`.cursor/hooks.json`）。没有可编程回调。

---

## 站点地图

[所有文档页面概览](/llms.txt)
