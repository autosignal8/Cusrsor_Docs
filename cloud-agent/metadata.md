> 原文：https://cursor.com/docs/cloud-agent/metadata

# Agent 元数据

### 预览

Agent 元数据处于预览阶段，可能会变化，包括破坏性变更。

Cloud Agents 可以在虚拟机内读取关于当前运行的键值元数据：Agent id、所有者、谁提交了这个回合、哪个模型在提供服务，以及检出了哪些仓库。Hooks 和安装脚本也可以读取这些值。

Agent 用它们的终端工具调用此 API。你不需要自己运行这些请求。

要让 Agent 读取元数据，请在提示词中包含以下内容：

```text
To read agent metadata, follow the instructions at
https://cursor.com/docs/cloud-agent/metadata
```

此 API 位于 Agent 虚拟机本地。它不是你用 [SDK](https://cursor.com/docs/sdk/typescript.md#agent-metadata) 或 [Cloud Agents API](https://cursor.com/docs/cloud-agent/api/endpoints.md) 创建 Agent 时设置的、由调用方拥有的 `metadata` 标签。那些 API 使用 Cursor API 密钥，并从虚拟机外部管理 Agent。

当虚拟机外部的某个事物需要验证 Agent 的身份时，请改为让 Agent 签发 [OIDC token](https://cursor.com/docs/cloud-agent/identity.md)。那些 JWT 经过签名并绑定受众。元数据不是凭证。它可以包含当前回合的提交者和正在提供服务的模型，而 token 不应携带这些信息。

Cursor 托管的 Cloud Agent 虚拟机在与 OIDC token 相同的套接字上提供元数据。自托管 worker 目前还不提供此 API。

## 读取一个值

Agent 通过位于 `CURSOR_AGENT_SOCKET` 的 Unix 套接字读取键。在 Cursor 托管的虚拟机上，默认值是 `/run/cursor/api.sock`。

```bash
curl --unix-socket "${CURSOR_AGENT_SOCKET:-/run/cursor/api.sock}" \
  http://cursor-agent/v1/meta-data/agent/id
```

请求是通过 Unix 套接字的 HTTP。URL 中的主机名会被忽略。

列出一个前缀，以查看存在哪些键：

```bash
curl --unix-socket "${CURSOR_AGENT_SOCKET:-/run/cursor/api.sock}" \
  http://cursor-agent/v1/meta-data/
```

```text
agent/
owner/
turn/
workspace/
```

然后请求一个键：

```bash
curl --unix-socket "${CURSOR_AGENT_SOCKET:-/run/cursor/api.sock}" \
  http://cursor-agent/v1/meta-data/owner/user-id
```

### 请求

通过 Unix 套接字的 `GET /v1/meta-data[/<path>]`。没有请求体，也没有额外的请求头。允许末尾斜杠，因此列出的 `agent/` 可以按 `/v1/meta-data/agent/` 请求。

缺失的键返回 `404`。

### 响应

成功的读取为 `text/plain; charset=utf-8`。键的响应是作为文本的值，除此之外没有其他内容。

| 种类 | 正文 |
| :----- | :-------------------------------------------------------------------------------------------------- |
| 键 | 值为字符串。具有多个值的键每行一个条目。 |
| 前缀 | 每行一个子项，已排序。嵌套前缀以 `/` 结尾。列表以一个末尾换行结束。 |

错误响应是 JSON。参见[速率限制与错误](https://cursor.com/docs/cloud-agent/metadata.md#rate-limits-and-errors)。

### 键何时出现

[安装脚本](https://cursor.com/docs/cloud-agent/setup.md)可以读取同一个套接字。只有当键有值时它才会出现：在编码回合开始之前，`turn/` 是缺失的；在运行记录分支之前，`workspace/branch-name` 是缺失的。所有者、团队和仓库键从 Agent 创建之时起就可用。

如果启动后套接字暂时缺失，请重试连接。

## 键

缺失的键会从列表中省略，直接请求时返回 `404`。列表只包含此刻存在的键。

### `agent/`

| 键 | 何时出现 | 说明 |
| :-------------- | :----------- | :------------------------------------------------------------------------------ |
| `agent/id` | 始终 | Cloud Agent id（`bcId`）。 |
| `agent/name` | 已知时 | 仪表盘中显示的名称。 |
| `agent/source` | 已知时 | Agent 的启动方式，例如 `WEBSITE`、`API`、`SLACK` 或 `AUTOMATIONS`。 |
| `agent/runtime` | 始终 | 在 Cursor 托管的 Cloud Agent 虚拟机上为 `managed`。 |

### `owner/`

| 键 | 何时出现 | 说明 |
| :------------------------- | :----------- | :--------------------------------------------------------------------------------------------- |
| `owner/user-id` | 已知时 | Agent 所有者的 Cursor 用户 id，为十进制字符串。允许列表请优先使用它，而不是邮箱。 |
| `owner/user-email` | 已知时 | 小写的所有者邮箱。邮箱可能会变化。 |
| `owner/service-account-id` | 已知时 | 当服务账号拥有该 Agent 时的服务账号 id。 |
| `owner/team-id` | 已知时 | 所属团队 id，为十进制字符串。 |

### `turn/`

`turn/` 只在编码回合处于活动状态时存在。回合之间这些键会消失。如果 `turn/` 缺失，就没有活动回合。

`turn/` 下的值始终反映当前回合。不要跨回合缓存它们。

| 键 | 何时出现 | 说明 |
| :---------------- | :------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `turn/id` | 回合期间 | 此编码回合的 id。它不同于 `agent/id`，后者是 Cloud Agent id（`bcId`）。 |
| `turn/user-id` | 已知时 | 提交此回合的人的 Cursor 用户 id，为十进制字符串。在[团队跟进](https://cursor.com/docs/cloud-agent/settings.md#team-follow-ups)中，它可能与 `owner/user-id` 不同。 |
| `turn/user-email` | 已知时 | 该人的小写邮箱。 |
| `turn/started-at` | 回合期间 | 回合开始时间，为 Unix 秒。 |
| `turn/model` | 已知时 | 为此回合提供服务的模型。如果你选择了 Auto，这是实际提供服务的模型，而不是 `Auto`。 |

OIDC token 不包含谁提交了回合，也不包含哪个模型在提供服务，因为 token 的寿命可以超过该回合。请改为从元数据读取这些键。

### `workspace/`

| 键 | 何时出现 | 说明 |
| :------------------------- | :-------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `workspace/repo-url` | 已知时 | 主仓库，形式为 `host/path`，例如 `github.com/acme/widgets`。主机名已小写，没有协议、凭证、端口、查询或 `.git` 后缀。在多仓库 Agent 上，这只是主仓库。 |
| `workspace/repo-urls` | 当该集合已知时 | 工作区中的每个仓库，形式与 `repo-url` 相同。主仓库在前，其余按排序排列，每行一个 URL。缺失表示该集合未知，而不是只有一个仓库。 |
| `workspace/branch-name` | 已知时 | 主仓库上的分支。 |
| `workspace/environment-id` | 已知时 | 此次运行使用的 Cursor 环境的 id。 |
| `workspace/automation-id` | 自动化时 | 当 `agent/source` 为 automations 时的自动化 id。 |

`workspace/repo-url` 是主仓库。要获取完整集合，请读取 `workspace/repo-urls`。

## 谁可以读取元数据

任何能够到达该套接字的进程都可以读取每一个键：Agent、它运行的代码，以及 Hooks。请把这些值视为对整个运行可见。

元数据没有签名。要向 AWS、GCP、Vault 或你自己的服务证明身份，请让 Agent 签发 [OIDC token](https://cursor.com/docs/cloud-agent/identity.md) 并验证 JWT。不要把元数据值当作凭证转发。

## 速率限制与错误

每个 Agent 虚拟机每分钟可以发出 **120** 次元数据请求，突发最多 20 次。该套接字同时最多接受 8 个连接。此上限与 OIDC 签发共享。

对 `429`、`503`、`500`、`502` 和 `504` 使用退避重试。把 `403` 视为致命错误：此 Agent 不被允许读取元数据。

`404` 和 `405` 响应包含一个 `usage` 字符串，重述如何调用 API。速率限制和饱和错误只有错误码：

```json
{ "error": "not_found", "usage": "GET /v1/meta-data[/<path>] ..." }
```

```json
{ "error": "rate_limited" }
```

| HTTP | `error` | 何时 |
| :-------- | :-------------------- | :-------------------------------------------------------------- |
| 404 | `not_found` | 未知或缺失的键 |
| 405 | `method_not_allowed` | 不是 `GET` |
| 429 | `rate_limited` | 超过每个 Agent 的请求预算；请遵守 `Retry-After` |
| 503 | `saturated` | 连接过多；请遵守 `Retry-After` |
| 500 | `host_error` | 内部错误；请重试 |
| 502 / 504 | `backend_unreachable` | Cursor 无法返回元数据；请重试 |
| 其他 | `backend_error` | Cursor 拒绝了该请求。`403` 是致命错误；`503` 可以重试 |

## 示例

Agent 或 Hook 可以把回合提交者与所有者进行比较。队友的跟进可以走更严格的路径：

```bash
SOCKET="${CURSOR_AGENT_SOCKET:-/run/cursor/api.sock}"

owner="$(curl -fsS --unix-socket "$SOCKET" \
  http://cursor-agent/v1/meta-data/owner/user-id)"
turn_user="$(curl -fsS --unix-socket "$SOCKET" \
  http://cursor-agent/v1/meta-data/turn/user-id || true)"

if [ -n "$turn_user" ] && [ "$turn_user" != "$owner" ]; then
  echo "follow-up from user $turn_user; owner is $owner"
fi
```

Agent 或 Hook 可以用 Agent id 和为此回合提供服务的模型给日志打标签：

```bash
SOCKET="${CURSOR_AGENT_SOCKET:-/run/cursor/api.sock}"

agent_id="$(curl -fsS --unix-socket "$SOCKET" \
  http://cursor-agent/v1/meta-data/agent/id)"
model="$(curl -fsS --unix-socket "$SOCKET" \
  http://cursor-agent/v1/meta-data/turn/model || true)"

echo "cloud_agent_id=$agent_id model=${model:-unknown}"
```

列出工作区中的每个仓库。`repo-urls` 每行一个 URL：

```bash
curl -fsS --unix-socket "${CURSOR_AGENT_SOCKET:-/run/cursor/api.sock}" \
  http://cursor-agent/v1/meta-data/workspace/repo-urls
```

```text
github.com/acme/widgets
github.com/acme/docs
```

## 相关页面

- [OIDC token](https://cursor.com/docs/cloud-agent/identity.md)：已签名的 JWT 和云身份联合
- [密钥与网络](https://cursor.com/docs/cloud-agent/security-network.md)：仪表盘密钥和出站控制
- [Cloud Agent 环境设置](https://cursor.com/docs/cloud-agent/setup.md)：可以读取此套接字的安装脚本
- [Hooks](https://cursor.com/docs/hooks.md)：在工具和对话边界运行此 API
- [服务账号](https://cursor.com/docs/account/enterprise/service-accounts.md)：当 Agent 以团队服务账号运行时

---

## 站点地图

[所有文档页面概览](/llms.txt)
