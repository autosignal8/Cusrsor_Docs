> 原文：https://cursor.com/docs/integrations/microsoft-teams

# Microsoft Teams

通过 Cursor 的 Microsoft Teams 集成，你可以在 Microsoft Teams 中提及 `@Cursor` 并给出提示词，直接使用 [Cloud Agents](https://cursor.com/docs/cloud-agent.md) 处理任务。

## 开始使用

### 安装

1. 前往 [Cursor 集成](https://www.cursor.com/dashboard/integrations)

2. 点击 Microsoft Teams 旁边的 *Connect*，或前往 [Microsoft Marketplace 列表](https://marketplace.microsoft.com/en-us/product/WA200010720)

3. 在你的 Microsoft Teams 工作区中安装 Cursor 应用

4. 在 Microsoft Teams 中安装完成后，你会回到 Cursor 以完成设置

   1. 如果你尚未连接仓库提供方，请连接 GitHub、GitLab、Azure DevOps 或 Bitbucket
   2. 启用按用量计费
   3. 确认隐私设置

5. 在 Microsoft Teams 中提及 `@Cursor`，开始使用 Cloud Agents

## 如何使用

提及 `@Cursor` 并给出提示词。Cursor 会根据你的消息、讨论串上下文以及最近的 Agent 活动，自动选择合适的仓库、模型、基础分支，或具名的 [Cloud Agent 环境](https://cursor.com/docs/cloud-agent/setup.md)。

要使用特定仓库，请在消息中写上它的名称：

- `@Cursor in cursor-app, fix the login bug`
- `@Cursor fix the auth issue in backend-api`

要使用特定模型，请在消息中提到它：

- `@Cursor with opus, fix the login bug`
- `@Cursor use gpt-5.2 to refactor the auth module`

要使用具名环境，请在消息中提到它，或加上 `env=<name>`：

- `@Cursor use the Platform environment to update the shared API`
- `@Cursor env=Platform update the shared API`
- `@Cursor env="Platform Services" update the shared API`

### 命令

运行 `@Cursor help` 可查看最新的命令列表。

| 命令 | 说明 |
| :--------------------------- | :---------------------------------------------------------------------------------------- |
| `@Cursor [prompt]` | 启动一个 Cloud Agent。在已有 Agent 的频道讨论串中，会追加后续指令 |
| `@Cursor help` | 显示设置与使用方面的帮助 |
| `@Cursor unlink` | 断开你的 Cursor 账号与 Microsoft Teams 的连接 |
| `@Cursor disconnect` | 断开你的 Cursor 账号与 Microsoft Teams 的连接 |
| `@Cursor [options] [prompt]` | 为一次运行设置仓库、环境、分支或模型 |

#### 选项

用这些选项自定义 Cloud Agent 的行为：

| 选项 | 说明 | 自然语言示例 | 内联示例 |
| :-------------------- | :---------------------------------------------------------------------------------- | :----------------------------- | :------------------------ |
| `repo` | 使用指定仓库 | `in acme/web-app` | `repo=acme/web-app` |
| `env` / `environment` | 使用具名的 [Cloud Agent 环境](https://cursor.com/docs/cloud-agent/setup.md) | `use the Platform environment` | `env="Platform Services"` |
| `branch` | 使用指定的基础分支 | `work from the main branch` | `branch=main` |
| `model` | 使用指定模型 | `with opus` | `model=opus` |

#### 语法格式

自然语言：

```bash
@Cursor with opus, fix the login bug in backend-api
```

内联：

```bash
@Cursor env="Platform Services" branch=dev model=opus Fix the login bug
```

#### 选项优先级

组合使用这些选项时：

- **显式值**会覆盖默认值
- **内联选项**会覆盖从你的消息中推断出的模型和仓库值
- 没有指定或推断出值时，适用 **Dashboard settings**
- 两者同时出现时，**`env`** 优先于 `repo`

机器人会从消息的任意位置解析选项，因此可以自然地书写命令。

#### 使用讨论串上下文

Cloud Agents 能理解并使用已有 Microsoft Teams 讨论中的上下文。当你的团队正在讨论一个问题，并希望 Agent 根据这段对话修改代码时，这很有用。

被调用时，Cloud Agents 会读取相关的讨论串或聊天上下文，理解并按照团队的讨论采取行动。

#### 后续指令

在频道讨论串中，于该 Agent 的讨论串里再用一次 `@Cursor` 提及来回复，即可追加后续指令。

在个人聊天和群组聊天中，使用 *Open in Web* 或 *Open in Desktop*，从 Cursor 继续对话。

### 状态更新与交接

Cloud Agent 启动时，Microsoft Teams 会显示一张启动卡片，其中包含所选的仓库、模型和分支。卡片上有 *Open in Web*、*Open in Desktop* 和 *Switch repository* 这些选项。

Cloud Agent 完成后，你会收到带有结果的 Microsoft Teams 通知。如果 Agent 打开了 pull request，卡片上会包含查看该 PR 的选项。

### 管理 Agent

使用 Microsoft Teams 卡片上的操作来管理 Cloud Agents。

可用选项：

- **Add follow-up**：从频道讨论串向已有 Agent 添加指令
- **Switch repository**：针对另一个仓库重新发起同一请求
- **Delete**：停止并归档该 Cloud Agent
- **Open in Web**：在 Web 界面中继续
- **Open in Desktop**：在 Cursor 中继续
- **Update settings**：管理你的 Cloud Agent 默认值
- **Give feedback**：发送关于 Agent 表现的反馈

## 配置

在 [Dashboard -> Cloud Agents](https://www.cursor.com/dashboard/cloud-agents) 管理默认设置和隐私选项。

### 设置

#### 默认模型

消息中没有指定模型时使用。可用选项见[设置](https://www.cursor.com/dashboard/cloud-agents)。

#### 仓库选择

Cursor 会根据以下信息自动选择合适的仓库：

1. **消息内容**：提示词中的仓库名称或关键词
2. **最近的 Agent 活动**：你最近使用过的仓库
3. **默认仓库**：没有匹配时的回退

要使用特定仓库，请在消息中写上它的名称。例如：`@Cursor in mobile-app, fix the login bug`。

#### 基础分支

Cloud Agent 的起始分支。留空则使用仓库的默认分支，通常是 `main`。

### 路由行为

Cursor 按以下顺序判断你的 Microsoft Teams 消息：

1. **显式选项**：提示词中的 `repo`、`env`、`branch` 和 `model` 值
2. **消息内容**：提示词中的仓库名称、模型名称或分支名称
3. **最近的 Agent 活动**：你最近使用过的仓库
4. **默认仓库**：没有匹配时的回退

### 隐私

Cloud Agents 支持 Privacy Mode。

进一步了解 [Privacy Mode](https://www.cursor.com/privacy-overview)，或管理你的[隐私设置](https://www.cursor.com/dashboard/cloud-agents)。

不支持 Privacy Mode (Legacy)。Cloud Agents 在运行期间需要临时存储代码。

#### 显示 Agent 摘要

显示 Agent 摘要和 diff 图像。其中可能包含文件路径或代码片段。你可以开启或关闭此项。

## 权限

Cursor 会请求以下 Microsoft Teams 权限，以便 Cloud Agents 在你的工作区中工作：

| 权限 | 说明 |
| :--------------------------- | :------------------------------------------------------------------ |
| `identity` | 识别启动或管理 Agent 的 Microsoft Teams 用户 |
| `messageTeamMembers` | 发送私信，用于设置、账号关联和通知 |
| `ChannelMessage.Read.Group` | 读取频道消息和回复，作为讨论串上下文 |
| `ChatMessage.Read.Chat` | 读取个人聊天和群组聊天消息，作为对话上下文 |
| `ChannelSettings.Read.Group` | 读取频道元数据，包括频道名称和描述 |
| `TeamSettings.Read.Group` | 读取团队元数据，包括团队名称和描述 |

Cursor 应用支持 Microsoft Teams 中的个人聊天、团队频道和群组聊天。

## 免责声明

Cursor 可能会出错。请仔细核对代码和回复。

## 隐私政策

有关 Cursor 如何收集、使用和保护你的数据，请参阅我们的[隐私政策](https://cursor.com/privacy)。

---

## 站点地图

[所有文档页面概览](/llms.txt)
