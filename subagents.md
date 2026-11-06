> 原文：https://cursor.com/docs/subagents

# Subagents

Subagents 是专门的 AI 助手，Cursor 的 Agent 可以把任务委派给它们。每个 Subagent 在自己的上下文窗口里运行，处理特定类型的工作，再把结果交回父 Agent。用 Subagents 拆开复杂任务、并行做事，并在主对话里留住上下文。

你可以在编辑器、CLI 和 [Cloud Agents](https://cursor.com/docs/cloud-agent.md) 里使用 Subagents。

### 上下文隔离

每个 Subagent 都有自己的上下文窗口。长时间的研究或探索不会占用主对话里的空间。

### 并行执行

同时启动多个 Subagent。可以分头处理代码库的不同部分，不必等前一个做完。

### 专项能力

用自定义提示词、工具访问权限和模型来配置 Subagent，处理特定领域的任务。

### 可复用性

定义好自定义 Subagent，就可以跨项目使用。

## Subagents 如何工作

Agent 遇到复杂任务时，可以自动启动一个 Subagent。Subagent 会收到一条带齐必要上下文的提示词，自行完成工作，再返回一条带有结果的最终消息。

Subagent 从干净的上下文开始。它看不到此前的对话历史，所以父 Agent 会把相关信息放进提示词。

### 前台与后台

Subagent 以两种模式之一运行：

| 模式 | 行为 | 最适合 |
| :------------- | :------------------------------------------------------------------- | :------------------------------------------ |
| **前台** | 一直阻塞，直到 Subagent 完成。结果会立刻返回。 | 你需要拿到输出才能继续的顺序任务。 |
| **后台** | 立刻返回。Subagent 自行继续工作。 | 长时间任务，或并行的多条工作流。 |

## 内置 Subagents

Cursor 带有两个内置 Subagent，会自动处理很吃上下文的操作。这两个 Subagent 是根据一批已经顶到上下文窗口上限的 Agent 对话分析出来的。

| Subagent | 用途 | 为什么把它做成 Subagent |
| :---------- | :------------------------------ | :--------------------------------------------------------------------------------------------------------------------------------------------- |
| **Explore** | 搜索并分析代码库 | 探索代码库会产生大量中间输出，把主上下文撑得很大。它用一个更快的模型，同时跑许多路搜索。 |
| **Browser** | 通过 MCP 工具控制浏览器 | 浏览器交互会吐出嘈杂的 DOM 快照和截图。Subagent 会把这些过滤成相关结果。 |

### 为什么会有这些 Subagent

这些操作有共同点：中间输出很嘈杂，专门的提示词和工具能帮上忙，而且可能吃掉大量上下文。把它们做成 Subagent 可以解决几件事：

- **上下文隔离** — 中间输出留在 Subagent 里。父 Agent 只看到最终摘要。
- **模型更灵活** — Explore Subagent 默认用更快的模型。主 Agent 做完一次搜索的时间里，它可以跑 10 路并行搜索。
- **按任务配置** — 每个 Subagent 的提示词和工具访问都按自己的任务调过。
- **成本效率** — 更快的模型更便宜。把吃 token 的工作放进选了合适模型的 Subagent，总体成本会降下来。

这些 Subagent 不用你配置。时机合适时，Agent 会自动使用它们。

## 何时使用 Subagents

| 适合用 Subagents 时…… | 适合用 Skills 时…… |
| :-------------------------------------------------------- | :------------------------------------------------------ |
| 长时间的研究任务需要隔开上下文 | 任务只有一个用途（生成变更日志、格式化） |
| 要并行推进多条工作流 | 你想要一个快、而且可以反复用的动作 |
| 任务要在很多步里用到专项能力 | 任务一次就能做完 |
| 你想让另一边独立核验工作 | 你不需要单独的上下文窗口 |

如果你在为「生成变更日志」或「格式化 imports」这类简单、单一用途的任务建 Subagent，不妨改用 [Skill](https://cursor.com/docs/skills.md)。

## 快速开始

时机合适时，Agent 会自动使用 Subagents。你也可以让 Agent 建一个自定义 Subagent：

在 .cursor/agents/verifier.md 创建一个 Subagent 文件，前面是 YAML frontmatter（name、description），后面是提示词。verifier 这个 Subagent 要核验已完成的工作，检查实现是否真的能用，跑测试，并报告哪些通过了、哪些还没做完。

想要更多控制的话，在项目目录或用户目录里自己创建自定义 Subagent。

## 自定义 Subagents

自定义 Subagent 用来写下专项知识、落实团队标准，或把重复的工作流自动化。

### 文件位置

| 类型 | 位置 | 作用域 |
| :-------------------- | :------------------ | :--------------------------------------------------- |
| **项目 Subagents** | `.cursor/agents/` | 仅当前项目 |
| | `.claude/agents/` | 仅当前项目（Claude 兼容） |
| | `.codex/agents/` | 仅当前项目（Codex 兼容） |
| **用户 Subagents** | `~/.cursor/agents/` | 当前用户的全部项目 |
| | `~/.claude/agents/` | 当前用户的全部项目（Claude 兼容） |
| | `~/.codex/agents/` | 当前用户的全部项目（Codex 兼容） |

名称冲突时，项目 Subagents 优先。多个位置都有同名 Subagent 时，`.cursor/` 优先于 `.claude/` 或 `.codex/`。

### 文件格式

每个 Subagent 是一个带 YAML frontmatter 的 markdown 文件：

```markdown
---
name: security-auditor
description: Security specialist. Use when implementing auth, payments, or handling sensitive data.
model: inherit
readonly: true
---

You are a security expert auditing code for vulnerabilities.

When invoked:
1. Identify security-sensitive code paths
2. Check for common vulnerabilities (injection, XSS, auth bypass)
3. Verify secrets are not hardcoded
4. Review input validation and sanitization

Report findings by severity:
- Critical (must fix before deploy)
- High (fix soon)
- Medium (address when possible)
```

### 配置字段

| 字段 | 类型 | 必填 | 默认值 | 说明 |
| :-------------- | :------ | :------- | :-------------------- | :----------------------------------------------------------------------------------------------------------------------------------- |
| `name` | string | 否 | 从文件名得出 | 显示名称和标识符。使用小写字母和连字符。 |
| `description` | string | 否 | — | 显示在 Task 工具提示里的简短说明。Agent 读它来决定要不要委派。 |
| `model` | string | 否 | `inherit` | 使用的模型：`inherit`，或一个具体的模型 ID。参见[模型配置](https://cursor.com/docs/subagents.md#model-configuration)。 |
| `readonly` | boolean | 否 | `false` | 为 `true` 时，Subagent 以受限的写权限运行（不能编辑文件，也不能运行会改变状态的 shell 命令）。 |
| `is_background` | boolean | 否 | `false` | 为 `true` 时，Subagent 在后台运行，不阻塞父 Agent。 |

### 模型配置

`model` 字段决定 Subagent 用哪个模型。有两种选择：

| 值 | 行为 |
| :------------------ | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `inherit` | 使用与父 Agent 相同的模型。这是默认值。 |
| 某个具体的模型 ID | 使用你指定的那个模型，例如 `composer-2` 或 `gpt-5.6-sol`。可用 ID 见[模型参考](https://cursor.com/docs/models-and-pricing.md)。 |

Subagent 需要和父 Agent 一样的推理能力时，选 `inherit`。你需要某个模型自己的能力、而且不管父 Agent 用的是什么时，填一个具体的模型 ID。

#### 模型参数

在模型 ID 后面加上方括号，可以设置这个模型的选项，例如速度、推理力度和上下文窗口。选项写成 `id=value`，多个选项用逗号分开。

| 示例 | 行为 |
| :---------------------------------------- | :--------------------------------------------------------------------------------------- |
| `composer-2.5[]` | 固定基础模型。空方括号选择标准变体，而不是快速变体。 |
| `composer-2.5[fast=false]` | 显式选择标准（非快速）变体。 |
| `claude-opus-5[effort=high]` | 把推理力度设为 `high`。 |
| `claude-opus-5[context=300k]` | 把上下文窗口设为 300k tokens。 |
| `claude-opus-5[effort=high,context=300k]` | 把多个选项合在一起。 |

有哪些选项取决于模型，写法与 SDK 的[模型参数](https://cursor.com/docs/sdk/typescript.md#model-parameters)一样，都是 `id=value`。

```markdown
---
name: planner
description: Plans complex changes before implementation.
model: claude-opus-5[effort=high]
---

Break the task into a clear, ordered implementation plan.
```

#### 已配置的模型什么时候不会被用上

Cursor 会采用你在 Subagent frontmatter 里写的 `model`，除非碰到下面几种情况：

- **团队管理员限制** — 你所在组织的管理员已经阻止了这个模型。
- **旧版 Max Mode 设置** — 在旧版按请求计费的套餐上，这个模型需要 [Max Mode](https://cursor.com/help/ai-features/max-mode.md)，而你没有启用。
- **套餐限制** — 你当前的套餐里没有这个模型。

这时 Cursor 会改用一个兼容的模型。如果你看到的模型行为不符合预期，检查套餐和模型设置。

```markdown
---
name: code-reviewer
description: Reviews code for correctness and style.
model: inherit
---

Review the code changes for bugs, style issues, and edge cases.
```

```markdown
---
name: search-agent
description: Searches the codebase for relevant files and symbols.
model: inherit
---

Search the codebase and return relevant file paths and code snippets.
```

```markdown
---
name: reasoning-agent
description: Handles complex architectural decisions.
model: gpt-5.6-sol
---

Analyze the architecture and recommend changes with detailed reasoning.
```

## 父模型与用量池

模型选择器（包括 Auto）作用于父 Agent。Subagent 可以跑另一个模型：

- 内置 Subagent（Explore、Browser）会为子任务选一个模型。
- 自定义 Subagent 使用 `inherit`，或 frontmatter 里的 `model` 字段。
- 父 Agent 启动 Subagent 时可以点名一个模型。

每次请求都按实际运行的那个模型的标价计费。具名的第三方模型按[第三方用量](https://cursor.com/docs/models-and-pricing.md#third-party-models)计费，从 The X Subscription 的预付额度扣，或从旧版 Cursor 套餐的 Other Models 池扣。父对话用的是 Auto、Grok 或 Composer 时也一样。在 Teams 和 Enterprise 上，符合条件的第三方请求还会收取 [Cursor Token Rate](https://cursor.com/docs/models-and-pricing.md#cursor-token-rate)。某个第三方模型若在每一个定价档位上的缓存读取价格都严格低于每百万 token $0.10，则可以豁免。

Subagent 任务卡片会显示实际运行的模型。[用量仪表板](https://cursor.com/dashboard/usage)会在对应的用量行上列出这个模型。

团队管理员可以在 [Team Settings > Models](https://cursor.com/dashboard/team-settings/models) 里为团队阻止模型。参见[模型访问控制](https://cursor.com/docs/enterprise/model-and-integration-management.md#model-access-control)。

## 使用 Subagents

### 自动委派

Agent 会根据这些因素主动委派：

- 任务的复杂度和范围
- 项目里自定义 Subagent 的描述
- 当前的上下文和可用工具

在 `description` 字段里写上「主动使用」或「总是用于」这类说法，可以促使自动委派。

### 显式调用

在提示词里用 `/name` 语法点名某个 Subagent：

```text
> /verifier confirm the auth flow is complete
> /debugger investigate this error
> /security-auditor review the payment module
```

也可以在话里自然提到它们来调用：

```text
> Use the verifier subagent to confirm the auth flow is complete
> Have the debugger subagent investigate this error
> Run the security-auditor subagent on the payment module
```

### 并行执行

同时启动多个 Subagent，尽量提高吞吐：

```text
> Review the API changes and update the documentation in parallel
```

Agent 会在一条消息里发出多个 Task 工具调用，所以这些 Subagent 会同时跑。

### 隔离的项目副本

Subagent 默认共用父 Agent 的 checkout。几个 Subagent 同时改文件时，可能互相盖掉对方的改动。你如果要求隔离，每个 Subagent 会在项目的一份自己的副本里运行：

```text
> Run a swarm of subagents to fix these five flaky tests, each in its own environment
```

每个 Subagent 会得到自己的环境，以及自己的分支：同一台机器上、工作目录分开的隔离 Git worktree，或者自己的云端环境，带一台专用 VM 和一份仓库克隆。改动留在每个 Subagent 自己的分支上，直到父 Agent 把结果合并进来。

这是同一次会话里、Subagent 这一层的隔离。若要隔离整个 Agent，把它放进 [worktree](https://cursor.com/docs/configuration/worktrees.md) 运行，或把任务交给[云端 Subagent](https://cursor.com/docs/subagents.md#cloud-subagents)。

## 云端 Subagents

在本地 Agent 会话里，你可以把工作交给一个云端 Subagent，它在自己的 VM 和分支上运行。长时间或并行的工作在云端进行时，本地工作区保持干净，响应也保持灵敏。父 Agent 继续在本地或云端运行，不会被打断。云端 Subagents 从 Cursor 桌面应用里的 [Agents Window](https://cursor.com/docs/agent/agents-window.md) 运行。

### 用 /in-cloud 启动云端 Subagent

输入 `/in-cloud`，你接下来提交的任务就会作为云端 Subagent 运行。它会拉起自己的 VM 和分支来做这件事。

这适合把长时间或并行的工作隔开，例如修 CI、查一个问题，或在你继续在本地工作的同时去探索代码库。

### 用 /autopilot 让 PR 自动往下做

让云端 Subagent 用 `/autopilot` 接管一个 pull request，或点击快捷操作胶囊。云端 Agent 在远端反复迭代，把 PR 准备到可以合并，同时不占着你的本地会话。

云端 Subagents 使用为你的仓库配置的[环境](https://cursor.com/docs/cloud-agent/setup.md)，并遵循与其他 [Cloud Agents](https://cursor.com/docs/cloud-agent.md) 相同的模型和能力规则。因为它们跑在云端 VM 上，它们的 [MCP 服务器](https://cursor.com/docs/cloud-agent/capabilities.md#mcp-tools)来自团队在 [cursor.com/agents](https://cursor.com/agents) 的配置，而不是你的本地会话。

## 恢复 Subagents

Subagent 可以恢复，用来接着之前的对话做下去。这对要分多次调用的长时间任务很有用。

每次执行都会返回一个 agent ID。把这个 ID 传回去，就能保留全部上下文并恢复该 Subagent：

```text
> Resume agent abc123 and analyze the remaining test failures
```

后台 Subagent 会一边运行一边写下状态。它结束后你仍然可以恢复，用保留下来的上下文继续对话。

## 常见模式

### 验证 Agent

验证 Agent 会独立检查声称已经做完的工作是不是真的做完了。它要处理的是一个常见问题：AI 把任务标成完成，但实现并不完整，或者已经坏了。

```markdown
---
name: verifier
description: Validates completed work. Use after tasks are marked done to confirm implementations are functional.
---

You are a skeptical validator. Your job is to verify that work claimed as complete actually works.

When invoked:
1. Identify what was claimed to be completed
2. Check that the implementation exists and is functional
3. Run relevant tests or verification steps
4. Look for edge cases that may have been missed

Be thorough and skeptical. Report:
- What was verified and passed
- What was claimed but incomplete or broken
- Specific issues that need to be addressed

Do not accept claims at face value. Test everything.
```

在 .cursor/agents/verifier.md 创建一个 Subagent 文件，YAML frontmatter 里包含 name 和 description。description 应为「核验已完成的工作。任务被标成完成后使用，用来确认实现是可用的。」提示词正文要让它保持怀疑，通过跑测试来确认实现真的能用，并留意边界情况。

这种模式适合用来：

- 在把工单标为完成之前，确认功能端到端是通的
- 抓住只做了一部分的功能
- 确认测试是真的通过了（而不只是测试文件存在）

### 编排者模式

工作流比较复杂时，父 Agent 可以按顺序协调多个专项 Subagent：

1. **Planner** 分析需求，并写出技术方案
2. **Implementer** 按这个方案把功能做出来
3. **Verifier** 确认实现与需求一致

每一次交接都带上结构化输出，下一个 Agent 拿到的上下文是清楚的。

## 示例 Subagents

### Debugger

```markdown
---
name: debugger
description: Debugging specialist for errors and test failures. Use when encountering issues.
---

You are an expert debugger specializing in root cause analysis.

When invoked:
1. Capture error message and stack trace
2. Identify reproduction steps
3. Isolate the failure location
4. Implement minimal fix
5. Verify solution works

For each issue, provide:
- Root cause explanation
- Evidence supporting the diagnosis
- Specific code fix
- Testing approach

Focus on fixing the underlying issue, not symptoms.
```

在 .cursor/agents/debugger.md 创建一个 Subagent 文件，YAML frontmatter 里包含 name 和 description。debugger 这个 Subagent 要专做根因分析：抓取堆栈跟踪，找出复现步骤，把失败位置隔离出来，做最小修复，并验证解决方案。

### 测试运行器

```markdown
---
name: test-runner
description: Test automation expert. Use proactively to run tests and fix failures.
---

You are a test automation expert.

When you see code changes, proactively run appropriate tests.

If tests fail:
1. Analyze the failure output
2. Identify the root cause
3. Fix the issue while preserving test intent
4. Re-run to verify

Report test results with:
- Number of tests passed/failed
- Summary of any failures
- Changes made to fix issues
```

在 .cursor/agents/test-runner.md 创建一个 Subagent 文件，YAML frontmatter 里包含 name 和 description（要提到「主动使用」）。test-runner 这个 Subagent 一看到代码改动就应主动跑测试，分析失败原因，在保住测试意图的前提下修复问题，并报告结果。

## 最佳实践

- **把 Subagent 写得聚焦** — 每个 Subagent 只承担一件清楚的事。不要做泛泛的「helper」Agent。
- **把描述写好** — `description` 字段决定 Agent 什么时候把任务委派给这个 Subagent。花时间打磨它。写几条提示词试一试，看触发的是不是你要的那个。
- **提示词保持简短** — 又长又散的提示词会把重点冲淡。写得具体、直接。
- **把 Subagent 放进版本控制** — 把 `.cursor/agents/` 提交进仓库，团队就都能用。
- **先让 Agent 生成** — 让 Agent 帮你起草最初的配置，再自己改。
- **用 Hooks 处理文件输出** — 如果要 Subagent 产出结构化的输出文件，可以考虑用 [Hooks](https://cursor.com/docs/hooks.md) 统一处理和保存结果。

### 应避免的反模式

**不要建几十个泛泛的 Subagent。** 放着 50 多个指令含糊的 Subagent，比如「帮着写代码」，效果不好。Agent 不知道该在什么时候用它们，你也会把时间花在维护上。

- **描述含糊** — 「用于一般任务」没有告诉 Agent 该何时委派。要写具体：「实现带 OAuth 提供商的认证流程时使用。」
- **提示词过长** — 一篇 2,000 字的提示词不会让 Subagent 更聪明。它会更慢，也更难维护。
- **和 Commands 重复** — 任务如果用途单一，也不需要隔离上下文，请改用 [Skill](https://cursor.com/docs/skills.md) 或 [Command](https://cursor.com/docs/customize-cursor.md#extension-components)。
- **Subagent 太多** — 先做 2-3 个聚焦的。只有当你有清楚、彼此不同的用途时再加。

## 管理 Subagents

### 创建 Subagents

最简单的做法是让 Agent 帮你建一个：

在 .cursor/agents/security-reviewer.md 创建一个 Subagent 文件，YAML frontmatter 里包含 name 和 description。security-reviewer 这个 Subagent 要检查代码里的常见漏洞，例如注入、XSS 和硬编码的密钥。

也可以自己建：把 markdown 文件放到 `.cursor/agents/`（项目）或 `~/.cursor/agents/`（用户）。

### 查看 Subagents

Agent 会把全部自定义 Subagent 放进它可用的工具里。看一下项目中的 `.cursor/agents/` 目录，就知道配置了哪些。

## 性能与成本

Subagent 有取舍。弄清这些取舍，有助于你决定什么时候用。

| 收益 | 取舍 |
| :----------------- | :------------------------------------------------------------ |
| 上下文隔离 | 启动开销（每个 Subagent 都要自己收集上下文） |
| 并行执行 | 更高的 token 用量（多个上下文同时在跑） |
| 专注于专项 | 延迟（简单任务上可能比主 Agent 更慢） |

### Token 与成本方面的考虑

- **Subagent 各自消耗 token** — 每个 Subagent 有自己的上下文窗口和 token 用量。五个 Subagent 并行，所用 token 大约是单个 Agent 的五倍。
- **评估开销** — 快速、简单的任务，主 Agent 往往更快。Subagent 擅长复杂、跑得久，或需要并行的工作。
- **Subagent 可能更慢** — 好处是上下文隔离，不是速度。Subagent 做一件简单的事时，可能比主 Agent 慢，因为它是从头开始的。

## 常见问题

### 内置的 Subagent 有哪些？

Cursor 有两个内置 Subagent：`explore` 用来搜索代码库，`browser` 用来通过 MCP 做浏览器自动化。它们会自动处理很吃上下文的操作。你不用配置。

### Subagent 可以启动别的 Subagent 吗？

可以，但有嵌套层数限制。从 Cursor 2.5 起，Subagent 可以再启动下级 Subagent，形成一棵分工协作的树。主 Agent 和它直接启动的 Subagent 可以再启动 Subagent，但被另一个 Subagent 启动的 Subagent 不能继续往下启动。
嵌套启动还要求当前模式能使用 Task 工具，Hooks 或工具策略也可以阻止启动。

### 怎样看到 Subagent 在做什么？

后台 Subagent 会把输出写到 `~/.cursor/subagents/`。父 Agent 可以读这些文件来看进度。

### Subagent 失败了会怎样？

Subagent 会把错误状态返回给父 Agent。父 Agent 可以重试，可以带上更多上下文再恢复，也可以换一种方式处理这次失败。

### Subagent 里能用 MCP 工具吗？

可以。Subagent 继承父 Agent 的全部工具，包括已配置服务器上的 MCP 工具。[云端 Subagent](https://cursor.com/docs/subagents.md#cloud-subagents) 是例外：它们跑在云端 VM 上，用的是为你的团队在 [cursor.com/agents](https://cursor.com/agents) 配置的 MCP 服务器，不是你本地会话里的服务器。

### 怎样调试一个行为不对的 Subagent？

看它的描述和提示词，确认指令具体、没有歧义。你也可以拿一件简单的事显式调用它来测试。

### 父 Agent 用的是 Auto 时，Subagent 能用第三方模型吗？

可以。Auto 作用于父 Agent。Subagent 可以跑一个具名的第三方模型，这部分用量按该模型的标价、作为第三方用量计费，从预付额度扣；在旧版 Cursor 套餐上，则从 Other Models 池扣。父 Agent 用的是 Grok 或 Composer 时也一样。参见[父模型与用量池](https://cursor.com/docs/subagents.md#parent-model-and-usage-pools)。

### 为什么我的 Subagent 用了另一个模型？

Subagent 用的模型可以和父级选择器里的不一样。内置 Subagent 会按子任务选模型，父 Agent 在启动时也可以指定模型。参见[父模型与用量池](https://cursor.com/docs/subagents.md#parent-model-and-usage-pools)。

Cursor 还会在这些情况下覆盖已配置的模型：团队管理员阻止了该模型，你的套餐不包含该模型，或者旧版按请求计费的套餐需要 [Max Mode](https://cursor.com/help/ai-features/max-mode.md) 而你没有启用。在未启用 Max Mode 的旧版按请求计费套餐上，不论 `model` 如何配置，Subagent 都使用 Composer 运行。如果团队管理员已经阻止了 Composer，Subagent 只有在启用 Max Mode 时才能运行。在按用量计费的套餐上，以及启用了 Max Mode 的旧版按请求计费套餐上，Subagent 默认使用父模型。详情见[模型配置](https://cursor.com/docs/subagents.md#model-configuration)。

---

## 站点地图

[所有文档页面概览](/llms.txt)
