> 原文：https://cursor.com/docs/integrations/gitlab

# GitLab

GitLab 集成会连接你的仓库，以便你使用 [Cloud Agents](https://cursor.com/docs/cloud-agent.md) 和 [Bugbot](https://cursor.com/docs/bugbot.md) 等功能。

## 设置

### GitLab.com

需要 Cursor 管理员权限和 GitLab Maintainer 权限。

GitLab 集成需要**付费 GitLab 方案**（Premium 或 Ultimate）。此集成所需的项目访问令牌在 GitLab Free 上不可用。

1. 前往[仪表盘中的集成](https://cursor.com/dashboard/integrations)
2. 点击 GitLab 旁边的 **Connect**（如果已经连接，则点击 **Manage Connections**）
3. 按照 GitLab 的安装流程操作
4. 回到 Integrations 标签页，点击 GitLab 连接旁边的 **Manage**，并选择 **Sync Repos**
5. 返回仪表盘，在你的仓库上配置功能

[GitLab 设置](/docs-static/images/bugbot/bugbot-gitlab.mp4)

要断开 GitLab 账号，请返回集成仪表盘并点击 **Disconnect Account**。

### GitLab Self-Hosted

GitLab 集成需要**付费 GitLab 方案**（Premium 或 Ultimate）。此集成所需的项目访问令牌在 GitLab Free 上不可用。

GitLab Self-Hosted 需要 [Teams](https://cursor.com/docs/account/teams/pricing.md) 或 [Enterprise](https://cursor.com/docs/enterprise.md) 方案。

### 网络

- GitLab 自托管实例需要来自 Cursor 的安全入站访问，以及用于 webhook 通知的出站访问。
- 实例的 OAuth token 端点（`/oauth/token`）和 token 检查（`/api/v4/personal_access_tokens/self`）只能在相同的 scheme、host 和 port 内重定向，最多 5 次。Cursor 会拒绝任何其他重定向，该请求也会失败。
- 你需要在 GitLab 实例上拥有管理员权限，才能创建该应用。

#### IP 白名单（推荐）

将这些 IP 地址加入你的允许列表：

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

除 IP 白名单之外的其他连接方式，见[高级网络](https://cursor.com/docs/integrations/gitlab.md#advanced-networking)。

### 创建 GitLab 应用

1. 在你的 GitLab 实例中创建一个新应用（优先使用实例级别）
2. 将重定向 URI 设为 `https://cursor.com/gitlab-connected`
3. 配置该应用：
   - **Trusted**：`true`
   - **Confidential**：`true`
   - **Scopes**：`api` 和 `write_repository`
4. 创建之后，你会得到 **Application ID** 和 **Secret**

### 在 Cursor 中注册

1. 前往[仪表盘中的集成](https://cursor.com/dashboard/integrations) → **Advanced** → **GitLab Self-Hosted**
2. 输入 GitLab 实例的 **hostname**
3. 粘贴 **Application ID** 和 **Secret**
4. 如果 Cursor 应通过另一个主机名访问该实例（例如负载均衡器），把它填入 **External Host**
5. 点击 **Register**
6. 从下拉菜单中选择你的 GitLab 实例
7. 点击 **Connect** 以完成安装
8. 回到 Integrations 标签页，点击 GitLab 连接旁边的 **Manage**，并选择 **Sync Repos**
9. 返回仪表盘，在你的仓库上配置功能

设置 External Host 之后，Cursor 会把 API 请求（包括 merge request）、OAuth token 请求，以及 Cloud Agent 的 git clone 和 push，都发送到该地址。**Connect** 在浏览器中打开的 GitLab 登录页仍然使用实例的 hostname。

### 断开 GitLab Self-Hosted

前往[仪表盘中的集成](https://cursor.com/dashboard/integrations) → **Advanced** → **GitLab Self-Hosted**，打开已注册实例的菜单，并选择 **Delete Instance**。Cursor 会移除该实例的 OAuth 凭据、webhook secret、服务账号和用户 token，以及它创建的 webhook。Bugbot 仓库设置、团队 Bugbot 设置和已学习的规则会被保留；如果你再次设置相同的 hostname，它们会恢复。

## 高级网络

自托管实例支持 IP 白名单以外的多种连接方式。设置详情以及支持的私有网络选项，见[私有连接](https://cursor.com/docs/cloud-agent/private-connectivity.md)。

### AWS PrivateLink

面向 Enterprise 客户。当你的 GitLab 实例位于 AWS 中，或可以放在 AWS Network Load Balancer 后面时，使用 AWS PrivateLink。PrivateLink 可以覆盖 Cursor 对 GitLab 的访问；在需要时，也可以让 GitLab 把 webhook 发回 Cursor，而无需经公网出站。

**最适合：** 托管在 AWS 上的 GitLab 实例，以及希望使用私有 VPC 端点连接的团队

**安全性：** HTTPS 加密、AWS PrivateLink、VPC 端点策略、服务账号访问 token

**缺点：** 需要与 Cursor 协调，并完成 AWS 端点服务的设置。

### Cloudflare Tunnel

面向 Enterprise 客户。当 AWS PrivateLink 不切实际，或你需要仅出站的部署模式时，使用 Cloudflare Tunnel。Cursor 会创建并拥有该隧道，并提供主机名和 token。你从自己的网络出站运行 `cloudflared`。你不需要 Cloudflare 账号或合同。

**最适合：** 没有入站网络访问的环境

**安全性：** HTTPS 加密、Cloudflare Tunnel、服务账号访问 token

**缺点：** 需要在你的环境中运行并维护 `cloudflared`。

## 提交作者

Cloud Agents 会以 Cursor 为每个项目创建的项目访问令牌所对应的 bot 用户身份提交，因此这些提交能够通过那些要求提交作者必须是 GitLab 用户的 push 规则。创建该令牌需要对项目拥有 Maintainer 权限。如果令牌不可用，或者 Cursor 无法解析其 bot 身份，Agent 会回退为 `Cursor Agent <cursoragent@cursor.com>`，而这些 push 规则会拒绝这些提交。

## Protected Git Scopes

把你的 GitLab 群组或命名空间锁定到你的 Cursor 组织，这样只有你的团队才能将其仓库用于 Cloud Agents、Automations 和 Bugbot。保护一个 scope 需要 GitLab Owner 角色。参见 [Protected Git Scopes](https://cursor.com/docs/enterprise/model-and-integration-management.md#protected-git-scopes)。

## 后续步骤

GitLab 集成连接之后，配置使用它的功能：

- [Bugbot](https://cursor.com/docs/bugbot.md)：自动 PR 审查，找出 bug 和安全问题
- [Cloud Agents](https://cursor.com/docs/cloud-agent.md)：在云端、在你的仓库上运行的 AI Agent

---

## 站点地图

[所有文档页面概览](/llms.txt)
