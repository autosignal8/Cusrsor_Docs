> 原文：https://cursor.com/docs/cloud-agent/self-hosted/choose-runtime

# 选择 Cloud Agents 的运行位置

Cursor 托管的 Cloud Agents 在隔离的云端虚拟机中运行每个 Agent，具备托管的生命周期、已保存的环境、产物捕获，以及用于密钥和网络访问的仪表盘控制。

[自托管机器](https://cursor.com/docs/cloud-agent/self-hosted.md)通过 [My Machines](https://cursor.com/docs/cloud-agent/self-hosted/my-machines.md) 或 [Team Pools](https://cursor.com/docs/cloud-agent/self-hosted/pool.md) 在你控制的硬件上运行工具调用。Agent 循环仍在 Cursor 的云端运行。

Cursor 托管的虚拟机运行在 x86\_64（amd64）上。对于 arm64，请在 arm64 硬件上通过 My Machines 或 Team Pool 运行自托管 worker。

## 自托管还是 Cursor 托管：哪种适合你？

Cursor 托管的 Cloud Agents 能满足超过 80% 客户的需求。用这棵决策树评估哪种方式最适合你的组织。

## 快速对比

| 选项 | 何时选择 | 你管理什么 |
| :------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Cursor 托管的 Cloud Agents** | 你希望 Cursor 在设置之后管理虚拟机的置备、隔离、快照、启动、产物、容量和环境部署。这是大多数团队的推荐路径。 | 首次环境配置、密钥、仓库访问和网络策略。在此之后，由 Cursor 管理主机和环境生命周期。 |
| **My Machines** | 你希望用一台个人笔记本电脑、devbox 或远程虚拟机，为特定用户和仓库执行工具调用。 | 机器、worker 进程、本地检出、凭证、运行时间、磁盘、网络访问，以及让机器保持干净可用的工作状态。 |
| **Team Pools** | 你需要由组织管理的 worker 机群，并具备服务账号身份验证、池路由、标签、Kubernetes、自动扩缩或专用硬件。 | 主机、镜像、虚拟机重置、容量、自动扩缩、worker 更新、监控、密钥、网络访问和事件响应。 |

## 从托管的 Cloud Agents 开始

托管的 Cloud Agents 通常是让 Agent 安全访问代码和内部系统、运维负担最低的方式。

当你可以通过以下方式配置访问时，使用托管路径：

- 带有设置命令、Dockerfile、快照和密钥的 [Cloud Agent 环境](https://cursor.com/docs/cloud-agent/setup.md)。
- [网络访问控制](https://cursor.com/docs/cloud-agent/security-network.md#network-access)，按用户、团队或环境限制出站域名。
- 当 Agent 需要访问你的 VPC 或内网中的服务时，在环境中使用 [Tailscale 或类似的私有网络客户端](https://cursor.com/docs/cloud-agent/setup.md#running-tailscale)。
- [私有连接](https://cursor.com/docs/cloud-agent/private-connectivity.md)，用于私有的 GitHub Enterprise Server、GitLab Enterprise、Artifactory 或 Nexus 等软件包仓库、私有源代码管理 API，以及相关的 webhook 流量。

这样，设置完成后由 Cursor 运营 Agent 基础设施，同时你的团队控制每个环境可以到达哪些仓库、密钥和网络资源。

## 何时适合 My Machines

[My Machines](https://cursor.com/docs/cloud-agent/self-hosted/my-machines.md) 最适合个人或小规模工作流：某位特定用户已经有一台具备正确检出、工具、凭证和私有网络访问的机器。

适用于：

- 开发者的 devbox 或远程工作站。
- 依赖本地状态、且你不想在云端环境中重建的一次性仓库。
- 在构建集中管理的 worker 池之前做一次快速测试。

My Machines 不是全组织的机群系统。每个 worker 属于启动它的用户，面向它被启动时所在的仓库，并且在会话运行期间必须保持在线。清理工作也由你负责：清除状态、刷新检出、修复依赖，以及让机器为下一次运行做好准备。

## 何时适合 Team Pools

[Team Pools](https://cursor.com/docs/cloud-agent/self-hosted/pool.md) 面向希望集中拥有 worker 硬件，或需要把工作路由到特定机群的 Enterprise 团队。

在你需要以下能力时使用池：

- 服务账号身份验证，而不是每个用户单独登录 worker。
- Kubernetes、自动扩缩、标签和机群监控。
- 专用硬件配置，例如 GPU worker 或高内存构建机器。
- 由公司管理的主机，用于执行全部终端命令、文件编辑、浏览器操作和本地 MCP 服务器。

代价是运维归你所有。你的团队负责运行机群、保持足够的 worker 可用、修补并刷写镜像、在两次运行之间重置虚拟机、管理容量、轮换凭证、监控健康状况，以及处理主机故障。如果你的主要需求是私有网络访问，请先尝试带有网络控制、Tailscale 或私有连接的托管 Cloud Agents。

## 安全模型差异

三种选项都支持 Privacy Mode。主要区别在于工具在何处执行，以及由谁运营该执行环境。密钥也有所不同：My Machines 使用机器自身的凭证，Team Pools 仅通过 Secret sync 接收仪表盘密钥。

| 问题 | 托管的 Cloud Agents | My Machines | Team Pools |
| :--------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------- | :------------------------------------- | :-------------------------------------- |
| Agent 循环在哪里运行？ | Cursor 云端 | Cursor 云端 | Cursor 云端 |
| 工具调用在哪里运行？ | Cursor 托管的隔离虚拟机 | 你的机器 | 你的 worker |
| 谁管理主机和环境生命周期？ | Cursor，在首次环境配置之后 | 你 | 你的团队 |
| Agent 如何到达私有资源？ | 环境网络、允许列表、Tailscale 或类似客户端，以及对受支持源代码管理路径的私有连接 | 你的机器的现有网络 | 你的 worker 机群的网络 |
| 哪些[环境](https://cursor.com/docs/cloud-agent/self-hosted.md#environments-on-self-hosted-machines)设置适用？ | 全部 | 仅仓库列表 | 仅仓库列表 |
| 哪些仪表盘密钥会到达机器？ | 团队、用户和环境范围的密钥 | 无 | 团队和用户密钥，通过 Secret sync |
| 最适合的运维场景 | 大多数团队和仓库 | 个人用户和特定机器 | 集中式企业机群 |

## 后续步骤

- [Cloud Agent 环境设置](https://cursor.com/docs/cloud-agent/setup.md)：继续使用托管的 Cloud Agents，并配置环境、密钥和网络访问。
- [My Machines](https://cursor.com/docs/cloud-agent/self-hosted/my-machines.md)：连接个人笔记本电脑、devbox 或虚拟机。
- [Team Pools](https://cursor.com/docs/cloud-agent/self-hosted/pool.md)：为你的团队设置一个共享池。
- [macOS 和 Linux worker 上的计算机操作](https://cursor.com/docs/cloud-agent/self-hosted/computer-use.md)：让 Agent 在你的机器上驱动桌面和浏览器。

---

## 站点地图

[所有文档页面概览](/llms.txt)
