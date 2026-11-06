> 原文：https://cursor.com/docs/cloud-agent/security

# 安全概览

本页说明 Cloud Agents 如何构建以及如何得到保护。它逐步说明 Agent 运行时发生什么、如何授予访问权限、代码和数据存放在哪里、如何隔离和加密，以及你在每个阶段拥有哪些控制。它回答团队对照自身安全要求评估 Cloud Agents 时会提出的问题。

配置参考，包括密钥类型、网络访问模式和出站 IP 范围，见[密钥与网络](https://cursor.com/docs/cloud-agent/security-network.md)。要在不使用长期密钥的情况下把 VM 联合到 AWS、GCP、Azure 或自定义验证方，见 [OIDC token](https://cursor.com/docs/cloud-agent/identity.md)。本页解释这些控制背后的模型；那些页面告诉你如何设置它们。

这是 Cursor 更广泛安全材料的配套说明。认证、子处理方和架构见 [Trust Center](https://trust.cursor.com/)。你在整个 Cursor 中拥有的控制见[安全与隐私加固](https://cursor.com/docs/enterprise/security-hardening.md)。Cursor 符合 SOC 2 Type 2，并承诺至少每年由信誉良好的第三方进行一次渗透测试。

## Cloud Agents 如何工作

Cloud Agent 是在 Cursor 云中的虚拟机里运行的编程 Agent，而不是在开发者的笔记本电脑上。该 VM 持有完整的开发环境：已克隆的仓库、已安装的依赖、已配置的密钥和网络访问。

单次运行经过这些阶段：

1. **启动。** 用户或集成从 Web 应用、IDE、CLI、API、Slack，或关联的 issue 或 pull request 启动一项任务。
2. **预配。** Cursor 为该 Agent 预配一台隔离的 VM，并把已授权的仓库克隆进去。
3. **运行。** Agent 在 VM 内运行代码和工具，并把进度、输出和产物流式传回给用户。
4. **持久化。** 对话状态、元数据和产物保存到 Cursor 管理的存储中，以便你审查并恢复该次运行。
5. **交接。** Agent 推送其分支并打开 pull request，供人在任何内容合并之前审查。除非你要求草稿，否则 pull request 会以可供审查的状态打开。
6. **回收。** 运行空闲后，VM 运行时资源会按生命周期计时器先休眠，然后删除。

## 访问与授权

Cloud Agents 通过 Cursor GitHub 或 GitLab App 访问你的代码，而不是通过任何单一个人的凭证。

- **管理员安装该应用。** 启用 Cloud Agents 需要同时在 Cursor 和你的 Git 提供方上拥有管理员权限。管理员在你的 Git 组织上安装 Cursor 应用，并只向你选择的仓库授予访问权限。
- **用户连接自己的账号。** 应用安装之后，每个想要启动 Agent 的用户都连接自己的 Git 账号。这是组织级安装之上的第二层，按用户划分。
- **访问权限是继承而来的，不会被扩大。** Cloud Agent 只能访问触发用户原本就能访问的仓库。启动 Agent 不会授予用户原本并不拥有的仓库访问权限。

团队管理员可以更进一步，用 [Protected Git Scopes](https://cursor.com/docs/enterprise/model-and-integration-management.md#protected-git-scopes) 把一个 Git 组织锁定到你的 Cursor 组织，这样只有你的团队才能在它的仓库上启动 Cloud Agents。

Cursor 员工无法访问 Cloud Agent VM 内部的代码。访问尝试由 Cursor 的安全团队监控。

## 隔离与基础设施

每个 Agent 运行在自己的 VM 边界内，而不是共享的进程沙箱。一个 Agent 看不到另一个 Agent 的代码、环境或状态。

- **按 Agent 划分的 VM。** 每个 Agent 都有专用环境，与其他 Agent 和其他用户隔离。
- **MicroVM 隔离。** 运行时工作区运行在基于 Firecracker 的 microVM 基础设施上。
- **账号级分离。** Cloud Agent VM 运行在与 Cursor 其余生产基础设施分开的 AWS 账号中，因此代码执行环境与 Cursor 的其他服务隔离。

## 加密

Cursor 对传输中和静态存储的 Cloud Agent 数据进行加密。

- **传输中。** 服务到服务以及客户端到服务的流量使用 TLS 1.2 或更高版本。
- **静态存储。** AES-256，并使用按 Agent 划分的密钥，使每个 Agent 的会话数据在自己的密钥下加密。
- **客户管理的密钥。** Enterprise 团队可以映射客户管理的 KMS 密钥（CMEK/BYOK），用于 Cloud Agent 的服务端加密，从而由你控制密钥轮换和访问。见[数据加密](https://cursor.com/docs/enterprise/privacy-and-data-governance.md#data-encryption)。

## 存储哪些数据、存在哪里、保存多久

Cloud Agent 会接触四类数据。每一类存放在不同的地方，并遵循自己的保留规则。

| 数据 | 包含的内容 | 存放位置 | 保留 |
| :--------------------- | :----------------------------------------------------------------------------------------------------- | :-------------------------------------------------------- | :-------------------------------------------------------------------------------------------------- |
| **运行时工作区** | 检出的仓库、构建产物，以及一次正在进行的运行的工具执行上下文 | 隔离的 Cloud Agent VM | 运行进入空闲后自动回收；当你发送后续提示词时，计时器会刷新 |
| **VM 快照** | VM 磁盘的时间点副本（包括已克隆的代码），用于启动和恢复，而无需重新克隆 | 活动 VM 之外的快照和缓存层，已加密 | 滚动的 30 天不活动窗口；每次启动或恢复都会延长它，然后自动删除 |
| **对话状态** | 构成对话记录的提示词、模型响应、工具调用、diff 上下文和演示产物 | Cursor 后端，使用按 Agent 划分的密钥加密 | 默认无限期保留，以便你回看并恢复运行；可以按需删除 |
| **密钥和 token** | 你配置的 Cloud Agent 密钥、OAuth token 和 API 凭证 | Cursor 后端中的加密凭证存储 | 一直保留，直到你删除或移除它们 |

[Delete Agent API](https://cursor.com/docs/cloud-agent/api/endpoints.md#delete-an-agent-permanently) 可以按需移除 Agent 的对话记录和产物。快照不能按需删除；它们遵循上面的 30 天不活动窗口。Enterprise 团队还可以用[保留策略](https://cursor.com/docs/cloud-agent/security-network.md#cloud-agent-retention-policies)限制对话的保留期限。关于保留和删除的完整细节，见[数据保留](https://cursor.com/docs/cloud-agent/security-network.md#data-retention)。

## 隐私与模型数据

Cloud Agents 在 [Privacy Mode](https://cursor.com/privacy-overview) 下运行。打开 Privacy Mode 后，Cursor 绝不会用 Cloud Agents 访问过的代码，或它们的运行所生成的提示词和响应来训练。大多数模型还运行在 Cursor 的零数据保留协议之下，因此提供方不会存储请求和响应，也不会用它们进行训练。按模型的细节和例外见[隐私与数据治理](https://cursor.com/docs/enterprise/privacy-and-data-governance.md)。

Cloud Agents 不支持 Legacy Privacy Mode，因为 Agent 在运行期间需要把代码和环境数据存放在云端。请在整个组织强制使用标准 Privacy Mode，使每次运行都继承其零数据保留保证。

## 自主性与提示词注入

Cloud Agents 会自动运行终端命令，以便在测试上迭代，而不必在每一步都停下来等待批准。这比前台 Agent 更自主，也改变了风险模型：在 Agent 所读取的内容中植入指令的攻击者（提示词注入攻击）可能试图让 Agent 把代码外泄到外部主机。见 [OpenAI 对 Cloud Agent 提示词注入风险的说明](https://platform.openai.com/docs/codex/agent-network#risks-of-agent-internet-access)。

约束这一风险的层次：

- **网络出站控制。** 把出站流量限制为默认集合加上你的允许列表，或仅限你的允许列表，这样被攻陷的 Agent 无处可发送数据。Enterprise 管理员可以在整个组织锁定该策略。见[网络访问](https://cursor.com/docs/cloud-agent/security-network.md#network-access)。
- **已脱敏的运行时密钥。** 把密钥标记为 [Runtime Secrets](https://cursor.com/docs/cloud-agent/security-network.md#runtime-secrets)，这样它们的值会从对话记录、工具输出和提交中剥离，并且不会到达模型。
- **文件排除。** 把敏感路径加入 [`.cursorignore`](https://cursor.com/docs/reference/ignore-file.md)，使它们不进入 Agent 的上下文。
- **人工介入的交接。** Agent 打开 pull request 供审查。在有人审查改动之前，不会合并任何内容。
- **签名提交。** 每个 Agent 提交都用由 HSM 支持的 Ed25519 密钥签名，并在 GitHub 上显示 “Verified” 徽章，因此 Agent 编写的改动可以归因，并可以满足要求签名提交的分支保护。见[签名提交](https://cursor.com/docs/cloud-agent/security-network.md#signed-commits)。

要进行更深入的防御，把这些与 [Hooks](https://cursor.com/docs/hooks.md) 搭配，在 Agent 生命周期节点上强制执行策略并记录活动，并让 [Bugbot](https://cursor.com/docs/bugbot.md) 或 [Security Agents](https://cursor.com/docs/security-agents.md) 在 Agent 输出交付之前审查它。

## 风险考量

| 风险 | 缓解措施 |
| :---------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **完整代码库位于云端** | 按 Agent 隔离的 VM、AES-256 加密，以及按生命周期计时器自动删除 VM 和快照。 |
| **第三方或内部人员访问** | Cursor 员工无法访问 Agent VM 中的代码，并且访问尝试受到监控。VM 运行在与 Cursor 其他服务分开的 AWS 账号中。 |
| **Agent 自主性** | 范围受仓库和触发用户的访问权限约束。外部可达范围限于已配置的工具和终端命令，由网络出站控制把关，并通过 pull request 审查。 |
| **网络访问与外泄** | 互联网访问默认开启，但可以限制为允许列表中的域名，最严可以只允许允许列表，并可以在整个组织锁定。 |
| **密钥暴露** | 加密的密钥存储、不进入模型的已脱敏运行时密钥、范围仅限于 Docker 构建的仅构建密钥，以及用于短期云联合的 [OIDC token](https://cursor.com/docs/cloud-agent/identity.md)。 |

## 可审计性

Cloud Agent 活动会被记录并可归因。

- **会话日志。** 运行会被记录，团队管理员可以从 [Cloud Agents 仪表盘](https://cursor.com/dashboard/cloud-agents)审查活动。
- **已归因的改动。** Agent 创建的每个提交和 pull request 都会归因，并在你的 Git 历史中可见，且提交经过签名和验证。
- **审计日志。** 身份验证和管理员事件会进入你的[审计日志](https://cursor.com/docs/enterprise/compliance-and-monitoring.md#audit-logs)，Enterprise 团队可以将其流式发送到 SIEM、webhook 或 S3。
- **运行诊断。** 内置的 [Cursor Cloud MCP](https://cursor.com/docs/cloud-agent/capabilities.md#cursor-cloud-mcp) 会公开一次运行的对话记录、运行事件、环境细节和设置日志。

## 数据删除

| 机制 | 移除的内容 | 方式 |
| :-------------------------------- | :----------------------------------------------- | :----------------------------------------------------------------------------------------------------------- |
| **Archive** | 在仪表盘中隐藏一个 Agent | 从[仪表盘](https://cursor.com/dashboard/cloud-agents)执行 Archive |
| **Delete Agent API** | Agent 的对话记录和产物 | [Delete Agent API](https://cursor.com/docs/cloud-agent/api/endpoints.md#delete-an-agent-permanently) |
| **快照到期** | VM 快照和缓存的代码 | 不活动 30 天后自动进行 |
| **保留策略（Enterprise）** | 早于你所选时间窗口的对话 | [保留策略](https://cursor.com/docs/cloud-agent/security-network.md#cloud-agent-retention-policies) |
| **账号删除** | 账号及其关联数据 | [删除账号](https://cursor.com/help/account-and-billing/delete-account.md) |

## 常见问题

### Cloud Agents 是否不如本地 Agent 安全？

它们的风险画像不同，并不是更差。让 Agent 在带有出站限制和最小权限的隔离沙箱中无人值守地运行，可以比开发者的笔记本电脑更严格。笔记本电脑通常拥有完整的互联网访问和更高的权限。

### Cursor 会永久存储整个仓库吗？

不会。Cursor 克隆仓库以运行 Agent，该克隆可以存在于 VM 快照中以加快以后的启动，但不会无限期保留。快照在不活动 30 天后删除。

### Cloud Agent 能否访问开发者无法访问的仓库？

不能。访问由触发该次运行的开发者的 Git 访问权限把关。Cloud Agent 无法访问该开发者原本就没有访问权限的仓库。

### 我们可以限制 Cloud Agents 能访问哪些仓库吗？

可以。Cloud Agents 只能访问你通过 Git 提供方连接所授权的仓库。你控制哪些仓库可用，管理员可以用 [Protected Git Scopes](https://cursor.com/docs/enterprise/model-and-integration-management.md#protected-git-scopes) 锁定范围。

### Cloud Agent 能否读取密钥和凭证？

通过仪表盘中的 Secrets 标签页配置密钥。它们在静态存储时用 KMS 加密，在传输中加密，并在运行时作为环境变量注入。把敏感值标记为 [Runtime Secrets](https://cursor.com/docs/cloud-agent/security-network.md#runtime-secrets)，使它们不进入对话记录、工具输出和提交。作为实践，不要把密钥放进仓库；如果敏感文件必须放在那里，请把它们加入 [`.cursorignore`](https://cursor.com/docs/reference/ignore-file.md)。对于云角色，请从 VM 签发 [OIDC token](https://cursor.com/docs/cloud-agent/identity.md)，而不是存储长期访问密钥。

### 我们可以审计 Cloud Agent 活动吗？

可以。会话会被记录，管理员可以从仪表盘审查活动，并且 Agent 创建的每个提交和 pull request 都会在你的 Git 历史中归因。Enterprise 团队可以把审计日志流式发送到 SIEM。

### 数据如何删除？

从仪表盘归档一个 Agent，或使用 [Delete Agent API](https://cursor.com/docs/cloud-agent/api/endpoints.md#delete-an-agent-permanently) 移除其对话记录和产物。完整的账号删除和 Enterprise 保留策略会按更宽的时间表移除数据。

## 相关页面

- [密钥与网络](https://cursor.com/docs/cloud-agent/security-network.md)：密钥类型、网络访问模式、出站 IP 范围和签名提交。
- [OIDC token](https://cursor.com/docs/cloud-agent/identity.md)：短期 JWT 和云联合。
- [隐私与数据治理](https://cursor.com/docs/enterprise/privacy-and-data-governance.md)：数据流、Privacy Mode 和加密。
- [安全与隐私加固](https://cursor.com/docs/enterprise/security-hardening.md)：你在 Cursor 各处配置的控制。
- [Trust Center](https://trust.cursor.com/)：认证、子处理方和架构。

本摘要用于帮助理解，并不凌驾于 [MSA](https://cursor.com/terms/msa)、[DPA](https://cursor.com/terms/dpa) 或其他有约束力的合同条款。

---

## 站点地图

[所有文档页面概览](/llms.txt)
