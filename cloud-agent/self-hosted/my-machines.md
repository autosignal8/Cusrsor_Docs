> 原文：https://cursor.com/docs/cloud-agent/self-hosted/my-machines

# My Machines

My Machines 是个人的[自托管机器](https://cursor.com/docs/cloud-agent/self-hosted.md)配置。它让某个特定用户在自己已经在用的机器上运行 Cloud Agent 工具调用：一台笔记本、devbox 或远程 VM。当那台机器就是某个仓库所需要的执行环境时，使用它。

你机器上的 worker 打开一条到 Cursor 的出站连接。Agent 循环在 Cursor 的云中运行，但终端命令、文件编辑、浏览器操作和其他工具调用在你的机器上执行。不需要入站端口，也不需要更改防火墙。

Cursor 管理的 Cloud Agents 是大多数团队的推荐路径，包括需要访问私有网络的团队。你可以使用网络允许列表、Tailscale 或类似客户端，以及面向受支持源代码管理路径的私有连接，而无需自己运维 worker。见[选择 Cloud Agents 的运行位置](https://cursor.com/docs/cloud-agent/self-hosted/choose-runtime.md)。

在以下情况使用 My Machines：

- 使用一台已经有你的仓库和工具的 devbox 或远程工作站
- 为某个特定仓库，在一位用户的机器上执行工具调用
- 复用你不想在云环境中重新创建的、机器本地的状态
- 在构建集中管理的池之前，先试用 worker 模型

对于全组织的 worker 集群，见 [Team Pools](https://cursor.com/docs/cloud-agent/self-hosted/pool.md)。

## 快速入门

### 1. 安装 CLI

```bash
# macOS, Linux, and WSL
curl https://cursor.com/install -fsS | bash

# Windows PowerShell
irm 'https://cursor.com/install?win32=true' | iex
```

确认 CLI 可用：

```bash
agent --version
```

### 2. 登录

对于个人机器，浏览器登录是最简单的路径：

```bash
agent login
```

### 3. 启动 worker

```bash
agent worker start
```

使用这台机器时，让该进程保持运行。默认情况下，My Machines worker 是长期存活的：它会一直保持连接，直到你停止它，并且可以复用于以后的 Cloud Agent 会话。

### 4. 运行一个 Agent

1. 前往 [cursor.com/agents](https://cursor.com/agents)。
2. 在设置 Agent 运行位置的菜单中，于 **My Machines** 下选择该机器。
3. 发送一个任务。

![Cursor 的 Run on 菜单，My Machines 下高亮显示 my-devbox](/docs-static/images/cloud-agent/my-machines-picker.png)

## 常用选项

### 为机器命名

当你为同一个仓库有多台机器时，使用一个易于识别的名称：

```bash
agent worker start --name "my-devbox"
```

### 从另一个仓库目录运行

```bash
agent worker start --worker-dir /path/to/repo
```

重复 `--worker-dir` 来注册多个仓库根目录：

```bash
agent worker \
  --worker-dir "$HOME/repos/app" \
  --worker-dir "$HOME/repos/infra" \
  start
```

每个路径都必须存在。对于每一个带有 Git 远程的根目录，worker 会注册路由元数据，以便 Cursor 把请求匹配到正确的检出。

你用 [`POST /v1/agents`](https://cursor.com/docs/cloud-agent/api/endpoints.md#create-an-agent)（`env.type: "machine"`）在某台机器上启动的 Agent，在 `repos` 中得到一个仓库。要用多个仓库启动一个 Agent，请使用[任意仓库池](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#any-repo-pools)。

在你的机器上，Cloud Agent 环境只设定一次运行使用哪些仓库。机器保留它自己的工具、凭证和网络，并且不会获得仪表盘密钥。见[自托管机器上的环境](https://cursor.com/docs/cloud-agent/self-hosted.md#environments-on-self-hosted-machines)。

### 使用 API 密钥

对于浏览器登录不现实的 devbox 或自动化，请使用来自 [Cursor Dashboard → API Keys](https://cursor.com/dashboard/api) 的个人用户 API 密钥：

```bash
agent worker start --api-key "your-user-api-key"
```

My Machines worker 需要个人凭证：浏览器登录、个人用户 API 密钥，或用户范围的 token。[服务账号 API 密钥](https://cursor.com/docs/account/enterprise/service-accounts.md)只能启动池 worker（`--pool`），而团队 Admin API 密钥和组织 API 密钥完全不能启动 worker。团队共享的 worker 见[自托管池](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#authenticate-workers)。

### 使用用户范围的 token

对于自行管理的、按用户划分的 worker，用 [`POST /v1/sub-tokens`](https://cursor.com/docs/cloud-agent/api/endpoints.md#create-a-user-scoped-worker-token) 签发一个短期的用户范围 token，然后用该 token 启动 worker：

```bash
agent worker start --auth-token "your-user-scoped-token"
```

对于长期存活的 worker，从文件读取 token：

```bash
agent worker start --auth-token-file /var/run/cursor/token
```

这在 Kubernetes 中很有用，因为来自 Secrets 的环境变量在 Pod 启动时就固定了。Secret 卷在 Pod 运行期间会更新，而挂载的 token 路径可以在 Pod 内实时更新，让你有机会在 Pod 运行时刷新该 token。

### 签发身份 token

在 `start` 之前传入 `--identity-socket`，让被认领的 Agent 签发短期 OIDC token。Worker 把 `CURSOR_AGENT_SOCKET` 设为该套接字路径。该 token 标识这次运行的所有者。套接字约定、信任模型以及 AWS，见 [OIDC token](https://cursor.com/docs/cloud-agent/identity.md#self-hosted-workers)。

```bash
agent worker --identity-socket start
```

### 启用 Computer Use

在 `start` 之前传入 `--computer-use`，让 Agent 在这台机器上点击、键入、截屏并驱动应用：

```bash
agent worker --computer-use --name "my-mac" start
```

在 macOS 上，首次启动会安装 **Cursor Computer Use** 辅助应用。在 System Settings → Privacy & Security 中授予它 **Accessibility** 和 **Screen Recording**，然后用一个会截屏的任务测试。在 Linux 上，请先安装桌面包。macOS 权限步骤、MDM 指导以及 Linux 显示选项，见 [Computer Use 与桌面共享](https://cursor.com/docs/cloud-agent/self-hosted/computer-use.md)。

### 打开实时工作区

在 Linux 机器上，于 `start` 之前传入 `--connect-workspace`，即可从 Cursor 桌面应用打开某个 Agent 的文件、改动、终端和转发的端口。它需要 Cursor 3.25 或更高版本，并以你的操作系统用户身份运行。谁可以打开它，以及如何关闭它，见[实时工作区](https://cursor.com/docs/cloud-agent/self-hosted.md#live-workspace)。

```bash
agent worker --connect-workspace start
```

## 从聊天界面触发这台机器

当你希望 Slack、GitHub 或 Linear 请求在你的某台具名机器上运行时，使用 `worker=` 或 `machine=`。这是仅有的、会指向 My Machines 的触发选项。

用 [`--name`](https://cursor.com/docs/cloud-agent/self-hosted/my-machines.md#name-the-machine) 启动该机器，然后在请求中包含该名称：

- 在 Slack 中，使用 `@Cursor worker=my-devbox 修复这个不稳定的测试` 或 `@Cursor machine=my-devbox 修复这个不稳定的测试`。
- 在 GitHub 中，评论 `@cursoragent worker=my-devbox 修复这个不稳定的测试` 或 `@cursoragent machine=my-devbox 修复这个不稳定的测试`。你必须是受信任的仓库评论者，并且目标机器必须属于与你的 GitHub 账号关联的那个 Cursor 用户。
- 在 Linear 中，在评论里提及 `@Cursor` 来启动这次运行。只有当该评论触发这次运行时，Cursor 才会接受 issue 正文中的 `worker=my-devbox` 或 `machine=my-devbox`。只有对同一次触发，Cursor 才会接受父标签名为 `worker` 或 `machine`、子标签名为 `my-devbox` 的标签。把该 issue 分配或委派给 Cursor 时，会忽略 issue 正文和这些标签。

### Cursor 如何挑选你的机器

只有当以下三点全部成立时，`worker=<name>` 请求才会在某台机器上运行：

1. 该机器属于触发该请求的 Cursor 用户。
2. 该机器的 `--name` 与所请求的 `<name>` 匹配。
3. 该机器已注册的仓库与触发的目标仓库匹配。

触发的目标仓库来自该界面，而不是来自机器名称：

- **Slack** 依次使用你消息中的 `repo=`（如果有）、频道默认仓库、你的用户默认仓库，然后是团队默认仓库。
- **Linear** 使用从 issue 或项目解析出的仓库（例如 `[repo=]`、issue 标签、项目标签，或仪表盘默认值）。见[仓库选择](https://cursor.com/docs/integrations/linear.md#repository-selection)。
- **GitHub** 使用提及了 `@cursoragent` 的那个 issue、pull request 或评审评论所在的仓库。

每台机器已注册的仓库来自其 worker 目录的 git 远程。要让一台机器服务多个仓库，为每个检出传入一次 [`--worker-dir`](https://cursor.com/docs/cloud-agent/self-hosted/my-machines.md#run-from-a-different-repo-directory)，或在每个仓库的检出中各启动一个 worker。

### 当 `worker=` 请求无法运行时

如果你有一台叫这个名字的机器，但它是为另一个仓库注册的，Cursor 会拒绝该请求，而不会在错误的检出上运行它：

> `worker=<name>` is registered on your machine but for a different repository. Start the worker in a checkout of the target repo first.

该错误在 Slack 中显示为一条临时回复，在 Linear 中显示为一条 Agent 活动错误，在 GitHub 上则对受信任的评论者显示为一条 `@cursoragent` 回复。这种行为是有意的：针对仓库 A 的请求绝不应当在仓库 B 的机器检出上运行。

如果没有机器同时匹配已关联的用户和目标仓库，该请求会失败，而不会回退到另一个环境。请确认机器名称、你的 Cursor 账号关联，以及 worker 目录的 git 远程。

单独的 `self_hosted`、`pool=` 和 `repo=` 不会指向 My Machines。请把它们用于 [Team Pool](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#triggering-pool-agents) worker。当你把 `repo=` 与 `worker=` 一起使用时，它设定 Cursor 用哪个仓库去匹配你的机器。

## Hooks

My Machines worker 运行与其他自托管机器 worker 相同的 hooks：来自你启动 worker 的那个工作区中 `.cursor/hooks.json` 的、基于命令的 hooks。在 Enterprise 上，它也会运行团队 hooks 和 Enterprise 管理的 hooks。

[池上的 Hooks](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#hooks)涵盖在 worker 上适用的内容，包括会话认领和释放该机器时的 `sessionStart` 和 `sessionEnd`。[Hooks 参考](https://cursor.com/docs/hooks.md)涵盖模式、事件和示例。

## 产物

自托管 worker 与 Cursor 托管的 Agent 上，产物行为相同。Agent 在 worker 内部生成产物，worker 通过 HTTPS 把它上传到 Cursor 管理的存储。下游的一切（PR 嵌入、仪表盘预览、通知附件）由 Cursor 的后端处理，不取决于 worker 运行在哪里。

产物默认开启。它们在 UI 中的样子，见[能力](https://cursor.com/docs/cloud-agent/capabilities.md#demos-and-artifacts)。

要禁用产物上传，请阻止到 `agent-stores.s3.us-east-1.amazonaws.com` 和 `cloud-agent-artifacts.s3.us-east-1.amazonaws.com` 的出站流量。Worker 把产物上传到前一个主机，并且只在 Agent 的存储不可用时使用后一个，因此只阻止 `cloud-agent-artifacts` 并不能停止上传。Agent 会话会继续工作；会话期间产生的产物会上传失败。

## 网络

Worker 需要对以下地址的出站 HTTPS 访问：

- `api2.cursor.sh` 和 `api2direct.cursor.sh`，用于 Agent 会话
- `downloads.cursor.com`，用于 CLI 更新，以及在 macOS 上首次安装 [Cursor Computer Use](https://cursor.com/docs/cloud-agent/self-hosted/computer-use.md#macos)
- `agent-stores.s3.us-east-1.amazonaws.com` 和 `cloud-agent-artifacts.s3.us-east-1.amazonaws.com`，用于[产物](https://cursor.com/docs/cloud-agent/self-hosted/my-machines.md#artifacts)上传
- `cursor.blob.core.windows.net`，用于下载[实时工作区](https://cursor.com/docs/cloud-agent/self-hosted/my-machines.md#open-the-live-workspace)服务器，仅限于用 `--connect-workspace` 启动的 worker

如果防火墙只能匹配通配符，`*.s3.us-east-1.amazonaws.com` 会覆盖这两个产物主机，但也会打开该区域中的每一个其他存储桶。当防火墙支持时，优先使用精确主机规则。

不需要入站端口、公共 IP 或 VPN 隧道。如果使用代理，请在 worker 环境中设置 `HTTPS_PROXY` 或 `https_proxy`。

### 失败模式

| 如果你阻止…… | 效果 |
| --- | --- |
| `api2.cursor.sh` 或 `api2direct.cursor.sh` | Worker 无法启动或继续 Agent 会话。 |
| `downloads.cursor.com` | CLI 更新，以及 macOS 上首次安装 Cursor Computer Use，都会失败。已经装好这二者的 worker 会继续运行。 |
| `agent-stores.s3.us-east-1.amazonaws.com` 和 `cloud-agent-artifacts.s3.us-east-1.amazonaws.com` | 产物上传失败。依赖产物的 PR 嵌入、仪表盘预览和通知附件会缺失。Agent 会话和其他工具调用会继续工作。 |
| `cursor.blob.core.windows.net` | 在用 `--connect-workspace` 启动的 worker 上，实时工作区无法启动。Agent 会继续运行。 |
| 某个特定工具或集成所需要的出站主机 | 只有该工具或集成会失败。Agent 会继续。 |

## MCP 服务器

MCP 服务器按传输类型路由：

| 传输 | 运行位置 | 使用场景 |
| --- | --- | --- |
| Command (stdio) | 你的机器 | MCP 进程在你的机器上启动，可以到达私有网络、内部 API 和本地服务。 |
| HTTP / SSE (url) | Cursor 后端 | Cursor 为基于 HTTP 的 MCP 服务器处理 OAuth、会话缓存和身份验证。 |

如果 MCP 服务器需要到达你私有网络上的端点，请使用 command (stdio) 传输。进程直接在你的机器上运行，并共享它的网络。对于基于 HTTP 的 MCP 服务器，Cursor 从其后端管理连接。

**Authenticate**（浏览器登录）只适用于 HTTP 服务器。把命令服务器的凭证作为环境变量，放在该服务器的 [`env`](https://cursor.com/docs/mcp.md#stdio-server-configuration) 配置中。在 cursor.com/agents 的 MCP 下拉菜单中编辑个人服务器，团队服务器则在 **Dashboard > Plugins & MCPs** 下。见 [MCP 工具](https://cursor.com/docs/cloud-agent/capabilities.md#mcp-tools)。

## 故障排除

运行一份预检调试报告：

```bash
agent worker debug
```

这会检查身份验证、隐私路由、仓库标签，以及 Cursor 能否看到匹配的 worker。要在启动 worker 之前打印同样的诊断信息，请使用 `agent worker start --debug`。

如果该机器没有出现在选择器中：

- 确认 worker 进程仍在运行。
- 确认 Cursor 应用和 CLI 使用同一个账号。
- 检查 worker 目录是否有预期的 Git 远程。
- 检查到[网络](https://cursor.com/docs/cloud-agent/self-hosted/my-machines.md#networking)中所列主机的出站访问。

如果实时工作区在桌面应用中打不开，见[排查实时工作区](https://cursor.com/docs/cloud-agent/self-hosted.md#troubleshoot-the-live-workspace)。

如果 Computer Use 在 Mac 上失败，该报告会确认 **Cursor Computer Use** 是否已安装，但不会确认其权限是否已授予。在 System Settings → Privacy & Security 中把 **Accessibility** 和 **Screen Recording** 授予 Cursor Computer Use，然后重试一个截屏任务。见 [macOS](https://cursor.com/docs/cloud-agent/self-hosted/computer-use.md#macos)。

## 后续步骤

- [Computer Use](https://cursor.com/docs/cloud-agent/self-hosted/computer-use.md)：让 Agent 在你的机器上驱动桌面和浏览器，并从 Cursor 观看或控制 Agent 桌面。
- [API 参考](https://cursor.com/docs/cloud-agent/api/endpoints.md#workers-and-pools)：worker、池、待处理请求队列和 worker token 的端点。

---

## 站点地图

[所有文档页面概览](/llms.txt)
