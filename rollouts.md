> 原文：https://cursor.com/docs/rollouts

# Rollouts

Rollouts 监控每个 pull request 从审查到生产的过程。它在 pull request 打开时写下 Rollout 计划，在变更部署后对照你的遥测数据进行检查，并报告它在每个环境中的健康状况。

在 [Automations](https://cursor.com/automations/rollouts) 中配置 Rollouts。

Rollouts 在 Teams 和 Enterprise 方案上可用。

## 工作原理

### Rollout 计划

pull request 打开时，Rollouts 会读取 diff 以及它所触及的系统，然后写下 Rollout 计划。计划列出 Rollouts 发现的风险、该变更应产生的效果、它将检查的信号，以及任何会使变更难以验证的埋点缺口。在 GitHub、GitLab.com 和 Bitbucket Cloud 上，Rollouts 把计划作为评论发布到 pull request。在 Origin 上，计划出现在 pull request 页面上。

要更改计划，在 pull request 评论中提及 Rollouts 评论所点名的 handle，并说明要观察或忽略什么。Rollouts 会修订计划并回复。只有对仓库拥有写权限的人才能更改计划。

### 部署跟踪

Rollouts 会在该变更的提交发生部署事件时唤醒，并对照你的日志、指标和追踪来运行计划。它分别跟踪每个环境，因此一项变更可以在预发环境中得到验证，同时仍在生产环境中被标记。

Rollouts 在部署发生时检查一次，然后在 20 分钟、1 小时、1 天和 3 天之后再次检查。

如果已合并的变更在没有记录到部署的情况下上线，请在该变更的页面上选择 **Run checks**。按钮位于环境标签页的右侧；在该变更还没有检查时，它单独位于那一行。选择环境和服务，然后开始检查。Rollouts 把该变更的版本视为在该环境中已上线，并按相同的时间表检查它。

### 回归

当 Rollouts 检测到回归时，它会点名它怀疑的变更，打开一个 issue，并通知作者。当变更没有造成回归、但没有按预期工作时，它也会这样做：新的代码路径已经运行，但其效果缺失、它出错，或其行为不正确。在 issue 页面上，选择 **Fix issue** 以在其上启动一个 Cloud Agent，或选择 **Close**。关闭时会要求填写原因（**Fixed**、**Not a Real Issue**、**Expected**、**Duplicate** 或 **Other**）和一条备注，两者都会留在该 issue 的历史中。Agent 会在该 issue 页面上的面板中打开。修复开始之后，**View fix** 会取代 **Fix issue**，并重新打开该 Agent。Rollouts 不会自行合并、还原或回滚变更。

### 跟踪变更

[Rollouts 页面](https://cursor.com/automations/rollouts)列出已合并的 pull request，并把它们分组为 **Attention**、**Monitoring**、**Pending** 和 **Verified**。**Mine** 列出你订阅的变更，**Team** 列出团队上的每一项变更。团队管理员会进入 **Team**，其他人会进入 **Mine**。变更所部署到的每个环境都会显示自己的状态，例如 **Deploying**、**Monitoring**、**Verified**、**Deploy failed** 或 **Issues found**。

当变更有未关闭的 issue 或失败的部署时，它位于 **Attention**。该变更上的每个 issue 都关闭之后，它计为 **Verified**。重新打开一个 issue 会把它移回 **Attention**。

### 服务

在 Rollouts 页面顶部选择 **Services**，即可看到部署流水线所报告的每个服务。要从该列表中隐藏某个服务，打开它并选择 **Archive**。它的部署仍保留在历史中。该服务会移到 **Archived** 下，在那里可以用 **Restore** 把它恢复回来。

## 设置 Rollouts

在 [Automations](https://cursor.com/automations) 中，打开 **Team** 标签页，并在 **From Cursor** 下的 Rollouts 卡片上选择 **Enable**。设置有四个步骤：

### 访问受监控的仓库

选择要监视的仓库。从这些仓库交付的每个 pull request 都会获得自己的监视，并绑定到它的作者。Rollouts 监视 Origin、GitHub、GitLab.com 和 Bitbucket Cloud 上的仓库。

### 把部署事件发送给 Rollouts

告诉 Rollouts 每次生产部署何时开始、何时结束。[创建 API 密钥](https://cursor.com/docs/api.md#creating-api-keys)，并把它存为 CI 密钥 `CURSOR_API_KEY`。使用用户 API 密钥、服务账号 API 密钥，或具有 `admin:*` 范围的团队 Admin API 密钥。你可以[手动](https://cursor.com/docs/rollouts.md#send-deployment-events)完成，也可以用 Agent 完成。使用 Agent 时，设置 Agent 会打开一个添加这些调用的 pull request，然后你合并它以完成设置。

### 遥测

连接你的可观测性工具，例如 Datadog，以便对照生产环境正在发生的情况验证每项变更。Rollouts 至少需要连接一个工具。如果一个都没有，变更会保持待处理，Rollouts 也无法检测问题。

### 通知

选择 pull request 作者如何收到关于其 Rollout 的通知。

## 发送部署事件

### 用 API 密钥交换 token

在部署作业的开头，用 `CURSOR_API_KEY` 密钥换取一个 token。下面的每次调用都发送这个 token。

```bash
TOKEN=$(curl -s -X POST https://api2.cursor.sh/auth/exchange_user_api_key \
  -H "Authorization: Bearer $CURSOR_API_KEY" -H "Content-Type: application/json" -d '{}' \
  | jq -r .accessToken)
```

### 每个环境和每个服务只创建一次

手动运行，或作为部署作业之前的引导步骤运行。它会创建下面调用所点名的条目，因此请在第一次报告之前运行它。

```bash
curl -s -X POST https://api.cursor.com/factory.v1.DeploymentsService/CreateEnvironment -H "Authorization: Bearer $TOKEN" -H "Content-Type: application/json" -H "Connect-Protocol-Version: 1" \
  -d '{"environmentId":"{env}","environment":{"displayName":"{env}"}}'
curl -s -X POST https://api.cursor.com/factory.v1.DeploymentsService/CreateService -H "Authorization: Bearer $TOKEN" -H "Content-Type: application/json" -H "Connect-Protocol-Version: 1" \
  -d '{"serviceId":"{service}","service":{"displayName":"{service}"}}'
```

### 创建部署

在你发布之前立即运行。`deployVersion` 是正在发布的提交 SHA。

```bash
DEPLOYMENT=$(curl -s -X POST https://api.cursor.com/factory.v1.DeploymentsService/CreateDeployment -H "Authorization: Bearer $TOKEN" -H "Content-Type: application/json" -H "Connect-Protocol-Version: 1" \
  -d '{"deployment":{"deploySourceUri":"https://github.com/{owner}/{repo}","environment":"environments/{env}","service":"services/{service}","deployVersion":"'"$GIT_SHA"'"},"event":{"started":{},"actor":"'"$CI_JOB_URL"'"}}' \
  | jq -r .deployment.name)
```

### 追加完成事件

在结果已知后立即运行。失败的报告绝不能导致部署失败。

```bash
curl -s -X POST https://api.cursor.com/factory.v1.DeploymentsService/AppendDeploymentEvent -H "Authorization: Bearer $TOKEN" -H "Content-Type: application/json" -H "Connect-Protocol-Version: 1" \
  -d '{"name":"'"$DEPLOYMENT"'","event":{"completed":{"succeeded":{}},"actor":"'"$CI_JOB_URL"'"}}'
# On failure:   "event":{"completed":{"failed":{"message":"<why>"}},"actor":...}
# If cancelled: "event":{"aborted":{},"actor":...}
```

## 设置

Rollouts 设置有四个部分：

- **Code access。** Rollouts 监视的仓库，最多 200 个，以及要监控哪些变更。
- **Deployment events。** 流水线如何报告部署了什么以及部署到哪里。
- **Data sources。** Rollouts 用来验证已部署变更并检测回归的 MCP 连接。
- **Notifications。** 你如何得知自己变更上的部署和回归。

Settings 顶部的开关为团队打开或关闭 Rollouts。它的标签显示为 **Enabled** 或 **Disabled**。只有团队管理员可以打开或关闭 Rollouts，或设置部署事件。其他成员可以看到这些控件，但不能更改它们。

### 选择要监控哪些变更

在 **Which changes to monitor** 下，用通俗的话描述 Rollouts 应跳过的 pull request。默认情况下，Rollouts 会跳过无法影响已部署环境中所运行内容的变更：仅文档的变更、格式调整、lint，或没有行为变化的错别字修复，以及仅测试的变更。清空这段文字，即可监控受监视仓库中的每一项变更。

只有明确匹配时，pull request 才会被跳过。当 Rollouts 跳过一个 pull request 时，它会在评论中说明原因。要仍然监控它，请提及该评论所点名的 handle。

### 通知

打开 **Personal Slack Notifications**，以便在 Slack 中得知你的变更。它一开始只发送 **Issues found** 消息。要同时在部署成功或失败时，或在检查没有发现问题时收到通知，请打开 **Deployment succeeded**、**Deployment failed** 或 **Check clear**。在 **Deliver to** 下，选择 **Direct message** 或 **Channel**。如果是频道，输入公开频道的名称或其 ID，邀请该字段下列出的每个应用，然后保存。不支持 Slack Connect 频道。

团队管理员必须先把 Rollouts 添加到 Slack。只有团队管理员可以更改团队的通知默认值。打开团队默认值后，当检查发现成员的 pull request 存在问题时，会向成员发送 Slack 消息，直到他们更改自己的设置。启用 Rollouts 会为 **Issues found** 消息打开 **Slack notifications for the team**，除非团队已经有通知默认值。Slack 通知在 Privacy Mode 中不可用。

## 相关页面

- [Automations](https://cursor.com/docs/cloud-agent/automations.md)
- [Security Agents](https://cursor.com/docs/security-agents.md)
- [Bugbot](https://cursor.com/docs/bugbot.md)

---

## 站点地图

[所有文档页面概览](/llms.txt)
