> 原文：https://cursor.com/docs/cloud-agent/settings

# Cloud Agents 设置

工作区管理员可以从 [Cloud Agents 仪表盘](https://cursor.com/dashboard/cloud-agents)配置 Cloud Agents。

## 环境管理

**Environments** 视图列出你的团队可用的已保存环境。环境的范围可以是一个仓库，或一组仓库。

打开一个环境以查看：

- 它适用的仓库
- 它使用的是快照还是 `.cursor/environment.json`
- 在[构建](https://cursor.com/docs/cloud-agent/builds.md)期间运行的安装脚本
- 运行时密钥和构建密钥
- 网络访问设置
- 版本历史、构建和设置运行

当你希望 Cursor 检查当前环境并提出新的设置时，使用 **Update with Agent**。当你希望 Cursor 从头开始设置环境时，使用 **New Setup Run**。使用版本历史中的 **Restore**，使先前的环境版本再次生效。

要从脚本管理环境，请使用 Cloud Agents API 中的[环境端点](https://cursor.com/docs/cloud-agent/api/endpoints.md#environments)。

**Builds** 标签页显示可供 Cloud Agents 使用的已准备环境版本。你可以查看日志、触发一次构建、选择或固定当前生效的构建，并从某一次特定构建启动 Agent。详情见 [Cloud Agent 构建](https://cursor.com/docs/cloud-agent/builds.md)。

## 默认设置

- **Default model**：未指定模型时，运行所使用的模型。选择 Cloud Agents 可用的任意模型。
- **Default repository**：为空时，Agent 会请用户选择仓库。在这里提供一个仓库，用户就可以跳过该步骤。
- **Base branch**：Agent 创建 pull request 时从中分出的分支。留空则使用仓库的默认分支。

## 网络访问设置

控制 Cloud Agents 可以到达哪些网络资源。用户和团队设置支持三种模式：

- **Allow all network access**：没有域名限制。
- **Default + allowlist**：[默认域名](https://cursor.com/docs/agent/security/run-modes.md#network-access)，以及你添加的任何域名。
- **Allowlist only**：仅限你明确添加的域名。

用户、团队管理员和环境所有者都可以配置网络访问。环境级设置可以继承用户或团队策略、添加环境允许列表，或定义自己的访问模式。完整详情见[网络访问](https://cursor.com/docs/cloud-agent/security-network.md)。

## 安全设置

所有安全选项都需要管理员权限。

- **Display agent summary**：控制 Cursor 是否显示 Agent 的文件 diff 图像和代码片段。如果你不希望在侧边栏中暴露文件路径或代码，请关闭此项。
- **Display agent summary in external channels**：把前一个开关扩展到 Slack 或你已连接的任何外部渠道。
- **Team follow-ups**：控制团队成员是否可以向团队中其他用户创建的 Cloud Agents 发送后续消息。参见下方的[团队跟进](https://cursor.com/docs/cloud-agent/settings.md#team-follow-ups)。
- **Automatically allow Projects to place their work**：让 [Project](https://cursor.com/docs/agent/projects.md) 中的 Agent 在 Project 所有者能够到达的任何[自托管](https://cursor.com/docs/cloud-agent/self-hosted.md)机器或池上开始工作，而无需先询问。关闭时，Agent 会在使用新的机器或池之前，在 Project 的聊天中请求批准。每个团队默认关闭。

## 团队功能设置

团队管理员可以为他们的团队启用或禁用这些功能：

- **Computer use**：控制 Agent 是否可以使用计算机交互能力（仅 Enterprise 团队可用）。

更改会立即保存，并立刻影响新的 Agent。

对于长时间运行的工作，在 Composer 中使用 [`/goal`](https://cursor.com/docs/agent/overview.md#goals-with-goal)。在 Cursor 应用中，`/goal` 需要较新的版本。

### 团队跟进

团队成员可以向同一团队中其他用户创建的 Cloud Agents 发送后续消息。当队友启动了一个 Agent，而你需要在他们不在时纠正方向、补充上下文或继续这项工作时，这很有用。

跟进建立在 Agent 可见性之上。队友必须能够查看该 Agent，才能发送跟进：他们必须属于同一个 Cursor 团队，并且需要自己拥有该 Agent 仓库的访问权限。参见[与团队共享 Agent](https://cursor.com/docs/cloud-agent.md#share-agents-with-your-team)。

团队管理员从 [Cloud Agents 安全设置](https://cursor.com/dashboard/cloud-agents)用三个选项控制此行为：

| 设置 | 行为 |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Disabled** | 只有原创建者可以向自己的 Agent 发送后续消息。不允许团队跟进。 |
| **Service accounts only** | 团队成员可以向由[服务账号](https://cursor.com/docs/account/enterprise/service-accounts.md)创建的 Agent 发送后续消息，但不能向其他人类用户创建的 Agent 发送。 |
| **All** | 任何团队成员都可以向团队中的任何 Agent 发送后续消息，无论是谁创建的。 |

### 横向移动与密钥暴露

启用团队跟进意味着，用户可以影响一个使用*另一位用户*的密钥和凭证运行的 Cloud Agent 的执行。一条后续消息可以指示 Agent 读取环境变量、把密钥打印到日志、把凭证推送到外部端点，或使用原创建者的访问 token 执行操作。

权限有限的团队成员可以通过指挥一个持有更高权限用户密钥的 Agent 来提升自己的访问权限。请像对待共享的 SSH 密钥或服务凭证一样谨慎对待此设置。

---

## 站点地图

[所有文档页面概览](/llms.txt)
