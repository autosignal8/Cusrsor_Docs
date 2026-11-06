> 原文：https://cursor.com/docs/security-agents

# Security Agents

Security Agents 会扫描你的代码，查找安全 bug、有风险的模式和漏洞。

在 Automations 中配置 Security Agents。每种 Agent 类型都有自己的页面：[Security Review](https://cursor.com/automations/from-cursor/security-review) 和 [Vulnerability Remediation](https://cursor.com/automations/from-cursor/vulnerability-remediation)。

## 工作原理

Security Agents 包含两种由 Cursor 管理的 Agent 类型：

- **Security Review** 在 pull request 合并前进行检查。用它在代码审查期间捕获漏洞。
- **Vulnerability Remediation** 扫描仓库里现有的代码库，并用修复来修补它。用它查找并修复既有漏洞、长期存在的问题，以及 PR 审查中遗漏的问题。

两种 Agent 类型都运行在 Automations 平台上，并且需要 Cloud Agents。

## 设置

在 Automations 中打开 [Security Review](https://cursor.com/automations/from-cursor/security-review) 或 [Vulnerability Remediation](https://cursor.com/automations/from-cursor/vulnerability-remediation) 页面，以配置该 Agent 类型。

### 源代码管理支持

Security Reviewer 和 Vulnerability Scanner 支持 Origin、GitHub、GitLab 和 Bitbucket Cloud 上的仓库。

| 源代码管理提供方 | Security Review | Vulnerability Remediation | Bugbot |
| :------------------------------------------------------------------------------- | :-------------- | :------------------------ | :------------------------------------------------------------------------------------------------------ |
| [Origin](https://cursor.com/docs/origin.md) | 支持 | 支持 | 支持 |
| [GitHub](https://cursor.com/docs/integrations/github.md) | 支持 | 支持 | 支持 |
| [GitLab](https://cursor.com/docs/integrations/gitlab.md) | 支持 | 支持 | 支持 |
| [Bitbucket Cloud](https://cursor.com/docs/integrations/bitbucket.md) | 支持 | 支持 | 支持 |
| [Bitbucket Data Center](https://cursor.com/docs/integrations/bitbucket.md#setup) | 不支持 | 不支持 | 在 Teams 和 Enterprise 上支持；仅自动审查；没有 `bugbot run`、`cursor review` 或 Autofix |
| [Azure DevOps Services](https://cursor.com/docs/integrations/azure-devops.md) | 不支持 | 不支持 | 支持（公开测试） |

### 触发器

**Security Reviewer Agent** 支持基于 Git 的 Automations 触发器，包括 pull request 和 merge request 事件。用这些触发器在代码变更时运行安全检查。

![Security Reviewer 基于 Git 的触发器配置](/docs-static/images/security-review/triggers.png)

**Vulnerability Scanner Agent** 支持基于 cron 的触发器。用这些触发器按重复计划扫描代码库，与 pull request 活动无关。

![Vulnerability Scanner 的 cron 触发器配置](/docs-static/images/security-review/vulnerability-scanner-triggers.png)

每个 Vulnerability Scanner 有一个计划。要更改它的运行时间，请编辑现有触发器。要按另一个计划扫描，请创建单独的 Vulnerability Scanner。跨越超过 1,000 个仓库的扫描可能触及提供方的速率限制，因此请把大范围拆成仓库范围更小的多个 Vulnerability Scanner。

### 安全检查

两种 Agent 类型都包含内置安全检查。根据你希望每种 Agent 审查的内容，启用或禁用单项检查。

### 自定义说明

使用自定义说明为每种 Agent 提供更多上下文。你可以描述要优先处理的问题类型、解释项目特定的安全预期，或定义 Agent 应如何表现。

### 工具和 MCP

两种 Agent 类型都支持工具和 MCP。Security Reviewer 在保存之前至少需要一个工具或 MCP。Vulnerability Scanner 可以在没有工具或 MCP 的情况下保存，并把发现报告到 [Automations 中的 Vulnerability Remediation 页面](https://cursor.com/automations/from-cursor/vulnerability-remediation)上的 **Remediation agents** 部分。

使用工具和 MCP，把 Security Agents 连接到团队跟踪安全工作的系统。

- 把漏洞发送到 Slack 频道、问题跟踪器或其他已连接的系统。
- 添加自定义说明，解释 Agent 应在何时以及如何使用每个 MCP。
- 在报告发现之前，从工具或 MCP 给 Agent 额外上下文。

### 环境设置

Security Agents 运行在 Cloud Agents 上，团队有两处例外。当 pull request 事件触发时，团队的 Security Reviewer 运行在 Cursor 托管的主机上，而不是 Cloud Agent；团队的 Vulnerability Scanner 也在那里运行扫描。[要求使用自托管机器](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#triggering-pool-agents)的团队会让两者都保持在 Cloud Agents 上。

你可以无需额外设置就使用 Cursor 的云。

## 在 Agent 中运行

在推送代码之前，使用 `/review-security` 或 `/review` Skill，从 Agent 运行 Security Agent。

**审查哪份 diff：** 默认情况下，`/review-security` 审查你的分支改动：相对于基分支的每一处改动，包括已提交和未提交的改动。如果希望反馈范围更窄，可以让它只审查未提交的改动。

**对照哪个分支：** `/review-security` 会与你的默认基分支比较。当基分支不是默认分支（例如 `main`）时，告诉 Agent 要对照哪个分支，或让它根据上下文推断。

![从 Agent 输入框运行 /review-security Skill](/docs-static/images/security-review/review-security-skill.png)

`/review` 和 `/review-security` 在 Cursor 3.7+、[cursor.com/agents](https://cursor.com/agents) 以及 [Cursor CLI](https://cursor.com/docs/cli/overview.md) 中可用。

## 计费

Security Agents 按团队用量级别计费：

- 用量计入团队的用量池。
- Agent 在共享的团队服务账号下运行，因此不会影响任何个人用户的用量。

## 分析

Security Agents 在各次 Agent 运行中跟踪三项关键指标。[Automations 中的 Security Review 页面](https://cursor.com/automations/from-cursor/security-review)会显示它们：

- **Vulnerabilities found**：Agent 报告的安全发现数量。
- **Issues fixed**：报告之后已解决的发现数量。
- **Resolution rate**：已报告发现中被修复的百分比。

为判断某个问题是否已修复，Cursor 使用 LLM 审查增量 diff，并评估被标记的问题是否已解决。

## 查看运行

每次 Agent 运行都会在 Automations 中跟踪。每种 Agent 类型的页面会列出它自己的 Agent 的 **Recent Runs**。用运行历史查看 Agent 何时运行、使用了哪些工具、最终状态，以及耗时多久。

打开一次运行，可以检查底层 Cloud Agent，了解 Agent 做了什么的更多细节。在 Cursor 托管主机上运行的审查或扫描没有 Cloud Agent。在 Security Review 页面的 **Recent Runs** 下找到托管审查，它会打开该审查会话的只读视图。

![Automations 中的 Security Agents 运行历史](/docs-static/images/security-review/recent-runs.png)

## Remediation Agents

Remediation Agent 会检查扫描器产生的发现，并帮助你验证和修复它们。

对于每条发现，Cursor 会启动一个 Agent，编写漏洞利用测试来验证该问题，并自动修补已确认的问题。

可以在 [Vulnerability Remediation](https://cursor.com/automations/from-cursor/vulnerability-remediation) 部分以及每个扫描器的详情页找到它。

### 工作原理

每条发现经过四个步骤：

1. **Flagged。** Vulnerability Scanner 报告该发现。
2. **Verified。** 验证 Agent 针对真实代码路径编写测试并运行它们。它返回三种判定之一：
   - **Confirmed**：测试确认了该问题。
   - **Refuted**：测试表明该问题不可能发生。
   - **Inconclusive**：Agent 未能得出结论。
3. **Tests PR。** 对于已确认的发现，验证 Agent 会打开一个包含其测试的草稿 pull request。在问题修复之前，这些测试会故意失败。
4. **Fix PR。** 修补 Agent 编写修复，对照测试进行检查，并打开一个草稿 pull request 供你审查。

Cursor 不会替你合并修复。是否合并始终由你决定。

### 修复设置

每个 Vulnerability Scanner 有两项修复设置。在 Automations 中该扫描器的设置里打开它们。

| 设置 | 作用 |
| :-------------------------------------- | :--------------------------------------------------------------------------- |
| **Verify new findings** | 为扫描器报告的每条新发现启动一个验证 Agent。 |
| **Open fix PRs for confirmed findings** | 为每条已确认的发现启动一个修补 Agent，由它打开一个草稿修复 PR。 |

### 审查发现

Vulnerability Remediation 部分按下一步需要把发现分组到各个标签页：

| 标签页 | 内容 |
| :---------------- | :--------------------------------------------------------------------------------------------------- |
| **Needs you** | 有待审查的修复 PR 的发现、等待修补的已确认发现，以及无结论的发现。 |
| **In flight** | 正在验证或修补的发现，以及已排队等待其中一项的发现。 |
| **Agent-refuted** | 验证 Agent 表明并非漏洞的发现。 |
| **Fixed** | 修复 PR 已合并的发现。 |
| **Not verified** | 尚未排队等待验证的发现。 |

打开一条发现，即可按需对它执行手动操作。

### Vulnerability Remediation API

Enterprise 团队可以使用 Vulnerability Remediation API 列出发现、请求验证和修复，并跟踪进度。在 [Cursor Dashboard → API Keys](https://cursor.com/dashboard/api) 创建 API 密钥，并使用 [Basic Authentication](https://cursor.com/docs/api.md#basic-authentication) 进行身份验证。

每个端点允许每个团队每分钟 20 次请求。列表端点使用游标分页：把响应中的 `nextCursor` 作为 `cursor` 传入以读取下一页。当 `nextCursor` 为 `null` 时，没有更多页面。

如果团队未启用 Vulnerability Remediation，每个端点都返回 `403 Forbidden`。

#### 列出发现

GET

`/security/findings`

返回团队的 Vulnerability Scanner 已报告的发现，先按仓库排序，再按发现 ID 排序。

需要具有 `read:*` 范围的 API 密钥。

##### 查询参数

`limit` number (optional)

每页的发现数量。默认值：`50`，最大：`100`。

`cursor` string (optional)

上一页的 `nextCursor`。

```bash
curl --get https://api.cursor.com/security/findings \
  -u YOUR_API_KEY: \
  --data-urlencode 'limit=50'
```

**响应：**

```json
{
  "findings": [
    {
      "findingId": "3f2b8c1e-7a4d-4e2f-9b6a-1c5d8e0f2a47",
      "repository": "github:your-org/your-repo",
      "severity": "high",
      "title": "Missing authorization check on invoice export",
      "location": "src/billing/export.ts",
      "status": "active"
    }
  ],
  "nextCursor": "Z2l0aHViOnlvdXItb3JnL3lvdXItcmVwbwozZjJi..."
}
```

`severity` 为 `critical`、`high` 或 `medium`。`status` 为 `active`，如果有人忽略了该发现则为 `dismissed`。

#### 请求验证和修复

POST

`/security/verifications`

让 Cursor 验证发现、修补它们，或两者都做。工作入队后请求即返回。Agent 会在几分钟内启动。

需要具有 `admin:*` 范围的 API 密钥。

##### 请求体

`findingIds` string\[] (required)

来自 `GET /security/findings` 的发现 ID。每次请求发送 1 到 50 个 ID。

`verify` boolean (required)

为 `true` 时，验证每一条尚未验证的发现。已经验证或正在验证的发现会保留其现有验证。

`patch` boolean (required)

已确认的发现是否获得修复 PR。将 `verify`、`patch` 或两者设为 `true`。

- **两者都设**：先验证，然后为已确认的发现打开修复 PR。
- **仅 `verify`**：验证，但不打开修复 PR。
- **仅 `patch`**：修补已有判定的发现，包括被否定的发现。

这些标志会覆盖扫描器的 **Open fix PRs for confirmed findings** 设置。

```bash
curl --request POST \
  --url https://api.cursor.com/security/verifications \
  -u YOUR_API_KEY: \
  --header 'Content-Type: application/json' \
  --data '{
    "findingIds": ["3f2b8c1e-7a4d-4e2f-9b6a-1c5d8e0f2a47"],
    "verify": true,
    "patch": true
  }'
```

**响应（`202 Accepted`）：**

```json
{
  "verifications": [
    {
      "findingId": "3f2b8c1e-7a4d-4e2f-9b6a-1c5d8e0f2a47",
      "verificationId": "3f2b8c1e-7a4d-4e2f-9b6a-1c5d8e0f2a47"
    }
  ],
  "skipped": []
}
```

保存每个 `verificationId` 以跟踪其进度。无法入队的发现会出现在 `skipped` 中，并带有原因：

- `finding_not_found`：没有此 ID 的发现。
- `not_verifiable`：无法验证，例如位于不支持的主机上。
- `not_verified`：尚无判定可供修补。请先请求 `verify`。

如果团队没有正在运行的 Vulnerability Scanner 来接受该请求，端点返回 `409 Conflict`：

```json
{
  "code": "error",
  "message": "No vulnerability scanner is running for this team"
}
```

#### 列出验证

GET

`/security/verifications`

返回团队的验证，最新的在前。

需要具有 `read:*` 范围的 API 密钥。

##### 查询参数

`limit` number (optional)

每页的验证数量。默认值：`50`，最大：`100`。

`cursor` string (optional)

上一页的 `nextCursor`。

```bash
curl --get https://api.cursor.com/security/verifications \
  -u YOUR_API_KEY: \
  --data-urlencode 'limit=50'
```

**响应：**

```json
{
  "verifications": [
    {
      "verificationId": "3f2b8c1e-7a4d-4e2f-9b6a-1c5d8e0f2a47",
      "findingId": "3f2b8c1e-7a4d-4e2f-9b6a-1c5d8e0f2a47",
      "repository": "github:your-org/your-repo",
      "commitSha": "9f3c2a1b7d8e4f5061728394a5b6c7d8e9f0a1b2",
      "severity": "high",
      "status": "completed",
      "verdict": "confirmed",
      "verifiedAt": "2026-10-20T18:04:11.000Z",
      "testsPrUrl": "https://github.com/your-org/your-repo/pull/128",
      "verificationAgent": {
        "id": "bc-5b1e9d2a-0c47-4f8e-a3d6-7e2b9c4f1a08",
        "url": "https://cursor.com/agents/bc-5b1e9d2a-0c47-4f8e-a3d6-7e2b9c4f1a08"
      },
      "patch": {
        "status": "completed",
        "agent": {
          "id": "bc-8d4a7f3c-2e19-4b6d-9a05-c1f7e8b3d264",
          "url": "https://cursor.com/agents/bc-8d4a7f3c-2e19-4b6d-9a05-c1f7e8b3d264"
        },
        "fixPrUrl": "https://github.com/your-org/your-repo/pull/129",
        "fixPrMerged": false
      }
    }
  ],
  "nextCursor": null
}
```

#### 获取一次验证

GET

`/security/verifications/{verificationId}`

返回一次验证。用它轮询请求直到完成。

需要具有 `read:*` 范围的 API 密钥。

响应是单个验证对象，形状与 `GET /security/verifications` 中的项相同。见[验证字段](https://cursor.com/docs/security-agents.md#verification-fields)。如果团队没有该 ID 的验证，端点返回 `404 Not Found`。

```bash
curl https://api.cursor.com/security/verifications/3f2b8c1e-7a4d-4e2f-9b6a-1c5d8e0f2a47 \
  -u YOUR_API_KEY:
```

#### 验证字段

| 字段 | 说明 |
| :------------------ | :------------------------------------------------------------------------- |
| `verificationId` | 验证的 ID。 |
| `findingId` | 此验证所检查的发现。 |
| `repository` | 仓库，形式为 `host:owner/repo`。 |
| `commitSha` | 验证所针对的提交。在得知之前为 `null`。 |
| `severity` | 扫描器报告的严重程度。 |
| `status` | 工作所处的状态。见下表。 |
| `verdict` | `confirmed`、`refuted`、`inconclusive`，或在验证运行期间为 `null`。 |
| `verifiedAt` | 得出判定的时间。在此之前为 `null`。 |
| `testsPrUrl` | 包含验证测试的草稿 pull request（如果已打开）。 |
| `verificationAgent` | 验证 Agent 的 ID 和 URL。在它启动之前为 `null`。 |
| `patch` | 修复；一旦 Cursor 决定是否修补。在此之前为 `null`。 |
| `patch.status` | 修复所处的状态。见下表。 |
| `patch.agent` | 修补 Agent 的 ID 和 URL。在它启动之前为 `null`。 |
| `patch.fixPrUrl` | 包含修复的草稿 pull request，打开之后可用。 |
| `patch.fixPrMerged` | 修复 PR 合并后为 `true`。 |

`status` 的值：

| 值 | 含义 |
| :---------- | :-------------------------------------------------------- |
| `queued` | 等待验证 Agent 启动。 |
| `verifying` | 验证 Agent 正在运行。 |
| `patching` | 验证已完成，修复已排队或正在进行。 |
| `completed` | 此发现没有更多正在运行的工作。 |

`patch.status` 的值：

| 值 | 含义 |
| :--------------- | :--------------------------------------------------------------- |
| `queued` | 等待修补 Agent 启动。 |
| `running` | 修补 Agent 正在处理修复。 |
| `completed` | 修补 Agent 已完成。查看 `fixPrUrl` 以获取修复 PR。 |
| `not_requested` | 没有为此发现请求修复。 |
| `false_positive` | 该发现被否定，因此不需要修复。 |
| `no_tests_pr` | 验证结束时没有判定或可供构建修复的测试。 |
| `failed` | 无法启动修补 Agent。 |

#### 错误

错误返回带有消息的 JSON 正文：

```json
{
  "code": "error",
  "message": "Vulnerability verification is not enabled for this team"
}
```

| 状态 | 时机 |
| :---------------------- | :----------------------------------------------------------------------------- |
| `400 Bad Request` | 请求体或查询无效，或该 cursor 不是由此 API 签发的。 |
| `401 Unauthorized` | API 密钥缺失，或不具备所需范围。 |
| `403 Forbidden` | 团队未启用 Vulnerability Remediation。 |
| `404 Not Found` | 该验证在你的团队中不存在。 |
| `409 Conflict` | 团队没有正在运行的 Vulnerability Scanner。 |
| `429 Too Many Requests` | 你对此端点超过了每分钟 20 次请求。 |

#### 验证并修复发现

1. 调用 `GET /security/findings`，挑选你要处理的发现。
2. 用它们的 `findingIds` 调用 `POST /security/verifications`。将 `verify` 和 `patch` 设为 `true`，以验证它们并为已确认的发现打开修复 PR。
3. 保存每个返回的 `verificationId`。
4. 轮询 `GET /security/verifications/{verificationId}`，直到 `status` 为 `completed`。
5. 审查 `patch.fixPrUrl` 处的 pull request。

## 相关页面

- [Bugbot](https://cursor.com/docs/bugbot.md) 在合并前审查 pull request 中的 bug。
- [Rollouts](https://cursor.com/docs/rollouts.md) 在每个 pull request 部署时进行监控，并报告它在每个环境中的健康状况。

---

## 站点地图

[所有文档页面概览](/llms.txt)
