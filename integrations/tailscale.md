> 原文：https://cursor.com/docs/integrations/tailscale

# Tailscale

Tailscale 集成让 [Rollouts](https://cursor.com/docs/rollouts.md) 能够访问仅在你的 tailnet 上可达的 [MCP](https://cursor.com/docs/mcp.md) 服务器。Cursor 会作为一个带标签的临时设备加入你的 tailnet，并连接到 tailnet 策略所允许的服务器。

使用 `https` 的远程 MCP 服务器可以使用这种连接。stdio 服务器运行在启动它的那台机器上，因此没有可供 Cursor 拨号的 tailnet 地址。[Grok Bot](https://cursor.com/docs/grok-bot/private-networks.md) 通过另一套设置访问私有网络。

Tailscale 在 Teams 和 Enterprise 方案上处于 beta。[联系销售](https://cursor.com/contact-sales?source=docs-tailscale)以申请为你的团队启用。

## 设置你的 tailnet

在 [Tailscale 管理控制台](https://login.tailscale.com/admin)中编辑 tailnet 策略，并创建 OAuth 客户端。

### 为 Cursor 选择一个标签

除非你另选，否则 Cursor 使用 `tag:cursor`。把该标签加入 `tagOwners`。

### 创建 OAuth 客户端

赋予它 `auth_keys` scope，并允许它分配 Cursor 标签。Cursor 使用该客户端在每次连接时签发一把短时有效的密钥。

### 允许该标签访问你的 MCP 服务器

添加一条从 Cursor 标签到你的 MCP 服务器标签和端口的规则。只授予 Cursor 所需的访问权限。它不需要子网路由、出口节点或 `autogroup:internet`。

```json
{
  "tagOwners": {
    "tag:cursor": ["autogroup:admin"],
    "tag:mcp": ["autogroup:admin"]
  },
  "acls": [
    { "action": "accept", "src": ["tag:cursor"], "dst": ["tag:mcp:443"] }
  ]
}
```

MCP 服务器必须以 `https` 提供服务，并且证书要对应其 URL 中的主机名，因为 Cursor 的 TLS 连接终止于你的服务器。对于 MagicDNS 名称，`tailscale serve --https=443` 会提供该证书。

你也可以粘贴一把 auth key，而不使用 OAuth 客户端。请把它设为可复用、临时、预先批准，并打上 Cursor 标签，因为 Cursor 每次需要时都会启动一台新设备。推荐使用 OAuth 客户端。

## 在 Cursor 中添加连接

你需要拥有管理团队 MCP 服务器的权限。

### 打开 Tailscale

前往[仪表盘中的集成](https://cursor.com/dashboard/integrations?view=tailscale)，在 Tailscale 旁边选择 **Manage**。

### 添加连接

选择 **Add Connection**。输入 **Display Name** 和你的 **Tailnet Name**（例如 `acme.ts.net`）。把 **Tag** 设为策略中的那个标签。它必须以 `tag:` 开头。

### 添加凭据

选择 **OAuth Client** 并输入客户端 ID 和 secret，或选择 **Auth Key** 并粘贴一把密钥。Secret 是只写的。要更改 secret，请替换它。

### 保存连接

选择 **Add Connection**。

一个团队可以有两条 Tailscale 连接。

## 为 MCP 服务器选择连接

在 Rollouts 中打开该 MCP 服务器。在 **Transport**（"Choose a private link, or keep the public path"）下，选择你的连接。**No connection** 会保持公网路径。

删除一条连接后，每个使用它的服务器都会失败关闭，直到你再次选择传输方式。这些服务器不会回退到公网。

## Cursor 如何加入你的 tailnet

- **临时设备。** 使用 OAuth 客户端时，Cursor 会签发一把一次性、预先批准且临时的密钥，该密钥在 10 分钟后过期。Cursor 从不存储这把签发出来的密钥。若 15 分钟内没有打开的连接，该设备会登出。
- **随机设备名称。** 每台设备的名称是 `cursor-external-tsnet-proxy-` 加上一个随机后缀。每次都应预期一个新名称，并且可能同时有不止一台设备在线。
- **你的策略就是访问控制。** Cursor 只能到达 Cursor 标签所能到达的地方。每台服务器都被固定到其 URL 中的主机和端口。没有端口的 URL 使用 `443`。
- **只写凭据。** Cursor 在存储客户端 secret 或 auth key 之前会对其加密，并且不会再次显示。

## 排查问题

### 为什么它要求我选择私有链接？

Cursor 会显示 "This MCP server is on a private Tailscale network. Choose a private link for it, then try again."。该服务器的 URL 是一个 tailnet 地址，并且它没有 Tailscale 连接。请把 **Transport** 设为一条连接。

### 服务器显示 Failed to connect

Rollouts 会在该服务器的状态中报告这一点。请按顺序检查以下各项：

- OAuth 客户端需要 `auth_keys` scope，以及分配该连接所用标签的权限。该标签必须列在 `tagOwners` 中。
- 策略必须允许 Cursor 标签到达该服务器 URL 中主机上的端口；当 URL 没有端口时，则为 `443`。
- 应用于 Cursor 标签的 posture 规则，例如 `defaultSrcPosture`，或其规则上的 `srcPosture`，可能会阻止 Cursor 的设备。Cursor 无法满足这些条件，因此请豁免该标签。
- 启用 Tailnet Lock 时，新设备必须先经过签名才能连接。Cursor 每次连接都会创建一台新设备。
- 只有 `https` URL 才能使用 Tailscale 连接。

---

## 站点地图

[所有文档页面概览](/llms.txt)
