> 原文：https://cursor.com/docs/rules

# Rules

Rules 为 Agent 提供系统级指令。它们把提示词、脚本等内容打包在一起，便于在团队中管理和分享工作流。

Cursor 支持四种规则：

### 项目规则

存储在 `.cursor/rules` 中，受版本控制，作用域为你的代码库。

### 用户规则

对你的 Cursor 环境全局生效。由 Agent (Chat) 使用。

### 团队规则

从仪表板管理的全团队规则。在 Team 和 [Enterprise](https://cursor.com/docs/enterprise.md) 方案中可用。

### AGENTS.md

Markdown 格式的 Agent 指令。`.cursor/rules` 的简单替代。

## 规则如何工作

大语言模型在各次补全之间不会保留记忆。Rules 在提示词层面提供持久、可复用的上下文。

应用时，规则内容会包含在模型上下文的开头。这为 AI 生成代码、理解编辑或协助工作流提供一致的指引。

## 项目规则

项目规则以 `.mdc` 文件的形式放在 `.cursor/rules` 中，并受版本控制。它们可以通过路径模式限定作用域、手动调用，或根据相关性被包含。

使用项目规则可以：

- 写入关于你的代码库的领域知识
- 自动化项目特定的工作流或模板
- 统一风格或架构决策

### 规则文件结构

每条规则都是一个 `.mdc` 文件，名称可以随意取。项目规则必须使用 `.mdc` 扩展名。`.cursor/rules` 中的普通 `.md` 文件会被规则系统忽略，因为它没有 frontmatter 来指定 `description`、`globs` 和 `alwaysApply`。如果你更喜欢纯 markdown，请改用 [AGENTS.md](https://cursor.com/docs/rules.md#agentsmd)。

```bash
.cursor/rules/
  react-patterns.mdc       # Recognized as a project rule
  api-guidelines.md        # Ignored (wrong extension)
  frontend/                # Organize rules in folders
    components.mdc
```

### 规则的构成

每条规则都是带有 frontmatter 元数据和正文的 markdown 文件。通过类型下拉菜单控制规则如何应用，该菜单会更改 `description`、`globs`、`alwaysApply` 这些属性。

| 规则类型                  | 说明                                           |
| :------------------------ | :--------------------------------------------- |
| `Always Apply`            | 应用于每一次聊天会话                           |
| `Apply Intelligently`     | 当 Agent 根据 description 判断它相关时         |
| `Apply to Specific Files` | 当文件匹配指定的模式时                         |
| `Apply Manually`          | 在聊天中被 @ 提及时（例如 `@my-rule`）         |

在底层，三个 frontmatter 字段共同决定规则何时被包含：

| `alwaysApply` | `description` | `globs`  | 行为                                                         |
| :------------ | :------------ | :------- | :----------------------------------------------------------- |
| `true`        | —             | —        | 始终包含。`globs` 和 `description` 会被忽略。                |
| `false`       | —             | 已提供   | 当匹配的文件处于上下文中时自动附加。                         |
| `false`       | 已提供        | 已省略   | Agent 读取 `description`，并在相关时引入该规则。             |
| `false`       | 已省略        | 已省略   | 仅当你在聊天中 `@` 提及该规则时才会包含。                    |

```md title="Always applied"
---
alwaysApply: true
---

- All source files must include the company copyright header
- When you are unsure about implementation details, read the relevant
  source files before proposing changes
- Never modify generated files in the `dist/` or `build/` directories
```

```md title="Auto-attached by file pattern"
---
globs: src/components/**/*.tsx
alwaysApply: false
---

- Use named exports, not default exports
- Co-locate styles in a module CSS file next to the component
- Keep components under 200 lines. Extract subcomponents into the same
  directory when a file grows beyond that
- Prefer composition over prop drilling. Pass children or render props
  instead of threading data through multiple layers
```

```md title="Agent-selected based on description"
---
description: RPC service conventions and patterns for the backend
alwaysApply: false
---

- Define each service in its own file under `src/services/`
- Always validate inputs at the service boundary before passing data
  to internal functions
- Return structured error objects with a `code` and `message` field,
  never throw raw strings
- Read `@service-template.ts` for the standard boilerplate before
  creating a new service
```

```md title="Manual — only via @-mention"
---
alwaysApply: false
---

- Every database migration must have both `up` and `down` functions
  so it can be fully reversed
- Never alter a column type in-place. Add a new column, backfill,
  then drop the old one in a separate migration
- Read `@migration-template.sql` for the expected file structure
  before writing a migration
```

### 从规则中引用文件

规则内容会按原文传给 Agent。像 `@migration-template.sql` 这样的文件提及会告诉 Agent 该看哪里，Agent 在需要内容时用它的工具读取该文件。文件内容不会内联到提示词中，在编辑器、CLI 和 Cloud Agents 中都是如此。任何必须始终处于上下文中的内容，请直接写在规则正文里。

### Glob 模式示例

使用 `globs` 把规则限定到特定文件或目录。多个模式用逗号分隔。

| 模式                          | 匹配                                          |
| :---------------------------- | :-------------------------------------------- |
| `*`                           | 任意单个文件名片段                            |
| `**`                          | 任意数量的目录（递归）                        |
| `*.ts`                        | 根目录中的所有 `.ts` 文件                     |
| `**/*.ts`                     | 任意目录中的所有 `.ts` 文件                   |
| `src/**`                      | `src/` 下任意位置的所有文件                   |
| `src/**/*.tsx`                | `src/` 下任意位置的所有 `.tsx` 文件           |
| `docs/**/*.md, docs/**/*.mdx` | `docs/` 下的 `.md` 和 `.mdx` 文件（逗号分隔） |
| `tailwind.config.*`           | 任意扩展名的 `tailwind.config`                |

### 创建规则

创建规则有两种方式：

- **聊天中的 `/create-rule`**：在 Agent 中输入 `/create-rule` 并描述你的需求。Agent 会生成带有正确 frontmatter 的规则文件，并保存到 `.cursor/rules`。
- **从 Customize**：在侧边栏打开 **Customize**，前往 **Rules**，然后点击 **Add Rule**。这会在 `.cursor/rules` 中创建一个新的规则文件。在 Customize 中你可以看到所有规则及其状态。

## 最佳实践

好的规则应当聚焦、可执行，并且范围明确。

- 把规则控制在 500 行以内
- 把大型规则拆成多条可组合的规则
- 提供具体示例或被引用的文件
- 避免含糊的指引。把规则写得像清晰的内部文档
- 在聊天中重复提示词时，复用规则
- 引用文件，而不是复制其内容。这样规则可以保持简短，也不会随着代码变化而过时。Agent 在需要时会读取被引用的文件，因此请告诉它要在其中查找什么

### 规则中应避免什么

- **复制整份风格指南**：改用 linter。Agent 已经了解常见的风格约定。
- **记录每一个可能的命令**：Agent 了解 npm、git 和 pytest 等常见工具。
- **为很少适用的边界情况添加指令**：让规则聚焦于你经常使用的模式。
- **重复代码库中已有的内容**：指向规范示例，而不是复制代码。

从简单开始。只有当你注意到 Agent 反复犯同样的错误时，再添加规则。在理解自己的模式之前，不要过度优化。

把规则提交到 git，让整个团队受益。当你看到 Agent 犯错时，就更新规则。你甚至可以在 GitHub issue 或 PR 上标记 `@cursor`，让 Agent 为你更新这条规则。

## 规则文件格式

每条规则都是带有 frontmatter 元数据和正文的 markdown 文件。frontmatter 元数据用于控制规则如何应用。正文就是规则本身。

```markdown
---
description: "This rule provides standards for frontend components and API validation"
alwaysApply: false
---

...rest of the rule content
```

如果 alwaysApply 为 true，该规则会应用于每一次聊天会话。否则，规则的 description 会交给 Cursor Agent，由它决定是否应用。

## 示例

### 前端组件与 API 校验的规范

此规则为前端组件提供规范：

在 components 目录中工作时：

- 样式始终使用 Tailwind
- 动画使用 Framer Motion
- 遵循组件命名约定

此规则对 API 端点强制执行校验：

在 API 目录中：

- 所有校验都使用 zod
- 用 zod schema 定义返回类型
- 导出由 schema 生成的类型

### Express 服务与 React 组件的模板

此规则为 Express 服务提供模板：

创建 Express 服务时，阅读 `@express-service-template.ts` 并遵循其结构：

- 遵循 RESTful 原则
- 包含错误处理中间件
- 设置适当的日志记录

此规则定义 React 组件的结构：

React 组件应遵循 `@component-template.tsx` 中的布局：

- Props 接口放在顶部
- 组件作为命名导出
- 样式放在底部

### 自动化开发工作流与文档生成

此规则自动化应用分析：

当被要求分析该应用时：

1. 用 `npm run dev` 运行开发服务器
2. 从控制台获取日志
3. 提出性能改进建议

此规则帮助生成文档：

通过以下方式帮助起草文档：

- 提取代码注释
- 分析 README.md
- 生成 markdown 文档

### 在 Cursor 中添加新设置

首先在 `@reactiveStorageTypes.ts` 中创建一个用于开关的属性。

在 `@reactiveStorageService.tsx` 的 `INIT_APPLICATION_USER_PERSISTENT_STORAGE` 中添加默认值。

对于 beta 功能，把开关加到 `@settingsBetaTab.tsx`，否则加到 `@settingsGeneralTab.tsx`。开关可以作为 `<SettingsSubSection>` 添加，用于一般的复选框。查看该文件的其余部分以获取示例。

```jsx
<SettingsSubSection
  label="Your feature name"
  description="Your feature description"
  value={
    vsContext.reactiveStorageService.applicationUserPersistentStorage
      .myNewProperty ?? false
  }
  onChange={(newVal) => {
    vsContext.reactiveStorageService.setApplicationUserPersistentStorage(
      "myNewProperty",
      newVal,
    );
  }}
/>
```

要在应用中使用，请导入 reactiveStorageService 并使用该属性：

```js
const flagIsEnabled =
  vsContext.reactiveStorageService.applicationUserPersistentStorage
    .myNewProperty;
```

提供方和框架也提供示例。社区贡献的规则可以在网上的众包合集和仓库中找到。

## 团队规则

Team 和 [Enterprise](https://cursor.com/docs/enterprise.md) 方案可以从 [Cursor 仪表板](https://cursor.com/dashboard/team-content) 创建规则，并在整个组织中强制执行。管理员可以配置每条规则对团队成员是否为必需。

团队规则与其他规则类型一起生效，并具有优先权，以确保组织标准在所有项目中得到保持。它们让你能够在整个团队中确保一致的编码标准、实践和工作流，而无需每个人单独设置或配置。

### 管理团队规则

团队管理员可以直接从 Cursor 仪表板创建并管理规则：

![团队管理员可以在此添加新规则的空白团队规则仪表板](/docs-static/images/context/rules/team-rules-empty.png)

团队规则创建之后，会自动应用于所有团队成员，并在仪表板中可见：

![显示一条将对所有团队成员强制执行的团队规则的团队规则仪表板](/docs-static/images/context/rules/team-rules-1.png)

### 激活与强制执行

- **Enable this rule immediately**：勾选后，规则在你创建时立即生效。未勾选时，规则会保存为草稿，直到你稍后启用它才会应用。
- **Enforce this rule**：启用后，该规则对所有团队成员为必需，并且不能在 Customize 中禁用。未强制执行时，团队成员可以在 Customize 的 **Team Rules** 下关闭该规则。

默认情况下，未被强制执行的团队规则可以由用户禁用。使用 Enforce this rule 可以防止这种情况。

### 格式以及团队规则如何应用

- **内容**：团队规则是自由格式文本。它们不使用项目规则的文件夹结构。
- **Glob 模式**：团队规则支持用于按文件限定应用范围的 glob 模式。设置了 glob 模式时（例如 `**/*.py`），仅当匹配的文件处于上下文中时，该规则才会应用。没有 glob 模式的规则应用于每一次对话。
- **应用位置**：当团队规则已启用，且未被用户禁用（被强制执行的规则无法禁用）时，它会包含在该团队所有仓库和项目的 Agent (Chat) 模型上下文中。
- **优先级**：规则按此顺序应用：**Team Rules → Project Rules → User Rules**。所有适用的规则会合并；当指引冲突时，更靠前的来源优先。

有些团队把强制执行的规则用作内部合规工作流的一部分。虽然这是受支持的，但 AI 指引不应成为你唯一的安全控制。

## 从仓库导入规则

规则不会被单独导入。要从 GitHub 仓库引入规则，请把它们打包进一个[插件](https://cursor.com/docs/plugins.md)，并通过 Marketplace 发布该插件：在 **Customize** 中用 **From GitHub Repository** 导入仓库（该仓库需要有 `.cursor-plugin/marketplace.json`），或把它添加为[团队 Marketplace](https://cursor.com/docs/plugins.md#add-a-team-marketplace)，然后[安装该插件](https://cursor.com/docs/plugins.md#installing-plugins)。规则会随插件一起到来，并在 Customize 中与你的其他规则一并显示。

## AGENTS.md

`AGENTS.md` 是用于定义 Agent 指令的简单 markdown 文件。对于简单直接的使用场景，把它放在项目根目录，作为 `.cursor/rules` 的替代。

与项目规则不同，`AGENTS.md` 是没有元数据或复杂配置的纯 markdown 文件。它非常适合需要简单、可读的指令，而又不想承担结构化规则开销的项目。

Cursor 支持项目根目录和子目录中的 AGENTS.md。

```markdown
# Project Instructions

## Code Style

- Use TypeScript for all new files
- Prefer functional components in React
- Use snake_case for database columns

## Architecture

- Follow the repository pattern
- Keep business logic in service layers
```

### 改进

### 嵌套 AGENTS.md 支持

子目录中的嵌套 `AGENTS.md` 现已可用。你可以在项目的任意子目录中放置 `AGENTS.md` 文件，在处理该目录或其子目录中的文件时，它们会自动应用。

这样就可以根据你正在处理的代码库区域，对 Agent 指令进行更细粒度的控制：

```bash
project/
  AGENTS.md              # Global instructions
  frontend/
    AGENTS.md            # Frontend-specific instructions
    components/
      AGENTS.md          # Component-specific instructions
  backend/
    AGENTS.md            # Backend-specific instructions
```

来自嵌套 `AGENTS.md` 文件的指令会与父目录的指令合并，更具体的指令优先。

## 用户规则

用户规则是在 **Customize → Rules** 中定义的全局偏好，适用于所有项目。它们由 Agent (Chat) 使用，非常适合设置偏好的沟通风格或编码约定：

```md
Please reply in a concise style. Avoid unnecessary repetition or filler language.
```

## 常见问题

### 为什么我的规则没有被应用？

检查规则类型。对于 `Apply Intelligently`，请确保定义了 description。对于 `Apply to Specific Files`，请确保文件模式与所引用的文件匹配。

### 规则可以引用其他规则或文件吗？

可以。按路径提及文件，例如 `@service-template.ts`，当规则应用且需要该内容时，Agent 会用它的工具读取该文件。文件不会被内联到提示词中，因此如果某些内容必须始终处于上下文中，请把它粘贴到规则正文里。参见[从规则中引用文件](https://cursor.com/docs/rules.md#referencing-files-from-a-rule)。你也可以在聊天中 @ 提及规则，以手动应用它们。

### 可以从聊天中创建规则吗？

可以，你可以让 Agent 为你创建一条新规则。

### 规则会影响 Cursor Tab 或其他 AI 功能吗？

不会。规则不会影响 Cursor Tab 或其他 AI 功能。

## 相关

- [Rules 帮助](https://cursor.com/help/customization/rules.md)

---

## 站点地图

[所有文档页面概览](/llms.txt)
