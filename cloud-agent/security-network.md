> 原文：https://cursor.com/docs/cloud-agent/security-network

# 密钥与网络

Cloud Agents 可在 Privacy Mode 下使用。我们绝不会用你的代码进行训练，并且只为运行 Agent 而保留代码。[进一步了解 Privacy Mode](https://www.cursor.com/privacy-overview)。

关于 Cloud Agents 如何架构和保护的说明，包括运行生命周期、访问模型、隔离、加密和数据处理，见[安全概览](https://cursor.com/docs/cloud-agent/security.md)。本页是它所描述的那些控制的配置参考。

**Privacy Mode (Legacy)** 不受支持。旧版隐私模式会阻止云端数据存储，而 Cloud Agents 在运行期间需要把代码和环境数据存放在云端。使用 Cloud Agents 之前，请从 [Dashboard → Cloud Agents](https://cursor.com/dashboard/cloud-agents) 切换到 Privacy Mode。

## 密钥保护

提供给 Cloud Agents 的密钥在静态存储和传输中都会加密。除 Cloud Agent 用户之外，任何人都看不到它们。

密钥可以设置为 Environment Variables、Runtime Secrets 或 Build Secrets。

### 环境变量

类型为 `Environment Variable` 的密钥对 Cloud Agent 可见。它们最适合非敏感配置，供 Agent 查看会有帮助，例如标志或公开 URL。与其他密钥类型一样，它们在静态存储和传输中仍然加密。

### 运行时密钥

Runtime Secrets 以前称为 Redacted Secrets。

类型为 `Runtime Secret` 的密钥仍会作为环境变量加载，但其内容会从 Agent 的工具调用结果、聊天记录、提交和提交消息中脱敏，并替换为占位字符串 `[REDACTED]`。它们最适合不应暴露给 Agent、也绝不应提交到仓库的敏感凭证。

因为 Runtime Secrets 在内部仍然作为环境变量工作，所以虽然不会展示给 Agent，但通过 Terminal 与 Agent 环境交互的用户仍然可以看到它们。

### 构建密钥

类型为 `Build Secret` 的密钥只对 [Docker 构建过程](https://cursor.com/docs/cloud-agent/security-network.md#manual-setup-with-dockerfile-advanced)可用（如果你配置了一个），不会暴露给正在运行的 Agent 环境。它们最适合不应暴露给 Agent 的私有包注册表或构建时凭证。

要在 Dockerfile 中安全地使用 Build Secret，请在 `RUN` 步骤中通过 [Docker secret mount](https://docs.docker.com/build/building/secrets/#secret-mounts) 引用它们，例如：

```docker
RUN --mount=type=secret,id=MY_TOKEN,env=MY_TOKEN,required=true \
    ./scripts/install-private-deps.sh
```

## OIDC 身份 token

对于云角色和内部 API，优先使用短期的 [OIDC token](https://cursor.com/docs/cloud-agent/identity.md)，而不是 Secrets 中的长期密钥。Cloud Agent 可以从本地套接字签发由 Cursor 签名的 JWT，并把它出示给 AWS、GCP、Azure、Vault 或任何 OIDC 验证方。

## 签名提交

Cloud Agents 用由 HSM 支持的 Ed25519 密钥为每个提交签名。在 GitHub 上，这些提交会显示 “Verified” 徽章，以便你的团队确认该提交来自 Cursor。

这对所有 Cloud Agents 自动生效。无需设置。

如果 GitHub 仓库强制要求签名提交的分支保护规则，Cloud Agent 的 PR 无需额外配置即可满足这些规则。

### 提交作者

在 GitHub 上，Cloud Agents 以 `Cursor Agent <cursoragent@cursor.com>` 作为作者并提交，而不是以启动该 Agent 的人。在 GitLab 上，它们以项目访问令牌的机器人用户身份提交。见 [GitLab 提交作者](https://cursor.com/docs/integrations/gitlab.md#commit-author)。

当 Cursor 能够查到启动该 Agent 的人时，会把这个人作为 `Co-authored-by` 尾注加到每个提交上。由服务账号启动的 Agent，例如自动化，不会带上该尾注。

## Protected Git Scopes

团队管理员可以把一个 Git 组织锁定到你的 Cursor 组织，这样只有你的团队才能在它的仓库上启动 Cloud Agents。见 [Protected Git Scopes](https://cursor.com/docs/enterprise/model-and-integration-management.md#protected-git-scopes)。

## 你应该知道的事

1. 为我们的 GitHub app 授予你想要编辑的仓库的读写权限。我们用它来克隆仓库并做出改动。
2. 你的代码在我们的 AWS 基础设施中的隔离 VM 内运行，并在 Agent 可访问期间存储在 VM 磁盘上。
3. Agent 默认拥有互联网访问。你可以为用户、团队和已保存的环境配置[网络出站控制](https://cursor.com/docs/cloud-agent/security-network.md#network-access)，以限制 Agent 可以访问的域名。
4. Agent 会自动运行所有终端命令，从而在测试上迭代。这与前台 Agent 不同，前台 Agent 要求用户批准每条命令。自动运行会引入数据外泄风险：攻击者可以实施提示词注入攻击，诱使 Agent 把代码上传到恶意网站。见 [OpenAI 关于 Cloud Agent 提示词注入风险的说明](https://platform.openai.com/docs/codex/agent-network#risks-of-agent-internet-access)。
5. 如果关闭了 Privacy Mode，我们会收集提示词和开发环境以改进产品。
6. 如果在启动 Cloud Agent 时关闭了 Privacy Mode，然后在 Agent 运行期间再启用它，该 Agent 会在完成之前继续以 Privacy Mode 关闭的状态运行。

## 数据保留

Cloud Agents 为每次运行存储两类数据：

- **对话历史。** 构成 Agent 对话记录的提示词、模型响应、工具调用和演示产物。这是你在 Web 上或从桌面客户端打开一个 Agent 时看到的数据。
- **环境快照。** 虚拟机磁盘的加密时间点副本。快照让你可以自定义 VM 环境，并让 Agent 启动或恢复时无需重新克隆仓库或再次运行设置。

对话历史默认无限期保留，以便你回看并恢复过去的运行。环境快照最多在不活动 **30 天**后保存。每次 Agent 从快照启动或恢复时，其到期时间会再延长 30 天。快照一旦 30 天未被使用，就会自动删除，与方案或策略无关。

你可以使用 [Delete Agent API](https://cursor.com/docs/cloud-agent/api/endpoints.md#delete-an-agent-permanently) 显式删除一个 Cloud Agent 的对话历史。该端点移除对话记录及其产物。它不会删除环境快照；快照不能按需删除，而是遵循上面的保留窗口。

### Cloud Agent 保留策略

自定义保留窗口面向部分 Enterprise 团队处于早期访问。[联系销售](https://cursor.com/contact-sales?source=docs-cloud-agent-retention)以申请访问。

Enterprise 团队管理员可以在 [Cloud Agents 仪表盘](https://cursor.com/dashboard/cloud-agents)的 **Team Settings** 中限制团队的 Cloud Agent 数据保留多久。可用窗口为 **Indefinite** 和 **90 days**。

当把策略设为 **90 days** 时：

- 后台作业会删除早于保留策略窗口的对话。
- 环境快照继续遵循上面描述的滚动 30 天不活动窗口。
- 该策略适用于团队拥有的每一次 Agent 运行，包括来自已保存环境和 [API](https://cursor.com/docs/cloud-agent/api/v0.md) 的运行。

切换回 **Indefinite** 会停止进一步删除对话，但不会恢复已经移除的数据。

## 网络访问

控制 Cloud Agents 可以到达哪些网络资源。这些设置在 [Cloud Agents 仪表盘](https://cursor.com/dashboard/cloud-agents)上对个人用户、已保存的环境和团队管理员可用。它们适用于 Cursor 托管的 Cloud Agents。在[自托管机器](https://cursor.com/docs/cloud-agent/self-hosted.md#environments-on-self-hosted-machines)上的运行使用该机器自己的网络、防火墙和代理。

### 私有网络访问

Cloud Agents 不必运行在你的硬件上才能到达私有资源。对于 VPC 或内网中的服务，在 Cloud Agent 环境中使用 Tailscale 用户态网络、Cloudflare Tunnel 或类似的私有网络客户端。设置说明见[运行 Tailscale](https://cursor.com/docs/cloud-agent/setup.md#running-tailscale)和[运行 Cloudflare Tunnel](https://cursor.com/docs/cloud-agent/setup.md#running-cloudflare-tunnel)。

无论使用 Tailscale 还是 Cloudflare Tunnel，你的私有服务都不需要接受来自公共互联网的入站流量。Agent 通过经过认证的网络路径连接，而服务仍留在你的私有网络上。

当 Agent 可以通过经过认证的 HTTPS 主机名到达私有服务时，Cloudflare Tunnel 很合适。你的网络中的连接器向外拨号到 Cloudflare，Cloud Agent 像调用任何其他外部 URL 一样调用该主机名。你可以用 Cloudflare Access 服务令牌保护该主机名，把令牌值存为 Cursor Secrets，并把该主机名加入 Cloud Agent 允许列表。

对于私有数据库这类 TCP 目标，使用在 Agent 环境中暴露本地 TCP 监听器的隧道客户端。然后 Agent 连接到 `localhost`，而隧道把流量转发到私有源站。

对于私有的 GitHub Enterprise Server、GitLab Enterprise、源代码管理 API、Artifactory 或 Nexus 这类包注册表，以及相关的 webhook 流量，Enterprise 团队可以使用带有 AWS PrivateLink 或 Cloudflare Tunnel 的[私有连接](https://cursor.com/docs/cloud-agent/private-connectivity.md)。

### 访问模式

三种模式控制 Cloud Agents 的出站网络访问：

| 模式 | 行为 |
| :--------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Allow all network access** | Cloud Agents 可以到达任何外部主机。不应用域名限制。 |
| **Default + allowlist** | Cloud Agents 可以到达[默认域名](https://cursor.com/docs/agent/security/run-modes.md#network-access)，以及你加入允许列表的任何域名。 |
| **Allowlist only** | Cloud Agents 只能到达你明确加入允许列表的域名。 |

即使在 **Allowlist only** 模式下，仍有一小部分域名保持可访问，以便 Cloud Agents 能够工作。其中包括 Cursor 自己的服务和源代码管理（SCM）提供方。

### 产物上传

Cloud Agents 把[产物](https://cursor.com/docs/cloud-agent/capabilities.md#demos-and-artifacts)（PR 上显示的截图、视频和日志引用）上传到 `cloud-agent-artifacts.s3.us-east-1.amazonaws.com`。

如果使用 **Default + allowlist** 或 **Allowlist only**，请把这个确切的主机加入允许列表，以便产物上传成功。不要把该条目放宽为 `*.s3.us-east-1.amazonaws.com`：通配符会把出站开放到该区域的每一个存储桶，并为遭受提示词注入的 Agent 创造一条外泄路径。阻止该主机会禁用上传；Agent 会话和其他工具调用仍会继续工作。

### 用户级设置

个人用户可以在 [Cloud Agents 仪表盘](https://cursor.com/dashboard/cloud-agents)的 **Security** 标题下配置自己的网络访问模式。你的用户级设置适用于你创建的所有 Cloud Agents。

当你选择包含允许列表的模式（**Default + allowlist** 或 **Allowlist only**）时，设置下方会出现允许列表配置部分，你可以在那里添加自定义域名。

### 环境级设置

已保存的环境可以有自己的网络访问模式和允许列表。当某个仓库或仓库组需要比团队其余部分更严格的出站时，使用环境级设置。

例如，你可以让一个邻近生产的环境使用 **Allowlist only**，而让敏感度较低的环境使用 **Default + allowlist**。使用更严格环境的 Agent 会继承这些限制。

环境级设置包括两个继承选项：

| 模式 | 行为 |
| :------------------------------------------- | :---------------------------------------------------------------------------------------- |
| **Inherit settings** | 使用适用的用户或团队网络访问设置。 |
| **Inherit settings + environment allowlist** | 使用适用的用户或团队设置，并加入环境允许列表中的域名。 |

你也可以把环境直接设为 **Allow all network access**、**Default + allowlist** 或 **Allowlist only**。

### 团队级设置

团队管理员可以从同一个仪表盘为整个团队设置默认网络访问模式。团队级允许列表与管理员为[沙箱默认网络允许列表](https://cursor.com/docs/agent/security/run-modes.md#network-access)配置的允许列表是同一个。没有单独的允许列表需要管理；一个允许列表同时控制 Cloud Agent 网络访问和沙箱默认值。

当存在团队级设置时：

- 如果环境定义了自己的模式，**环境设置适用**于使用该环境的 Agent。
- 如果环境继承设置，并且用户配置了自己的设置，则**用户设置优先**。
- 如果环境和用户都没有配置设置，则**团队默认值适用**。

### 锁定设置（Enterprise）

锁定仅对 Enterprise 团队可用。

Enterprise 团队管理员可以使用 **Lock Network Access Policy** 选项锁定网络访问设置。锁定后：

- 团队级设置适用于每位成员，无论其个人偏好如何。
- 用户不能从自己的仪表盘覆盖已锁定的设置。

这让管理员对整个组织中 Cursor 托管的 Cloud Agents 的网络访问拥有完全控制。已锁定的设置不会到达自托管机器，包括由 **Require Self-Hosted Machines** 路由到那里的运行。

### 与沙箱网络策略的关系

**Default + allowlist** 模式中的 “Default” 域名，与桌面 Agent 沙箱使用的[默认网络允许列表](https://cursor.com/docs/agent/security/run-modes.md#network-access)相同。团队级允许列表也是共享的：当管理员在仪表盘上配置允许列表时，它同时适用于 Cloud Agent 网络访问和[沙箱网络策略](https://cursor.com/docs/reference/sandbox.md)。

## 出站 IP 范围

Cloud Agents 在访问外部服务、API 或仓库时，会从特定的 IP 地址范围发起网络连接。

### API 端点

IP 范围可通过 [JSON API 端点](https://cursor.com/docs/ips.json)获取：

```bash
curl https://cursor.com/docs/ips.json
```

#### 响应格式

```json
{
  "version": 1,
  "modified": "2025-09-24T16:00:00.000Z",
  "cloudAgents": {
    "us3p": ["100.26.13.169/32", "34.195.201.10/32", "..."],
    "us4p": ["54.184.235.255/32", "35.167.37.158/32", "..."],
    "us5p": ["3.12.82.200/32", "52.14.104.140/32", "..."]
  },
  "gitEgressProxy": ["184.73.225.134/32", "3.209.66.12/32", "52.44.113.131/32"]
}
```

- **version**：API 响应的 schema 版本号
- **modified**：IP 范围上次更新时间的 ISO 8601 时间戳
- **cloudAgents**：包含 IP 范围的对象，按集群作为键
- **gitEgressProxy**：[git 出站代理](https://cursor.com/docs/cloud-agent/security-network.md#git-egress-proxy-and-ip-allow-list)所使用的 IP 地址

IP 范围以 [CIDR 记法](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)发布。如果需要，你可以使用在线转换工具把 CIDR 记法转换为 IP 地址范围。

### 使用这些 IP 范围

这些已发布的 IP 范围可能被 Cloud Agents 用于：

- 克隆并推送到远端仓库（除非使用 [git 出站代理](https://cursor.com/docs/integrations/github.md#ip-allow-list-configuration)）
- 下载包和依赖
- 对外部服务发起 API 调用
- 在 Agent 执行期间访问 Web 资源

如果组织使用防火墙规则或 IP 允许列表来控制网络访问，你可能需要把这些 IP 范围加入允许列表，以确保 Cloud Agents 能够正常访问你的服务。

**重要考虑：**

- 我们会不时为扩展和运维需要更改 IP 地址。
- 我们不建议把按 IP 地址加入允许列表作为主要安全机制。
- 如果必须使用这些 IP 范围，我们强烈建议定期监控该 JSON API 端点。

### Git 出站代理与 IP 允许列表

Cursor 支持一项类似但不同的功能，即[使用 git 出站代理来配合 IP 允许列表](https://cursor.com/docs/integrations/github.md#ip-allow-list-configuration)。该代理把所有 git 流量路由经过一组更窄的 IP，并适用于所有 git 主机，包括 GitHub、GitLab、Azure DevOps 和 Bitbucket。只有 git 流量使用该代理。来自 Agent 的其他请求，包括 GitHub API 调用和 `gh`，来自 [Cloud Agent 范围](https://cursor.com/docs/cloud-agent/security-network.md#api-endpoint)。

对于 git 主机，我们建议使用上面链接中描述的 IP 允许列表配置，因为它直接与 Cursor GitHub app 集成。

如果需要把代理 IP 直接加入允许列表，请使用这些地址：

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

从 `100.63.97.141` 到 `184.194.208.56` 的 21 个地址已分配，但尚未承载流量。它们被提前公布，以便你在投入使用之前把它们加入允许列表。上面的 [API 端点](https://cursor.com/docs/cloud-agent/security-network.md#api-endpoint)尚未返回它们。

### Origin IP

如果团队把 Cloud Agents 与 [Origin](https://cursor.com/docs/origin.md) 一起使用，请在上面的 git 出站代理 IP 之外，再把这些额外 IP 加入允许列表：

```text
34.192.39.182
50.16.106.255
44.217.29.124
3.223.245.201
54.164.185.10
34.194.133.23
35.170.116.221
```

这些 IP 地址是稳定的。如果列表发生变化，使用 IP 允许列表的团队会在任何地址被添加或移除之前收到提前通知。

拥有私有 GitHub Enterprise Server 或 GitLab Enterprise 部署的 Enterprise 客户可以使用[私有连接选项](https://cursor.com/docs/cloud-agent/private-connectivity.md)，以便 Cloud Agents 和 Bugbot 访问私有源代码管理系统。

---

## 站点地图

[所有文档页面概览](/llms.txt)
