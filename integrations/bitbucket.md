> 原文：https://cursor.com/docs/integrations/bitbucket

# Bitbucket

把 Bitbucket Cloud 仓库连接到 [Cloud Agents](https://cursor.com/docs/cloud-agent.md) 和 [Bugbot](https://cursor.com/docs/bugbot.md)。把 Bitbucket Data Center 仓库连接到 Bugbot。

[Security Reviewer 与 Vulnerability Scanner](https://cursor.com/docs/security-agents.md#source-control-support) 支持 Bitbucket Cloud 仓库。它们不支持 Bitbucket Data Center 仓库。

## 设置

### Bitbucket Cloud

Bitbucket Cloud 集成处于公开 beta。它支持 `bitbucket.org` 上的仓库。

Bitbucket Cloud 的设置分为两部分，每一部分需要不同的角色：

- 每位开发者连接自己的 Bitbucket 账号，这样 Cursor 才能以该用户的身份克隆仓库、推送分支并打开 pull request。
- Bitbucket 工作区管理员安装 Cursor 应用，这样 Bugbot 和 Cloud Agent 的状态评论才能以 Cursor 的身份出现。把已安装的应用关联到你的 Cursor 团队，需要一位能够访问该 Bitbucket 工作区的 Cursor 团队管理员。

### 连接你的 Bitbucket 账号

你需要对希望使用的 Bitbucket 工作区仓库拥有访问权限。

1. 前往[仪表盘中的集成](https://cursor.com/dashboard/integrations)
2. 点击 Bitbucket 旁边的 **Connect**
3. 在 Bitbucket 中授权 Cursor
4. 返回仪表盘，确认该集成显示为 **Connected**

[Bitbucket 设置](/docs-static/images/bitbucket/bitbucket-demo.mp4)

每个从 Cursor 启动 Cloud Agents 或打开 pull request 的人，都应该连接自己的 Bitbucket 账号。

### 在 Bitbucket 中安装 Cursor 应用

Bitbucket 工作区管理员必须为该工作区安装 Cursor 应用。这会给 Cursor 一个稳定的应用身份，用于仓库事件、Bugbot 审查评论、行内评论和状态更新。

第 1 至第 4 步需要 Bitbucket 工作区管理员。第 5 步和第 6 步需要一位能够访问该 Bitbucket 工作区的 Cursor 团队管理员。如果由同一个人完成全部设置，则需要同时具备这两个角色。

1. 前往[仪表盘中的集成](https://cursor.com/dashboard/integrations)
2. 找到 Bitbucket 并打开管理菜单
3. 点击 **Install Cursor app**
4. 在你的 Bitbucket 工作区中安装该应用
5. 在 Bitbucket 工作区设置中，于 **Forge Apps** 下选择 **Cursor**
6. 点击 **Connect to Cursor**，并选择要关联的 Cursor 团队

工作区完成关联后，仪表盘会显示已为该 Bitbucket 工作区安装 Cursor 应用。

应用安装之后，Bitbucket 可能需要几分钟才会通知 Cursor。如果 Cursor 不能立刻找到该工作区，请等待并再次尝试关联步骤。

### Cursor 如何使用 Bitbucket Cloud 身份

Cursor 会为不同的操作使用不同的 Bitbucket 身份：

| 操作 | Bitbucket 身份 |
| ------------------------------------------------------------ | ---------------------------- |
| Bugbot 摘要评论、行内评论和构建状态 | Cursor 应用 |
| pull request 上的 Cloud Agent 进度评论 | Cursor 应用 |
| Git clone、分支 push、提交和 pull request 创建 | 已连接的 Bitbucket 用户 |

Cloud Agents 打开的 pull request 会归属于启动该 Agent 的人。安装工作区应用之后，Bugbot 的输出会显示为来自 Cursor。

### 权限

Bitbucket Cloud 集成会请求支持 Cloud Agents 和 Bugbot 所需的权限：

| 权限 | 用途 |
| ---------------------- | ------------------------------------------ |
| **Account** | 识别已连接的 Bitbucket 用户 |
| **Repositories** | 列出、克隆并读取仓库内容 |
| **Pull requests** | 读取 pull request 的 diff 和元数据 |
| **Pull request write** | 创建 pull request 并发布评论 |
| **Webhooks** | 接收仓库和 pull request 事件 |

### 断开 Bitbucket Cloud

断开 Bitbucket Cloud 有两个彼此独立的效果：

- **Disconnect account** 会从 Cursor 中移除你个人的 Bitbucket OAuth 连接。
- **Disconnect Cursor app** 会解除 Bitbucket 工作区与你的 Cursor 团队之间的关联。在工作区管理员于 Bitbucket 中移除该应用之前，应用可以仍然安装在 Bitbucket 里。

要完全停止来自该工作区的应用事件，请从 Bitbucket 工作区设置中卸载 Cursor 应用。

### 切换 Bitbucket 工作区

一个 Cursor 团队一次只使用一个 Bitbucket 工作区。要把团队切换到另一个工作区，Cursor 团队管理员在[仪表盘中的集成](https://cursor.com/dashboard/integrations)中打开 Bitbucket 管理菜单，并选择 **Switch workspace**。切换会断开 Cursor 应用与当前工作区的连接，因此 Cursor 不再列出其中的仓库，Bugbot 也不再在那里发表评论。该应用仍安装在 Bitbucket 中。

然后完成新工作区的连接。在它的 Bitbucket 工作区设置中，于 **Forge Apps** 下选择 **Cursor**，点击 **Connect to Cursor**，并选择你的 Cursor 团队。如果该工作区尚未安装 Cursor 应用，请先安装。

### Bitbucket Data Center

Bitbucket Data Center 需要 [Teams](https://cursor.com/docs/account/teams/pricing.md) 或 [Enterprise](https://cursor.com/docs/enterprise.md) 方案。它支持 Bugbot。不支持 Cloud Agents。

### 前提条件

- Cursor 团队管理员权限
- 一个专用的 Bitbucket Data Center 服务账号
- 该服务账号的 HTTP 访问令牌
- 服务账号对每个你希望用于 Bugbot 的仓库拥有仓库管理员权限

该服务账号会读取仓库和 pull request、管理 webhook，并发布 Bugbot 评论和构建状态。

### 网络

Cursor 需要通过 HTTPS 访问你的 Bitbucket Data Center 实例，以便发起 API 请求和 Git clone。你的实例也需要出站 HTTPS 访问，以便向 Cursor 发送 webhook 通知。

#### IP 允许列表（推荐）

把这些 Cursor IP 地址加入你的入站允许列表：

```text
184.73.225.134
3.209.66.12
52.44.113.131
100.63.97.141
100.63.144.57
18.210.232.136
3.208.51.163
3.224.130.48
3.234.118.132
34.197.19.148
34.198.251.120
34.199.187.247
35.170.160.152
44.215.226.85
52.202.172.69
54.81.109.217
54.204.61.44
54.236.99.119
67.202.63.191
184.193.125.229
184.193.223.40
184.194.140.210
184.194.175.144
184.194.208.56
```

如果 Cursor 应使用与仓库 clone 主机名不同的负载均衡器或 API 主机名，请在注册时把它填为外部主机。

对于没有公网入站访问的实例，见[高级网络](https://cursor.com/docs/integrations/bitbucket.md#advanced-networking)。

### 在 Cursor 中注册

1. 为 Bitbucket 服务账号创建一个 HTTP 访问令牌
2. 前往[仪表盘中的集成](https://cursor.com/dashboard/integrations)
3. 打开 **Advanced**，然后选择 **Bitbucket Data Center**
4. 输入仓库 clone URL 中使用的 **Bitbucket Hostname**
5. 如果 API 流量使用不同的主机名，把它填入 **External Host**
6. 输入 **Service Account Token**
7. 点击 **Register**
8. 打开 [Bugbot in Automations](https://cursor.com/automations/from-cursor/bugbot)，在来自该实例的仓库上启用 Bugbot

Cursor 使用服务账号身份来发布 Bugbot 审查评论、行内发现、webhook 和构建状态。

Bugbot 会在 pull request 打开或更新时自动审查。`bugbot run` 和 `cursor review` 这类评论命令在 Bitbucket Data Center 上不会运行。Cursor 会回复该评论来说明这一点。

[Bugbot Autofix](https://cursor.com/docs/bugbot.md#autofix) 在 Bitbucket Data Center 上不受支持。如果 Autofix 已开启，Bugbot 会跳过它，并在审查中说明。

### 断开 Bitbucket Data Center

前往[仪表盘中的集成](https://cursor.com/dashboard/integrations)，打开 Bitbucket Data Center 配置页面，并删除已注册的实例。从 Cursor 中移除该实例之后，请在 Bitbucket Data Center 中撤销服务账号令牌。

## 高级网络

对于无法接受来自公网流量的实例，Bitbucket Data Center 支持私有连接方式。

### AWS PrivateLink 或 Cloudflare Tunnel

面向 Enterprise 客户。允许 Cursor 通过私有网络连接访问你的实例。设置见[私有连接](https://cursor.com/docs/cloud-agent/private-connectivity.md)，或[联系你的 Cursor 代表](https://cursor.com/contact-sales?source=docs-bugbot-private-network)。

**最适合：** 位于防火墙之后的实例，包括托管在 AWS 上的实例，以及可以运行 `cloudflared` 的环境

**安全性：** HTTPS 加密、AWS PrivateLink、Cloudflare Tunnel、VPC 允许列表、服务账号访问 token

**缺点：** 需要与 Cursor 协调。目前不支持 Google Private Service Connect。

### 反向代理隧道

面向 Enterprise 客户。在本地运行一个反向代理隧道，由它建立到 Cursor 的出站连接。网络请求经该隧道转发，因此 Bitbucket 实例不需要公网入站访问。设置请[联系你的 Cursor 代表](https://cursor.com/contact-sales?source=docs-bugbot-on-prem-proxy)。

**最适合：** 没有入站网络访问的环境

**安全性：** HTTPS 加密、服务账号访问 token

**缺点：** 与更直接的连接方式相比，会带来额外的复杂度、维护要求，以及潜在的安全方面的考虑

## 排查问题

### Cursor 找不到我的 Bitbucket 工作区

Bitbucket 应用安装事件可能需要几分钟才会同步。请等待，然后从 Bitbucket 工作区设置中重试 **Connect to Cursor** 这一步。

### 我安装了 Cursor 应用，但无法把它关联到我的 Cursor 团队

- 确认你是 Cursor 团队管理员。安装该应用需要 Bitbucket 工作区管理员，但把工作区关联到 Cursor 团队需要 Cursor 团队管理员。
- 确认你的 Bitbucket 用户可以访问你正在关联的工作区。

### Bugbot 评论没有显示为来自 Cursor

在 Bitbucket 工作区中安装 Cursor 应用，并把它关联到你的 Cursor 团队。仅有个人的 Bitbucket 连接，不足以让评论以 Cursor 应用的身份出现。

### Cloud Agent 无法访问 Bitbucket 仓库

- 从集成仪表盘连接你的个人 Bitbucket 账号。
- 确认你的 Bitbucket 用户对该仓库拥有读写权限。
- 确认该仓库位于 `bitbucket.org` 上的 Bitbucket Cloud。

### Cursor 无法列出我的 Bitbucket Data Center 仓库

- 确认服务账号令牌处于有效状态。
- 确认服务账号可以访问这些仓库。
- 确认 Cursor 可以通过 HTTPS 到达实例主机名或已配置的外部主机。

### 我无法在 Bitbucket Data Center 仓库上启用 Bugbot

- 确认你是 Cursor 团队管理员。
- 确认已注册的实例拥有服务账号令牌。
- 确认服务账号拥有仓库管理员权限。

## 后续步骤

Bitbucket 连接之后，配置使用它的功能：

- [Bugbot](https://cursor.com/docs/bugbot.md)：自动 PR 审查，找出 bug 和安全问题
- [Cloud Agents](https://cursor.com/docs/cloud-agent.md)：用于 Bitbucket Cloud 仓库的 AI Agent

---

## 站点地图

[所有文档页面概览](/llms.txt)
