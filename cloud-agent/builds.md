> 原文：https://cursor.com/docs/cloud-agent/builds

# Cloud Agent 构建

构建会在后台准备你的 Cloud Agent 环境。每个 Agent 都从一台预先构建好的机器启动，你的仓库、工具和依赖都已就绪。

使用构建，你可以获得：

- **更快的启动**：克隆、安装和依赖相关的工作提前完成，因此 Agent 从就绪的环境启动，而不必每次启动都等待。
- **可靠的启动**：Agent 始终从最近一次成功的构建启动。失败的安装或错误的配置不会替换仍可用的那一次。
- **可观察的环境**：你可以看到每一次构建，查看其日志和提交，并追溯每个 Agent 使用了哪一次构建。

## 构建如何工作

一次构建是已准备好的 Cloud Agent 环境的可启动快照。Cursor 会在 Agent 运行之前创建构建，并让最近一次成功的构建保持可启动。

每次构建都遵循这一生命周期：

1. **触发**：构建会按计划启动，或在你保存环境版本之后、根据手动请求，或应 Agent 的请求而启动。参见[构建何时发生](https://cursor.com/docs/cloud-agent/builds.md#when-builds-occur)。
2. **准备**：Cursor 从你的基础镜像开始，按默认分支克隆环境中的每个仓库，并运行 `install` 命令直到完成。
3. **快照**：Cursor 保存机器的磁盘状态，以及环境版本和每个仓库的确切 commit SHA。
4. **激活**：成功的构建会变为生效状态。
5. **启动 Agent**：新的 Agent、自动化和代码审查都从当前生效的构建启动。

Cursor 会保留当前生效构建的预热副本。这样就把仓库克隆和依赖安装从 Agent 的启动路径中去掉了。

如果新的构建失败，Agent 会继续使用最近一次成功的构建。损坏的依赖更新、install 命令或 Dockerfile 不会替换当前生效的环境。

构建用于 Cursor 托管的 Cloud Agents。在[自托管机器](https://cursor.com/docs/cloud-agent/self-hosted.md#environments-on-self-hosted-machines)上的运行会在你的机器上按当前状态启动，从不会启动一次构建。

对于[要求使用自托管机器](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#triggering-pool-agents)的团队，构建处于关闭状态，因为构建运行在 Cursor 托管的机器上。Cursor 不会构建该团队的环境，也不会构建其成员的个人环境。Builds 标签页会注明环境构建已关闭，并且不提供 **Trigger build**。对这些团队和成员，以及在自托管机器上的任何运行中，保存环境快照都会被拒绝。

## 构建何时发生

Cursor 会出于四种原因启动构建。Builds 标签页会用触发类型标注每一次构建。

| 触发类型 | 何时运行 |
| :------------------- | :-------------------------------------------------------------------- |
| Recurring | 按固定计划为每个环境运行 |
| Configuration change | 当你保存环境配置或更改其密钥时 |
| Manual | 当你在 Builds 标签页中选择 **Trigger build** 时 |
| Agent-requested | 当 Agent 运行一次测试构建时，例如在环境设置期间 |

### Recurring 构建

Cursor 会定期检查每个环境，并在有变化时重新构建。这使当前生效的构建保持在接近每个仓库默认分支最新提交的位置，因此 Agent 启动时使用的是新代码和已预热的依赖缓存，而不必在启动时拉取并重新安装。

### 被跳过的构建

当自上次完成的构建以来没有任何变化时，周期性检查会跳过构建：环境中任何仓库的默认分支都没有新提交，配置和密钥也没有变化。被跳过的检查不会运行 install 命令，并保留当前生效的构建。它不会出现在 Builds 标签页中。

不活跃的仓库很少出现 Recurring 构建。活跃的仓库会更频繁地重新构建。

Cursor 只会跳过周期性构建。Manual、Agent-requested 和 Configuration change 构建总会运行。

## 构建期间和 Agent 启动时会运行什么

让每个环境命令对应一个不同的阶段：

| 命令 | 何时运行 | 用途 |
| :---------- | :----------------------------- | :------------------------------------------------------------------------------------- |
| `install` | 每次构建期间 | 安装依赖、生成代码、编译产物，以及预热磁盘缓存 |
| `start` | 每次 Agent 运行开始时 | 启动 Docker、数据库、隧道和其他服务 |
| `terminals` | 每次 Agent 运行开始时 | 在与 Agent 共享的 `tmux` 终端中启动应用进程 |

让 `install` 能够完整跑完并且幂等。它可能会反复运行，也可能在先前准备好的磁盘状态之上运行。`npm install`、`pnpm install` 和 `pip install` 这类命令已经支持这种模式。

构建只保留磁盘状态。当 Cursor 为机器创建快照时，正在运行的进程、shell 导出的变量和内存中的缓存都会停止。把服务和其他特定于会话的工作放在 `start` 或 `terminals` 中。磁盘状态包括仓库检出和 Docker 的数据目录（`/var/lib/docker`），因此镜像和已停止的容器会保留。

你现有的环境输入仍然适用。构建会使用已保存的快照、`.cursor/environment.json`、Dockerfile、install 与启动命令、密钥和网络设置。

## 构建如何处理 Git 状态

构建在运行时会记录每个仓库所检出的提交。

- **默认分支运行**从当前生效构建所记录的提交开始。计划中的构建会在后台刷新该提交。当 **Update stale builds** 开启，且某次构建的年龄超过你的 **Staleness threshold** 时，Agent 会在启动时拉取默认分支的最新代码。当该设置关闭时，Agent 会原样使用构建所记录的提交。默认阈值是 24 小时。设为 `0` 即始终拉取。
- **功能分支运行**从当前生效构建已准备好的磁盘开始，然后 Cursor 检出请求的分支。源代码与你选择的分支一致，同时复用该构建中的依赖。
- **多仓库环境**为每个仓库记录一个提交，并一起准备完整的工作区。

如果功能分支更改了依赖，Agent 会收到你的环境上下文和 install 命令，以便在测试前刷新环境。

## 密钥如何与构建配合

构建可以访问团队密钥和环境密钥。把它们用于私有软件包仓库、产物存储，以及 `install` 所需的其他凭证。

用户密钥只在 Agent 启动时加入。它们在构建期间不可用，也不会成为共享快照的一部分。

保存环境配置或更改其密钥会触发一次新的构建。

## 管理构建

打开某个环境的 **Builds** 标签页，可以：

- 查看每次构建的类型、状态和开始时间
- 打开某次构建以查看其详情和日志
- 选择 **Trigger build** 以按需运行一次构建
- 激活草稿构建，或停用某次构建
- 取消进行中的构建
- 从某一次特定构建启动 Agent
- 配置 **Update stale builds** 和 **Staleness threshold**
- 设置 **Install timeout**，即 `install` 在构建失败前可以运行多少分钟。输入 5 到 300 之间的整数，或留空以使用默认的 90 分钟

**Trigger build** 以选择它的人的身份运行。其已连接的源代码管理账号需要能够访问环境中的每个仓库。对于 GitHub 仓库，这意味着 Write 访问权限或更高。

每次 Agent 运行都会记录它从哪一次构建启动。用这条来源信息，把环境行为与该构建中的确切配置和仓库提交进行对比。要查看它，请让 Agent 调用 [Cursor Cloud MCP](https://cursor.com/docs/cloud-agent/capabilities.md#cursor-cloud-mcp) 的 `environment-info` 工具。其 `build` 字段包含构建 ID（`bld-...`），以及这次运行是复用了构建的检出，还是拉取了最新代码。功能分支上的运行，或因其构建已过期而拉取了更新代码的运行，其 Git `HEAD` 可能与构建所记录的提交不同。

## 调试构建

打开失败的构建以查看其事件和日志。在你诊断失败原因时，Agent 会继续从当前生效的成功构建启动。

要精确复现，请从失败的构建启动一个 Agent。Agent 会打开处于失败状态的机器，在那里它可以查看日志、更新环境、运行测试构建并验证结果。

你也可以让 Cloud Agent 通过内置的 [Cursor Cloud MCP](https://cursor.com/docs/cloud-agent/capabilities.md#cursor-cloud-mcp) 检查并管理构建。例如：

```text
Inspect the latest failed Build for this environment. Fix the environment
configuration, run a test Build, and verify it before proposing the final
install and start commands.
```

## 构建行为参考

### Agent 使用哪一次构建？

默认情况下，Agent 使用其环境最新的成功且生效的构建。

在测试或调试时，你也可以从某一次特定构建启动 Agent。

### 第一次成功的构建之前会发生什么？

在环境有一次成功的构建之前，Agent 会在 Cursor 的默认镜像上启动，而不包含该环境的设置，例如它的快照、Dockerfile 或 `install` 命令。失败的构建不会中断现有的 Agent 工作流。

### 源代码有多新？

功能分支运行会在构建启动后检出请求的分支。默认分支运行从当前生效构建所记录的提交开始。如果 **Update stale builds** 开启，且构建的年龄超过你的 **Staleness threshold**，Agent 会在启动时拉取默认分支的最新代码。

### 构建会取代快照或 Dockerfile 吗？

不会。已保存的快照或 Dockerfile 定义了用于创建构建的基础机器。随后 Cursor 克隆仓库、运行 `install`，并创建一份新的可启动快照。

### 构建支持多个仓库吗？

支持。一次构建会准备环境中的所有仓库，并记录每个仓库所用的提交。

### 构建要额外收费吗？

不额外收费。构建已包含在 Cloud Agents 中。

## 相关

- [云端环境设置](https://cursor.com/docs/cloud-agent/setup.md)
- [Cloud Agent 能力](https://cursor.com/docs/cloud-agent/capabilities.md)
- [Cloud Agents 设置](https://cursor.com/docs/cloud-agent/settings.md)

---

## 站点地图

[所有文档页面概览](/llms.txt)
