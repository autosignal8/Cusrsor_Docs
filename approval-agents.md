> 原文：https://cursor.com/docs/approval-agents

# PR Routing & Approval

PR Routing & Approval 把 pull request 路由给合适的审查者，并可以批准低风险改动。

在 [Automations](https://cursor.com/automations/from-cursor/pr-routing-and-approval) 中配置 PR Routing & Approval。

## 工作原理

PR Routing & Approval 在你的 pull request 上运行。它根据代码所有权和提交历史分配审查者，并在满足你的条件时批准低风险 PR。

它不能代替完整的代码审查。它使用风险评分、批准策略文件、AI 审查 Agent 的发现以及你的配置，来决定是路由还是批准。

## 核心能力

### 审查者分配

PR Routing & Approval 可以根据代码所有权和提交历史为 pull request 分配审查者。

用审查者分配来描述 Agent 应在何时请求审查者。Agent 还会考虑适用的策略文件、风险设置、AI 审查者的发现，以及当前的审查状态。

### 基于风险的批准

当满足你的批准条件时，PR Routing & Approval 可以批准低风险的 pull request。

用批准条件描述 PR 在 Agent 批准之前必须满足的条件。Agent 还会考虑适用的策略文件、风险设置、AI 审查者的发现，以及当前的审查状态。

## 核心功能

### 对 AI 审查者的感知

PR Routing & Approval 可以使用来自其他 Cursor 审查系统的发现：

- **Bugbot Review Context** 在批准决定中使用 Bugbot 的发现。
- **Security Review Context** 在批准决定中使用 Security Agent 的发现。

启用这些上下文后，Agent 会等待相关的审查 Agent 检查完成，并把它们的发现用作批准信号。

Agent 最多等待大约 8 分钟。如果到那时某项检查仍未完成，Agent 不会批准该 PR。打开 **PR pushed / updated** 触发器后，下一次推送会再次运行 Agent。

Agent 只等待 PR 最新提交上的检查。如果某位审查者尚未在该提交上启动检查，或跳过了它，Agent 会在没有该审查者发现的情况下做出决定。除非启用了对草稿 PR 的审查，否则 Bugbot 会跳过草稿 PR；当它被设置为每个 PR 只运行一次时，它会跳过首次审查之后的提交。见 [Bugbot 配置](https://cursor.com/docs/bugbot.md#configuration)。

如果 Bugbot 或 Security Agents 报告了需要人工审查的发现，PR Routing & Approval 不会批准该 PR。

Security Agents 需要 Team 或 Enterprise 方案。

### 风险评分

PR Routing & Approval 可以按风险对 PR 分类，并强制执行最高批准阈值。

- **Use Risk Score** 启用风险分类，并可以通过提示词进一步自定义。
- **Maximum Risk Threshold** 设置 Agent 可以批准的最高风险级别。

如果 PR 超过配置的阈值，Agent 不会批准它。

### 批准策略文件

PR Routing & Approval 可以发现仓库策略文件，并在决定是否批准之前应用它们。

对于每个更改的文件，Agent 会检查该文件所在目录以及每个祖先目录，查找这个确切的文件名：

```text
APPROVAL_POLICY.md
```

只有完全匹配的基本名称才会被信任。在目录策略发现期间，`POLICY.md`、`approval_policy.md`、`APPROVAL_POLICY.md.bak` 和 `team_APPROVAL_POLICY.md` 这类文件会被忽略。

最接近且适用的 `APPROVAL_POLICY.md` 对那个目录下的文件拥有最高优先级。祖先策略仍然适用，除非它们与更具体的策略冲突。

### 路由策略

PR Routing & Approval 还会检查顶层路由文件：

```text
.cursor/approval-policies/ROUTING.md
```

`ROUTING.md` 是产品条目的 YAML 列表。每个条目包含：

- `product`：产品或领域名称。
- `boundary`：语义边界，或明确的相对于仓库的路径或 glob。
- `policies`：策略提示词指针，可以是明确的文件路径或语义描述。

如果缺少 `ROUTING.md`，基于目录的 `APPROVAL_POLICY.md` 发现仍然会运行。缺少路由不会削弱策略发现。

### 策略优先级

适用的批准策略提示词会覆盖通用批准条件、风险阈值、审查者选择指导、自定义批准说明，以及默认的自动审查立场。

如果策略冲突，Agent 遵循最具体的策略。如果具体程度不明确，它遵循更严格的说明，并避免自动批准。

如果 PR 更改了批准策略、路由文件、被路由的策略文件或针对审查者的策略文件，Agent 不会使用更改后的内容来放宽同一 PR 的审查要求。在基分支版本可用时，它使用基分支版本；无法确定基版本时，则要求人工审查。

## 设置

打开 [Automations 中的 PR Routing & Approval](https://cursor.com/automations/from-cursor/pr-routing-and-approval) 进行配置。

### 启用路由和批准

打开你需要的能力：

- **Enable PR Routing and Requests for Review** 根据代码所有权和提交历史分配审查者。
- **Automatically Approve PRs** 在你配置批准条件后批准低风险 PR。

选择 Agent 应运行的组织和仓库。

PR Routing & Approval 仅支持 GitHub 和 [Origin](https://cursor.com/docs/origin.md) 仓库。保存之前，请从该 Agent 中移除任何 GitLab、Bitbucket 或 Azure DevOps 仓库。

### 配置触发器

触发器决定 Agent 何时运行。PR Routing & Approval 支持如下 pull request 事件：

- **PR opened** 在创建非草稿 pull request，或草稿被标记为可以审查时运行 Agent。
- **PR pushed / updated** 在向现有 PR 推送新提交时运行 Agent。
- **PR commented** 在现有 PR 上发布了匹配正则表达式的评论时运行 Agent。

触发器可以限定到仓库或组织。对于团队拥有的仓库，团队管理员可以配置更广的团队范围。

### 配置审查信号

在 **Configuration** 中，选择 Agent 应使用的信号：

- **Use Bugbot Review Context**
- **Use Security Review Context**
- **Use Risk Score**
- **Maximum Risk for Approval**

用这些信号决定 Agent 在批准之前是否应依赖 AI 审查者的输出、安全发现和风险阈值。

**Configuration** 中的第一个选项 **Mode** 设置 Agent 如何在速度和成本之间取舍。**Default** 更快。**Low** 成本更低、运行更慢。

### 自定义批准规则

使用 **Custom Prompt** 为团队添加批准条件。你可以描述本地的审查预期、可以安全批准的 PR 示例，或需要人工审查的情况。

对于适用的文件，策略文件仍然优先于自定义提示词。

如果未设置自定义提示词，Agent 会使用 Cursor 管理的默认条件。

### 配置工具和 MCP

Agent 必须至少启用一项主要操作：

- **Request Reviewers**
- **Approve PR**

可选集成可以包括：

- Slack 通知。
- Microsoft Teams 通知。
- 用于额外工具访问的 MCP 服务器。

使用自定义提示词来指导 Agent 应如何使用 MCP 工具。

### 保存并启用

配置完成后，保存该 Agent。可以从详情页启用或禁用现有 Agent。

在 Team 方案上，每位团队成员都可以创建和编辑 PR Routing & Approval。在 Enterprise 方案上，只有团队管理员可以编辑它；其他成员可以查看。

---

## 站点地图

[所有文档页面概览](/llms.txt)
