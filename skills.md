> 原文：https://cursor.com/docs/skills

# Agent Skills

Agent Skills 是一项开放标准，用于用专门能力扩展 AI Agent。Skills 把领域知识和工作流打包起来，供 Agent 用来完成特定任务。

## 什么是 Skills？

Skill 是一个可移植、受版本控制的包，用来教 Agent 如何执行领域特定的任务。Skills 可以包含脚本、模板和参考资料，Agent 可以用自己的工具来执行它们。

### 可移植

Skills 可以在任何支持 Agent Skills 标准的 Agent 上工作。

### 受版本控制

Skills 以文件形式存储，可以在你的仓库中跟踪。

### 可操作

Skills 可以包含脚本、模板和参考资料，Agent 用自己的工具来执行它们。

### 渐进式

Skills 按需加载资源，从而高效使用上下文。

## Skills 如何工作

Cursor 启动时，会自动从 Skill 目录发现 Skills，并把它们提供给 Agent。可用的 Skills 会呈现给 Agent，由它根据上下文决定何时相关。

也可以在 Agent 聊天中输入 `/` 并搜索 Skill 名称来手动调用。这样调用的 Skill 会附加到一条消息上。若要在整个会话中保持某个 Skill 开启，请把它用作 Custom Mode，并使用 Option+Enter（Mac）或 Alt+Enter（Windows）。参见 [Custom Modes](https://cursor.com/docs/agent/prompting.md#custom-modes)。

## Cursor 内置 Skills

Cursor 包含一小套内置 Skills，用来改进你的一般工作流。这些 Skills 由 Cursor 管理，并与你自己添加的 Skills 一起显示。

| Skill                     | 作用                                                                                         |
| ------------------------- | ---------------------------------------------------------------------------------------------------- |
| `/automate`               | 创建由日程、Slack 消息、GitHub 事件和其他来源触发的 Cursor Automations。 |
| `/autopilot`              | 监控 pull request，并处理反馈、冲突、失败的检查和后续工作。       |
| `/canvas`                 | 创建在对话旁边渲染的交互式 React 产物。                          |
| `/create-hook`            | 创建 Cursor Hooks，并针对 Agent 生命周期事件更新 `hooks.json`。                            |
| `/create-rule`            | 创建具有适当作用域和指令的 Cursor Rules。                                    |
| `/create-skill`           | 创建 Agent Skills，包括其结构和 `SKILL.md` 文件。                                |
| `/create-subagent`        | 创建具有聚焦角色和委派指令的自定义 Subagents。                             |
| `/cursor-blame`           | 调查由 AI 编写的改动，以及产生这些改动的提示词。                                 |
| `/loop`                   | 按指定间隔重复运行一段提示词或一个 Skill。                                           |
| `/migrate-to-skills`      | 把符合条件的动态 Rules 和 slash commands 转换为 Agent Skills。                                |
| `/review`                 | 选择并运行合适的代码审查 Agent。                                                  |
| `/review-bugbot`          | 用 Bugbot 审查代码中可能的 bug 和回归。                                            |
| `/review-security`        | 用 Security Review 审查代码中的安全漏洞。                                      |
| `/sdk`                    | 帮助你用 Cursor SDK 构建应用和集成。                                   |
| `/shell`                  | 把提供的文本作为字面 shell 命令运行。                                                   |
| `/split-to-prs`           | 把大型改动拆成更小的 pull request。                                                     |
| `/statusline`             | 配置 Cursor CLI 的 status line。                                                               |
| `/update-cli-config`      | 更新 `~/.cursor/cli-config.json` 中的 Cursor CLI 设置。                                          |
| `/update-cursor-settings` | 查找并更新相应的 Cursor 或 VS Code 设置。                                         |

在 Agent 聊天中输入 `/` 并选择名称，即可运行任意内置 Skill。当你的请求明显符合其用途时，Agent 也可能自动使用某些内置 Skills。

## Skill 目录

Skills 会自动从这些位置加载：

| 位置            | 作用域                                    |
| ------------------- | ---------------------------------------- |
| `.agents/skills/`   | 项目级                            |
| `.cursor/skills/`   | 项目级                            |
| `~/.agents/skills/` | 本机上的用户级（全局） |
| `~/.cursor/skills/` | 本机上的用户级（全局） |

`~/.cursor/skills/` 中的用户级 Skills 会留在你的机器上，直到你[为 Cloud Agents 同步它们](https://cursor.com/docs/skills.md#use-personal-skills-with-cloud-agents)或[把它们发布给你的团队](https://cursor.com/docs/plugins.md#publish-a-skill-to-your-team)。Cursor 不会把 `~/.agents/skills/` 或未同步的本地 Skills 复制到 Cloud Agents、Agents Window 的远程 SSH 会话，或[自托管 worker](https://cursor.com/docs/cloud-agent/self-hosted/pool.md)。在自托管 worker 上，请使用仓库中的项目 Skills，或把 Skills 内置到 worker 镜像中。

为了兼容，Cursor 也会从 Claude 和 Codex 的目录加载 Skills：`.claude/skills/`、`.codex/skills/`、`~/.claude/skills/` 和 `~/.codex/skills/`。

每个 Skill 应该是一个包含 `SKILL.md` 文件的文件夹：

```text
.agents/
└── skills/
    └── my-skill/
        └── SKILL.md
```

Skills 还可以包含用于脚本、参考资料和资源的可选目录：

```text
.agents/
└── skills/
    └── deploy-app/
        ├── SKILL.md
        ├── scripts/
        │   ├── deploy.sh
        │   └── validate.py
        ├── references/
        │   └── REFERENCE.md
        └── assets/
            └── config-template.json
```

### 嵌套的 Skill 目录

Skill 目录可以组织成子目录。这有助于按类别、团队或领域对相关 Skills 分组。Cursor 会递归遍历 Skills 根目录，并收录它找到的每一个 `SKILL.md`：

```text
.cursor/
└── skills/
    ├── shipping/
    │   ├── land-it/
    │   │   └── SKILL.md
    │   └── careful-merge-conflicts/
    │       └── SKILL.md
    ├── debugging/
    │   └── using-datadog-mcp/
    │       └── SKILL.md
    └── workflow/
        └── tdd/
            └── SKILL.md
```

类别文件夹只起组织作用。Skill 的身份来自包含 `SKILL.md` 的文件夹（此处为 `land-it`、`tdd` 等），而不是上层类别。

Cursor 也会发现嵌套项目子目录中的 Skills。仓库内任意位置的 `.cursor/skills/`（或 `.agents/skills/`）文件夹都会被收录，因此 monorepo 可以把 Skills 与它们所适用的包放在一起：

```text
my-monorepo/
├── .cursor/skills/         # repo-wide skills
│   └── land-it/SKILL.md
└── apps/
    └── web/
        └── .cursor/skills/  # app-specific skills
            └── deploy-web/SKILL.md
```

嵌套项目目录中的 Skills 会自动限定到该目录内的文件。在上面的示例中，只有当 Agent 处理 `apps/web/` 下的文件时才会出现 `deploy-web`，而仓库范围的 `.cursor/skills/` 中的 Skills 在各处都可用。这类似于 [`paths` frontmatter 字段](https://cursor.com/docs/skills.md#scoping-a-skill-to-specific-files)——你不必在嵌套 Skill 上设置 `paths`，就能把它限定到其所在目录。

## SKILL.md 文件格式

每个 Skill 都在带有 YAML frontmatter 的 `SKILL.md` 文件中定义：

```markdown
---
name: my-skill
description: Short description of what this skill does and when to use it.
---

# My Skill

Detailed instructions for the agent.

## When to Use

- Use this skill when...
- This skill is helpful for...

## Instructions

- Step-by-step guidance for the agent
- Domain-specific conventions
- Best practices and patterns
- Use the ask questions tool if you need to clarify requirements with the user
```

### Frontmatter 字段

| 字段                      | 必填 | 说明                                                                                                                                                                        |
| -------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `name`                     | 是      | Skill 标识符。只能使用小写字母、数字和连字符。必须与父文件夹名称一致。                                                                                 |
| `description`              | 是      | 描述该 Skill 做什么以及何时使用。Agent 用它来判断相关性。                                                                                        |
| `paths`                    | 否       | 把 Skill 限定到匹配文件的 glob 模式。接受逗号分隔的字符串或列表。设置后，仅当 Agent 处理匹配的文件时，该 Skill 才会出现。 |
| `disable-model-invocation` | 否       | 为 `true` 时，仅在通过 `/skill-name` 显式调用时才包含该 Skill。Agent 不会根据上下文自动应用它。                                     |
| `icon`                     | 否       | 当该 Skill 用作 [Custom Mode](https://cursor.com/docs/agent/prompting.md#custom-modes) 时，徽章上显示的图标。默认为闪电图标。                          |
| `color`                    | 否       | 当该 Skill 用作 Custom Mode 时的徽章颜色。取值为 `default`、`green`、`cyan`、`blue`、`purple`、`magenta`、`orange`、`yellow`、`red` 或 `brand` 之一。                        |
| `metadata`                 | 否       | 用于附加元数据的任意键值映射。                                                                                                                               |

在 [Cloud Agents](https://cursor.com/docs/cloud-agent.md) 中，如果某个 Skill 的 `SKILL.md` 无法读取、frontmatter 无法解析，或没有 `description`，该次运行会显示一条点名该 Skill 的警告。没有 description 时，Agent 不太可能自行选用该 Skill，但你仍然可以按名称调用它。

## 把 Skill 限定到特定文件

使用 `paths` 字段把 Skill 限制为匹配一个或多个 glob 模式的文件。此后，仅当 Agent 正在读取或编辑匹配的文件时，该 Skill 才会呈现给它。这样，在无关的工作中，针对特定文件的指引就不会进入上下文。

```markdown
---
name: react-component-patterns
description: Conventions for writing React components in this codebase.
paths:
  - "**/*.tsx"
  - "packages/ui/**/*.ts"
---

# React component patterns

...
```

你也可以传入一个逗号分隔的字符串：

```markdown
---
name: python-style
description: Style rules for Python files.
paths: "**/*.py, scripts/**/*.py"
---
```

模式遵循标准 glob 语法。如果某个 Skill 无论打开哪些文件都应可用，请不要设置 `paths`。

旧的 `globs` 字段仍可作为旧 Skill 的回退被接受，但新 Skill 应使用 `paths`。

## 禁用自动调用

默认情况下，当 Agent 判断 Skills 相关时会自动应用它们。设置 `disable-model-invocation: true` 可以让 Skill 表现得像传统的 slash command：只有当你在聊天中显式输入 `/skill-name` 时，它才会被包含进上下文。

## 把 Skill 用作 Custom Mode

任何带有有效 frontmatter 块的 Skill 都可以作为 [Custom Mode](https://cursor.com/docs/agent/prompting.md#custom-modes) 的基础，从而在整个会话中把该 Skill 保持在上下文里。处于激活状态的模式会在聊天输入框中显示一个徽章。可以用可选的 `icon` 和 `color` frontmatter 字段来设置它的样式：

```markdown
---
name: tdd
description: Test-driven development playbook for this repo.
icon: beaker
color: green
---
```

图标来自 Cursor 的图标集，名称如 `code`、`terminal`、`bug`、`git-branch`、`book-open`、`beaker`、`shield` 和 `rocket`。无法识别的图标或颜色会回退到默认徽章，也就是闪电图标。

## 在 Skills 中包含脚本

Skills 可以包含 `scripts/` 目录，其中是 Agent 可以运行的可执行代码。在 `SKILL.md` 中使用相对于 Skill 根目录的路径来引用脚本。

```markdown
---
name: deploy-app
description: Deploy the application to staging or production environments. Use when deploying code or when the user mentions deployment, releases, or environments.
---

# Deploy App

Deploy the application using the provided scripts.

## Usage

Run the deployment script: `scripts/deploy.sh <environment>`

Where `<environment>` is either `staging` or `production`.

## Pre-deployment Validation

Before deploying, run the validation script: `python scripts/validate.py`
```

Agent 会读取这些指令，并在该 Skill 被调用时执行所引用的脚本。脚本可以用任何语言编写——Bash、Python、JavaScript，或 Agent 实现所支持的任何其他可执行格式。

脚本应当自包含，包含有帮助的错误信息，并妥善处理边界情况。

## 可选目录

Skills 支持这些可选目录：

| 目录     | 用途                                                |
| ------------- | ------------------------------------------------------ |
| `scripts/`    | Agent 可以运行的可执行代码                    |
| `references/` | 按需加载的附加文档              |
| `assets/`     | 模板、图像或数据文件等静态资源 |

让主要的 `SKILL.md` 保持聚焦，并把详细的参考材料移到单独的文件中。这样可以高效使用上下文，因为 Agent 会渐进式加载资源——只在需要时加载。

## 查看 Skills

要查看已发现的 Skills，在侧边栏打开 **Customize** 并前往 **Skills**。从插件或你的项目安装的 Skills 会与 Rules 一起出现在 **Agent Decides** 部分。

## 把个人 Skills 用于 Cloud Agents

`~/.cursor/skills/` 中的个人 Skills 在你的本地机器上运行。打开 **Sync Skills for Cloud Agents**，即可把同样的 Skills 用于 [Cloud Agents](https://cursor.com/docs/cloud-agent.md)。

已同步的 Skills 只对你私有。它们不会与你的团队分享。

已同步的 Skills 会加载到你自己启动的 Cloud Agents 中。[Automation](https://cursor.com/docs/cloud-agent/automations.md) 运行不会加载它们，包括设置为 [Run as **Me**](https://cursor.com/docs/cloud-agent/automations.md#run-as) 的自动化。要给某个 Automation 提供 Skill，请把它添加到仓库的 `.cursor/skills/` 文件夹。

### 同步你的 Skills

1. 打开 **Settings → Agents**。
2. 在 **Context and Tools** 下，打开 **Sync Skills for Cloud Agents**。
3. 确认。Cursor 会把 `~/.cursor/skills/` 的内容移到同步存储中，以便你的 Cloud Agents 可以使用它们。

此后，只要 Cursor 正在运行，你保存后不久 Cursor 就会上传 Skill 的更改。Cloud Agents 使用最新的已同步副本。

你也可以从 **Customize → Skills** 开始同步。

只有 `~/.cursor/skills/` 会同步。项目 Skills、`~/.agents/skills/` 以及你机器上的其他文件都留在本地。

### 停止同步

在 **Settings → Agents** 下关闭 **Sync Skills for Cloud Agents**。Cursor 会把已同步的 Skills 移回你的机器。

### 团队管理员控制

在 Teams 和 Enterprise 方案中，管理员决定成员是否可以同步：

1. 打开 [Team Settings](https://cursor.com/dashboard/team-settings)。
2. 前往 **Security & Identity**。
3. 关闭 **Sync Skills for Cloud Agents**，以为团队中的所有人禁用同步。

当团队设置开启时，每位成员仍然自行选择是否同步。当它关闭时，团队中没有人可以同步。

要与队友分享某个 Skill，请[把它发布到你的团队 Marketplace](https://cursor.com/docs/plugins.md#publish-a-skill-to-your-team)。发布与同步不同：队友可以安装已发布的 Skill，并且 Cursor 会在为你的团队托管的仓库中保存一份副本。

## 从仓库安装 Skills

Skills 不会被单独导入。要从 GitHub 仓库引入 Skills，请把它们打包进一个[插件](https://cursor.com/docs/plugins.md)，并通过 Marketplace 发布该插件：在 **Customize** 中用 **From GitHub Repository** 导入仓库（该仓库需要有 `.cursor-plugin/marketplace.json`），或把它添加为[团队 Marketplace](https://cursor.com/docs/plugins.md#add-a-team-marketplace)，然后[安装该插件](https://cursor.com/docs/plugins.md#installing-plugins)。Skills 会随插件一起到来，并在 Customize 中与你的其他 Skills 一并显示。

## 把规则和命令迁移到 Skills

Cursor 在 2.4 中包含内置的 `/migrate-to-skills` Skill，帮助你把现有的动态规则和 slash commands 转换为 Skills。

该迁移 Skill 会转换：

- **动态规则**：使用 "Apply Intelligently" 配置的规则——`alwaysApply: false`（或未定义）且没有定义 `globs` 模式的规则。这些会被转换为标准 Skills。
- **Slash commands**：用户级和工作区级的命令都会转换为带有 `disable-model-invocation: true` 的 Skills，从而保留其显式调用行为。

要迁移：

1. 在 Agent 聊天中输入 `/migrate-to-skills`
2. Agent 会识别符合条件的规则和命令，并把它们转换为 Skills
3. 在 `.cursor/skills/` 中查看生成的 Skills

带有 `alwaysApply: true` 或特定 `globs` 模式的规则不会被迁移，因为它们具有与 Skill 行为不同的显式触发条件。用户规则也不会被迁移，因为它们并不存储在文件系统上。

## 了解更多

Agent Skills 是一项开放标准。更多内容见 [agentskills.io](https://agentskills.io)。

## 相关

- [Skills 帮助](https://cursor.com/help/customization/skills.md)
- [向团队发布 Skill](https://cursor.com/docs/plugins.md#publish-a-skill-to-your-team)
- [自托管机器](https://cursor.com/docs/cloud-agent/self-hosted/choose-runtime.md)

---

## 站点地图

[所有文档页面概览](/llms.txt)
