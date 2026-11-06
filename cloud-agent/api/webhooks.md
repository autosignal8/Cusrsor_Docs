> 原文：https://cursor.com/docs/cloud-agent/api/webhooks

# Webhooks

创建 Agent 时如果提供了 webhook URL，Cursor 会发送 HTTP POST 请求，通知你状态变化。目前只支持 `statusChange` 事件，具体是 Agent 进入 `ERROR` 或 `FINISHED` 状态时。

## Webhook 验证

为确认 webhook 请求确实来自 Cursor，请验证每个请求所带的签名：

### 请求头

每个 webhook 请求包含以下请求头：

- **`X-Webhook-Signature`**：包含 HMAC-SHA256 签名，格式为 `sha256=<hex_digest>`
- **`X-Webhook-ID`**：此次投递的唯一标识符（便于记录日志）
- **`X-Webhook-Event`**：事件类型（目前只有 `statusChange`）
- **`User-Agent`**：始终设为 `Cursor-Agent-Webhook/1.0`

### 签名验证

要验证 webhook 签名，请计算预期签名，并与收到的签名比较：

```javascript
const crypto = require("crypto");

function verifyWebhook(secret, rawBody, signature) {
  const expectedSignature =
    "sha256=" +
    crypto.createHmac("sha256", secret).update(rawBody).digest("hex");

  return signature === expectedSignature;
}
```

```python
import hmac
import hashlib

def verify_webhook(secret, raw_body, signature):
    expected_signature = 'sha256=' + hmac.new(
        secret.encode(),
        raw_body,
        hashlib.sha256
    ).hexdigest()

    return signature == expected_signature
```

计算签名时，始终使用原始请求正文（任何解析之前）。

## 载荷格式

webhook 载荷以 JSON 发送，结构如下：

```json
{
  "event": "statusChange",
  "timestamp": "2024-01-15T10:30:00Z",
  "id": "bc_abc123",
  "status": "FINISHED",
  "source": {
    "repository": "https://github.com/your-org/your-repo",
    "ref": "main"
  },
  "target": {
    "url": "https://cursor.com/agents?id=bc_abc123",
    "branchName": "cursor/add-readme-1234",
    "prUrl": "https://github.com/your-org/your-repo/pull/1234"
  },
  "summary": "Added README.md with installation instructions"
}
```

请注意，部分字段是可选的，仅在有值时才会包含。

## 最佳实践

- **验证签名**：始终验证 webhook 签名，以确认请求来自 Cursor
- **处理重试**：如果你的端点返回错误状态码，webhook 可能会重试
- **尽快返回**：尽快返回 2xx 状态码
- **使用 HTTPS**：生产环境中的 webhook 端点始终使用 HTTPS URL
- **保存原始载荷**：保存原始 webhook 载荷，便于调试和日后验证

---

## 站点地图

[所有文档页面概览](/llms.txt)
