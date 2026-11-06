> 原文：https://cursor.com/docs/cloud-agent/self-hosted/integrations

# 集成

在基础设施合作伙伴的平台上运行 [Team Pool](https://cursor.com/docs/cloud-agent/self-hosted/pool.md) worker，或从一份你克隆并改编的参考模板开始。每个选项运行的都是同一个 worker：[Cursor CLI](https://cursor.com/docs/cli/overview.md) 打开一条到 Cursor 的出站 HTTPS 连接，Cursor 通过该连接发送 Agent 工具调用。

合作伙伴指南和模板是参考架构。你拥有 worker 镜像、基础设施、密钥、扩缩策略，以及生产验证。

## 合作伙伴指南

每个合作伙伴都为自己的平台维护一份运行自托管机器 worker 的指南：

- **AWS Lambda。** [Lambda MicroVM 上的 Cursor 自托管机器](https://docs.aws.amazon.com/lambda/latest/dg/microvms-integrations-cursor-self-hosted-machines.html)
- **Cloudflare。** [Cloudflare Sandbox 上的 Cursor Cloud Agents](https://developers.cloudflare.com/sandbox/tutorials/cursor-cloud-agents/)
- **Namespace。** [Cursor 集成](https://namespace.so/docs/integrations/cursor)
- **Modal。** [Modal 上的 Cursor](https://modal.com/docs/cursor)
- **Daytona。** [Daytona 上的 Cursor 自托管机器](https://www.daytona.io/docs/en/guides/cursor/cursor-self-hosted-machines)
- **E2B。** [E2B 上的 Cursor Agent](https://docs.e2b.dev/agents/cursor)
- **Vercel。** [配合 Vercel Sandbox 使用 Cursor](https://vercel.com/kb/guide/cursor-vercel-sandbox)
- **Tensorlake。** [Tensorlake Sandboxes 上的 Cursor Cloud Agents](https://docs.tensorlake.ai/sandboxes/cursor-cloud-agents)
- **Coder。** [适用于 Cursor 的 Agent Relay](https://coder.com/docs/@main/ai-coder/agent-relay)
- **Superserve。** [在 Superserve 上运行 Cursor 自托管机器](https://docs.superserve.ai/integrations/managed-agents/cursor-cloud-agents)
- **Render。** [配合 Render Sandboxes 的 Cursor Cloud Agents](https://render.com/docs/sandboxes-cursor-cloud-agents)

## 参考模板

克隆这些仓库之一，从一份可以工作的 worker 部署开始。每一份都运行一个 [worker 控制器](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#worker-controller)，它认领待处理的池请求，并为每次认领启动一个 worker：

- **AWS Lambda MicroVMs。** [anysphere/aws-lambda-workers](https://github.com/anysphere/aws-lambda-workers)：一个 `--spawn` hook 为每个被认领的请求启动一台经 Firecracker 隔离的 Lambda MicroVM。
- **Cloudflare Containers。** [anysphere/cloudflare-workers](https://github.com/anysphere/cloudflare-workers)：一个 Cloudflare Worker 充当控制器，并为每个被认领的请求启动一个 Cloudflare Container。
- **Kubernetes。** [anysphere/k8s-workers](https://github.com/anysphere/k8s-workers)：一个 Helm 示例，在你的集群中运行 `agent worker controller --spawn`，为每个被认领的请求创建一个 Pod，或用 `--warm-idle` 保持预热的空闲 Pod，且不需要 CRD。这是新部署所使用的 Kubernetes 路径。

Cursor Kubernetes operator 和 `WorkerDeployment` Helm chart 已弃用。已经在运行该 operator 的集群可以继续使用它；[operator 参考](https://cursor.com/docs/cloud-agent/self-hosted/kubernetes.md)仍然为它们保留。不要在它上面开始新的部署。

## 每个集成都需要什么

平台会变，但 worker 的要求不变：

- **Cursor Enterprise 计划**，并且由团队管理员在 [Cloud Agents 仪表盘](https://cursor.com/dashboard/cloud-agents#self-hosted)中启用自托管机器。
- `CURSOR_API_KEY` 中的[服务账号 API 密钥](https://cursor.com/docs/account/enterprise/service-accounts.md)。见[为 worker 进行身份验证](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#authenticate-workers)。
- 到 `api2.cursor.sh`、`api2direct.cursor.sh`、`agent-stores.s3.us-east-1.amazonaws.com` 和 `cloud-agent-artifacts.s3.us-east-1.amazonaws.com` 的出站 HTTPS。见[网络](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#networking)。
- 一个[池名称](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#pool-names)或[标签](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#labels)，以便 Cursor 把正确的请求路由到该平台上的 worker。

如果只要一台个人机器，而不是由平台管理的集群，请使用 [My Machines](https://cursor.com/docs/cloud-agent/self-hosted/my-machines.md)。

## 后续步骤

- [Team Pools](https://cursor.com/docs/cloud-agent/self-hosted/pool.md)：在你把它自动化到某个平台上之前，先手工启动一个池 worker。
- [Worker 控制器](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#worker-controller)：每个模板所基于的 `--spawn` hook 模型。
- [Kubernetes operator（已弃用）](https://cursor.com/docs/cloud-agent/self-hosted/kubernetes.md)：已经在运行 `WorkerDeployment` operator 的集群的参考。
- [API 参考](https://cursor.com/docs/cloud-agent/api/endpoints.md#workers-and-pools)：worker、池、待处理请求队列和 worker token 的端点。

---

## 站点地图

[所有文档页面概览](/llms.txt)
