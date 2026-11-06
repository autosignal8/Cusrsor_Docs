> 原文：https://cursor.com/docs/integrations/github

# GitHub

Cursor 的 GitHub 应用会连接你的仓库，以便你使用 [Cloud Agents](https://cursor.com/docs/cloud-agent.md) 和 [Bugbot](https://cursor.com/docs/bugbot.md) 等功能。

## 设置

### GitHub.com

需要 Cursor 管理员权限和 GitHub 组织管理员权限。

1. 前往[仪表盘中的集成](https://cursor.com/dashboard/integrations)
2. 点击 GitHub 旁边的 **Connect**（如果已经连接，则点击 **Manage Connections**）
3. 选择 **All repositories** 或 **Selected repositories**
4. 返回仪表盘，在你的仓库上配置功能

[GitHub 设置](/docs-static/images/bugbot/bugbot-install.mp4)

要断开 GitHub 账号，请返回集成仪表盘并点击 **Disconnect Account**。

Cursor 账号第一次连接 GitHub 时，**Connect** 会通过 GitHub 登录来关联你的 GitHub 账号，而不会安装该应用。你可以把第二个 Cursor 账号连接到已经安装了 Cursor 应用的 GitHub 账号：这一次 Connect 不会移动或更改现有安装。

每个 GitHub App 安装只属于一个 Cursor 账号或团队。如果另一个 Cursor 账号或团队已经拥有该安装，从你这里连接会失败，并显示 "GitHub installation is already connected to another Cursor target."，安装仍归其所有者。有一个例外：当团队管理员连接的组织安装，是同一团队的某位成员此前以个人身份连接的，Cursor 会把该安装转移到团队。GitHub Enterprise Server 安装不会出现这个错误。

### GitHub Enterprise Server

### 前提条件

- 运行受支持的 GitHub Enterprise Server 版本（建议 v3.8 或更高版本）
- 对你的 GHES 实例拥有管理员权限
- 拥有 Cursor 团队管理员权限（要在仪表盘中看到 GitHub Enterprise 注册选项，此项为必需）

### 网络

GHES 需要来自 Cursor 的安全入站访问，以及用于 webhook 通知的出站访问。

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
34.192.39.182
50.16.106.255
44.217.29.124
3.223.245.201
54.164.185.10
34.194.133.23
35.170.116.221
```

除 IP 白名单之外的其他连接方式，见[高级网络](https://cursor.com/docs/integrations/github.md#advanced-networking)。

#### 代理要求

如果你在 GHES 前面运行代理，请确保它允许 Cursor 的 GitHub App 集成使用经过身份验证的 GitHub REST 和 GraphQL API。在应用设置过程中，以及 webhook 送达之后，Cursor 会使用这些 API 来解析仓库身份、查看 pull request 状态、读取 checks 和 reviews，并更新先前的 Bugbot 输出。

代理应当允许来自 Cursor 的、经过身份验证的 GitHub API 请求通过，不要拦截或改写它们。

### 注册 Cursor Enterprise App

1. 前往[仪表盘中的集成](https://cursor.com/dashboard/integrations)。在 **Source Control** 部分找到 **GitHub Enterprise** 行（列在 GitHub 下面），点击 **Manage apps**。
2. 输入 GHES 实例的 **base URL**（例如 `https://git.yourcompany.com`）
3. 输入将拥有该应用的 **Organization** 名称
   - 这应当是你的 GHES 安装内部、贵公司的 Organization
   - 你需要对该 Organization 拥有管理员权限
   - 注册完成后，其他 Organization 也可以访问该应用
   - 留空则使用你的用户账号（不推荐）
4. 点击 **Register**
5. 为 Cursor Enterprise Application 选择一个名称（建议使用默认名称）
6. 该应用会显示在你的 GHES 实例中可用的 GitHub Apps 下面
7. 返回仪表盘，在你的仓库上配置功能

对于启用了数据驻留的 GitHub Enterprise Cloud（`*.ghe.com`），使用上述注册步骤，并将 `https://<subdomain>.ghe.com` 作为 base URL。

## 与另一个团队共享安装

[Organization](https://cursor.com/docs/enterprise/organizations.md) 中的团队可以把它的 GitHub 安装共享给同一 Organization 中的其他团队。这些团队可以使用该安装上的仓库来运行 Cloud Agents、Automations 和 Bugbot，而无需再次安装 GitHub 应用。GitHub.com 和 GitHub Enterprise Server 的安装都可以共享。

### 打开共享对话框

作为拥有该安装的团队的管理员，前往[仪表盘中的集成](https://cursor.com/dashboard/integrations)，在 **GitHub** 或 **GitHub Enterprise** 行上选择 **Share with another team**。

### 授予访问权限

在 **Share GitHub installation** 中，选择 **GitHub account** 和 **Team**，然后点击 **Grant access**。

接收方团队可以使用该安装上的每一个仓库，用量向该团队计费。集成页面上它的 GitHub 行会标明拥有该安装的团队。

共享同一安装的团队中，对于给定仓库，只有一个团队可以开启 Bugbot。如果另一个团队已经为该仓库开启了 Bugbot，必须由那个团队的管理员先关闭。

要移除访问权限，再次打开 **Share with another team**，在 **Already shared with** 下该团队旁边选择 **Revoke**。撤销还会删除该团队针对此安装中各仓库的 Bugbot 设置。

## IP 允许列表配置

如果你的组织使用 GitHub 的 IP 允许列表功能来限制对仓库的访问，可以把 Cursor 配置为使用一组 IP 范围较窄的托管出站代理。

无论你在下面选择哪种允许列表方式，都只有在 Cursor 为你的团队启用之后，Cloud Agents 才会通过出站代理发送 git 流量。请[联系支持](https://cursor.com/help.md)并提供团队 ID 以启用。该代理适用于团队，不适用于个人账号。来自 Agent 虚拟机的其他 GitHub 调用（例如 `gh` 或 GitHub API 请求）不会使用该代理。如果你的 Agent 会发起这些调用，请同时把 [Cloud Agent 出站 IP 范围](https://cursor.com/docs/cloud-agent/security-network.md#egress-ip-ranges)加入允许列表。

### 为已安装的 GitHub Apps 启用 IP 允许列表配置（推荐）

Cursor GitHub 应用已经预配置了 IP 列表。你可以为已安装的应用启用允许列表，从而自动继承这份列表。这是**推荐做法**，因为我们可以更新列表，而你的组织会自动收到更新。

这里继承的是注册在 Cursor GitHub App 上的 IP 列表。它不会放行碰巧使用这些地址的每一个客户端。

启用方法：

1. 前往组织的 Security 设置
2. 进入 IP allow list 设置
3. 勾选 **"Enable IP allow list configuration for installed GitHub Apps"**

详细步骤见 [GitHub 文档](https://docs.github.com/en/enterprise-cloud@latest/organizations/keeping-your-organization-secure/managing-security-settings-for-your-organization/managing-allowed-ip-addresses-for-your-organization#allowing-access-by-github-apps)。

### 直接把 IP 加入允许列表

如果你的组织在 GitHub 中使用由 IdP 定义的允许列表，或者无法使用预配置的允许列表，请添加 [Git 出站代理与 IP 允许列表](https://cursor.com/docs/cloud-agent/security-network.md#git-egress-proxy-and-ip-allow-list)中列出的代理 IP。

## 高级网络

自托管实例支持 IP 白名单以外的多种连接方式。设置详情以及支持的私有网络选项，见[私有连接](https://cursor.com/docs/cloud-agent/private-connectivity.md)。

### AWS PrivateLink

面向 Enterprise 客户。当你的 GitHub Enterprise Server 位于 AWS 中，或可以放在 AWS Network Load Balancer 后面时，使用 AWS PrivateLink。PrivateLink 可以覆盖 Cursor 对 GHES 的访问；在需要时，也可以让 GHES 把 webhook 发回 Cursor，而无需经公网出站。

**最适合：** 托管在 AWS 上的 GHES 实例，以及希望使用私有 VPC 端点连接的团队

**安全性：** HTTPS 加密、AWS PrivateLink、VPC 端点策略、服务账号访问 token

**缺点：** 需要与 Cursor 协调，并完成 AWS 端点服务的设置。

### Cloudflare Tunnel

面向 Enterprise 客户。当 AWS PrivateLink 不切实际，或你需要仅出站的部署模式时，使用 Cloudflare Tunnel。你的网络运行 `cloudflared`，Cursor 提供隧道主机名和 token。

**最适合：** 没有入站网络访问的环境

**安全性：** HTTPS 加密、Cloudflare Tunnel、服务账号访问 token

**缺点：** 需要在你的环境中运行并维护 `cloudflared`。

## 权限

GitHub 应用会请求以下权限，以支持 Cursor 的功能：

| 权限 | 用途 |
| ---------------------------------- | ---------------------------------------------------------------------------- |
| **仓库访问** | 克隆你的代码，并创建工作分支 |
| **Pull requests** | 创建 PR，并留下审查评论 |
| **Issues** | 跟踪审查过程中发现的 bug 和任务 |
| **Checks 与 statuses** | 报告代码质量和测试结果 |
| **Actions 与 workflows** | 监控 CI/CD 流水线，并从 pull request 触发 CI 重新运行 |
| **Administration** | 读取分支保护和必需的 check 规则，以判断 PR 是否可以合并 |
| **自定义仓库角色** | 判断用户的访问级别，从而显示正确的合并和审查选项 |
| **组织自定义属性** | 在筛选中展示由组织定义的仓库元数据 |

所有权限都遵循最小权限原则。

## Protected Git Scopes

把你的 GitHub 组织锁定到你的 Cursor 组织，这样只有你的团队才能将其仓库用于 Cloud Agents、Automations 和 Bugbot。保护一个 scope 需要 GitHub 组织所有者或管理员权限。参见 [Protected Git Scopes](https://cursor.com/docs/enterprise/model-and-integration-management.md#protected-git-scopes)。

## 排查问题

### Agent 无法访问仓库

- 安装具有仓库访问权限的 GitHub 应用
- 检查私有仓库的权限
- 确认你的 GitHub 账号权限

### pull request 权限被拒绝

- 授予该应用对 pull request 的写入权限
- 检查分支保护规则
- 如果应用安装已过期，请重新安装

### 在 GitHub 设置中看不到应用

- 检查是否安装在组织级别
- 从 [github.com/apps/cursor](https://github.com/apps/cursor) 重新安装
- 如果安装已损坏，请联系支持

## 后续步骤

GitHub 集成连接之后，配置使用它的功能：

- [Bugbot](https://cursor.com/docs/bugbot.md)：自动 PR 审查，找出 bug 和安全问题
- [Cloud Agents](https://cursor.com/docs/cloud-agent.md)：在云端、在你的仓库上运行的 AI Agent

---

## 站点地图

[所有文档页面概览](/llms.txt)
