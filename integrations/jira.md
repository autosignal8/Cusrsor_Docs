> 原文：https://cursor.com/docs/integrations/jira

# Jira

通过 Cursor 的 Jira 集成，你可以把 Jira 工作项分配给 Cursor，或在 Jira 中提及 `@Cursor`，用 [Cloud Agents](https://cursor.com/docs/cloud-agent.md) 处理这些工作项。

## 开始使用

### 要求

安装 Jira 集成之前，请确认你具备：

- 已启用 Rovo 的 Jira Commercial Cloud
- 对你要安装该应用的 Jira 站点拥有管理员权限
- 对你要连接的团队拥有 Cursor 管理员权限
- 已将 GitHub、GitLab、Azure DevOps 或 Bitbucket 连接到 Cursor，以便访问仓库和 pull request

Cursor Jira 集成目前仅在 Cursor Teams 和 Enterprise 方案上可用。

Cursor Jira 集成目前不支持 Atlassian HIPAA 或 FedRAMP（包括 Government Cloud）实例。

### 安装

1. 以 Cursor 管理员身份，前往 [Cursor 集成](https://www.cursor.com/dashboard/integrations)

2. 点击 Jira 旁边的 *Connect*

3. 继续前往 [Atlassian Marketplace](https://marketplace.atlassian.com/apps/3903220956/cursor) 中的 Cursor 应用列表

4. 点击 *Get it now*

5. 选择你要安装 Cursor 的 Jira 站点，然后点击 *Review*

6. 查看该应用，然后点击 *Get it now*

7. 安装完成后，你会进入 Cursor Jira 应用的配置页面。请等待几分钟，让 Atlassian 把这次安装通知给我们，然后点击 *Connect to Cursor* 按钮。

8. 把该 Jira 站点连接到你的 Cursor 团队

   - 如果你希望启用用户级身份验证（这会让团队成员对自己的 Agent 有更多可见性和控制），而不是用服务账号运行 Agent，请打开 *Require individual authentication* 开关。

9. 在 Cursor 中完成剩余的 Cloud Agent 设置：

   - 连接 GitHub、GitLab、Azure DevOps 或 Bitbucket
   - 启用按用量计费
   - 确认隐私设置
   - 选择默认仓库、模型和基础分支（位于 Cursor Dashboard 的 Cloud Agents 设置中）

10. 返回 Jira，从一个工作项开始使用 Cursor

    - 如果启用了用户级身份验证，每位用户还需要完成下面的更多设置

### 身份验证模式

你可以在 Cursor Jira 集成的管理仪表盘上，通过打开 *Require individual authentication* 开关，选择 Jira 如何为 Cloud Agents 进行身份验证。

| 模式 | 工作方式 | 使用的设置 |
| :----------------------------- | :----------------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 服务账号身份验证 | Cloud Agents 在服务账号下运行。 | 使用团队的 Cloud Agent 设置来处理路由、模型、仓库和默认值。默认情况下，服务账号模式无法使用团队的 MCP 服务器。如果 Agent 需要它们，请打开 *Require individual authentication*。 |
| 用户级身份验证 | 在每位用户的账号下运行所有 Cloud Agents。 | 使用每位用户的 Cloud Agent 设置来处理路由、模型、仓库和默认值。也允许用户在自己的账号下找到正在运行的 Agent。 |

如果你在 Enterprise 团队的 Cloud Agents 设置中打开 **Restrict Cloud Agent Usage**，那么即使该开关关闭，每位 Jira 用户也必须先连接一个 Cursor 账号，Cursor 才会启动 Agent。Cursor 会对照该限制检查这个账号。开关关闭时，Agent 仍然在服务账号下运行。

要连接每位用户：

1. 在 Jira 工作项上启动一个 Agent
2. 会出现连接账号的提示
3. 按照链接，把该 Jira 账号连接到与该团队关联的 Cursor 账号
4. 在 Cursor 中完成剩余的 Cloud Agent 设置：
   - 如果你尚未连接仓库提供方，请连接 GitHub、GitLab、Azure DevOps 或 Bitbucket
   - 选择默认仓库、模型和基础分支（位于 Cursor Dashboard 中 *My Settings* 下的 Cloud Agents 设置）

## 如何使用

打开一个 Jira 工作项，然后把它分配给 Cursor，或在评论中提及 `@Cursor`。Cursor 会使用工作项的标题、描述、评论以及可用的仓库设置来启动一个 Cloud Agent。

你可以让 Cursor 修复 bug、添加功能、更新测试，或调查工作项中描述的任务。

### 委派工作项

当工单已经把任务描述清楚时，把 Jira 工作项分配给 Cursor。

1. 打开 Jira 工作项
2. 点击 assignee 字段
3. 选择 Cursor
4. 查看从该工作项启动的 Cloud Agent

### 提及 Cursor

当你想添加具体指令时，在 Jira 评论中提及 `@Cursor`。你可以在同一条评论中写上仓库、分支或模型。

示例：

- `@Cursor please investigate this regression`
- `@Cursor repo=acme/backend branch=release fix this before the release cut`
- `@Cursor model=gpt-5.6-sol and update the related tests`

### 后续指令

从 Jira 工作项打开 Rovo chat，以继续与 Cursor 对话。

### 状态更新与交接

Cloud Agent 启动后，Jira 会在工作项上显示 Agent 状态。Cursor 会在工作期间发布进度，并在任务完成时返回摘要。

如果 Cursor 打开了 pull request，完成更新会链接到该 PR，以便审查。

## 配置

在 [Dashboard -> Cloud Agents](https://www.cursor.com/dashboard/cloud-agents) 的 *Team Settings* 或 *My Settings* 下管理默认设置和隐私选项。

### 设置

#### 默认模型

当 Jira 工作项或评论中没有指定模型时使用。可用选项见[设置](https://www.cursor.com/dashboard/cloud-agents)。

#### 仓库选择

Cursor 会根据以下信息选择仓库：

1. **显式值**：Jira 评论或工作项中的 `repo`、`branch` 或 `model` 值
2. **工作项内容**：标题、描述和评论中的仓库名称、服务名称或关键词
3. **路由规则**：[自定义的关键词到仓库映射](https://cursor.com/docs/integrations/jira.md#routing-rules)
4. **最近的 Agent 活动**：你最近使用过的仓库
5. **默认仓库**：没有匹配时的回退

要使用特定仓库，请在评论中写上它。例如：`@Cursor repo=acme/mobile-app fix the login bug`。你的团队服务账号或用户账号（取决于你开启的模式）*必须*能够访问这个仓库，否则启动 Cloud Agent 的尝试会失败。

#### 基础分支

Cloud Agent 的起始分支。留空则使用仓库的默认分支（推荐）。

#### 分支前缀

Cloud Agents 所创建的分支名称的前缀。

### 选项

在使用 `@Cursor` 提及时，用这些选项自定义 Cloud Agent 的行为：

| 选项 | 说明 | 示例 |
| :------- | :------------------ | :------------------ |
| `repo` | 指定仓库 | `repo=acme/web-app` |
| `branch` | 指定基础分支 | `branch=main` |
| `model` | 指定模型 | `model=opus` |

Cursor 会跳过代码块中的选项，并按原样把代码留在你的提示词中。

### 路由规则

路由规则让你定义会自动映射到特定仓库的关键词。当 Jira 工作项或评论包含特定关键词时，Cursor 会把 Cloud Agent 路由到关联的仓库。

路由规则让你可以告诉 Agent：哪些项目、工作项、关键词和其他数据，应当决定哪些仓库用于哪些工作项。

#### 设置路由规则

1. 前往 [Dashboard -> Cloud Agents](https://www.cursor.com/dashboard/cloud-agents)
2. 找到 **Routing Rules** 部分
3. 添加关键词到仓库的映射

#### 示例规则

| 关键词 | 仓库 |
| :--------- | :---------------------- |
| `frontend` | `acme/web-app` |
| `mobile` | `acme/mobile-app` |
| `api` | `acme/backend-services` |
| `docs` | `acme/documentation` |

配置这些规则后：

- 标题为 `Fix the frontend nav bug` 的工作项会路由到 `acme/web-app`
- 内容为 `@Cursor update the mobile onboarding flow` 的评论会路由到 `acme/mobile-app`
- 内容为 `@Cursor add rate limiting to the api` 的评论会路由到 `acme/backend-services`

#### 路由如何工作

Cursor 按以下顺序判断 Jira 工作项和评论：

1. **显式值**：Jira 评论或工作项中的 `repo`、`branch` 或 `model` 值
2. **工作项内容**：标题、描述和评论中的仓库名称、服务名称或关键词
3. **路由规则**：自定义的关键词到仓库映射
4. **最近的 Agent 活动**：你最近使用过的仓库
5. **默认仓库**：没有匹配时的回退

### 隐私

Cloud Agents 支持 Privacy Mode。

进一步了解 [Privacy Mode](https://www.cursor.com/privacy-overview)，或管理你的[隐私设置](https://www.cursor.com/dashboard/cloud-agents)。

不支持 Privacy Mode (Legacy)。Cloud Agents 在运行期间需要临时存储代码。

## 权限

安装期间，Jira 会显示 Cursor 应用所请求的权限。Cursor 使用这些权限来：

- 识别启动或管理 Cloud Agent 的 Jira 用户
- 读取工作项字段、描述、评论和相关上下文
- 发布状态更新、完成摘要和 pull request 链接
- 在工作项被分配给 Cursor 或提及 `@Cursor` 时接收事件

安装该应用之前，请在 Atlassian Marketplace 中查看权限提示。

## 常见问题

### 支持哪些 Jira 站点？

Cursor Jira 集成支持已启用 Rovo 的 Atlassian 商业云站点。不支持 Atlassian HIPAA、FedRAMP 和 Government Cloud 实例。

### 是否需要按用量计费？

需要。Cloud Agents 需要按用量计费。在 Cursor 中完成 Cloud Agent 设置时启用按用量计费。

### 谁可以安装 Jira 集成？

需要一位同时是 Jira 管理员和 Cursor 团队管理员的用户来完成初始设置。

### 一个 Jira 站点可以连接到多个 Cursor 团队吗？

不能。每个 Jira 站点只连接一个 Cursor 团队。一个 Cursor 团队可以连接多个 Jira 站点。团队管理员可以从 Jira 集成设置中再添加一个站点。用户要关联自己的 Jira 账号，必须是连接到该站点的那个 Cursor 团队的成员。

### 用户是否需要连接自己的 Cursor 账号？

这取决于你选择的身份验证模式。服务账号身份验证会在一个服务账号下运行所有 Cloud Agents，并使用团队设置。用户级身份验证会把每位 Jira 用户连接到 Cursor，让用户在自己的 Cursor 仪表盘中找到从 Jira 启动、正在运行的 Cloud Agents，并使用每位用户自己的设置来处理路由、模型、仓库和默认值。如果你的 Enterprise 团队打开了 **Restrict Cloud Agent Usage**，那么无论哪种模式，每位用户都必须连接。

### 在 Cursor 能够创建 PR 之前，还需要设置什么？

把 GitHub、GitLab、Azure DevOps 或 Bitbucket 连接到 Cursor，并确保 Cloud Agent 设置包含你的团队希望使用的仓库、模型和基础分支。

### 用户如何继续与 Cursor 对话？

从 Jira 工作项打开 Rovo chat，以继续与 Cursor 对话。也可以在 Cursor 中打开该 Cloud Agent，并在那里继续对话。

## 免责声明

Cursor 可能会出错。请仔细核对代码和回复。

## 隐私政策

有关 Cursor 如何收集、使用和保护你的数据，请参阅我们的[隐私政策](https://cursor.com/privacy)。

---

## 站点地图

[所有文档页面概览](/llms.txt)
