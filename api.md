> 原文：https://cursor.com/docs/api

# Cursor API 概览

Cursor 提供多种 API，便于你以编程方式访问团队数据、AI 编程 Agent 以及分析数据。

## 可用的 API

| API                                                                                   | 说明                                                                                     | 可用性                         |
| ------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- | ------------------------------ |
| [Admin API](https://cursor.com/docs/account/teams/admin-api.md)                       | 管理团队成员、设置、用量数据、支出和模型访问。构建自定义仪表盘和监控工具。             | Enterprise 团队                |
| [Analytics API](https://cursor.com/docs/account/teams/analytics-api.md)               | 全面了解团队的 Cursor 用量、AI 指标、活跃用户和模型用量。                               | Enterprise 团队                |
| [AI Code Tracking API](https://cursor.com/docs/account/teams/ai-code-tracking-api.md) | 在 commit 和改动级别跟踪 AI 生成的代码贡献，用于归因和分析。                            | Enterprise 团队                |
| [Bugbot API](https://cursor.com/docs/bugbot.md#api)                                   | 触发 Bugbot 审查，并获取每次审查的分析数据。                                            | Enterprise 团队                |
| [Cloud Agents API](https://cursor.com/docs/cloud-agent/api/endpoints.md)              | 以编程方式创建并管理 AI 编程 Agent，用于自动化工作流和代码生成。                        | Beta（包含 Cloud Agents 的方案） |
| [Origin API](https://cursor.com/docs/api/origin.md)                                   | 处理 Origin 仓库、commit、检查、pull request 和应用安装。                               | 早期 Beta                      |
| [TypeScript SDK](https://cursor.com/docs/sdk/typescript.md)                           | 从 TypeScript 运行 Cursor Agent，用同一接口覆盖本地和云端运行时。                       | 所有用户                       |
| [Python SDK](https://cursor.com/docs/sdk/python.md)                                   | 从 Python 运行 Cursor Agent，提供用于本地和云端运行时的同步与异步客户端。               | 所有用户                       |
| [SDK Bridge](https://cursor.com/docs/sdk/bridge.md)                                   | 基于开放的 bridge 协议和独立二进制文件，用其他语言构建 Agent SDK。                      | 所有用户                       |

Cloud Agents API 和 SDK 运行的是 Cursor Agent 工作流（工作区上下文、工具、命令和编辑）。它们不是独立的模型推理或 chat-completions API。[Cursor Router](https://cursor.com/docs/cursor-router.md) 会在你使用 Auto / `auto-smart` 时为这些 Agent 运行选择模型；参见 [TypeScript SDK 中的 Router](https://cursor.com/docs/sdk/typescript.md#cursor-router) 或 [Python SDK](https://cursor.com/docs/sdk/python.md#cursor-router)。

## 身份验证

Admin API、Analytics API、AI Code Tracking API 和 Bugbot API 接受 Basic 身份验证。Cloud Agents API 接受 Basic 或 Bearer 身份验证。Origin API 通过 Origin CLI 或 Origin App 使用 Bearer 凭据。

### Basic 身份验证

在 Basic 身份验证中，把你的 API key 用作用户名（密码留空）：

```bash
curl https://api.cursor.com/teams/members \
  -u YOUR_API_KEY:
```

或者直接设置 Authorization 头：

```bash
Authorization: Basic {base64_encode('YOUR_API_KEY:')}
```

### Bearer 身份验证（Cloud Agents API）

[Cloud Agents API](https://cursor.com/docs/cloud-agent/api/endpoints.md) 也接受 `Authorization: Bearer <key>` 头。两种方案的行为完全相同，使用你的 HTTP 客户端更容易实现的那种即可：

```bash
curl https://api.cursor.com/v1/me \
  -H "Authorization: Bearer YOUR_API_KEY"
```

### 创建 API key

团队管理员可以在仪表盘的 API Keys 页面创建并管理 API key。

#### Admin API 与 AI Code Tracking API

1. 前往 **cursor.com/dashboard** → **API Keys**
2. 点击 **New API Key**
3. 为 key 起一个描述性名称（例如「用量仪表盘集成」）
4. 立即复制生成的 key。之后将无法再次看到它

Key 格式：`crsr_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`

所需 scope：`admin:*`

#### Analytics API

从 [Cursor Dashboard → API Keys](https://cursor.com/dashboard/api) 生成 API key。

#### Cloud Agents API

从 [Cursor Dashboard → API Keys](https://cursor.com/dashboard/api) 创建用户 API key，或使用 Team settings 中的 [服务账号 API key](https://cursor.com/docs/account/enterprise/service-accounts.md)。

API key 绑定到你的组织，所有管理员都可以查看。key 不受原创建者账号状态的影响。

#### Origin API

对于以用户身份发起的请求，使用 Origin CLI 登录，或向它提供个人用户 API key。CLI 在调用 Origin 之前，会用该 key 换取一个短期访问 token。具有 `admin:*` scope 的 Team Admin API key 无法通过 Origin 的身份验证。应用使用 app JWT 和安装访问 token。参见 [Origin API 身份验证](https://cursor.com/docs/api/origin/reference/authentication.md)。

## 速率限制

所有 API 都实施速率限制，以保证公平使用和系统稳定。限制按已认证的用户、团队或组织分别计算，且大多数限制的范围是单个端点。除非某个端点写明了不同的限制，默认值为每分钟 20 次请求。

### 各 API 的速率限制

| API                      | 端点类型                                                                  | 速率限制                                      |
| ------------------------ | ------------------------------------------------------------------------- | --------------------------------------------- |
| **Admin API**            | 大多数端点                                                                | 每分钟 20 次请求                              |
| **Admin API**            | `/teams/filtered-usage-events` 和 `/organizations/filtered-usage-events` | 每分钟 60 次请求                              |
| **Admin API**            | `/teams/user-spend-limit`                                                 | 每分钟 250 次请求                             |
| **Admin API**            | `/teams/user-spend-limits`                                                | 每分钟 20 次请求                              |
| **Organization API**     | 大多数端点                                                                | 每个端点每分钟 20 次请求                      |
| **Organization API**     | `GET /organizations/teams/{teamId}/grok-bot/operations/*`                 | 每个端点每分钟 120 次请求                     |
| **Analytics API**        | 大多数团队级端点                                                          | 每分钟 100 次请求                             |
| **Analytics API**        | `/analytics/team/conversation-insights`                                   | 每分钟 20 次请求                              |
| **Analytics API**        | 按用户划分的端点                                                          | 每分钟 50 次请求                              |
| **AI Code Tracking API** | 所有端点                                                                  | 每个端点每分钟 20 次请求                      |
| **Bugbot API**           | `/bugbot/review`                                                          | 每分钟 30 次请求                              |
| **Bugbot API**           | 带 `dryRun: true` 的 `/bugbot/review`                                     | 每分钟 10 次请求（在触发限制之外另计）        |
| **Cloud Agents API**     | 所有端点                                                                  | 标准速率限制                                  |

### 速率限制响应

当你超出速率限制时，会收到 `429 Too Many Requests` 响应。Admin API 和 Organization API 的响应包含 `Retry-After: 60` 以及以下正文：

```json
{
  "code": "error",
  "message": "Rate limit exceeded"
}
```

## 缓存

多个 API 支持使用 ETag 的 HTTP 缓存，以减少带宽占用并提高性能。

### 支持的 API

- **Analytics API**：所有端点（包括团队级和按用户）都支持 HTTP 缓存
- **AI Code Tracking API**：端点支持 HTTP 缓存

### 缓存如何工作

1. **初次请求**：向任意受支持的端点发起请求
2. **响应包含 ETag**：API 在响应中返回 `ETag` 头
3. **后续请求**：在 `If-None-Match` 头中包含 `ETag` 值
4. **304 Not Modified**：如果数据未变化，你会收到没有正文的 `304 Not Modified` 响应

### 示例

```bash
# Initial request
curl -X GET "https://api.cursor.com/analytics/team/dau" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -D headers.txt

# Response includes: ETag: "abc123xyz"

# Subsequent request with ETag
curl -X GET "https://api.cursor.com/analytics/team/dau" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "If-None-Match: \"abc123xyz\""

# Returns 304 Not Modified if data hasn't changed
```

### 缓存时长

- 缓存时长：15 分钟（`Cache-Control: public, max-age=900`）
- 响应包含 `ETag` 头
- 在后续请求中包含 `If-None-Match` 头，以便在数据未变化时收到 `304 Not Modified`

### 好处

- **减少带宽占用**：304 响应不含正文
- **更快的响应**：避免处理未变化的数据
- **对速率限制友好**：304 响应不计入速率限制
- **更好的性能**：对频繁轮询的端点尤其有用

## 最佳实践

### 1. 实现指数退避

收到 429 响应后，等待一段时间再重试，并逐步增加延迟：

```python
import time
import requests

def make_request_with_backoff(url, headers, max_retries=5):
    for attempt in range(max_retries):
        response = requests.get(url, headers=headers)
        
        if response.status_code == 429:
            # Exponential backoff: 1s, 2s, 4s, 8s, 16s
            wait_time = 2 ** attempt
            print(f"Rate limited. Waiting {wait_time}s before retry...")
            time.sleep(wait_time)
            continue
            
        return response
    
    raise Exception("Max retries exceeded")
```

### 2. 将请求分散到不同时间

把 API 调用分散到一段时间里，避免集中发起突发请求：

- 将批处理任务安排在不同的时间间隔运行
- 处理大型数据集时，在请求之间加入延迟
- 使用队列系统来平滑流量峰值

### 3. 利用缓存

**对于 Analytics API 和 AI Code Tracking API：**

这些 API 支持使用 ETag 的 HTTP 缓存。关于如何使用 ETag 减少带宽占用并避免不必要的请求，请参见上方的 [缓存](https://cursor.com/docs/api.md#caching) 一节。

**主要好处：**

- 减少带宽占用
- 数据未变化时响应更快
- 不计入速率限制（针对 304 响应）

在 Analytics API 中使用日期快捷方式（`7d`、`30d`），而不是时间戳，以获得更好的缓存支持。

### 4. 监控你的用量

跟踪你的请求模式，以便保持在限制以内：

- 记录 API 调用的时间戳和响应码
- 为 429 响应设置告警
- 监控每日和每周的用量趋势
- 根据实际需要调整轮询间隔

### 5. 合理分批

对于支持分页的端点：

- 使用合适的分页大小，让每次请求获取更多数据
- 对于 Analytics API 的按用户端点：使用 `users` 参数筛选特定用户
- 对于大规模数据提取：在可用时使用 CSV 端点（它们能高效地流式传输数据）

### 6. 以合适的间隔轮询

不要对更新不频繁的端点过度轮询：

- **Admin API** `/teams/daily-usage-data`：最多每小时轮询一次（数据按小时聚合）
- **Admin API** `/teams/filtered-usage-events`：最多每小时轮询一次（数据按小时聚合）
- **Admin API** `/organizations/pooled-usage`：最多每小时轮询一次（数据按小时聚合）
- **Admin API** `/organizations/filtered-usage-events`：最多每小时轮询一次（数据按小时聚合）
- **Analytics API**：使用日期快捷方式（`7d`、`30d`）以获得更好的缓存支持
- **AI Code Tracking API**：数据以近实时方式写入，但每隔几分钟轮询一次即可

### 7. 妥善处理错误

为所有 API 调用实现适当的错误处理：

```javascript
async function fetchAnalytics(endpoint) {
  try {
    const response = await fetch(`https://api.cursor.com${endpoint}`, {
      headers: {
        'Authorization': `Basic ${btoa(API_KEY + ':')}`
      }
    });
    
    if (response.status === 429) {
      // Rate limited - implement backoff
      throw new Error('Rate limit exceeded');
    }
    
    if (response.status === 401) {
      // Invalid API key
      throw new Error('Authentication failed');
    }
    
    if (response.status === 403) {
      // Insufficient permissions
      throw new Error('Enterprise access required');
    }
    
    if (!response.ok) {
      throw new Error(`API error: ${response.status}`);
    }
    
    return await response.json();
  } catch (error) {
    console.error('API request failed:', error);
    throw error;
  }
}
```

## 常见错误响应

所有 API 都使用标准 HTTP 状态码：

### 400 Bad Request

请求参数无效，或缺少必填字段。

```json
{
  "error": "Bad Request",
  "message": "Some users are not in the team"
}
```

### 401 Unauthorized

API key 无效或缺失。

```json
{
  "error": "Unauthorized",
  "message": "Invalid API key"
}
```

### 403 Forbidden

API key 有效，但权限不足（例如在非 Enterprise 方案上使用 Enterprise 功能）。

```json
{
  "error": "Forbidden",
  "message": "Enterprise access required"
}
```

### 404 Not Found

请求的资源不存在。

```json
{
  "error": "Not Found",
  "message": "Resource not found"
}
```

### 429 Too Many Requests

已超出速率限制。请实现指数退避。

```json
{
  "error": "Too Many Requests",
  "message": "Rate limit exceeded. Please try again later."
}
```

### 500 Internal Server Error

服务器端错误。如果持续出现，请联系支持人员。

```json
{
  "error": "Internal Server Error",
  "message": "An unexpected error occurred"
}
```

---

## 站点地图

[所有文档页面概览](/llms.txt)
