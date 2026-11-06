> 原文：https://cursor.com/docs/cloud-agent/self-hosted

# 自托管机器

自托管机器把 Cloud Agent 的工具执行移到你管理的机器上。团队仍然通过桌面应用、[cursor.com](https://cursor.com/agents) 和[移动端](https://cursor.com/docs/cloud-agent/mobile.md)使用 Cloud Agents。Cursor 运行 Agent 循环、推理和规划。你的 worker 执行文件编辑和终端命令。它也运行 Computer Use 工具和本地 MCP 服务器。

Cursor 管理的 Cloud Agents 是大多数团队的推荐路径，也是最快的上手方式。在把 Cloud Agents 带到你自己的机器上之前，见[选择 Cloud Agents 的运行位置](https://cursor.com/docs/cloud-agent/self-hosted/choose-runtime.md)。

## 谁应该使用自托管机器

当 Cursor 的托管云无法满足你的约束时，使用自托管机器：

- 你有严格的网络要求，代码或服务无法从网络外部到达。对于私有源代码管理或包注册表，请从托管的 Cloud Agents 和[私有连接](https://cursor.com/docs/cloud-agent/private-connectivity.md)开始（[AWS PrivateLink](https://cursor.com/docs/cloud-agent/private-connectivity.md#aws-privatelink) 或 [Cloudflare Tunnel](https://cursor.com/docs/cloud-agent/private-connectivity.md#cloudflare-tunnel)）。
- 你有自定义硬件，例如 GPU 机器或用于 iOS 开发的 Mac。使用你已经在运行的机器，或来自[合作伙伴主机](https://cursor.com/docs/cloud-agent/self-hosted/integrations.md)的 VM，例如 AWS Lambda、Cloudflare、Namespace、Modal、Daytona、E2B、Vercel、Tensorlake、Superserve 或 Render。
- 你有自定义镜像，例如不同的操作系统或现有的构建流水线，难以保存为 [Cloud Agent Build](https://cursor.com/docs/cloud-agent/builds.md)。

如果想试用，请看 [My Machines 快速入门](https://cursor.com/docs/cloud-agent/self-hosted/my-machines.md#quickstart)。

### 在这些情况下优先使用托管的 Cloud Agents

你不需要自己拥有计算资源，也能把 Agent 保持在安全边界之内。当这些控制覆盖你的要求时，请继续使用 Cursor 管理的 Cloud Agents：

- 每个 Agent 一台隔离的 VM，由 Cursor 预配和拆除，无需你去规划容量、打补丁、扩缩或待命维护 worker 集群。
- [网络允许列表](https://cursor.com/docs/cloud-agent/security-network.md#network-access)按用户、团队或环境限制出站域名。
- 通过 AWS PrivateLink 或 Cloudflare Tunnel 的[私有连接](https://cursor.com/docs/cloud-agent/private-connectivity.md)，通往自托管的 GitHub Enterprise Server、GitLab Enterprise 和私有源代码管理 API，再加上 [Tailscale 或类似客户端](https://cursor.com/docs/cloud-agent/security-network.md#private-network-access) 用于 VPC 中的服务。
- Privacy Mode，以及范围限定到你所选环境的、由客户控制的密钥。完整模型见 [Cloud Agent 安全与网络](https://cursor.com/docs/cloud-agent/security-network.md)。

## 什么会离开你的网络

完整检出、构建缓存和机器本地凭证留在你的机器上。运行期间，worker 把 Agent 需要的内容发送给 Cursor，例如文件内容、终端输出、diff、截图、本地 MCP 结果和路由元数据。如果启用[桌面共享](https://cursor.com/docs/cloud-agent/self-hosted/computer-use.md#share-the-agent-desktop)，它还会流式传输 Agent 桌面。如果启用[实时工作区](https://cursor.com/docs/cloud-agent/self-hosted.md#live-workspace)，它会把 Agent 的文件、终端和转发的端口中继给在桌面应用中打开该 Agent 的人。

Worker 把 Cloud Agent 产物，例如截图、视频和日志引用，上传到 Cursor 管理的存储，以便它们出现在 pull request 和仪表盘中。不要让密钥进入工具输出和产物。

[Privacy Mode](/data-use) 也适用于自托管机器。启用后，从 worker 发送的代码不会被 Cursor 或模型提供方用于训练。

Worker 需要对以下地址的出站 HTTPS 访问：

- `api2.cursor.sh` 和 `api2direct.cursor.sh`，用于 Agent 会话
- `agent-stores.s3.us-east-1.amazonaws.com` 和 `cloud-agent-artifacts.s3.us-east-1.amazonaws.com`，用于产物上传

不需要入站端口、公共 IP 或 VPN 隧道。如果使用代理，请在 worker 环境中设置 `HTTPS_PROXY` 或 `https_proxy`。

要禁用产物上传，请在 worker 上阻止到两个产物主机的出站流量。Worker 把产物上传到 `agent-stores.s3.us-east-1.amazonaws.com`，并且只在 Agent 的存储不可用时使用 `cloud-agent-artifacts.s3.us-east-1.amazonaws.com`，因此只阻止 `cloud-agent-artifacts` 并不能停止上传。同时阻止两者只会阻止产物上传。Agent 会继续工作，包括工具调用和结果，但它的产物不会出现在 pull request 或仪表盘中。

每个用户最多可以连接 10,000 个 worker，每个团队最多 10,000 个。对于更大的全公司部署，[联系我们](https://cursor.com/contact-sales?source=self-hosted-agents)讨论扩容。

## 工作原理

| 术语 | 定义 | 示例 |
| :------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Worker** | 你拥有的一台机器，用 Cursor CLI 注册到 Cursor。Agent 在这里完成工作：编辑文件、运行命令和访问代码。 | 你的 AWS 账号中的一台 Linux VM，或你桌上的一台 Mac mini。 |
| **Team Pool** | 你可以在 Cursor 客户端 UI 中选择的路由目标。聊天在团队池中等待，直到某个 worker 认领它们。一旦 worker 认领了一次聊天，该聊天中的所有活动都会转发到该 worker。 | `gpu` 团队池把需要 GPU 的请求路由到只由带 GPU 的机器提供服务。`ios` 团队池只由 Mac 提供服务，用于与 iOS 开发相关的聊天。 |
| **Controller** | 你运行的代码，根据需求调整 worker 容量。 | 一个请求到达没有空闲 worker 的团队池。你的控制器注意到并启动一台新机器。 |

使用 Cursor CLI 在你的机器上启动 worker。`agent worker start` 会打开一条到 Cursor 后端的长期出站 HTTPS 连接，Cursor 通过该连接发送 Agent 工具调用。Cursor 从不连入你的网络：只需要从你的机器到 Cursor 的出站连接。

Worker 有两种配置：

1. **My Machines。** 最适合个人工作流和一次性任务：你的开发机、一台空闲 VM，或一台你不想重建状态的机器。把机器连接到你的账号。多个 Agent 可以在同一台机器上运行。见 [My Machines](https://cursor.com/docs/cloud-agent/self-hosted/my-machines.md)。
2. **Team Pools。** 最适合团队和 Enterprise：共享容量、服务账号身份验证，以及集中管理的镜像。把机器注册到一个池名称下，Cursor 把每个新聊天路由到池中一台可用的机器，每台机器一个 Agent。运行控制器来扩缩该池。见 [Team Pools](https://cursor.com/docs/cloud-agent/self-hosted/pool.md)。

要在第三方 VM 或沙箱上运行 Team Pool worker，见[集成](https://cursor.com/docs/cloud-agent/self-hosted/integrations.md)。

## 自托管机器上的环境

[Cloud Agent 环境](https://cursor.com/docs/cloud-agent/setup.md#what-is-a-cloud-agent-environment)是 Cursor 托管的 Agent 启动时所依据的已保存设置：仓库、依赖、密钥、启动命令和网络访问。自托管运行只使用该环境的仓库。选择加入密钥同步的 Pool worker 还会获得团队和用户密钥。其他每一项环境设置只适用于 Cursor 托管的 Cloud Agents，因此你要在机器上设置它们。

**仓库。** Worker 如何处理环境的仓库取决于你如何运行它：

- 用 `--clone-git-repos` 启动的[任意仓库池](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#any-repo-pools) worker 会在请求的分支或提交上检出每个仓库。
- 由仓库支持的池 worker 或 My Machines worker 使用它已经有的检出，不会切换分支或克隆。你的 [`sessionStart` hook](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#hooks)会收到请求中的每个仓库，作为 `repo_urls` 和 `repos`，因此它可以克隆 worker 还没有的那些仓库。

**密钥。** 用 `--sync-dashboard-secrets` 启动的池 worker，在团队管理员于 [Cloud Agents 仪表盘](https://cursor.com/dashboard/cloud-agents#self-hosted)中打开 **Secret sync** 之后，会以环境变量的形式获得密钥：

- 对该次运行的仓库可用的团队和用户密钥。任意仓库池只获得不限定于某个仓库的密钥。
- 传给[创建 Agent](https://cursor.com/docs/cloud-agent/api/endpoints.md#create-an-agent)的 `envVars`。

环境范围的密钥和 Build Secrets 绝不会到达 worker。My Machines worker 不会获得仪表盘密钥，而是使用机器自己的凭证。

**其余一切。** 用自托管的对应项替换其余每一项设置：

| 环境设置 | 自托管对应项 |
| :------------------------------------------------------------------------------------------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 安装脚本和启动命令 | `.cursor/hooks.json` 中的 `sessionStart` hook、池 worker 上的 [`--on-session-start`](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#hooks)，或 worker 镜像 |
| 终端和端口 | hook 或你自己的进程管理器 |
| Dockerfile、镜像、快照和用户 | 机器或 worker 镜像，以 worker 的操作系统用户身份运行 |
| [Builds](https://cursor.com/docs/cloud-agent/builds.md) | 准备好的 worker 镜像，或来自 [worker 控制器](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#warm-pool)的预热 worker |
| [网络访问设置](https://cursor.com/docs/cloud-agent/security-network.md#network-access) | 机器的防火墙或 HTTPS 代理 |
| 环境范围的密钥 | 机器上的密钥，或通过 Secret sync 获得的团队和用户密钥 |
| `environment.json` 中的 MCP 服务器限制 | 团队的 [MCP 服务器](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#mcp-servers)，每次运行都可用，没有按环境的限制 |
| 仓库中的 `.cursor/environment.json` | worker 目录中的 `.cursor/hooks.json` |

## 实时工作区

用 `--connect-workspace` 启动 Linux worker，即可从 Cursor 桌面应用打开 Agent 的实时工作区：它的文件和改动、一个终端，以及转发的端口。它适用于 [My Machines](https://cursor.com/docs/cloud-agent/self-hosted/my-machines.md) 和 [Team Pools](https://cursor.com/docs/cloud-agent/self-hosted/pool.md) worker。

```bash
# My Machines
agent worker --connect-workspace start

# Team Pools
agent worker --pool my-pool --connect-workspace start
```

- **默认关闭。** Cursor 不会替你打开它。Worker 在启动时读取该标志，因此添加标志后请重启。要关闭它，请在不带该标志的情况下重启 worker。
- **仅 Linux。** 在 macOS 和 Windows 上，worker 会记录 `--connect-workspace requires a Linux worker`，并在没有实时工作区的情况下启动。这些 worker 的行为与现在一样。
- **Cursor 3.25 或更高版本。** Worker 只安装已发布签名能够验证的服务器构建。更早的 Cursor 版本会显示 “Update Cursor to 3.25 or later to open this workspace.”
- **多一个出站主机。** 当桌面应用打开它的某个 Agent 时，worker 会从 `cursor.blob.core.windows.net` 下载该 Cursor 版本的服务器构建到 `~/.cursor-server/bin/<commit>/`，每个版本一次，并在 `127.0.0.1` 上运行它。工作区流量使用 worker 现有的出站连接，因此不会打开入站端口。

实时工作区以 worker 的操作系统用户身份运行。任何能够打开该 worker 某个 Agent 的人，都会以该用户身份获得终端和文件访问：Agent 的所有者，以及当团队管理员在 [Cloud Agents 仪表盘](https://cursor.com/dashboard/cloud-agents)中打开 **Allow VM sharing** 时的队友。仓库中的任务和设置也以该用户身份运行，包括工作区打开时运行的任务。Worker 只服务于它已经在运行的 Agent。

### 排查实时工作区

### 为什么工作区显示 Worker offline？

该 Agent 的 worker 没有连接到 Cursor。请再次启动 worker。Worker 恢复后，桌面应用会重新连接。

### 为什么终端不可用？

终端标签页会说明原因。“To use the terminal, restart the worker with the --connect-workspace flag” 表示 worker 启动时没有该标志。“Terminal is only available on Linux workers” 表示它运行在 macOS 或 Windows 上。要确认标志已生效，请用 `--verbose` 重启，并在启动输出中查找 `cursor-server enabled (--connect-workspace)`。

### 为什么 Cursor 要求我更新到 3.25 或更高版本？

你的 Cursor 版本没有已签名的服务器构建，而 worker 拒绝未签名的构建。请更新桌面应用，然后再次打开该 Agent。

### 为什么服务器下载没有通过验证？

当下载与其 Cursor 签名不匹配，或携带无效签名时，worker 不会安装或运行任何内容，worker 日志会说明原因。如果机器上的另一个 Agent 或 worker 进程仍在运行该构建的一份副本，请结束该会话或停止该 worker，然后重试。字节不匹配的下载在重试时仍会失败。如果日志显示下载本身失败，请允许到 `cursor.blob.core.windows.net` 的出站 HTTPS。Cursor 不提供镜像。

## 支持的部署模式

在任何可以安装 Cursor CLI 及其依赖的地方运行 worker：

- **个人机器。** 通过 [My Machines](https://cursor.com/docs/cloud-agent/self-hosted/my-machines.md) 连接笔记本电脑、开发机、Mac 或远程 VM。
- **持久主机或容器。** 在 `systemd`、`launchd`、Docker 或其他进程管理器下运行一个或多个池 worker。
- **动态基础设施。** 使用内置的 [worker 控制器](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#worker-controller)或 Cloud Agents API，在请求到达时启动机器。
- **Kubernetes。** 从 [anysphere/k8s-workers](https://github.com/anysphere/k8s-workers) 模板开始。它在你的集群中运行 `agent worker controller --spawn`，并为每个已认领的请求创建一个 worker Pod，或用 `--warm-idle` 保持预热 Pod，无需 CRD。见[集成](https://cursor.com/docs/cloud-agent/self-hosted/integrations.md#reference-templates)。
- **合作伙伴主机和模板。** 借助合作伙伴指南，在 AWS Lambda、Cloudflare、Namespace、Modal、Daytona、E2B、Vercel、Tensorlake、Coder、Superserve 或 Render 上运行池 worker，或克隆 Cursor 的 AWS Lambda MicroVM、Cloudflare Containers 或 Kubernetes 参考模板。见[集成](https://cursor.com/docs/cloud-agent/self-hosted/integrations.md)。

部署指南、合作伙伴指南和模板是参考架构。你拥有 worker 镜像、基础设施、密钥、扩缩策略和生产验证。

## 费用

每种运行时选项都使用所选模型，并遵循其[价格](https://cursor.com/docs/models-and-pricing.md#model-pricing)。Cursor 管理的 Cloud Agents 包含执行基础设施。使用自托管机器时，你还要为机器、容器或集群付费并运维它们。

## 要求

每个 worker 都需要 [Cursor CLI](https://cursor.com/docs/cli/overview.md) 和出站 HTTPS 访问。在每台机器上安装 CLI：

```bash
curl https://cursor.com/install -fsS | bash
```

**My Machines**

- 个人凭证：浏览器登录，或来自 [Cursor Dashboard → API Keys](https://cursor.com/dashboard/api) 的个人用户 API 密钥。

  ```bash
  agent login
  ```

- 在团队中，由团队管理员在 [Cloud Agents 仪表盘](https://cursor.com/dashboard/cloud-agents#remote-machines)中打开 **Allow Personal Machines** 或 **Allow Self-Hosted Machines**。**Allow Personal Machines** 是团队的 Remote Control 设置。当两者都关闭时，Cloud Agents 不能在成员的机器上运行。

**Team Pools**

- 一份 Cursor Enterprise 方案。

- 用于 worker 身份验证的[服务账号 API 密钥](https://cursor.com/docs/account/enterprise/service-accounts.md)。个人登录和其他类型的 API 密钥不能启动池 worker。

  ```bash
  export CURSOR_API_KEY="<team service-account API key>"
  ```

- 由团队管理员在 [Cloud Agents 仪表盘](https://cursor.com/dashboard/cloud-agents#self-hosted)中配置的自托管设置：**Allow Self-Hosted Machines** 让用户选择加入，**Require Self-Hosted Machines** 把每一次 Cloud Agent 运行路由到你的 worker。

**Computer Use**（可选）

- 一台已登录桌面会话的 macOS worker。CLI 在首次启动时安装 **Cursor Computer Use** 辅助应用；在 **System Settings** 中授予它 **Accessibility** 和 **Screen Recording**。
- 或一台带有桌面包的 Linux worker。Worker 会创建自己的桌面，或复用现有的 X11 显示：

  ```bash
  sudo apt-get install -y --no-install-recommends \
    dbus-x11 ffmpeg tigervnc-standalone-server \
    x11-utils x11-xserver-utils xdotool xfce4
  ```

  macOS 权限步骤和完整的 Linux 设置见[计算机使用与桌面共享](https://cursor.com/docs/cloud-agent/self-hosted/computer-use.md)。

## 后续步骤

- [选择 Cloud Agents 的运行位置](https://cursor.com/docs/cloud-agent/self-hosted/choose-runtime.md)：比较托管的 Cloud Agents、My Machines 和 Team Pools。
- [My Machines](https://cursor.com/docs/cloud-agent/self-hosted/my-machines.md)：在几分钟内连接你的第一个 worker，然后配置个人 worker、工作区根目录和本地 MCP 服务器。
- [Team Pools](https://cursor.com/docs/cloud-agent/self-hosted/pool.md)：把 worker 组织成团队池，并用控制器[扩缩 worker 容量](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#worker-controller)。
- [集成](https://cursor.com/docs/cloud-agent/self-hosted/integrations.md)：AWS Lambda、Cloudflare、Namespace、Modal、Daytona、E2B、Vercel、Tensorlake、Coder、Superserve 和 Render 的合作伙伴指南，以及 AWS Lambda MicroVM、Cloudflare Containers 和 Kubernetes 的参考模板（[anysphere/k8s-workers](https://github.com/anysphere/k8s-workers)）。
- [Computer Use](https://cursor.com/docs/cloud-agent/self-hosted/computer-use.md)：让 Agent 在你的 worker 上操作桌面和浏览器。
- [API 参考](https://cursor.com/docs/cloud-agent/api/endpoints.md#workers-and-pools)：worker、池、待处理请求队列（列出、SSE 监视、认领和释放）以及 worker token 的端点。
- [自托管机器](https://cursor.com/help/ai-features/self-hosted-machines.md)：关于设置、Team Pools、集成和故障排除的简短解答。

### 把自托管机器带到你的 Enterprise

Team Pools 需要 Enterprise 方案。与销售讨论 worker 集群、私有连接和推广上线。

---

## 站点地图

[所有文档页面概览](/llms.txt)
