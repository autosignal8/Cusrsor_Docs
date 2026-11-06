> 原文：https://cursor.com/docs/integrations/azure-devops

# Azure DevOps

Azure DevOps 集成把 Azure DevOps Services 仓库连接到 [Cloud Agents](https://cursor.com/docs/cloud-agent.md) 和 [Bugbot](https://cursor.com/docs/bugbot.md)。

Azure DevOps 集成处于公开 beta。它支持位于 `dev.azure.com` 的 Azure DevOps Services。不支持 Azure DevOps Server。

## 支持的功能

Cloud Agents 会克隆你的代码、在分支上工作，并打开 pull request。Bugbot 会审查 pull request，并把发现的问题作为评论发布。

以下功能尚不支持 Azure DevOps：

- [Automations](https://cursor.com/docs/cloud-agent/automations.md)
- [Bugbot autofix](https://cursor.com/docs/bugbot.md#autofix)
- [Security Agents](https://cursor.com/docs/security-agents.md)，包括 Security Reviewer 和 Vulnerability Scanner

这些功能目前可用于 [GitHub](https://cursor.com/docs/integrations/github.md)。Azure DevOps 支持已在路线图上。

## 设置

你需要对希望在 Cursor 中使用的 Azure DevOps 组织和仓库拥有访问权限。

1. 前往[仪表盘中的集成](https://cursor.com/dashboard/integrations)
2. 点击 Azure DevOps 旁边的 **Connect**
3. 使用你用于 Azure DevOps 的 Microsoft 账号登录
4. 查看 Microsoft Entra OAuth 同意屏幕并批准访问
5. 返回 Cursor，从你的 Azure DevOps 组织中选择仓库
6. 在你希望 Cursor 使用的仓库上配置 Cloud Agents

要断开 Azure DevOps 账号，请返回集成仪表盘并点击 **Disconnect Account**。

## 设置 Bugbot

设置需要 Microsoft Entra ID 管理员授予租户管理员同意。

Bugbot 通过 Cursor 在你的租户中预配的 Microsoft Entra 服务主体来审查 Azure DevOps 的 pull request。该服务主体会安装 Bugbot 所监听的 service hooks，读取 pull request 的 diff，并发布审查评论和构建状态。每个 Azure DevOps 组织的设置都按以下顺序执行一次：

1. **Project Collection Administrator** 从[仪表盘中的集成](https://cursor.com/dashboard/integrations)连接 Azure DevOps，并在 [Bugbot in Automations](https://cursor.com/automations/from-cursor/bugbot) 中为某个仓库开启 Bugbot。这会发现组织的 Entra 租户，并自动预配 Cursor 服务主体。预配需要一次性的 [Microsoft Entra 管理员同意](https://cursor.com/docs/integrations/azure-devops.md#grant-microsoft-entra-admin-consent)。
2. **项目管理员**把 Cursor 服务主体加入[每个你希望审查的项目](https://cursor.com/docs/integrations/azure-devops.md#add-the-service-principal-to-your-projects)的 **Project Administrators** 组。Bugbot 需要项目管理权限才能管理它的 service hooks。
3. Azure DevOps 应用权限更改会有若干分钟的延迟。权限更改后立刻失败的开关，几分钟后即使不做改动也可能成功。

当缺少某一步时，仓库开关会失败，错误会指出缺少的是哪一步。修复之后，再次开启该仓库。

Azure DevOps 上的 Bugbot 适用于团队仓库。不支持个人范围的仓库。

### 授予 Microsoft Entra 管理员同意

在 Cursor 的服务主体能够存在于你的目录之前，Microsoft Entra ID 管理员必须一次性授予租户管理员同意。同意需要 Global Administrator、Application Administrator 或 Cloud Application Administrator 角色。它作用于整个租户，因此仅凭仓库管理员无法完成。

启用时的错误会告诉你何时缺少同意。你也可以自己构造同意链接：

```text
https://login.microsoftonline.com/{your-tenant-id}/v2.0/adminconsent?client_id=29df4809-337a-4255-bd57-d4a1476b0e65&redirect_uri=https://cursor.com/azure-devops-connected&scope=499b84ac-1321-427f-aa17-267ca6975798/.default%20offline_access%20openid%20profile
```

把 `{your-tenant-id}` 替换为 [Microsoft Entra 管理中心](https://entra.microsoft.com)中的租户 ID。`client_id` 是 Cursor 的应用程序 ID。`scope` 覆盖 Azure DevOps API（`499b84ac-1321-427f-aa17-267ca6975798` 是 Microsoft 为它分配的标识符），以及标准登录 scope。Cursor 不请求任何 Microsoft Graph 数据。服务主体会保留在你的租户中，你可以随时在 Entra 中撤销它。

管理员同意之后，Microsoft 会把他们送回 `cursor.com/azure-devops-connected`，页面会确认这次授权。授权保存在你的租户中，因此下一次启用仓库就会完成设置。

较早的一次同意只覆盖 Cursor 当时请求的权限。如果在你的租户同意之后 Cursor 又增加了权限，设置会在中途失败，重新连接也无法修复。请再次打开同意链接，以刷新这次授权。

由个人 Microsoft 账号支撑的 Azure DevOps 组织无法承载该服务主体。请先将该组织连接到 Microsoft Entra ID。

### 让服务主体访问你的组织

同意之后，Cursor 会把服务主体加入你的 Azure DevOps 组织，并为它请求 Basic 访问级别。这两步都会自动完成，使用的是连接 Azure DevOps 的那个管理员账号。随后，该服务主体会出现在 **Organization settings** → **Users** 下。

有两件事会阻止这一过程：

- **所连接的账号无法向该组织添加用户。** 请以该组织的管理员身份重新连接 Azure DevOps。
- **没有可用的 Basic 访问级别。** 腾出一个 Basic 席位，或在 **Organization settings** → **Users** 下把一个席位分配给该服务主体。

### 把服务主体加入你的项目

Service hooks 是 Azure DevOps 用来通知 Bugbot 有 pull request 的方式，只有项目管理员可以管理它们。请把 Cursor 服务主体加入每个你希望审查的项目的 **Project Administrators** 组，位置在 **Project settings** → **Permissions**。仅有仓库的读取权限不够。

权限更改可能需要几分钟才会生效，因此授权后立刻失败的开关，几分钟后即使不做改动也可能成功。

### 在仓库上启用 Bugbot

1. 打开 [Bugbot in Automations](https://cursor.com/automations/from-cursor/bugbot)
2. 在安装列表中找到你的 Azure DevOps 仓库
3. 为每个你希望审查的仓库开启 Bugbot

开启某个仓库时，Bugbot 会安装它的 service hooks；关闭时则会移除。为某个仓库关闭 Bugbot，或断开 Azure DevOps，会立刻停止对该仓库的审查。

### 审查如何触发

Bugbot 会在每个 pull request 打开和更新时进行审查。也可以通过评论 `cursor review` 或 `bugbot run`，按需请求一次审查。

评论触发有一项 Azure DevOps 限制。它只对这样的人有效：其 Azure DevOps 登录地址与拥有该仓库的团队中的某个 Cursor 账号相匹配。其他人的评论不会启动审查。自动审查没有这一限制，并且会覆盖每一位作者。

### 筛选哪些作者会被审查

默认情况下，Bugbot 会审查每一位作者。要缩小范围，请在该仓库的 Bugbot 设置中关闭 **Run for All Authors**，然后选择 **Only Review PRs by...** 或 **Skip PRs by...**。

该列表接受的是 **Azure DevOps sign-in addresses**，不是用户名，字段上也是这样标注的。登录地址类似于 `taylor@contoso.com`，也就是组织管理员在 **Organization settings** → **Users** 下看到的值。用户名匹配不到任何人，因此对该作者，Bugbot 会保持沉默。

### 与其他提供方的差异

仓库设置和仓库规则在 Azure DevOps 上可用。以下这些不可用：

- **Auto-Enable for New Repositories。** 请你自己为每个新仓库开启 Bugbot。
- **Automatically Learn Rules。** 你手工编写的仓库规则仍然生效。
- **Personal Bugbot settings。** 改为由仓库设置应用于所有人。
- **Autofix。** Bugbot 会报告发现的问题，但不会打开修复提交。

### 构建状态与分支策略

Bugbot 会在每个已审查的 pull request 上，以上下文 `cursor-bugbot/review` 发布一条构建状态。各结论的含义见 [CI 检查状态](https://cursor.com/docs/bugbot.md#ci-check-statuses)。

如果把该上下文设为必需状态的分支策略，请把策略的 **Reset conditions** 设为 **Reset status whenever there are new changes**。否则，较早一次 push 产生的状态会在新提交落地之后继续满足该策略。

### 从较早的 Bugbot 设置迁移

在 August 2026 之前为 Azure DevOps 启用过 Bugbot 的组织，使用的是较早的按仓库设置。仍使用该设置的仓库，在你开启时会显示 "This Azure DevOps repository still uses the earlier Bugbot setup."。

迁移是自动的。以该组织的管理员身份连接 Azure DevOps，然后在 [Bugbot in Automations](https://cursor.com/automations/from-cursor/bugbot) 中再次开启该仓库。Cursor 会移除较早的 service hooks，并在同一步骤中完成新的设置。

如果 Cursor 报告无法移除较早的设置，请你自行移除：

1. 在 Azure DevOps 中打开 **Project settings** → **Service hooks**
2. 删除投递到 `https://api2.cursor.sh/azure_devops_webhook` 的 Cursor **Web Hooks** 订阅
3. 在 [Bugbot in Automations](https://cursor.com/automations/from-cursor/bugbot) 中再次开启该仓库

迁移之后，新设置的前提条件仍然适用。你的租户需要一次性的 [Microsoft Entra 管理员同意](https://cursor.com/docs/integrations/azure-devops.md#grant-microsoft-entra-admin-consent)，服务主体需要[在你的组织中拥有 Basic 访问级别](https://cursor.com/docs/integrations/azure-devops.md#give-the-service-principal-access-to-your-organization)，并加入[每个项目的 Project Administrators 组](https://cursor.com/docs/integrations/azure-devops.md#add-the-service-principal-to-your-projects)。

## 仓库 URL

Cursor 支持以下格式的 Azure DevOps Services 仓库 URL：

```text
https://dev.azure.com/{organization}/{project}/_git/{repository}
```

Azure DevOps 使用组织、项目和仓库这三层结构。Cursor 会在 Azure DevOps 组织下，把仓库显示为 `{project}/{repository}`。

如果你的组织仍在使用 `*.visualstudio.com` URL，请在 Azure DevOps 中打开该仓库，复制 `dev.azure.com` URL，然后再添加到 Cursor。

当你从本地检出启动 Cloud Agent，而它的 Git remote 是 Azure DevOps 的 SSH remote（`ssh.dev.azure.com` 或 `vs-ssh.visualstudio.com`），或者是 `*.visualstudio.com` URL 时，Cursor 会把该 remote 转换成 `dev.azure.com` 格式。

## 权限

Cursor 通过 Microsoft Entra OAuth 连接 Azure DevOps。该连接让 Cursor 能够：

| 访问 | 用途 |
| ------------------------------ | ------------------------------------------------------------------------------ |
| **组织和项目** | 列出你可以访问的 Azure DevOps 组织、项目和仓库 |
| **代码仓库** | 克隆仓库并创建工作分支 |
| **Pull requests** | 打开、更新和合并由 Cloud Agents 创建的 pull request |

Bugbot 会增加第二个身份。Cursor 在你的租户中预配一个 Microsoft Entra 服务主体，并用它执行每一次 Bugbot 操作。因此，当设置该集成的人更换角色或离开后，审查仍会继续。

| 操作 | Azure DevOps 身份 |
| -------------------------------------------------------------------------- | ------------------------------- |
| Bugbot 的审查评论、行内评论和构建状态 | Cursor 服务主体 |
| Bugbot service hook 的安装与移除 | Cursor 服务主体 |
| Cloud Agents 的 Git clone、分支 push、提交以及 pull request 创建 | 已连接的 Azure DevOps 用户 |

## 排查问题

### 我看不到我的 Azure DevOps 仓库

- 确认该仓库托管在 `dev.azure.com` 的 Azure DevOps Services 上。
- 确认你所连接的 Microsoft 账号可以访问该组织、项目和仓库。
- 如果你的 Microsoft 访问权限发生了变化，请从集成仪表盘重新连接 Azure DevOps。

### Cloud Agent 无法打开 pull request

- 确认所选的 Azure DevOps 仓库已在 Cursor 中连接。
- 检查你的 Azure DevOps 账号能否在目标仓库中创建分支和 pull request。
- 如果目标分支阻止创建或更新 pull request，请检查分支策略。

### 仓库 URL 被拒绝

请使用 Azure DevOps 中的 `dev.azure.com` 仓库 URL。此集成不接受 Azure DevOps Server 的 URL。

### Bugbot 不审查我的 pull request

- 确认该仓库已在 [Bugbot in Automations](https://cursor.com/automations/from-cursor/bugbot) 中开启。
- 确认该仓库属于一个 Cursor 团队。Azure DevOps 上的 Bugbot 不会审查个人范围的仓库。
- 确认 Cursor 服务主体位于该项目的 **Project Administrators** 组中，路径为 **Project settings** → **Permissions**。
- 如果你筛选了作者，请确认 pull request 作者是按 Azure DevOps 登录地址列出的，而不是按用户名。

### 在 pull request 上评论不会触发审查

评论触发只有在你的 Azure DevOps 登录地址与拥有该仓库的团队中的某个 Cursor 账号匹配时才会生效。属于同一个人的两个地址，在这里仍然算作两个人。

请核对 Azure DevOps 个人资料上的登录地址与 Cursor 账号上的电子邮件。如果缺少该地址，请让团队管理员邀请它。无论是否匹配，自动审查都会继续运行。

### Bugbot 忽略了我的个人设置

个人 Bugbot 设置尚不适用于 Azure DevOps 仓库。仓库设置适用。请在 [Bugbot in Automations](https://cursor.com/automations/from-cursor/bugbot) 中，于仓库级别设置你想要的行为。

### 新的 push 之后，必需的 Bugbot 状态仍然是绿色

打开 `cursor-bugbot/review` 状态对应的分支策略，把 **Reset conditions** 设为 **Reset status whenever there are new changes**。否则 Azure DevOps 会保留较早的状态，策略就会在 Bugbot 尚未审查的提交上通过。

## 后续步骤

Azure DevOps 集成连接之后，配置使用它的功能：

- [Bugbot](https://cursor.com/docs/bugbot.md)：自动 PR 审查，找出 bug 和安全问题
- [Cloud Agents](https://cursor.com/docs/cloud-agent.md)：在云端、在你的仓库上运行的 AI Agent
- [Cloud Agent 设置](https://cursor.com/docs/cloud-agent/setup.md)：已保存的环境、多仓库设置、密钥和 Dockerfile

---

## 站点地图

[所有文档页面概览](/llms.txt)
