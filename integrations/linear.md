> 原文：https://cursor.com/docs/integrations/linear

# Linear

在 Linear 中把 issue 委派给 Cursor，或在评论中提及 `@Cursor`，即可直接使用 [Cloud Agents](https://cursor.com/docs/cloud-agent.md)。

[媒体](/docs-static/images/integrations/linear/linear-agent.mp4)

## 开始使用

### 安装

连接 Linear 集成必须是 Cursor 管理员。其他团队设置对非管理员成员也可用。

1. 前往 [Cursor 集成](https://www.cursor.com/dashboard/integrations)
2. 点击 Linear 旁边的 *Connect*
3. 连接你的 Linear 工作区并选择团队
4. 点击 *Authorize*
5. 在 Cursor 中完成剩余的 Cloud Agent 设置：
   - 连接仓库提供方，并选择一个默认仓库
   - 启用按用量计费
   - 确认隐私设置

### 账号关联

首次使用时会提示在 Cursor 与 Linear 之间关联账号。创建 PR 需要已连接仓库提供方。

## 如何使用

把 issue 委派给 Cursor，或在评论中提及 `@Cursor`。Cursor 会分析 issue，并自动过滤掉非开发类工作。

### 委派 issue

1. 打开 Linear issue
2. 点击 assignee 字段
3. 选择 "Cursor"

![在 Linear 中把 issue 委派给 Cursor](/docs-static/images/integrations/linear/linear-delegate.png)

### 提及 Cursor

在评论中提及 `@Cursor`，以分配一个新 Agent 或提供额外指令，例如：`@Cursor fix the authentication bug described above`。

## 工作流

Cloud Agents 会在 Linear 中显示实时状态，并在完成时自动创建 PR。在 [Cursor 仪表盘](https://www.cursor.com/dashboard/cloud-agents)中跟踪进度。

![Linear 中的 Cloud Agent 状态更新](/docs-static/images/integrations/linear/linear-activity.png)

### 后续指令

你可以在 Agent 会话中回复，内容会作为后续指令发送给该 Agent。只需在 Linear 评论中提及 `@Cursor`，即可向正在运行的 Cloud Agent 提供额外指导。

## 配置

在 [Dashboard → Cloud Agents](https://www.cursor.com/dashboard/cloud-agents) 配置 Cloud Agent 设置。

| 设置 | 位置 | 说明 |
| :--------------------- | :--------------- | :-------------------------------------------------------- |
| **Default Repository** | Cursor Dashboard | 未配置项目仓库时使用的主仓库 |
| **Default Model** | Cursor Dashboard | 用于 Cloud Agents 的 AI 模型 |
| **Base Branch** | Cursor Dashboard | 创建 PR 所基于的分支（通常是 `main` 或 `develop`） |

### 配置选项

你可以用几种方法配置 Cloud Agent 的行为：

**Issue 描述或评论**：使用 `[key=value]` 语法，例如：

- `@cursor please fix [repo=anysphere/everysphere]`
- `@cursor implement feature [model=claude-3.5-sonnet] [branch=feature-branch]`

**Issue 标签**：使用父子标签结构，父标签是配置键，子标签是值。

**项目标签**：与 issue 标签相同的父子结构，应用于项目级别。

支持的配置键：

- `repo`：指定目标仓库（例如 `owner/repository`）
- `branch`：指定创建 PR 所用的基础分支
- `model`：指定要使用的 AI 模型

### 仓库选择

Cursor 按以下优先级决定在哪个仓库上工作：

1. **Issue 描述/评论**：issue 文本或评论中的 `[repo=owner/repository]` 语法
2. **Issue 标签**：附加在特定 Linear issue 上的仓库标签
3. **项目标签**：附加在 Linear 项目上的仓库标签
4. **默认仓库**：在 Cursor 仪表盘设置中指定的仓库

#### 设置仓库标签

在 Linear 中创建仓库标签：

1. 在 Linear 工作区中前往 **Settings**
2. 点击 **Labels**
3. 点击 **New group**
4. 将群组命名为 "repo"（不区分大小写，但必须正好是 "repo"，不能是 "Repository" 或其他变体）
5. 在该群组内，用 `owner/repo` 格式为每个仓库创建标签

然后可以把这些标签分配给 issue 或项目，以指定 Cloud Agent 应在哪个仓库上工作。

![在 Linear 中配置仓库标签](/docs-static/images/integrations/linear/linear-project-labels.png)

## 高级功能

### 分诊规则（高级）

在 Linear 中设置自动化规则，以自动把 issue 委派给 Cursor：

1. 前往 Linear 项目设置
2. 打开分诊规则
3. 创建会自动执行以下操作的规则：
   - 添加特定标签
   - 把 issue 分配给 Cursor
   - 根据条件触发 Cloud Agents

分诊规则是一项高级功能，目前有一些限制。Linear 要求规则触发时必须有人工 assignee，不过这一要求可能会在以后的更新中取消。

### 获取帮助

查看 [Agent 活动](https://www.cursor.com/dashboard/cloud-agents)，联系支持时请附上 request ID。

## 反馈

通过 Linear 评论，或 Cursor 仪表盘中的支持渠道分享反馈。

---

## 站点地图

[所有文档页面概览](/llms.txt)
