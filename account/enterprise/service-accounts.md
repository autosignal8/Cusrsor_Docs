> 原文：https://cursor.com/docs/account/enterprise/service-accounts

# Service Accounts

Service accounts 在 [Enterprise 方案](https://cursor.com/docs/enterprise.md)上可用。

Service accounts 是非人类账户，让团队能够大规模、安全地自动化由 Cursor 驱动的工作流。借助 service accounts，你可以调用 API、对 [CLI](https://cursor.com/docs/cli/overview.md) 进行身份验证，并调用 [Cloud Agents](https://cursor.com/docs/cloud-agent.md)，而无需把集成绑定到个别开发者的个人账户。

## 为什么使用 service accounts

随着团队用 Cursor Cloud Agents、API 和 CLI 找到自动化编程任务的新方式，集中、安全的自动化变得关键。Service accounts 通过以下方式解决这一点：

- **与个人解耦**：人员和角色变化时，自动化仍继续运行
- **安全的凭据管理**：轻松轮换 API 密钥，而不打断工作流
- **集中的访问控制**：管理员在一处管理所有 service account 权限
- **归属与可审计性**：把 Cloud Agent 运行关联到发起的服务或系统

## 关键功能

### 不需要额外席位

Service accounts 包含在 Enterprise 方案中，不另收费。它们不占用席位许可证。

### 用量消耗

Service accounts 像人类用户一样从团队用量池消耗用量。所有用量都会被跟踪，并在团队的分析和账单中可见。

### Cloud Agent 集成

Service accounts 可以以编程方式发起 [Cloud Agent](https://cursor.com/docs/cloud-agent.md) 运行。这支持如下自动化场景：

- Linear 中创建的工单触发 Cloud Agent 实现一个功能
- Sentry 中的错误发起 Cloud Agent 调查并修复问题
- 内部工程服务启动迁移或重构任务

### 管理员可见性

由 service accounts 发起的 Cloud Agent 运行对所有团队管理员可见。这确保对组织中自动化工作流的可见性和监督。

### 仓库访问

Service accounts 可以在任何已通过 [Cursor GitHub 应用](https://cursor.com/docs/integrations/github.md)授权的仓库上发起 Cloud Agent 运行。

GitHub 集成必须在团队级别连接，service accounts 才能访问仓库。如果你有个人 GitHub 集成但没有团队级集成，service accounts 将无法发起 Cloud Agent 运行。

要在团队级别连接 GitHub：

1. 前往 **Dashboard** → **Settings** → **Integrations**
2. 把 Cursor GitHub 应用连接到你的组织
3. 授权你希望 service accounts 访问的仓库

仓库访问由团队 GitHub 应用安装所配置的权限管理。

## 创建 service account

管理员可以从 [Cursor 仪表板](https://cursor.com/dashboard)创建和管理 service accounts。

1. 前往 **Dashboard** → **Settings** → **API Keys** → **Service Accounts**
2. 点击 **New Service Account**
3. 输入 service account 的名称和可选描述
4. 点击 **Create**

创建 service account 时会生成 API 密钥。请立即复制此密钥——它只显示一次，之后无法取回。

安全地存储 API 密钥。如果丢失，需要轮换它以生成新密钥。

## 管理 API 密钥

每个 service account 都可以关联 API 密钥。你可以：

- **查看掩码密钥**：查看每个密钥的最后几个字符以便识别
- **轮换密钥**：生成新密钥并使旧密钥失效
- **编辑名称和描述**：重命名活动的 service account 或更新其描述。其现有 API 密钥继续有效
- **设置支出限额**：限制 service account 在每个计费周期可以花费的金额
- **暂停 service accounts**：使某个 service account 的所有 API 密钥停止工作，而不撤销它们，之后可以恢复
- **归档 service accounts**：归档 service account 并撤销其所有 API 密钥

### 轮换 API 密钥

要轮换 API 密钥：

1. 前往 **Dashboard** → **Settings** → **API Keys** → **Service Accounts**
2. 找到该 service account，点击其 API 密钥旁的轮换图标
3. 立即复制新密钥

旧密钥会立即失效。更新任何使用旧密钥的集成。

## 支出限额

管理员可以限制每个 service account 在每个计费周期的花费。

1. 前往 **Dashboard** → **Settings** → **API Keys** → **Service Accounts**
2. 打开 service account 旁的操作菜单，点击 **Edit spend limit**
3. 选择 **Limit spend each billing period** 并输入金额，或选择 **No limit**
4. 点击 **Save**

Service Accounts 表在 **Spend limit** 列显示每个账户的当前限额。新的 service accounts 默认没有限额。

当 service account 达到限额时，其请求会在该计费周期的剩余时间内被阻止，并返回 “Service account spend limit reached” 错误。团队管理员可以随时提高或移除限额，以立即恢复访问。

支出限额不适用于 Bugbot：Bugbot 用量通过 Bugbot 订阅计费，因此 Bugbot service accounts 从不会因支出限额而被阻止。

## 通过 API 使用 service accounts

Service accounts 使用其 API 密钥进行身份验证。向 [Cloud Agents API](https://cursor.com/docs/cloud-agent/api/endpoints.md) 发请求时，在 `Authorization` 头中使用该密钥：

```bash
curl -X POST https://api.cursor.com/agents \
  -H "Authorization: Bearer YOUR_SERVICE_ACCOUNT_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "repo": "owner/repo",
    "prompt": "Implement the feature described in issue #123"
  }'
```

完整 API 参考见 [Cloud Agents API 文档](https://cursor.com/docs/cloud-agent/api/endpoints.md)。

## 通过 CLI 使用 service accounts

Service accounts 可以把 API 密钥设为 `CURSOR_API_KEY` 来对 [Cursor CLI](https://cursor.com/docs/cli/overview.md) 进行身份验证。这是在 CI/CD 流水线、cron 任务和其他无法进行浏览器登录的非交互环境中运行 CLI 的推荐方式。

```bash
export CURSOR_API_KEY=your_service_account_api_key

# Run a task in a CI pipeline
agent -p --force "Refactor the authentication module to use OAuth 2.0"
```

同一环境变量在任何上下文中都有效，包括本地开发。所有身份验证选项见 [CLI 身份验证文档](https://cursor.com/docs/cli/reference/authentication.md)，脚本模式见[无头 CLI 指南](https://cursor.com/docs/cli/headless.md)。

## 安全最佳实践

- **定期轮换密钥**：为 service accounts 建立密钥轮换计划
- **使用描述性名称**：用用途命名 service accounts（例如 “Linear Integration”、“Sentry Auto-Fix”）
- **限制范围**：为不同的自动化工作流创建单独的 service accounts
- **监控用量**：在团队分析仪表板中查看 service account 活动
- **撤销未使用的账户**：归档不再使用的 service accounts

## 暂停 service account

暂停 service account 会使其 API 密钥停止工作，直到你恢复它。密钥会保留，因此恢复时无需轮换即可恢复访问。

要暂停 service account：

1. 前往 **Dashboard** → **Settings** → **API Keys** → **Service Accounts**
2. 点击 service account 旁的暂停图标
3. 确认暂停操作

已暂停的账户显示 **Suspended** 徽章。要恢复，点击旁边的播放图标并确认。

## 归档 service account

归档 service account 会：

- 撤销与该账户关联的所有 API 密钥
- 使使用这些密钥的任何集成中断
- 保留账户记录以便审计

要归档 service account：

1. 前往 **Dashboard** → **Settings** → **API Keys** → **Service Accounts**
2. 点击 service account 旁的归档图标
3. 确认归档操作

在 Service Accounts 页面点击 **Show Archived** 可以查看已归档账户。这有助于保持团队所用 service accounts 的完整审计轨迹。

### Service accounts 在 Enterprise 方案上可用

用面向 API 和 Cloud Agents 的非人类账户，大规模自动化由 Cursor 驱动的工作流。

---

## 站点地图

[所有文档页面概览](/llms.txt)
