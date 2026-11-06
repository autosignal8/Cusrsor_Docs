> 原文：https://cursor.com/docs/integrations/slack

# Slack

通过 Cursor 的 Slack 集成，你可以在 Slack 中提及 `@cursor` 并给出提示词，直接使用 [Cloud Agents](https://cursor.com/docs/cloud-agent.md) 处理任务。

[媒体](/docs-static/images/cloud-agent/slack/slack-agent.mp4)

## 开始使用

### 安装

1. 前往 [Cursor 集成](https://www.cursor.com/dashboard/integrations)

2. 点击 Slack 旁边的 *Connect*，或从这里打开[安装页面](https://cursor.com/api/install-slack-app)

3. 系统会提示你在工作区中安装 Cursor 的 Slack 应用。

4. 在 Slack 中安装完成后，你会回到 Cursor 以完成设置

   1. 连接仓库提供方（如果尚未连接），并选择一个默认仓库
   2. 启用按用量计费
   3. 确认隐私设置

5. 在 Slack 中提及 `@cursor`，开始使用 Cloud Agents

要取消关联你的 Slack 账号，前往 [Dashboard → Cloud Agents](https://www.cursor.com/dashboard/cloud-agents)，点击 **Slack integration** 旁边的 **Unlink Slack**。在你能再次从 Slack 启动 Cloud Agents 之前，需要重新关联。

## 如何使用

提及 `@cursor` 并给出提示词。Cursor 会尝试从你的消息中识别仓库、模型、基础分支，或具名的 [Cloud Agent 环境](https://cursor.com/docs/cloud-agent/setup.md)。选择仓库时，也会参考你最近的 Agent 活动。

对于具名环境，请在提示词中写上它的名称。例如：`@Cursor use the Platform environment to update the shared API`。

### 命令

运行 `@Cursor help` 可查看最新的命令列表。

| 命令 | 说明 |
| :-------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `@Cursor [prompt]` | 启动一个 Cloud Agent。在已有 Agent 的讨论串中，这会追加后续指令。谁可以跟进由 Team follow-ups 控制，并不只取决于所有权。 |
| `@Cursor settings` | 配置默认项以及频道的默认仓库。同时显示[团队默认 pool](https://cursor.com/docs/integrations/slack.md#team-default-pool)和[频道默认 pool](https://cursor.com/docs/integrations/slack.md#channel-default-pool) |
| `@Cursor [options] [prompt]` | 为一次运行设置目标、模型、分支、PR 行为、worker 或输出频道 |
| `@Cursor agent [prompt]` | 在讨论串中强制新建一个 Agent（例如 `@Cursor start a new agent to fix billing`） |
| `@Cursor list my agents` | 显示你正在运行的 Agent |
| `@Cursor pool` | 显示用于 Slack 启动的[团队默认 pool](https://cursor.com/docs/integrations/slack.md#team-default-pool)和本频道的[默认 pool](https://cursor.com/docs/integrations/slack.md#channel-default-pool) |
| `@Cursor pool set <name>` | 设置团队默认 pool（团队管理员） |
| `@Cursor pool unset` | 清除团队默认 pool（团队管理员） |
| `@Cursor pool set <name> channel` | 设置本频道的默认 pool（任何团队成员） |
| `@Cursor pool unset channel` | 清除本频道的默认 pool（任何团队成员） |

#### 选项

用这些选项自定义 Cloud Agent 的行为：

| 选项 | 说明 | 自然语言示例 | 内联示例 |
| :-------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :----------------------------- | :--------------------------- |
| `repo` | 使用指定仓库 | `in acme/backend` | `repo=acme/backend` |
| `env` / `environment` | 使用具名的 [Cloud Agent 环境](https://cursor.com/docs/cloud-agent/setup.md) | `use the Platform environment` | `env=Platform` |
| `branch` | 使用指定的基础分支 | `work from the dev branch` | `branch=dev` |
| `model` | 使用指定模型 | `with opus` | `model=opus` |
| `autopr` | 启用或禁用自动创建 PR | 需要内联选项 | `autopr=false` |
| `worker` / `machine` | 在指定的 [My Machine](https://cursor.com/docs/cloud-agent/self-hosted/my-machines.md) 上运行 | 需要内联选项 | `worker=my-devbox` |
| `pool` | 在指定的 [Team Pool](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#triggering-pool-agents) 上运行。当频道已有[频道默认 pool](https://cursor.com/docs/integrations/slack.md#channel-default-pool)，或你的团队已有[团队默认 pool](https://cursor.com/docs/integrations/slack.md#team-default-pool)时，此项可选 | 需要内联选项 | `pool=my-pool` |
| `self_hosted` / `sh` | 在你的某个 Team Pool 上运行。若已设置[频道默认 pool](https://cursor.com/docs/integrations/slack.md#channel-default-pool)或[团队默认 pool](https://cursor.com/docs/integrations/slack.md#team-default-pool)，则使用该 pool；`self_hosted=false` 会跳过这两者。取值为 `true`/`t`/`1` 或 `false`/`f`/`0` | 需要内联选项 | `self_hosted=true` 或 `sh=1` |
| `channel` | 把 Agent 更新发到你和 Cursor 都能访问的另一个频道 | 需要内联选项 | `channel=#eng-bots` |

#### 语法格式

自然语言：

```bash
@Cursor with opus, fix the login bug in backend-api
```

内联：

```bash
@Cursor env=Platform branch=dev model=opus autopr=false Fix the login bug
```

环境名包含空格时请加引号：

```bash
@Cursor env="Platform Services" Update the shared API
```

#### 选项优先级

组合使用这些选项时：

- **显式值**会覆盖默认值
- 如果同一选项重复出现，**靠后的值**会覆盖靠前的值
- **内联选项**优先于设置弹窗中的默认值
- 两者同时出现时，**`env`** 优先于 `repo`
- **`pool`、`worker`、`machine` 以及 `self_hosted=false`（或 `sh=0`）**优先于[频道默认 pool](https://cursor.com/docs/integrations/slack.md#channel-default-pool)和[团队默认 pool](https://cursor.com/docs/integrations/slack.md#team-default-pool)

机器人会从消息的任意位置解析选项，因此可以自然地书写命令。

Cursor 会忽略三反引号代码块中的 `key=value` 选项，包括单行代码块，并按原样把代码留在你的提示词中。在下面这条消息里，`pool=None` 位于代码块内部，因此不会据此选择 pool：

````bash
@Cursor why does this crash? ```engine = create_engine(db_url, pool=None)```
````

#### 使用讨论串上下文

Cloud Agents 能理解并使用已有讨论串中的上下文。当你的团队正在讨论一个问题，并希望 Agent 根据这段对话实现解决方案时，这很有用。

被调用时，Cloud Agents 会读取整段讨论串作为上下文，根据团队的讨论来理解并实现解决方案。

#### 何时使用强制命令

**什么时候需要 `@Cursor agent`？**

在已有 Agent 的讨论串中，`@Cursor [prompt]` 会追加后续指令。谁可以跟进由 [Team follow-ups](https://cursor.com/docs/cloud-agent/settings.md#team-follow-ups) 控制。如果 Team follow-ups 为 Disabled，则只有所有者可以跟进。要另外启动一个 Agent，请使用 `@Cursor agent [prompt]`，或用自然语言提出：

```bash
@Cursor start a new agent to refactor billing
```

「create a new agent」、「launch a fresh agent」或「new agent please」这类说法效果相同。

**什么时候需要上下文菜单中的 `Add follow-up`？**

在 Agent 的回复上使用上下文菜单 (⋯) 来添加后续指令。当一个讨论串里有多个 Agent，而你需要指定跟进哪一个时，这很有用。

#### 私信

你也可以直接给 Cursor 发消息，而不必在频道中提及 `@Cursor`。一条新的顶层私信会在自己的讨论串中启动一个新 Agent。在该讨论串中回复即是跟进它的 Agent，因此那里不需要 `@Cursor`，私信的上下文菜单也不会显示 **Add follow-up**。如果某条回复提出的是新需求，Cursor 可以改为在讨论串中启动一个新 Agent，并替换掉第一个。

Agent 工作时，Cursor 会在讨论串下方显示状态，例如 "Cursor is reading code..."，直到 Agent 完成这一回合。要同时运行第二个 Agent，请在私信中用一条新的顶层消息开始新的聊天，或在频道中提及 `@Cursor`。在频道里，每条顶层提及都会启动自己的 Agent。

### 状态更新与交接

在 Cursor 开始修改代码之前，如果你正在修复 bug，或者实现细节有助于你跟上进度，它会在讨论串中发布一份简短计划。直接明了的请求会跳过这条说明。工作期间，Cursor 会更新讨论串下方的简短状态。

Cloud Agent 运行时，你首先会看到 *Open in Cursor* 选项。

![Slack 中的 Open in Cursor 按钮](/docs-static/images/cloud-agent/slack/slack-open-in-cursor.png)

Cloud Agent 完成后，你会在 Slack 中收到通知，并可以选择在 GitHub 中查看已创建的 PR。

![Slack 中在 GitHub 查看 PR](/docs-static/images/cloud-agent/slack/slack-view-pr.png)

### 管理 Agent

要查看所有正在运行的 Agent，请运行 `@Cursor list my agents`。

在任意 Agent 消息上点击三个点 (⋯)，通过上下文菜单管理 Cloud Agents。

![Slack Agent 上下文菜单](/docs-static/images/cloud-agent/slack/slack-context-menu.png)

可用选项：

- **Add follow-up**：向已有 Agent 添加指令。私信中不显示此项；在讨论串中回复即为跟进
- **Switch repository**：用同一请求，在另一个仓库或 [Cloud Agent 环境](https://cursor.com/docs/cloud-agent/setup.md)上启动一个新 Agent，并从该目标的默认分支开始。第一个 Agent 的更改不会被带过去；它的分支和 pull request 保持原样
- **Delete**：停止并归档该 Cloud Agent
- **View request ID**：查看用于排查问题的唯一 request ID（联系支持时请附上）
- **Give feedback**：提供关于 Agent 表现的反馈

如果 Agent 需要当前范围之外的仓库或环境，Slack 也会在启动消息上显示 **Switch repository**。选择新目标会以同样的方式启动一个新 Agent。

## 配置

在 [Dashboard → Cloud Agents](https://www.cursor.com/dashboard/cloud-agents) 管理默认设置和隐私选项。

### 设置

#### Default Model

消息中没有指定模型时使用。可用选项见[设置](https://www.cursor.com/dashboard/cloud-agents)。

#### 仓库选择

Cursor 会根据以下信息自动选择合适的仓库：

1. **消息内容**：提示词中的仓库名称或关键词
2. **最近的 Agent 活动**：你最近使用过的仓库
3. **路由规则**：自定义的关键词到仓库映射（见下文）
4. **默认仓库**：没有匹配时的回退

要使用特定仓库，请在消息中写上它的名称。例如：`@Cursor in mobile-app, fix the login bug`。

#### 基础分支

Cloud Agent 的起始分支。留空则使用仓库的默认分支（通常是 `main`）

#### Team for Slack Agents

如果你属于多个付费 Cursor 团队，请选择 Slack Agent 在哪个团队下运行。你从 Slack 启动的 Agent 会在该团队下运行、计费并使用其设置。关联 Slack 账号时，在 **Cursor Team** 下选择；之后也可以在 [Dashboard → Cloud Agents](https://www.cursor.com/dashboard/cloud-agents) 中用 **Team for Slack Agents** 更改。如果 Slack 工作区被限制为只能对应一个 Cursor 团队，Slack Agent 始终在该团队下运行。

### 频道设置

使用 `@Cursor settings` 在频道级别配置默认设置。这些设置按团队生效，并会覆盖你在该频道中的个人默认值。

频道设置只适用于公开频道。

尤其适用于以下情况：

- 不同频道负责不同的仓库
- 团队希望所有成员使用一致的设置

配置频道设置：

1. 在目标频道中运行 `@Cursor settings`
2. 为该频道设置默认仓库
3. 在该频道中使用 Cloud Agents 的所有团队成员都会采用这些默认值

你的 Cursor 团队中的任何人都可以更改频道的默认仓库或[默认 pool](https://cursor.com/docs/integrations/slack.md#channel-default-pool)。Cursor 会在 `@Cursor settings` 消息下方的讨论串中回复，说明每次更改是谁做的以及新的值。当更改替换了已有默认值时，回复会同时写出两者，例如 "changed this channel's default Self-Hosted pool from `secure` to `gpu`"。如果频道已经是该值，你会收到一条仅自己可见的回复，说明没有任何更改。

频道设置优先于个人默认值，但你可以在消息中提及特定仓库来覆盖它们。

### 路由规则

路由规则让你定义会自动映射到某个目标的关键词。当消息包含某个关键词时，Cursor 会把 Agent 路由到关联的仓库或 [Cloud Agent 环境](https://cursor.com/docs/cloud-agent/setup.md)。环境可以把多个仓库打包在一起，因此一个关键词就能启动 Agent，并且它需要的每个仓库都已经配置好。

#### 设置路由规则

1. 前往 [Dashboard → Cloud Agents](https://www.cursor.com/dashboard/cloud-agents)
2. 找到 **Routing Rules** 部分
3. 添加关键词到目标的映射，把每个关键词指向一个仓库或一个环境

#### 示例规则

| 关键词 | 目标 |
| :--------- | :---------------------- |
| `frontend` | `acme/web-app` |
| `mobile` | `acme/mobile-app` |
| `api` | `acme/backend-services` |
| `platform` | `Platform` 环境 |

配置这些规则后：

- `@Cursor fix the frontend nav bug` → 路由到 `acme/web-app`
- `@Cursor update the mobile onboarding flow` → 路由到 `acme/mobile-app`
- `@Cursor add a migration across the platform` → 在 `Platform` 环境中启动，其全部仓库已就绪

把环境作为目标，对多仓库环境很有用。了解如何在 [Cloud Agent 文档](https://cursor.com/docs/cloud-agent/setup.md)中配置，包括[多仓库环境](https://cursor.com/docs/cloud-agent/setup.md#multi-repo-environments)。

#### 路由如何工作

Cursor 按以下顺序判断你的消息：

1. **消息内容**：提示词中的仓库名称或关键词
2. **最近的 Agent 活动**：你最近使用过的仓库
3. **路由规则**：自定义的关键词到目标映射（仓库或环境）
4. **频道默认值**：为此频道设置的仓库
5. **默认仓库**：没有匹配时的回退

### 团队默认 pool

团队管理员可以把一个 [Team Pool](https://cursor.com/docs/cloud-agent/self-hosted/pool.md) 设为 `@Cursor` 启动时的默认值。成员随后会在该 pool 上运行，不必在每次提及中添加 `pool=<name>` 或 `self_hosted=true`。该默认值适用于整个团队中每一个没有[频道默认 pool](https://cursor.com/docs/integrations/slack.md#channel-default-pool)的频道。

Team Pool 需要 Enterprise 方案。只有在 [Cloud Agents 仪表盘](https://cursor.com/dashboard/cloud-agents#self-hosted)中开启 **Allow Self-Hosted Machines** 时，团队默认值和频道默认值才会生效。如果管理员将其关闭，Slack 会忽略这两个默认值，提及将在 Cursor 的托管基础设施上运行。

可以在 Slack 中管理该默认值。设置或清除它所需的权限，与在仪表盘中更改 Self-Hosted 设置相同。任何人都可以查看。

```bash
@Cursor pool set my-pool
@Cursor pool
@Cursor pool unset
```

`@Cursor settings` 也会列出团队默认 pool。

#### 频道默认 pool

你的 Cursor 团队中的任何人都可以为频道指定自己的 pool，方式与设置频道的[默认仓库](https://cursor.com/docs/integrations/slack.md#channel-settings)相同。该频道中的 `@Cursor` 提及随后会在频道的 pool 上运行，而不是团队默认值；其他频道仍使用团队默认值。团队默认值本身仍然只有管理员可以更改。

在要更改的频道中运行：

```bash
@Cursor pool set my-pool channel
@Cursor pool
@Cursor pool unset channel
```

`@Cursor pool` 会在团队默认值旁边显示该频道的 pool。如果想从列表中选择，请运行 `@Cursor settings`，点击 **Set Pool for Channel**，然后选择你们团队的一个 pool。选择 **Use the team default (no channel pool)** 即可清除。清除后，该频道中的提及会回到团队默认值。

Cursor 公布 pool 更改的方式与其他[频道设置](https://cursor.com/docs/integrations/slack.md#channel-settings)相同。回复会写明是谁设置、更改或移除了 pool，位于你的 `@Cursor pool` 命令下方的讨论串中；如果是从列表中选择，则位于 `@Cursor settings` 消息下方。

#### Cursor 如何选择提及的运行位置

消息中的选项始终优先。Cursor 按以下顺序确定目标：

1. **消息中的选项。** `pool=<name>` 指向该 pool。`worker=` 或 `machine=` 指向你的某一台 [My Machines](https://cursor.com/docs/cloud-agent/self-hosted/my-machines.md)。`self_hosted=false`（或 `sh=0`）在 Cursor 的托管基础设施上运行。以上每一项都会跳过两个默认 pool。单独的 `self_hosted=true`（或 `sh=1`）会填入频道默认 pool；如果频道没有，则填入团队默认 pool。
2. **你的默认 My Machines worker。** 如果你自己为该仓库设有默认 worker，它的优先级高于两个默认 pool。
3. **频道默认 pool。** 当你提及 Cursor 的频道自己设有 pool 时使用。
4. **团队默认 pool。** 当消息没有上述任何选项，且频道没有自己的 pool 时使用。

#### 仓库与默认 pool

有频道或团队默认 pool 时，[仓库选择](https://cursor.com/docs/integrations/slack.md#how-routing-works)的方式相同：消息内容、最近活动、路由规则、频道默认值，然后是你的默认仓库和团队的默认仓库。接下来会发生什么，取决于 pool 的注册方式：

- **任意仓库 pool，未解析到仓库。** Slack 会在该 pool 上启动一个不带仓库的 Agent。源代码管理由 worker 负责，与任何[任意仓库 pool](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#any-repo-pools)相同。如果某个默认仓库确实解析成功，Agent 会把它作为上下文，但不会限制哪些 worker 可以认领这次运行。
- **绑定仓库的 pool，未解析到仓库。** Slack 会用一条临时回复拒绝这次提及，而不会回退到托管基础设施。请在提及中添加 `repo=` 或 `pool=`，使用 `self_hosted=false` 在托管基础设施上运行，或用 `@Cursor pool` 和 `@Cursor settings` 检查默认值。
- **已解析到仓库，且该 pool 提供该仓库。** Slack 会在设置了 `repo=` 和 `pool=` 的情况下启动，与显式的 `pool=<name>` 提及相同。
- **已解析到仓库，但该 pool 的 worker 只提供其他仓库。** Slack 会用一条临时回复拒绝这次提及。用 `repo=` 选择该 pool 所提供的仓库，或用 `pool=` 选择提供此仓库的 pool。
- **还没有 worker 连接到该 pool。** `@Cursor pool set` 接受任意名称，因为 [pool 是持久的](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#manage-pools)，其 worker 会加入也会离开。解析到仓库的提及会在 pool 中等待，直到有 worker 加入。没有仓库的提及会被一条临时回复拒绝。

### 隐私

Cloud Agents 支持 Privacy Mode。

进一步了解 [Privacy Mode](https://www.cursor.com/privacy-overview)，或管理你的[隐私设置](https://www.cursor.com/dashboard/cloud-agents)。

不支持 Privacy Mode (Legacy)。Cloud Agents 在运行期间需要临时存储代码。

#### Display Agent Summary

显示 Agent 摘要和 diff 图像。其中可能包含文件路径或代码片段。可以开启或关闭。

#### Display Agent Summary in External Channels

对于与其他工作区的 Slack Connect，或包含外部成员（例如 Guest）的频道，可以选择是否在外部频道中显示 Agent 摘要。

## 权限

Cursor 会请求以下 Slack 权限，以便 Cloud Agents 在你的工作区中工作：

| 权限 | 说明 |
| :------------------ | :---------------------------------------------------------------------------------- |
| `app_mentions:read` | 检测 @提及，以便启动 Cloud Agents 并响应请求 |
| `channels:history` | 在添加后续指令时，读取讨论串中此前的消息作为上下文 |
| `channels:join` | 在被邀请或收到请求时自动加入公开频道 |
| `channels:read` | 访问频道元数据（ID 和名称），以便发布回复和更新 |
| `chat:write` | 在 Agent 完成时发送状态更新、完成通知和 PR 链接 |
| `files:read` | 下载共享文件（日志、截图、代码示例）作为补充上下文 |
| `files:write` | 上传 Agent 更改的可视化摘要，便于快速查看 |
| `groups:history` | 读取私有频道中此前的消息，作为多轮对话的上下文 |
| `groups:read` | 访问私有频道元数据，以便发布回复并保持对话连贯 |
| `im:history` | 访问私信历史，作为持续对话的上下文 |
| `im:read` | 读取私信元数据，以识别参与者并维持正确的讨论串 |
| `im:write` | 发起私信，用于私下通知或单独沟通 |
| `mpim:history` | 访问多人私信历史，用于多方参与的对话 |
| `mpim:read` | 读取多人私信元数据，以便指向参与者并确保正确送达 |
| `reactions:read` | 观察 emoji 反应，用作用户反馈和状态信号 |
| `reactions:write` | 添加 emoji 反应以标记状态：⏳ 表示运行中，✅ 表示已完成，❌ 表示失败 |
| `team:read` | 识别工作区详情，以便区分不同安装并应用设置 |
| `users:read` | 将 Slack 用户与 Cursor 账号匹配，用于权限和安全访问 |

## 免责声明

Cursor 可能会出错。请仔细核对代码和回复。

## 隐私政策

有关 Cursor 如何收集、使用和保护你的数据，请参阅我们的[隐私政策](https://cursor.com/privacy)。

---

## 站点地图

[所有文档页面概览](/llms.txt)
