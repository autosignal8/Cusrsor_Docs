> 原文：https://cursor.com/docs/cloud-agent

# Cloud Agents

Cloud Agents 使用相同的[Agent 基础](https://cursor.com/learn/agents.md)，但运行在云端的隔离虚拟机中，并配备完整的开发环境，而不是在你的本地机器上运行。开发环境与你笔记本电脑上的配置类似：已克隆的仓库、已安装的依赖、密钥、启动命令和网络访问。

有效的开发环境会给 Agent 提供代码库与组织的完整上下文，以便它们测试并验证自己的工作。

## 为什么使用 Cloud Agents？

你可以并行运行任意数量的 Agent，并且不要求你的本地机器连接到互联网。

由于可以访问自己的虚拟机，Cloud Agents 能够构建、测试修改后的软件并与之交互。它们也可以使用计算机来控制桌面和浏览器。Cloud Agents 支持 [MCP 服务器](https://cursor.com/docs/mcp.md)，从而可以访问数据库、API 和第三方服务等外部工具与数据源。

Cloud Agents 也可以在多仓库环境中运行。当一项任务跨越独立的前端、后端、基础设施或共享库仓库时，就使用这种环境。Agent 可以检查完整工作区、做出协同改动，并在它修改的仓库中打开 pull request。

## 如何访问

在任何人能从某个仓库启动 Cloud Agent 之前，Cursor 账号管理员需要为该账号连接源代码管理。请设置 [GitHub (Cloud and Enterprise Server)](https://cursor.com/docs/integrations/github.md)、[GitLab (Cloud and Self-Hosted)](https://cursor.com/docs/integrations/gitlab.md)、[Bitbucket Cloud](https://cursor.com/docs/integrations/bitbucket.md) 或 [Azure DevOps](https://cursor.com/docs/integrations/azure-devops.md)。要在没有仓库的情况下开始，请使用[从零开始](https://cursor.com/docs/cloud-agent/setup.md#start-from-scratch)。

你可以在工作的任何地方启动 Cloud Agents：

1. **Cursor for iOS**：从 [Cursor iOS 应用](https://cursor.com/docs/cloud-agent/mobile.md)启动并管理 Agent
2. **Cursor Web**：在任意设备上从 [cursor.com/agents](https://cursor.com/agents) 启动并管理 Agent
3. **Cursor Desktop**：在 Agent 输入框下方的下拉菜单中选择 **Cloud**
4. **Slack**：使用 @cursor 命令启动 Agent
5. **GitHub 或 Bitbucket**：在 GitHub PR 或 issue 上，或在 Bitbucket PR 上评论 `@cursor`，以启动 Agent
6. **Linear**：使用 @cursor 命令启动 Agent
7. **API**：使用 API 启动 Agent

在 **Android** 上，用 Chrome 打开 [cursor.com/agents](https://cursor.com/agents)，然后点按 **Install App**，将其安装为渐进式 Web 应用（PWA）。原生 iPhone 和 iPad 应用以及更多移动端选项见 [Cursor for iOS](https://cursor.com/docs/cloud-agent/mobile.md)。

### 在 Slack 中使用 Cursor

进一步了解如何设置和使用 Slack 集成，包括触发 Agent 和接收通知。

## 工作原理

### 代码托管方连接

Cloud Agents 从 GitHub、GitLab、Azure DevOps Services 或 Bitbucket Cloud 克隆你的仓库，在单独的分支上工作，然后把改动推送到你的仓库以便交接。

你需要对你的仓库以及任何依赖仓库或子模块拥有读写权限。

### 环境

Agent 的能力取决于它们所运行的环境。一个能写代码、但不能运行测试、查询服务或访问 API 的 Agent，无法对自己的工作形成闭环。

不为 Cloud Agents 配置开发环境，就像不给你的工程师一台电脑。因此，环境设置是提升 Cloud Agents 效果最重要的一步。它让 Cloud Agents 像工程师一样工作：编写代码、测试并验证工作、交付软件。

你可以通过由 Agent 引导的设置、已保存的快照，或 `.cursor/environment.json` 中的 Dockerfile 来配置环境。参见 [Cloud Agent 环境设置](https://cursor.com/docs/cloud-agent/setup.md)以开始。[构建](https://cursor.com/docs/cloud-agent/builds.md)会在后台准备每个环境，让 Agent 启动时仓库和依赖都已就绪。

Cloud Agents 仪表盘会显示某个 Agent 使用了哪个环境以及哪一次构建，以及环境详情和版本历史。在 Agent 页面上，将鼠标悬停在页面顶部的仓库名称上，即可查看该次运行使用的环境。配置详情见 [Cloud Agent 环境设置](https://cursor.com/docs/cloud-agent/setup.md)。

### 运行时与环境控制

Cursor 为每个 Cloud Agent 管理虚拟机的置备、隔离、快照、启动、产物和容量。你可以添加密钥、限制出站域名、通过 Tailscale 或类似客户端连接私有网络，并对支持的源代码管理路径使用私有连接。

环境与网络控制的完整集合见 [Cloud Agent 安全与网络](https://cursor.com/docs/cloud-agent/security-network.md)。要在你自行管理的硬件上运行工具，见[自托管机器](https://cursor.com/docs/cloud-agent/self-hosted.md)。

## 模型

Cloud Agents 使用经过筛选的一组模型。对于支持的模型，你可以选择上下文窗口大小。

## MCP 支持

Cloud Agents 可以使用为你的团队配置的 [MCP（Model Context Protocol）](https://cursor.com/docs/mcp.md) 服务器。通过 [cursor.com/agents](https://cursor.com/agents) 中的 MCP 下拉菜单添加并管理 MCP 服务器。

同时支持 HTTP 和 stdio 两种传输方式。需要 OAuth 的 MCP 服务器也支持 OAuth。设置详情见 [Cloud Agent 能力](https://cursor.com/docs/cloud-agent/capabilities.md)。

Cloud Agents 还包含内置的 [Cursor Cloud MCP](https://cursor.com/docs/cloud-agent/capabilities.md#cursor-cloud-mcp)，用于运行诊断，包括记录、运行事件、环境详情和设置日志。

## Hooks 支持

Cloud Agents 会运行仓库中 `.cursor/hooks.json` 里基于命令的 Hooks。在 Enterprise 方案中，它们还会运行团队 Hooks 和企业托管的 Hooks。

这样，当工作在云端运行时，格式化工具、审计脚本和策略检查仍然有效。支持的 Hooks 包括工具与文件 Hooks（`preToolUse`、`beforeShellExecution`、`afterFileEdit`），以及生命周期 Hooks（`beforeSubmitPrompt`、`subagentStart` / `subagentStop`、`preCompact`、`afterAgentResponse` / `afterAgentThought` 和 `stop`）。

在只读环境的早期探索性回合中，Hooks 不会运行；一旦 Agent 拥有可写环境，它们就会开始运行。有些 Hooks 特定于 IDE（Tab Hooks、`workspaceOpen`）。来自 `~/.cursor/hooks.json` 的用户级 Hooks 也不可用，因为云端虚拟机无法访问你的本地主目录。

完整支持矩阵和详情见 [Hooks：Cloud Agent 支持](https://cursor.com/docs/hooks.md#cloud-agent-support)。

## 产物与远程桌面控制

Cloud Agents 会生成可合并的 PR，并附带产物来演示它们的改动。你也可以控制 Agent 的远程桌面，以使用修改后的软件。

- **产物**：Agent 会生成截图、视频和日志，让你确切看到改了什么，以及 Agent 如何验证自己的工作。
- **远程桌面控制**：接管 Agent 的桌面，在完整的开发环境中亲自测试软件，而无需在本地检出该分支。把控制权交还给 Agent，以便它继续工作。

产物、计算机操作和远程桌面控制的详情见 [Cloud Agent 能力](https://cursor.com/docs/cloud-agent/capabilities.md)。

## 与团队共享 Agent

把某个 Agent 的 URL 发给队友，对方就可以打开这次运行，查看对话、代码改动，以及 Agent 生成的产物。

Agent 对其启动时所在 Cursor 团队的成员可见。Cursor 还会核验每位查看者的仓库访问权限：要打开队友的 Agent，请在 [Integrations](https://cursor.com/dashboard/integrations) 下连接你自己的源代码管理账号，并确保你能够访问该 Agent 工作过的仓库。仅有团队成员身份并不会授予访问权限。

查看是只读的。要让队友发送后续消息并继续这项工作，团队管理员可以启用[团队跟进](https://cursor.com/docs/cloud-agent/settings.md#team-follow-ups)。

## 相关页面

- 进一步了解 [Cloud Agent 能力](https://cursor.com/docs/cloud-agent/capabilities.md)。
- 进一步了解 [Cloud Agent 环境设置](https://cursor.com/docs/cloud-agent/setup.md)。
- 进一步了解 [Cloud Agent 构建](https://cursor.com/docs/cloud-agent/builds.md)。
- 进一步了解 [应用](https://cursor.com/docs/cloud-agent/apps.md)。
- 进一步了解 [Cloud Agent 安全](https://cursor.com/docs/cloud-agent/security-network.md)。
- 进一步了解 [OIDC token](https://cursor.com/docs/cloud-agent/identity.md)。
- 进一步了解 [Agent 元数据](https://cursor.com/docs/cloud-agent/metadata.md)。
- 进一步了解 [Cloud Agent 设置](https://cursor.com/docs/cloud-agent/settings.md)。

## 计费

Cloud Agents 与其他 Agent 工作一样消耗你方案中的用量，按所选模型的[费率](https://cursor.com/docs/models-and-pricing.md#model-pricing)计费。在[旧版按请求计费方案](https://cursor.com/docs/account/pricing/request-based-legacy.md)中，它们按 API 费率加上 [Max Mode](https://cursor.com/help/ai-features/max-mode.md) 加价计费，并计入你包含的请求次数。你可以选择上下文窗口大小，更大的上下文窗口会增加 token 用量和费用。首次开始使用时，系统会要求你设置支出上限。

## 故障排除

### Agent 运行没有启动

- 确保你已登录，并已连接 GitHub、GitLab、Azure DevOps 或 Bitbucket 账号。
- 检查你是否拥有必要的仓库权限。
- 你需要包含 Cloud Agents 的方案：Premium 及更高方案、Teams、Enterprise，或旧版付费方案。Free 和 Plus 不包含 Cloud Agents。

### 我的密钥对 Cloud Agent 不可用

- 确保你已在 [cursor.com/dashboard/cloud-agents](https://cursor.com/dashboard/cloud-agents) 中添加密钥
- 密钥的范围是工作区或团队；请确保你使用的是正确的账号
- 密钥在 Agent 启动时注入。已经在运行的 Agent 不会获取新密钥，因此添加后请启动一个新的 Agent

### 找不到 Secrets 标签页

- 如果看不到它，请确保你拥有必要的权限

### 快照会复制 .env.local 文件吗？

快照会保存你的基础环境配置（已安装的软件包、系统依赖等）。如果在创建快照时包含 `.env.local` 文件，这些文件也会被保存。不过，管理环境变量的推荐做法是使用 Cursor Settings 中的 Secrets 标签页。

### 队友打不开我的 Agent

- 该队友必须与你属于同一个 Cursor 团队。
- 他们需要在 [Integrations](https://cursor.com/dashboard/integrations) 下连接自己的源代码管理账号，并且能够访问该 Agent 工作过的仓库。
- 详情见[与团队共享 Agent](https://cursor.com/docs/cloud-agent.md#share-agents-with-your-team)。

### Slack 集成无法使用

请确认你的工作区管理员已安装 Cursor Slack 应用，并且你拥有相应权限。

## 命名沿革

Cloud Agents 以前称为 Background Agents。

---

## 站点地图

[所有文档页面概览](/llms.txt)
