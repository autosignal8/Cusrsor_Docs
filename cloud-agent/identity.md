> 原文：https://cursor.com/docs/cloud-agent/identity

# OIDC token

Cloud Agents 可以从 Agent 运行所在的机器签发短期 [OIDC](https://openid.net/specs/openid-connect-core-1_0.html) JWT，并用它们代入云角色或调用内部服务，而无需在 [Secrets](https://cursor.com/docs/cloud-agent/security-network.md#secret-protection) 中存储长期凭证。

Agent 用它们的终端工具调用此 API。你不需要自己运行这些请求。

要让 Agent 签发 token，请在提示词中包含：

```text
To mint OIDC tokens, follow the instructions at
https://cursor.com/docs/cloud-agent/identity
```

此 API 位于运行 Agent 的机器本地。它与 [Cloud Agents API](https://cursor.com/docs/cloud-agent/api/endpoints.md) 无关，后者使用 Cursor API 密钥，并从该机器外部管理 Agent。在 Cursor 管理的 VM 上，此套接字还提供 [Agent 元数据](https://cursor.com/docs/cloud-agent/metadata.md)。

Cursor 管理的 Cloud Agent VM 提供 token 套接字。它们签发的每个 token 都带有 `agent_runtime: managed`。[自托管机器](https://cursor.com/docs/cloud-agent/self-hosted.md)的 worker 在你用 `--identity-socket` 启动它们时提供该套接字。它们的 token 带有 `agent_runtime: self_hosted`。见[自托管 worker](https://cursor.com/docs/cloud-agent/identity.md#self-hosted-workers)。

Team Pool 控制器也可以在任何 worker 启动之前，在认领响应中获得一个 token。见[认领 token](https://cursor.com/docs/cloud-agent/identity.md#claim-tokens)。

## 工作原理

1. Agent 调用本地套接字，并请求一个受众为验证方所期望值的 token。
2. Cursor 签发一个绑定到该 Agent 和所有者的 RS256 JWT。
3. Agent 把 JWT 发送给你的云或验证方（AWS STS、GCP、Azure、Vault，或你运行的服务）。
4. 验证方对照 Cursor 发布的 JWKS 检查签名，并根据 `sub`、`team_id` 或 `cloud_agent_id` 等声明进行授权。

## 签发 token

Agent 通过 `CURSOR_AGENT_SOCKET` 中的路径上的 Unix 套接字签发 token（在 Cursor 管理的 VM 上，它始终设为 `/run/cursor/api.sock`；在自托管 worker 上，该路径随每个已认领的 Agent 而变化）。

```bash
curl --unix-socket "${CURSOR_AGENT_SOCKET}" \
  -H 'Content-Type: application/json' \
  -d '{"aud":"sts.amazonaws.com"}' \
  http://cursor-agent/v1/tokens/oidc
```

请求是通过 Unix 套接字的 HTTP。URL 中的主机名会被忽略。

当验证方期望重放绑定时，包含可选的 `nonce`：

```bash
curl --unix-socket "${CURSOR_AGENT_SOCKET}" \
  -H 'Content-Type: application/json' \
  -d '{"aud":"https://oidc.example.com","nonce":"unpredictable-value"}' \
  http://cursor-agent/v1/tokens/oidc
```

### 请求

通过 Unix 套接字的 `POST /v1/tokens/oidc`。必须使用 `Content-Type: application/json`。请求体最大为 4 KB。

| 字段 | 必填 | 说明 |
| :---------- | :------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `aud` | 是 | 验证方检查的受众字符串。可打印 ASCII，不含空白，最多 512 个字符。示例：`sts.amazonaws.com`、`https://oidc.example.com`。 |
| `nonce` | 否 | 回显到 JWT `nonce` 声明中的不透明字符串。最多 512 个字符。 |
| `sub_claim` | 否 | 作为 `<name>:<value>` 放入 `sub` 的声明名称，供只匹配 `sub` 和 `aud` 的验证方使用。最多 64 个字符。发现文档在 `x_cursor_sub_claims_supported` 中列出支持的名称；目前为 `team_id`、`organization_id` 和 `environment_id`。不支持的名称会被拒绝。如果该声明对此 Agent 没有值，例如个人账号上的 `team_id`，签发会失败，而不会回退到默认主体。 |

Cursor 不会对受众做允许列表。你的验证方必须拒绝意外的 `aud` 值。

### 响应

```json
{
  "token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6Ii4uLiJ9...",
  "expires_at": 1785500000
}
```

| 字段 | 说明 |
| :----------- | :------------------------------------------------------- |
| `token` | 已签名的 JWT。 |
| `expires_at` | 以 Unix 秒表示的过期时间。与 JWT 的 `exp` 声明一致。 |

Token 的有效期为 **5 分钟**。没有刷新端点。需要新 token 时再次签发。

### 声明何时出现

[安装脚本](https://cursor.com/docs/cloud-agent/setup.md)可以在同一个套接字上签发。Token 只包含签发时已有值的声明：在编码轮次开始之前没有 `turn_id` 和 `turn_start`，在运行记录分支之前没有 `branch_name`。所有者、团队和仓库声明从 Agent 创建之时起就会设置。

如果启动后立刻缺少套接字，请重试连接。

## 自托管 worker

[自托管机器](https://cursor.com/docs/cloud-agent/self-hosted.md)的 worker 在你用 `--identity-socket` 启动它时提供相同的 API：

```bash
agent worker --pool gpu --identity-socket start
```

该标志默认关闭。在 worker 命令上、`start` 之前传入它。Pool worker 和 [My Machines](https://cursor.com/docs/cloud-agent/self-hosted/my-machines.md) worker 使用同一个标志。在 My Machines worker 上省略 `--pool`：

```bash
agent worker --identity-socket start
```

设置该标志后，worker 为每个已认领的 Agent 打开一个套接字，并在 Agent 的 shell 中把 `CURSOR_AGENT_SOCKET` 设为该套接字的路径。请求和响应约定以及错误码与 Cursor 管理的 VM 一致。[速率限制和错误](https://cursor.com/docs/cloud-agent/identity.md#rate-limits-and-errors)列出了两者的签发限制。

这些 token 带有 `agent_runtime: self_hosted`，以及相同的所有者、团队和仓库声明。Worker 提供 token API。它不提供 [Agent 元数据](https://cursor.com/docs/cloud-agent/metadata.md)。以该 worker 的操作系统用户身份运行的任何进程都可以在该认领的套接字上签发。见[信任模型](https://cursor.com/docs/cloud-agent/identity.md#trust-model)。

## 验证 token

把这些 URL 发布给你的身份提供方或资源服务器：

| 端点 | URL |
| :-------- | :-------------------------------------------------------- |
| 签发者 | `https://api.cursor.com` |
| 发现 | `https://api.cursor.com/.well-known/openid-configuration` |
| JWKS | `https://api.cursor.com/keys` |

[认领 token](https://cursor.com/docs/cloud-agent/identity.md#claim-tokens)使用不同的签发者。

```bash
curl -sS https://api.cursor.com/.well-known/openid-configuration
curl -sS https://api.cursor.com/keys
```

发现遵循 [OpenID Connect Discovery 1.0](https://openid.net/specs/openid-connect-discovery-1_0.html)。Token 在 Agent VM 上签发，因此发现文档没有 `authorization_endpoint` 或 `token_endpoint`。

### 较早的签发者 URL

Cursor 仍在 `https://api2.cursor.sh/cloud-agent/identity` 提供第二份发现文档。已签发的 token 不再携带该签发者。请把验证方指向 `https://api.cursor.com`。

至少检查：

- 使用 RS256 和 JWKS 的 `kid` 验证签名
- `iss` 为 `https://api.cursor.com`
- `aud` 是你的服务所期望的受众
- `nbf` / `exp`，并留出少量时钟偏差余量（`nbf` 比 `iat` 早 5 秒）
- 你的策略所使用的 `sub` 或其他声明

发现文档包含 `x_cursor_audience_bound: true`。每个 token 都是为调用方提供的 `aud` 签发的。不要接受为其他受众签发的 token。发现文档还会发布 `x_cursor_sub_claims_supported`，即签发请求可以用 `sub_claim` 投影到 `sub` 中的声明名称。

## JWT 声明

头部：`alg=RS256`、`typ=JWT`，以及 `kid`。

| 声明 | 是否始终存在 | 说明 |
| :------------------------- | :-------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `iss` | 是 | `https://api.cursor.com`，或在[认领 token](https://cursor.com/docs/cloud-agent/identity.md#claim-tokens)上为 `https://api.cursor.com/self-hosted/claims`。 |
| `sub` | 是 | 稳定的所有者主体：默认为 `user:<id>` 或 `service_account:<id>`，或者当签发请求设置了 `sub_claim` 时为 `<claim>:<value>`（例如 `team_id:123`）。不是邮箱。 |
| `aud` | 是 | 来自签发请求的受众。 |
| `iat` | 是 | 签发时间，Unix 秒。 |
| `nbf` | 是 | 生效时间（`iat - 5`）。 |
| `exp` | 是 | 过期时间（`iat + 300`）。 |
| `jti` | 是 | 每次签发的唯一 id。 |
| `cloud_agent_id` | 是 | Cloud Agent id（`bcId`）。 |
| `nonce` | 否 | 仅当签发请求包含它时才出现。 |
| `agent_runtime` | 是 | 在 Cursor 管理的 Cloud Agent VM 上为 `managed`，在[自托管机器](https://cursor.com/docs/cloud-agent/identity.md#self-hosted-workers) worker 上为 `self_hosted`。 |
| `worker_id` | 仅自托管 | 自托管 worker 的 id：提供套接字的 worker，或一次[认领](https://cursor.com/docs/cloud-agent/identity.md#claim-tokens)的 `workerId`。 |
| `owner_email` | 已知时 | 小写的用户邮箱。允许列表请优先使用 `sub` 或 `owner_user_id`；邮箱可以改变。 |
| `owner_user_id` | 已知时 | Cursor 用户 id，十进制字符串。 |
| `owner_service_account_id` | 已知时 | 当服务账号拥有该 Agent 时的服务账号 id。 |
| `team_id` | 已知时 | 所属团队 id，十进制字符串。 |
| `turn_id` | 轮次进行中 | 此次编码轮次的 id。与 `cloud_agent_id` 不同，后者是 Cloud Agent id（`bcId`）。 |
| `turn_start` | 轮次进行中 | 运行开始时间，Unix 秒。 |
| `repo_url` | 已知时 | 主仓库，形式为 `host/path`，例如 `github.com/acme/widgets`。主机名小写，没有协议、凭证、端口、查询或 `.git` 后缀。在多仓库 Agent 上，这只是主仓库。 |
| `repo_urls` | 已知时 | 工作区中的每个仓库，形式与 `repo_url` 相同。主仓库在前，其余排序。仅当集合已知完整时才出现。缺失表示集合未知，而不是只有一个仓库。 |
| `repo_count` | 已知时 | `repo_urls` 中的条目数量。恰好在 `repo_urls` 出现时出现。当你的验证方只能匹配单个值时，把它与 `repo_url` 一起使用（`repo_count == 1`）。 |
| `branch_name` | 已知时 | 当前分支。 |
| `environment_id` | 已知时 | 此次运行所使用的 Cursor 环境的 id。 |
| `source` | 已知时 | Agent 如何启动，例如 `WEBSITE`、`API`、`SLACK` 或 `AUTOMATIONS`。 |
| `automation_id` | 自动化时 | 当 `source` 为自动化时的自动化 id。 |

`repo_url` 是主仓库。要把 Agent 限制在特定仓库，请用 `repo_urls` 钉住完整集合。

## 认领 token

[Team Pool 控制器](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#build-your-own-controller)在[认领待处理请求](https://cursor.com/docs/cloud-agent/api/endpoints.md#claim-a-pending-request)时、在任何 worker 启动之前，可以获得一个 token。该 token 告诉你的系统这次认领对应哪个 Agent 和所有者，以便你在创建机器或向它提供凭证之前同时检查两者。控制器用它的服务账号 API 密钥请求该 token。不涉及套接字。内置控制器会在每次认领时用 [`--identity-audience`](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#identity-tokens) 请求一个。

### 请求认领 token

把 `identityToken` 加入认领请求体。`audience` 为必填。每次认领都发送一个新的 `nonce`。

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

响应在 `identityToken.token` 中携带 JWT：

```json
{
  "id": "bc-00000000-0000-0000-0000-000000000002",
  "workerId": "pw_123",
  "identityToken": {
    "token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6Ii4uLiJ9...",
    "expiresAt": "2026-10-02T21:05:00.000Z"
  }
}
```

认领 token 的有效期为 **5 分钟**，并且 `expiresAt` 与 JWT 的 `exp` 声明一致。如果 Cursor 无法签发该 token，则不会认领任何内容。[认领待处理请求](https://cursor.com/docs/cloud-agent/api/endpoints.md#claim-a-pending-request)列出了字段限制、错误码，以及认领响应丢失时该怎么做。

### 认领 token 的签发者

认领 token 有自己的签发者。在套接字上签发的 token 仍使用 `https://api.cursor.com`。两个签发者发布同一份 JWKS。

| 端点 | URL |
| :-------- | :--------------------------------------------------------------------------- |
| 签发者 | `https://api.cursor.com/self-hosted/claims` |
| 发现 | `https://api.cursor.com/self-hosted/claims/.well-known/openid-configuration` |
| JWKS | `https://api.cursor.com/keys` |

认领发现文档列出与根文档相同的 `claims_supported`。它的 `x_cursor_sub_claims_supported` 为空，因为认领请求不接受 `sub_claim`。

为 `https://api.cursor.com` 配置的验证方或身份提供方，例如 [AWS IAM 示例](https://cursor.com/docs/cloud-agent/identity.md#aws-iam-example)，会拒绝认领 token。只把认领签发者添加到应当接受它们的服务。

认领 token 携带与套接字 token 相同的 [JWT 声明](https://cursor.com/docs/cloud-agent/identity.md#jwt-claims)，但有这些差异：

- `iss` 为 `https://api.cursor.com/self-hosted/claims`。
- `agent_runtime` 为 `self_hosted`。
- `worker_id` 是认领中的 `workerId`。
- `sub` 和所有者声明指向 Agent 的所有者，而不是发起认领的服务账号。`sub` 始终是 `user:<id>` 或 `service_account:<id>`。

### 验证认领 token

至少检查：

- 使用 RS256 和 JWKS 的 `kid` 验证签名
- `iss` 为 `https://api.cursor.com/self-hosted/claims`
- `aud` 是你发送的受众
- `nbf` / `exp`，并留出少量时钟偏差余量
- `nonce` 是你随此次认领发送的值
- `cloud_agent_id` 是被认领的 `id`，`worker_id` 是被认领的 `workerId`
- `team_id` 是你的团队

用 `sub` 或 `owner_user_id` 识别用户，不要用 `owner_email`。邮箱可以改变。

Cursor 不跟踪某个 token 是否被使用过。要使每个 token 只能使用一次，请存储它的 `jti`（或你的 nonce），并在 token 过期前拒绝重复使用。

## 信任模型

该 token 标识的是 Cloud Agent 运行，而不是机器上的某个特定进程。任何能够到达该套接字的进程都可以签发 token：Agent、它运行的代码，以及 Hooks。把权限范围限定为你愿意授予该次运行整体的权限。

你不能选择 token 对应哪个 Agent。Cursor 根据这次运行填写声明，因此机器上的进程不能为另一个 Agent 签发 token。

在自托管 worker 上，Agent 和 worker 进程以同一个操作系统用户身份运行。以该用户身份运行的任何进程都可以为已认领的运行签发 token。把角色范围限定为你愿意授予该机器上该用户的权限。

[认领 token](https://cursor.com/docs/cloud-agent/identity.md#claim-tokens)点名被认领的 Agent 及其所有者。任何持有该池服务账号密钥的人都可以在密钥的仓库范围内认领团队的待处理请求，并获得一个点名启动每一项请求的队友的 token。把密钥留在控制器上，并且只为你所期望的受众接受认领 token。

## 速率限制和错误

每个已认领的 Agent 每分钟最多可以签发 **30 个 token**。Cursor 管理的 VM 允许最多 5 个 token 的突发，然后是每分钟 25 个。自托管 worker 允许最多 10 个的突发。套接字同时最多接受 8 个连接。在 Cursor 管理的 VM 上，这 8 个连接与 [Agent 元数据](https://cursor.com/docs/cloud-agent/metadata.md)共享。缓存 token 直到它过期，而不是每次调用都签发。

对 `429`、`503`、`500`、`502` 和 `504` 使用退避重试。把 `403` 视为致命错误：此 Agent 不允许签发。

错误正文带有机器可读的代码。无效请求错误（400、404、405、413 和 415）还包含一个 `usage` 字符串，复述完整的请求约定。速率限制和饱和错误只有代码：

```json
{ "error": "invalid_aud", "usage": "POST /v1/tokens/oidc ..." }
```

```json
{ "error": "rate_limited" }
```

| HTTP | `error` | 时机 |
| :-------- | :--------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 400 | `invalid_json`、`invalid_aud`、`invalid_nonce` 或 `invalid_sub_claim` | 错误的请求体 |
| 404 | `not_found` | 错误的路径 |
| 405 | `method_not_allowed` | 不是 `POST` |
| 413 | `body_too_large` | 请求体超过 4 KB |
| 415 | `invalid_content_type` | 缺少 `Content-Type`，或不是 JSON |
| 429 | `rate_limited` | 超过每个 Agent 的签发配额；遵守 `Retry-After` |
| 503 | `saturated` | 连接过多；遵守 `Retry-After` |
| 500 | `host_error` | 内部错误；重试 |
| 502 / 504 | `backend_unreachable` | Cursor 无法签发该 token；重试 |
| 其他 | `backend_error` | Cursor 拒绝了签发。`400` 表示需要修正请求（例如不支持的 `sub_claim`，或对该 Agent 没有值的声明）。`403` 是致命错误。`503` 可以重试。 |

## AWS IAM 示例

当你希望 AWS 通过 `AssumeRoleWithWebIdentity` 信任由 Cursor 签名的 JWT 时，使用 OIDC。对于更简单的、由 Cursor 管理的代入角色流程（External ID + `CURSOR_AWS_ASSUME_IAM_ROLE_ARN`），见[使用 AWS IAM 角色](https://cursor.com/docs/cloud-agent/setup.md#using-aws-iam-roles)。

1. 创建一个 IAM OIDC 身份提供方，其 URL 为 `https://api.cursor.com`。
2. 把受众设为 `sts.amazonaws.com`（或你的角色所期望的另一个受众）。
3. 只为你打算允许的主体和团队信任该角色。

示例信任策略：

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::123456789012:oidc-provider/api.cursor.com"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "api.cursor.com:aud": "sts.amazonaws.com"
        },
        "StringLike": {
          "api.cursor.com:sub": "user:*"
        }
      }
    }
  ]
}
```

用精确的 `sub` 收紧它，例如一个用户的 `user:42`，或作为服务账号运行的 Agent 的 `service_account:<id>`。AWS 信任策略只匹配 `aud` 和 `sub`，因此要通过使用 `"sub_claim":"team_id"` 签发，并匹配投影后的主体，把信任范围限定到一个团队：

```json
"StringEquals": {
  "api.cursor.com:aud": "sts.amazonaws.com",
  "api.cursor.com:sub": "team_id:123"
}
```

信任策略在 Cursor 管理的 token 和自托管 token 上看到相同的 `aud` 和 `sub`。你不能把 `agent_runtime` 放进 `sub`。

按照当前的 [AWS IAM OIDC](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_oidc.html) 说明创建提供方并处理指纹。

Agent 使用 `"aud":"sts.amazonaws.com"` 签发（当信任策略匹配团队主体时再加上 `"sub_claim":"team_id"`），并把 JWT 传给 STS。如果使用[网络允许列表](https://cursor.com/docs/cloud-agent/security-network.md#network-access)，请允许 `sts.amazonaws.com`（以及你调用的任何区域 STS 主机）。

## 其他验证方

相同的 token 可用于任何符合 OIDC 的验证方：

- **GCP** Workload Identity Federation
- **Azure** 联合凭证 / Entra ID
- **Vault** JWT/OIDC 身份验证
- 验证 RS256 JWT 的内部 API

把提供方指向发现 URL，要求你的受众，并根据 `sub`、`team_id` 或 `cloud_agent_id` 等声明进行授权。要把 Agent 限制在特定仓库，请用 `repo_urls` 钉住完整集合；`repo_url` 只点名主仓库。

签发只使用本地套接字。把 JWT 与 AWS、GCP、Azure 或你的服务交换，仍然需要到那些主机的出站网络访问。

## 相关页面

- [Agent 元数据](https://cursor.com/docs/cloud-agent/metadata.md)：Cursor 管理的 VM 套接字上的键值运行元数据
- [密钥与网络](https://cursor.com/docs/cloud-agent/security-network.md)：仪表盘密钥和出站控制
- [Cloud Agent 设置](https://cursor.com/docs/cloud-agent/setup.md#using-aws-iam-roles)：由 Cursor 管理的 AWS 角色代入
- [安全概览](https://cursor.com/docs/cloud-agent/security.md)：隔离和访问模型
- [服务账号](https://cursor.com/docs/account/enterprise/service-accounts.md)：当 Agent 作为团队服务账号运行时

---

## 站点地图

[所有文档页面概览](/llms.txt)
