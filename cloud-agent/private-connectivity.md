> 原文：https://cursor.com/docs/cloud-agent/private-connectivity

# 私有连接

Cursor 为需要与公共互联网无法到达的系统协作的 Enterprise 团队支持私有网络连接。这包括自托管的 GitHub Enterprise Server、GitLab Enterprise、Bitbucket Data Center、Artifactory、Nexus、私有源代码管理 API，以及这些系统返回 Cursor 的 webhook 流量。

需要访问你的源代码管理系统的 Cursor 服务使用同一套私有连接设置，包括 [Cloud Agents](https://cursor.com/docs/cloud-agent.md)、[Bugbot](https://cursor.com/docs/bugbot.md)、[Security Agents](https://cursor.com/docs/security-agents.md)，以及 Cursor 后端服务。

要设置私有连接，请联系 [hi@cursor.com](mailto:hi@cursor.com) 或你的 Cursor 销售代表。

## 支持的选项

| 选项 | 最适合 | 云提供方 | 状态 |
| :---------------- | :---------------------------------------------------------------------------------------------------------------------- | :----------------------------------------- | :-------- |
| AWS PrivateLink | 在 Cursor 与你的 Git 提供方或包注册表之间建立私有连接，包括返回 Cursor 的 webhook 流量 | AWS | 支持 |
| Cloudflare Tunnel | 当 AWS PrivateLink 不切实际时，由 Cursor 访问私有源站 | 任何可以运行 `cloudflared` 的环境 | 支持 |

## 如何选择

当你的私有 Git 提供方或包注册表位于 AWS 中，或可以放在 AWS Network Load Balancer 后面时，使用 AWS PrivateLink。这是自托管 GitHub Enterprise Server 和 GitLab Enterprise 的首选路径。

AWS PrivateLink 可以覆盖两个流量方向：

- Cursor 访问你的私有 Git 提供方，以克隆仓库并调用 Git API。
- 你的 Git 提供方通过 `api2.cursor.sh` 向 Cursor 发送 webhook 或回调，而无需公共互联网出站。

当你无法发布 AWS 端点服务，或需要一种只要求从你的网络发出出站隧道的部署模型时，使用 Cloudflare Tunnel。

如果团队需要 Google Private Service Connect（PSC），请联系 Cursor。Cursor 目前不提供面向客户的 PSC 服务。

## 前提条件

开始之前，请确认你具备：

- 一个 Cursor Enterprise 工作区
- 可通过 HTTPS 端口 443 访问的自托管 GitHub Enterprise Server、GitLab Enterprise、Bitbucket Data Center，或私有包注册表（例如 Artifactory 或 Nexus）
- Git 或注册表主机名的公开受信任 TLS 证书
- 该主机名的 DNS 所有权
- 如果使用 AWS PrivateLink，创建端点服务或接口 VPC 端点的 AWS 权限
- 如果使用 Cloudflare Tunnel，运行 `cloudflared` 的权限

对于这些私有连接路径，Cursor 不支持自签名证书、未加密连接、SSH、自定义端口，或仅 IPv6 的端点服务。

如果在 GitHub Enterprise Server 前面运行代理，请确保它允许 Cursor 的 GitHub App 集成使用经过身份验证的 GitHub REST 和 GraphQL API。

## AWS PrivateLink

AWS PrivateLink 支持 Cursor 与你的 Git 提供方或包注册表之间任一方向的私有流量。根据网络策略，你可能需要一个方向或两个方向。

### 方向 1：Cursor 到你的 Git 提供方或包注册表

当 Cursor 需要克隆仓库、调用 Git API，或到达 Artifactory 或 Nexus 这类私有包注册表时，使用此选项。

#### 1. 创建 AWS 端点服务

在 Git 提供方或包注册表的 HTTPS 端点前面创建一个 Network Load Balancer。把该负载均衡器发布为 AWS VPC 端点服务。

向 Cursor 发送：

- 端点服务名称，例如 `com.amazonaws.vpce.us-east-1.vpce-svc-0123456789abcdef0`
- AWS 区域
- Git 或注册表主机名，例如 `github.example.com` 或 `artifactory.example.com`
- 端点服务是否启用了由 AWS 管理的私有 DNS
- Network Load Balancer 是否保留客户端 IP，或后端是否过滤源 IP

如果端点服务不在 `us-east-1`，请在端点服务上启用跨区域访问。

#### 2. 允许 Cursor 的 AWS 主体

Cursor 会提供要添加到端点服务允许主体中的 AWS 主体。添加 Cursor 提供的精确主体：

```text
arn:aws:iam::<cursor-aws-account-id>:root
```

在该主体被允许之前，Cursor 无法创建其接口端点。如果主体缺失或不能精确匹配，AWS 会返回 `InvalidServiceName`。

如果负载均衡器保留客户端 IP，或后端过滤源 IP，请允许这些 Cursor PrivateLink 子网 CIDR：

```text
10.2.8.0/21
10.2.24.0/21
10.2.40.0/21
```

#### 3. 接受端点连接

Cursor 创建接口端点之后，如果你的端点服务要求手动接受，请在 AWS 账号中接受该端点连接。

#### 4. 配置 DNS

如果端点服务为 Git 或注册表主机名公开了由 AWS 管理的私有 DNS，Cursor 会在其接口端点上启用私有 DNS。

如果端点服务不公开私有 DNS，Cursor 会在自己一侧创建私有 DNS，并把该主机名映射到端点 DNS 名称。

在 Cursor 中使用与 TLS 证书和 DNS 上出现的相同主机名。

### 方向 2：你的 Git 提供方到 `api2.cursor.sh`

当 GitHub Enterprise Server 或 GitLab Enterprise 主机无法到达公共互联网，但仍需要向 Cursor 发送 webhook 或回调时，使用此选项。

Cursor 为 `api2.cursor.sh` 发布了一个 AWS PrivateLink 端点服务。你在自己的 AWS 账号中创建一个接口 VPC 端点，并启用私有 DNS，使 `api2.cursor.sh` 从你的 Git 提供方网络解析到私有端点 IP。

#### 端点服务详情

在你创建端点之前，Cursor 会确认你的 AWS 主体已在允许列表中。

| 字段 | 值 |
| :------------------------- | :----------------------------------------------------------------------------------- |
| 服务名称 | `com.amazonaws.vpce.us-east-1.vpce-svc-054b15427d4bea2b7` |
| 服务 ID | `vpce-svc-054b15427d4bea2b7` |
| 主区域 | `us-east-1` |
| 支持的使用者区域 | `us-east-1`、`us-east-2`、`us-west-2`、`eu-central-1`、`eu-west-1`、`ap-southeast-2` |
| IP 地址类型 | 仅 IPv4 |
| 私有 DNS 名称 | `api2.cursor.sh` |

#### 模式 1：由 AWS 管理的私有 DNS

这是推荐模式。设置 `private_dns_enabled = true`。

```hcl
resource "aws_vpc_endpoint" "cursor_api2" {
  vpc_id              = aws_vpc.app.id
  service_name        = "com.amazonaws.vpce.us-east-1.vpce-svc-054b15427d4bea2b7"
  service_region      = "us-east-1"
  vpc_endpoint_type   = "Interface"
  subnet_ids          = [for subnet in aws_subnet.app_private : subnet.id]
  private_dns_enabled = true
  security_group_ids  = [aws_security_group.cursor_api2_endpoint.id]
}
```

AWS 会把你的 VPC 关联到 `api2.cursor.sh` 的托管私有托管区。在 VPC 内部，`api2.cursor.sh` 解析到端点 ENI 的 IP。不需要 Route 53 记录。

#### 模式 2：客户管理的私有托管区

如果你希望拥有该 DNS 记录，使用此模式。设置 `private_dns_enabled = false`，然后创建一个范围限定到使用者 VPC 的 `api2.cursor.sh` 私有托管区。

```hcl
resource "aws_vpc_endpoint" "cursor_api2" {
  vpc_id              = aws_vpc.app.id
  service_name        = "com.amazonaws.vpce.us-east-1.vpce-svc-054b15427d4bea2b7"
  service_region      = "us-east-1"
  vpc_endpoint_type   = "Interface"
  subnet_ids          = [for subnet in aws_subnet.app_private : subnet.id]
  private_dns_enabled = false
  security_group_ids  = [aws_security_group.cursor_api2_endpoint.id]
}

resource "aws_route53_zone" "cursor_api2" {
  name    = "api2.cursor.sh"
  comment = "Customer-managed PHZ for api2.cursor.sh scoped to the app VPC."

  vpc {
    vpc_id = aws_vpc.app.id
  }
}

resource "aws_route53_record" "cursor_api2_a" {
  zone_id = aws_route53_zone.cursor_api2.zone_id
  name    = "api2.cursor.sh"
  type    = "A"

  alias {
    name                   = aws_vpc_endpoint.cursor_api2.dns_entry[0].dns_name
    zone_id                = aws_vpc_endpoint.cursor_api2.dns_entry[0].hosted_zone_id
    evaluate_target_health = false
  }
}
```

如果 GitHub Enterprise Server 或 GitLab Enterprise 使用端点 VPC 之外的 DNS，请把 `api2.cursor.sh` 查询转发到 VPC 解析器，或创建等效的私有 DNS 覆盖。不要创建公共 DNS 覆盖。

## Cloudflare Tunnel

当 AWS PrivateLink 不适合时，使用 Cloudflare Tunnel。

Cursor 会创建隧道并分享：

- Cursor 控制的 DNS 下的一个公共主机名
- 通过安全的 1Password 分享传递的隧道令牌
- 一份示例 `cloudflared` 配置

你的网络运行 `cloudflared`，并向 Cloudflare 打开出站连接。不需要入站防火墙规则。

示例 `cloudflared` 配置：

```yaml
ingress:
  - hostname: <cursor-provided-hostname>
    service: https://<your-internal-service>:443
  - service: http_status:404
```

示例运行命令：

```bash
docker run -d --restart=always --name cloudflared \
  -v /path/to/config.yml:/etc/cloudflared/config.yml \
  cloudflare/cloudflared:latest \
  tunnel --config /etc/cloudflared/config.yml \
  run --token <TUNNEL_TOKEN>
```

保管好隧道令牌。不要通过电子邮件或聊天发送它。

## 完成源代码管理连接

私有网络配置完成之后，在 Cursor 中完成源代码管理设置：

- 对于 GitHub Enterprise Server，按照 [GitHub 集成设置](https://cursor.com/docs/integrations/github.md#setup)操作。
- 对于 GitLab Enterprise，按照 [GitLab 集成设置](https://cursor.com/docs/integrations/gitlab.md#setup)操作。
- 对于 Bitbucket Data Center，按照 [Bitbucket 集成设置](https://cursor.com/docs/integrations/bitbucket.md#setup)操作。
- 使用由你的 TLS 证书和私有 DNS 配置所覆盖的同一个主机名。
- 如果代理位于 Git 提供方前面，请确保它允许[前提条件](https://cursor.com/docs/cloud-agent/private-connectivity.md#prerequisites)中描述的经过身份验证的 API 流量。

Cursor 使用已连接的源代码管理集成，供 Cloud Agents、Bugbot、Security Agents 以及其他需要仓库访问的 Cursor 服务使用。

### 检查私有 webhook 路径

如果 Git 提供方通过 `api2.cursor.sh` PrivateLink 路径向 Cursor 发送 webhook，请从 GitHub Enterprise Server 或 GitLab Enterprise 所使用的同一网络路径运行这些检查：

```bash
getent hosts api2.cursor.sh
# or, if dig is available
dig +short api2.cursor.sh
curl -sS https://api2.cursor.sh/
```

每个解析到的 IP 都应位于你的使用者 VPC CIDR 之内。如果看到 `3.x.x.x` 或 `44.x.x.x` 这类公共 IP，说明私有 DNS 尚未生效。

`curl` 请求应返回 HTTP `200`，正文以 `Welcome to Cursor.` 开头。该响应表示请求到达了正在运行的 Cursor `api2` 后端。

## 故障排除

| 现象 | 可能原因 | 修复 |
| :------------------------------------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------- |
| Cursor 无法完成到你的 Git 提供方的私有连接 | Cursor 无法到达或挂接到该端点服务 | 确认端点服务名称、区域和允许的主体与 Cursor 提供的值一致，然后把时间戳告知 Cursor |
| Cursor 报告端点连接正在等待客户操作 | 端点服务需要在你的 AWS 账号中批准 | 查看该服务待处理的端点连接请求，并批准 Cursor 的请求 |
| Bugbot 或 Cloud Agents 能连接到 GHES，但在应用设置、仓库同步或 webhook 处理期间失败 | GHES 前面的代理正在阻止或改写经过身份验证的 GitHub REST 或 GraphQL API 请求 | 允许 Cursor 的 GitHub App 集成使用经过身份验证的 GitHub REST 和 GraphQL API |
| `api2.cursor.sh` 解析到公共 IP | 私有 DNS 不在 GitHub Enterprise Server 或 GitLab Enterprise 所使用的解析器路径上 | 启用由 AWS 管理的私有 DNS，或把 DNS 转发到端点 VPC 解析器 |
| 到 `api2.cursor.sh:443` 的 TCP 超时 | 安全组、NACL、路由表或防火墙阻止了到端点 ENI 的流量 | 允许从你的 Git 提供方网络到端点 ENI 的 TCP 443 |
| `api2.cursor.sh` 的 TLS 失败 | DNS 指向了错误的目标，或客户端没有使用 SNI | 检查端点 DNS，并在启用 SNI 的情况下重试 |
| `curl https://api2.cursor.sh/` 没有返回 `Welcome to Cursor.` | 流量没有到达健康的 Cursor 后端 | 把时间戳、源 VPC 和解析到的端点 IP 发送给 Cursor |
| Cloudflare Tunnel 无法连接 | `cloudflared` 无法到达 Cloudflare，或令牌/配置有误 | 检查出站防火墙规则、令牌和 `cloudflared` 日志 |

## Google Private Service Connect

Cursor 目前不提供面向客户的 Google Private Service Connect。

如果你需要从 GCP VPC 到 Cursor 服务的私有连接，或从 Cursor 到你的 GCP 项目中私有服务的私有连接，请联系 Cursor，以便我们界定这项需求。目前，当这些部署模型合适时，请使用 AWS PrivateLink 或 Cloudflare Tunnel。

## 要发送给 Cursor 的内容

对于到你的 Git 提供方或包注册表的 AWS PrivateLink：

- 端点服务名称
- AWS 区域
- Git 或注册表主机名
- 是否启用了私有 DNS
- 负载均衡器是否保留客户端 IP 或过滤源 IP

对于通过 AWS PrivateLink 访问 `api2.cursor.sh`：

- Cursor 应加入允许列表的 AWS 主体
- 你将创建接口端点的 VPC 和区域
- 你计划使用由 AWS 管理的私有 DNS，还是客户管理的 DNS

对于 Cloudflare Tunnel：

- 内部源站 URL
- 安全 1Password 分享的客户联系人
- 任何主机名或命名限制

## 延伸阅读

- [AWS：创建端点服务](https://docs.aws.amazon.com/vpc/latest/privatelink/create-endpoint-service.html)
- [AWS：管理 VPC 端点服务的 DNS 名称](https://docs.aws.amazon.com/vpc/latest/privatelink/manage-dns-names.html)
- [AWS：使用接口 VPC 端点访问 AWS 服务](https://docs.aws.amazon.com/vpc/latest/privatelink/create-interface-endpoint.html)
- [Cloudflare Tunnel 文档](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/)

---

## 站点地图

[所有文档页面概览](/llms.txt)
