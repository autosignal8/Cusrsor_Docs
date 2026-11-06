> 原文：https://cursor.com/docs/grok-bot/private-networks

# 连接到私有网络

Grok Bot 计算机运行在 Cursor 的云端，并通过[共享的静态出口 IP 地址](https://cursor.com/docs/grok-bot/security.md#static-egress-ips)访问互联网。如果 Bot 需要的系统位于私有网络上，请把流量经由成员的桌面路由，或通过 **Team Setup Scripts** 在每台团队计算机上安装你组织的网络客户端。

**Team Setup Scripts** 仅限 Enterprise。其他方案上不会出现。**Network Controls 也仅限 Enterprise。** 那一层[网络策略](https://cursor.com/docs/grok-bot/security.md#network-policy)是独立的，仍然适用；能够到达私有网络并不能替代你的目的地允许列表。

## 你能做什么

- **把某一个成员的流量经由其桌面路由**，使目的地看到该设备的 IP 地址。解析到私有地址或 VPN 地址的站点仍会被阻止，除非已为你的组织允许。
- **自动在每台团队计算机上安装你的网络客户端**，来自一份由管理员管理的清单，无需逐台设置。
- **让 Bot 到达私有网络上的服务**，而不必把这些服务暴露到互联网，也不必把共享出口 IP 加入你的允许列表。
- **控制权留在你这一侧。** 你的网络、你的访问规则、你的身份提供商。你可以随时从自己的管理控制台撤销一台计算机。

## 选择连接方式

| 方式 | 适用场景 |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| 经由桌面路由流量 | 某一个成员需要让公共站点看到其设备的 IP 地址，例如某个站点把办公室 IP 加入了允许列表 |
| 用 Team Setup 安装网络客户端 | 你的 Enterprise 团队需要在每台托管计算机上有一致的连接 |

### 经由成员的桌面路由

在 Grok Bot 桌面应用中，打开 **Settings** > **Computer**，并在 **Your computer** 下打开 **Route traffic through this computer**。该路由使用当前设备的网络和 IP 地址。当设置被关闭，或 Enterprise 管理员禁用 **Allow Local Egress Routing** 时，路由停止。

该路由不会向 Bot 开放你的私有网络。解析到私有地址或 VPN 地址的站点会被阻止，除非已为你的组织允许，并且该设置会显示本次会话中阻止了多少个站点。要到达私有服务，请[用 Team Setup 安装网络客户端](https://cursor.com/docs/grok-bot/private-networks.md#install-a-networking-client-with-team-setup)。

每个成员控制自己的桌面路由。Enterprise 管理员可以从 [Cursor 仪表盘中的 Grok Bot](https://cursor.com/dashboard/bot) 为整个团队移除该选项。见[通过你的桌面路由流量](https://cursor.com/docs/grok-bot/settings.md#route-traffic-through-your-desktop)。

### 用 Team Setup 安装网络客户端

这是你自己运行的一种模式，不是 Cursor 管理的网络模式。Cursor 提供的是挂钩：Team Setup Scripts 在每台团队计算机上运行你的安装脚本。其余由你负责：安装客户端、配置它、让计算机完成身份验证、维护访问规则，并跟上供应商的变化。Cursor 默认不安装任何东西，不运行或监控你的网络客户端，仪表盘也不显示客户端状态。供应商的文档是安装和配置其软件的权威来源；本页覆盖的是 Cursor 这一侧。

## 开始之前

- 你是 Enterprise 方案上的团队管理员。**Team Setup Scripts** 仅限 Enterprise，其他方案上不会出现。
- 你的网络客户端运行在基于 Debian 的 Linux 上，并且可以从 shell 脚本安装和启动。团队计算机运行 Linux，脚本以计算机用户身份运行，并且可以使用 `sudo`。
- 你的网络为该客户端提供了进入方式：按供应商的架构，在你想到达的 VPC 或内网内有网关、出口节点或隧道连接器。
- 你已经决定计算机如何向你的网络做身份验证。不要把凭证放进设置脚本；如果脚本需要凭证，把它存为 [Team Secret](https://cursor.com/docs/grok-bot/teams.md#team-secrets)。

## Team Setup 如何运行你的脚本

**Team Setup Scripts** 位于 [Cursor 仪表盘](https://cursor.com/dashboard/bot) **Grok Bot** 页面的 **Bot Computers** 下：在每台团队计算机上运行的安装脚本清单。每个清单包含一个或多个脚本条目，每个条目有 **ID**、**Setup Script**，以及可选的 **Check Script**。要只在同一批成员的计算机上运行清单，改为在该组的 Grok Bot 标签页下添加；见[组设置](https://cursor.com/docs/grok-bot/teams.md#group-settings)。组清单遵循与下面相同的规则。

- 脚本在每台团队计算机上**以计算机用户身份**运行；特权安装使用 `sudo`。
- 它们在计算机启动时运行，并在运行期间定期刷新，大约每天一次。
- 如果存在 Check Script 且退出码为 0，则跳过 Setup Script；用它避免每次都重新安装。设置运行之后，检查会再运行一次以验证成功。
- 条目按顺序一次运行一个。每个脚本有 30 分钟超时。失败的脚本不会阻止计算机；它会在以后的刷新中重试。
- 脚本把团队的 [Team Secrets](https://cursor.com/docs/grok-bot/teams.md#team-secrets) 作为环境变量读取，因此脚本需要的凭证永远不会出现在清单中。机密只在脚本运行期间存在，脚本输出在记录之前会被涂黑。设置来自多个团队的计算机不会收到任何机密。

## 创建清单

### 打开 Team Setup Scripts

前往 [Cursor 仪表盘中的 Grok Bot](https://cursor.com/dashboard/bot)，在 **Bot Computers** 下找到 **Team Setup Scripts**。

### 创建清单

在 **Manifests** 旁边选择 **+**，创建 **New Manifest**，然后输入 **Manifest ID**，例如 `private-network`。

### 编写设置脚本

在脚本条目中设置 **ID**（例如 `install-network-client`），并编写 **Setup Script**：按照提供商的 Linux 安装文档安装你的客户端，然后按你的配置规定的方式启动它并连接到你的网络。

### 添加检查脚本

可选地添加 **Check Script**（例如 `command -v <your-client>`），这样已经有该客户端的计算机会跳过安装。

### 保存

选择 **Save**。

更喜欢编辑 JSON？在编辑器顶部从 **Form** 切换到 **JSON**。清单结构：

```json
{
  "manifestId": "private-network",
  "entries": [
    {
      "id": "install-network-client",
      "setup": "#!/usr/bin/env bash\nset -euo pipefail\n# 1) Install your networking client per your provider's Linux install docs\n# 2) Start the client and connect it to your network per your configuration",
      "check": "command -v <your-client>"
    }
  ]
}
```

不要把机密值粘贴进脚本。设置脚本以计算机用户身份在每台团队计算机上运行，清单是应用到整个机群的纯文本。如果脚本需要身份验证密钥、token 或其他凭证，把它存为 [Team Secret](https://cursor.com/docs/grok-bot/teams.md#team-secrets)，并从环境中读取，例如 `"$MY_AUTH_KEY"`。在供应商支持的地方，优先让成员在计算机的浏览器中交互式登录，那里适用你的身份提供商策略。

## 设置你的网络客户端

无论使用什么工具，最后一步都一样：每台计算机向你的网络做身份验证，你在供应商的控制台中确认连接，然后让 Bot 去访问一个它以前到不了的内部主机名来验证。交互式登录发生在计算机的浏览器中，与成员已经[在那里登录公司工具](https://cursor.com/docs/grok-bot/identity.md)的方式相同。在全团队推出之前，先从一两台试点计算机开始。

### Tailscale

在每台计算机上运行 Tailscale，并经由你网络内的[出口节点](https://tailscale.com/docs/features/exit-nodes)路由。这是该模式已经实际跑通过的配置。Tailscale 还提供用于暴露特定私有网段的[子网路由器](https://tailscale.com/docs/features/subnet-routers)；在依赖它之前，先在一台试点计算机上验证这种安排。

**开始之前：** 你运营一个 tailnet，并在你想到达的 VPC 或内网内有一个出口节点。

**Team Setup 清单：**

- **Setup Script：** 按照 [Tailscale 的安装文档](https://tailscale.com/docs/install/linux)为 Linux 安装 Tailscale，然后按你的配置把计算机加入 tailnet。
- **Check Script：** `command -v tailscale`。

**连接并验证：**

1. 让每台计算机完成身份验证。Tailscale 会打印一个登录 URL，在计算机的浏览器中打开，那里适用你的身份提供商策略。如果你希望计算机加入时不需要成员登录，把一个可复用、带标签的 [auth key](https://tailscale.com/docs/concepts/auth-keys) 存为 [Team Secret](https://cursor.com/docs/grok-bot/teams.md#team-secrets)，并从环境中传入，例如 `tailscale up --auth-key "$TS_AUTHKEY"`。然后每台团队计算机都在同一把密钥和同一组标签下加入，而不是在成员的身份下加入，因此要相应地限定该密钥的 ACL。绝不要把密钥粘贴进脚本本身。
2. 确认该计算机出现在你的 Tailscale 管理控制台中，并且被允许使用出口节点。
3. 让 Bot 访问一个内部主机名。

**如果它不工作：**

- 客户端已安装，但没有人完成身份验证。使用浏览器登录时，这一步是手工的；使用 auth key 时，检查 Team Secret 是否存在、其名称是否与脚本读取的一致，以及密钥是否已过期。在 Tailscale 管理控制台中检查机器列表。
- 你的团队网络策略是仅允许列表，并且阻止了 Tailscale 的协调服务器或中继。允许 Tailscale 文档中的端点。正在运行的计算机大约在一分钟内应用更改。休眠的计算机在下次唤醒时应用。
- 出口节点没有在你的 tailnet 中宣告或批准。在管理控制台中检查路由设置。
- 计算机被重建了，例如在镜像更新或重置之后，会话没有保留下来。重新进行身份验证。

### Cloudflare Tunnel

同样的 Team Setup 机制适用于 [Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/)：你网络内的 `cloudflared` 连接器通过 Cloudflare 的边缘发布私有服务，由 Cloudflare Access 保护，计算机按主机名到达它们。这种安排还没有像 Tailscale 那样在团队计算机上实际跑通过，因此在推出之前先在一台试点计算机上验证。

**开始之前：** 你在私有网络内运行 Cloudflare Tunnel 连接器，所需服务已通过它路由，并且你已经选定 Cloudflare Access 策略。基于身份的策略很适合这种模式，因为成员通过浏览器登录。Access 服务 token 是机密；如果使用它们，存为 [Team Secrets](https://cursor.com/docs/grok-bot/teams.md#team-secrets)，而不是放进设置脚本。

**Team Setup 清单：**

- **Setup Script：** 按照 [Cloudflare 的下载文档](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/downloads/)安装 `cloudflared`。计算机上只放客户端；连接器留在你的网络中。
- **Check Script：** `command -v cloudflared`。

**连接并验证：**

1. 私有 HTTP 服务：通过你经由隧道路由的主机名访问它们。Cloudflare Access 强制执行你的策略；使用基于身份的策略时，成员通过计算机的浏览器登录。
2. 私有 TCP 服务，例如数据库或 SSH：在计算机上运行 `cloudflared access tcp`，打开一个本地监听器，并把工具指向它。Cloudflare 的文档涵盖了这个流程。Bot 可以在需要该服务时在自己的 shell 中启动监听器。
3. 让 Bot 访问该私有主机名或服务。

**如果它不工作：**

- 你网络内的连接器已停止。在 Cloudflare 仪表盘中检查隧道健康状况；这是你这一侧的失败，不是计算机的。
- Cloudflare Access 拒绝了请求。检查 Access 日志，并确认成员已完成身份验证。
- 你的团队网络策略是仅允许列表，并且阻止了隧道主机名或 Cloudflare 的端点。允许它们。正在运行的计算机大约在一分钟内应用更改。休眠的计算机在下次唤醒时应用。
- 服务 token 被嵌进了设置脚本。不要这样做；清单是纯文本。使用基于身份的 Access，或把 token 存为 Team Secret，并让脚本从环境中读取。
- Bot 需要时，`cloudflared access tcp` 监听器没有在运行。监听器不会跨会话保留；需要时再启动一个。

如何选择：Tailscale 给计算机网络级的可达性，并且有出口节点时，出口来自你的网络。Cloudflare Tunnel 通过 Cloudflare 的边缘、在 Access 之后发布特定服务：按服务而不是整个网络，其他服务看到的出口地址不会改变。

其他能在基于 Debian 的 Linux 上安装并运行的客户端遵循相同的 Team Setup 步骤；在推出之前先在一台试点计算机上验证你的客户端。

## 向现有计算机推出

- **新计算机**在启动时应用清单。
- **正在运行的计算机**在定期刷新时采用清单更改，大约每天一次。
- **要立即应用清单更改**，重建计算机，或让成员从桌面应用重置它。在 Enterprise 上，管理员可以从仪表盘重建或终止成员的计算机。持久磁盘会保留，下一台计算机在启动时应用当前清单。
- **镜像更新**会自动重建计算机，你的脚本会重新应用。登录会话，包括网络客户端的登录，可能需要在计算机重建后重新建立。

## 与网络策略配合

Grok Bot [网络策略](https://cursor.com/docs/grok-bot/security.md#network-policy)是独立的一层，控制团队计算机可以到达哪些目的地。如果你的团队使用 **Team allowlist only**，请根据供应商文档添加网络客户端需要的目的地，例如协调服务器、中继和网关。正在运行的计算机大约在一分钟内应用策略更改。休眠的计算机在下次唤醒时应用。你不需要重建计算机。

## 限制

- 这是你自己运营的一种模式，不是 Cursor 管理的网络模式。Cursor 不安装、不运行、也不监控你的网络客户端，客户端本身的支持来自你的供应商。
- 除非你把流量经由自己的网络路由，常规互联网流量仍然通过 Cursor 的共享静态 IP 范围出口。没有按客户专用的出口 IP。
- 目前没有脚本结果的机群视图。在全团队推出之前先验证一台试点计算机。
- 这不是 Cloud Agents 的设置。Cloud Agents 文档有自己的 [Tailscale](https://cursor.com/docs/cloud-agent/setup.md#running-tailscale) 和 [Cloudflare Tunnel](https://cursor.com/docs/cloud-agent/setup.md#running-cloudflare-tunnel) 做法，针对的是另一个产品界面；Grok Bot 计算机按本页所述通过 Team Setup 配置。如果 Grok Bot 把工作委派给 Cloud Agents，那些 Agent 在 Cloud Agent 网络设置下运行，而不是在你计算机的网络客户端下运行。

## 常见问题

### 我可以把 auth key 或凭证放进设置脚本吗？

不要放进脚本文本。清单是纯文本，仪表盘会警告不要把机密粘贴进去。把凭证存为 Grok Bot 页面上的 [Team Secret](https://cursor.com/docs/grok-bot/teams.md#team-secrets)，并让脚本把它作为环境变量读取。Team Secrets 仅限 Enterprise。在供应商支持的地方，交互式浏览器登录仍然是最简单的选择，因为你的身份提供商策略会适用于它。

### 我可以使用哪些网络工具？

任何你的团队可以从 shell 脚本在基于 Debian 的 Linux 上安装并运行的客户端。Tailscale 和 Cloudflare Tunnel 是本页的具体示例。先在一台试点计算机上验证你的客户端。

### 现有计算机会得到新清单吗？

会。正在运行的计算机会定期刷新清单，大约每天一次。要立即应用清单更改，重建计算机，或让成员从桌面应用重置它。

### 这会改变我的服务看到的 IP 地址吗？

对于经由你的网络路由的流量，会。到你私有网络的流量从你的网络内部到达，经由你的出口节点、网关或隧道连接器。其他一切仍从 Cursor 的共享静态范围出口。

### 我们使用严格的网络允许列表。我们的客户端能工作吗？

只有在你允许它的端点时才行。把客户端需要的目的地加入团队允许列表。正在运行的计算机大约在一分钟内应用更改。休眠的计算机在下次唤醒时应用。

### 这和 Cloud Agents 文档中的 Tailscale 与 Cloudflare Tunnel 章节一样吗？

不一样。那些做法专门针对 Cloud Agent VM；那里的 Tailscale 需要用户空间网络和代理变量。Grok Bot 计算机使用 Team Setup 以及你自己的网关或连接器，如本页所述。

### 哪些方案包含 Team Setup Scripts？

**Team Setup Scripts** 仅限 Enterprise。团队管理员管理清单。如果你在仪表盘的 Grok Bot 页面上看不到它，说明你不在 Enterprise 上，或者需要客户团队为组织启用 Grok Bot。

### 如果设置脚本在某些计算机上失败会怎样？

计算机仍会启动。失败的条目会在以后的刷新中重试。让脚本可以重复运行，并使用 Check Script，使健康的计算机跳过安装。

## 相关页面

- [Grok Bot 安全](https://cursor.com/docs/grok-bot/security.md)
- [面向 Teams 与 Enterprise 的 Grok Bot](https://cursor.com/docs/grok-bot/teams.md)
- [配置身份与访问](https://cursor.com/docs/grok-bot/identity.md)
- [配置检查 TLS 的代理](https://cursor.com/docs/grok-bot/proxies.md)，针对从成员设备到 Cursor 的路径
- [Cloud Agents：运行 Tailscale](https://cursor.com/docs/cloud-agent/setup.md#running-tailscale) 和[运行 Cloudflare Tunnel](https://cursor.com/docs/cloud-agent/setup.md#running-cloudflare-tunnel)


---

## 站点地图

[所有文档页面概览](/llms.txt)
