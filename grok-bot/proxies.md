> 原文：https://cursor.com/docs/grok-bot/proxies

# 配置检查 TLS 的代理

Grok Bot 桌面应用从每个成员的设备连接到两个地方：Cursor 的 API，位于 `*.cursor.sh`，用于聊天、登录和批准；以及成员的托管计算机，位于嵌套的 `*.*.cursorvm.com` 主机名，用于计算机设置、屏幕和 shell。会检查 TLS 的安全 Web 网关常常放行前者而弄坏后者。桌面应用随后会在计算机设置期间挂起或报错；在某些设置中，聊天仍能工作，而计算机永远连不上。Zscaler 是最常见的例子，本页用它说明具体做法；同样的步骤适用于任何会重新签名 TLS 或缓冲响应的网关。本页面向运行你网关的 IT 团队。共享的域名列表和流式测试位于[企业网络配置](https://cursor.com/docs/enterprise/network-configuration.md)；本页覆盖 Grok Bot 额外需要的部分。

本页讲的是从成员设备到 Cursor 的路径。它不改变托管计算机本身可以连接到哪里；那是[网络策略](https://cursor.com/docs/grok-bot/security.md#network-policy)。从计算机到达私有网络上的服务，见[连接到私有网络](https://cursor.com/docs/grok-bot/private-networks.md)。

## 症状

- **计算机设置挂起或失败。** 应用永远无法完成与计算机的连接。聊天可能继续工作，因为它能到达 `api2.cursor.sh`，也可能一起卡住；无论哪种，计算机链路都需要 `cursorvm.com`。
- **在热点或个人设备上能用**，在公司网络上或网关客户端运行时失败。
- **在办公室能用，在家失败。** 例外只应用到了办公室位置。见[应用到每个配置文件，包括离网](https://cursor.com/docs/grok-bot/proxies.md#apply-to-every-profile-including-off-network)。
- **登录或聊天也卡住。** `cursor.sh` 上的 TLS 检查或响应缓冲仍然开启。

## 允许这些域名模式

在网关和任何 DNS 过滤中允许以下全部。优先使用通配符，而不是逐个列出主机名；计算机主机名是按计算机生成的。

| 模式 | 用途 |
| ------------------------------------ | -------------------------------------------------------- |
| `*.cursor.sh` | 聊天、登录、批准，以及 Cursor API 的其余部分 |
| `*.cursor-cdn.com` | 静态资源 |
| `*.cursorapi.com` | 扩展市场及相关 API |
| `*.cursorvm.com` | 托管计算机及其控制面 |
| `*.*.cursorvm.com` | 同上，再深一层。必需。 |
| `cursor.com`、`downloads.cursor.com` | 安装和更新桌面应用 |

两个 `cursorvm.com` 模式都要添加。计算机主机名在 `cursorvm.com` 之下有两级标签，形式为 `<computer>.<cluster>.cursorvm.com`，单级通配符只匹配一级。只配置了 `*.cursorvm.com` 的网关看起来正确，计算机仍然不可达。这是在已经允许 `cursor.sh` 的网络上计算机设置失败的常见原因。

## 把同样的域名排除在 TLS 检查和缓冲之外

只允许流量是不够的。对上面的每个域名：

- **绕过 TLS（SSL）检查。** 当网关用自己的证书重新签名连接时，即使主机名已被允许，计算机设置的握手也会失败。Cursor 的连接本来就是端到端加密的。
- **关闭响应缓冲。** 聊天和计算机链路是流式的。把响应一直持有到完成的网关，会让应用一直等待永远不会到达的输出。

如果你的策略要求检查全部流量，网关必须满足 [SSL 检查与 DLP](https://cursor.com/docs/enterprise/network-configuration.md#ssl-inspection-and-dlp) 下的要求：HTTP/2 或 Cursor 的 HTTP/1.1 回退、不缓冲的 Server-Sent Events 透传，以及没有强制超时的长连接。

## 应用到每个配置文件，包括离网

Zscaler Client Connector 在设备离开办公室后仍会运行，并且会应用它自己的离网配置文件。加到办公室位置或单条策略上的例外不会跟着设备回家。把允许规则、TLS 检查豁免和任何 DNS 例外应用到 Grok Bot 成员所属的每个位置、配置文件和策略组，包括漫游和离网。其他有独立在网和离网策略的网关也需要同样处理。

判断方法：同一台笔记本电脑上，网关客户端仍在运行时，Grok Bot 在办公室能用，在家失败。

## 从成员的设备验证

在 IT 应用更改之后，在成员的设备上、网关客户端运行时执行这些检查。

### 检查证书由谁签发

```bash
curl -v https://api2.cursor.sh |& grep -C1 issuer:
```

签发者应当是 **Amazon RSA**。如果它写的是 Zscaler 或你的网关供应商，则此设备配置文件上的 `cursor.sh` 仍在进行 TLS 检查。

### 检查计算机主机名能否解析

```bash
nslookup test.us9.cursorvm.com
```

你应当得到地址。如果查找失败，试试 `nslookup test.us9.cursorvm.com 1.1.1.1`。如果公共解析器有应答而你的默认解析器没有，则阻止来自该设备的 DNS 或网关配置文件，并且那里缺少嵌套的 `*.*.cursorvm.com` 例外。

### 测试流式传输

运行[测试代理连通性](https://cursor.com/docs/enterprise/network-configuration.md#testing-proxy-connectivity)下的 HTTP/1.1 和 HTTP/2 流式测试。输出应当逐行到达，而不是一次全部到达。

### 在应用中重试

在同一台设备上打开 Grok Bot 桌面应用并连接到计算机。如果仍然失败，从该设备重复这些检查；热点通过而公司网络失败，即可确认原因是网络。

## 范围

三种控制很容易混淆：

| 你想要 | 使用 |
| ------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------- |
| 让成员设备通过你的网关到达 Cursor 及其托管计算机 | 本页 |
| 限制托管计算机可以到达哪些目的地 | [网络策略](https://cursor.com/docs/grok-bot/security.md#network-policy)，仅限 Enterprise |
| 把托管计算机的流量经由成员设备路由 | [通过你的桌面路由流量](https://cursor.com/docs/grok-bot/settings.md#route-traffic-through-your-desktop) |
| 在每台托管计算机上安装网络客户端 | [Team Setup Scripts](https://cursor.com/docs/grok-bot/private-networks.md#install-a-networking-client-with-team-setup)，仅限 Enterprise |

默认情况下，托管计算机自己的流量从 Cursor 的[共享静态出口 IP](https://cursor.com/docs/grok-bot/security.md#static-egress-ips)离开，不经过成员设备上的网关。当成员启用 **Route traffic through this computer** 时，被路由的流量使用该设备的网络，并受其网关策略约束。

## 常见问题

### 我们已允许 \*.cursorvm.com。为什么计算机设置仍然失败？

两个常见原因。缺少嵌套的 `*.*.cursorvm.com` 模式，因此计算机的主机名不匹配。或者域名已被允许，但仍在进行 TLS 检查，于是网关的证书破坏了设置握手。添加嵌套模式，把两个模式都排除在检查之外，然后运行这些[检查](https://cursor.com/docs/grok-bot/proxies.md#verify-from-a-members-device)。

### 我们允许了 cursor.sh。为什么这覆盖不了计算机？

它们使用不同的主机名。聊天、登录和批准前往 `api2.cursor.sh`，大多数网关已经允许。计算机链路前往嵌套的 `cursorvm.com` 主机名，需要自己的允许规则和检查豁免。这也是为什么在某些网络上聊天可以继续工作，而计算机永远连不上。

### 在办公室能用，在家不行。有什么不同？

网关客户端在家仍在运行，而其离网配置文件没有得到这些例外。把它们也应用到漫游和离网配置文件。

### 我们的策略要求对全部流量进行 TLS 检查。

那么网关必须原样放行流式传输。要求位于 [SSL 检查与 DLP](https://cursor.com/docs/enterprise/network-configuration.md#ssl-inspection-and-dlp)。把 Cursor 的域名排除在外是可靠的路径；Cursor 的连接本来就是端到端加密的。

### 这份列表也覆盖 Cursor 编辑器吗？

是的。它与[企业网络配置](https://cursor.com/docs/enterprise/network-configuration.md#ip-allowlisting)是同一份列表。编辑器的聊天和 Tab 功能不需要 `cursorvm.com` 模式，因此对编辑器没问题的网关仍可能弄坏 Grok Bot。

## 相关页面

- [企业网络配置](https://cursor.com/docs/enterprise/network-configuration.md)
- [面向 Teams 与 Enterprise 的 Grok Bot](https://cursor.com/docs/grok-bot/teams.md)
- [Grok Bot 安全](https://cursor.com/docs/grok-bot/security.md)
- [连接到私有网络](https://cursor.com/docs/grok-bot/private-networks.md)
- [网络、代理和远程连接](https://cursor.com/help/troubleshooting/network.md)

### 需要网关配置方面的帮助？

联系我们的团队，获取部署协助和优先支持。


---

## 站点地图

[所有文档页面概览](/llms.txt)
