> 原文：https://cursor.com/docs/plugins

# Plugins

Plugins 把 Rules、Skills、Agents、Commands、MCP 服务器和 Hooks 打包成可分发的捆绑包。

Cursor 在支持自身插件格式的同时，也支持 [Agent Plugins](https://agent-plugins.org) 开放标准。你可以在 [Customize](https://cursor.com/docs/customize-cursor.md) 页面安装并管理它们，或在 [Cursor Marketplace](/marketplace) 浏览官方插件。社区插件和 MCP 服务器可在 [cursor.directory](https://cursor.directory) 浏览。你也可以[自行构建](https://cursor.com/docs/plugins.md#creating-plugins)，与其他开发者分享。

## Plugins 包含什么

一个插件可以任意组合捆绑以下组件：

| 组件            | 适用于         | 说明                                                       |
| :-------------- | :------------- | :--------------------------------------------------------- |
| **Rules**       | Cursor Plugins | 持久的 AI 指引和编码规范（`.mdc` 文件）                    |
| **Skills**      | 两种格式       | 面向复杂任务的专门 Agent 能力                              |
| **Agents**      | Cursor Plugins | 自定义 Agent 配置和提示词                                  |
| **Commands**    | Cursor Plugins | Agent 可执行的命令文件                                     |
| **MCP Servers** | 两种格式       | Model Context Protocol 集成                                |
| **Hooks**       | Cursor Plugins | 由事件触发的自动化脚本                                     |

## Agent Plugins 标准

Plugins 把 Agent 可以使用的可复用组件捆绑在一起。[Agent Plugins](https://agent-plugins.org) 是打包可移植 Skills 和 MCP 服务器的开放标准，就像 [Agent Skills](https://cursor.com/docs/skills.md) 为单个 Skill 定义了标准一样。Cursor 同时支持 Agent Plugins 和 Cursor Plugins。

- **Agent Plugins**：符合规范的插件，在插件根目录有 `plugin.json` 清单，用于打包 Skills 和 MCP 服务器
- **Cursor Plugins**：带有 `.cursor-plugin/plugin.json` 清单的插件，可加入 Rules、Agents、Commands、Hooks 和[变量](https://cursor.com/docs/reference/plugins.md#variables)

遵循 Agent Plugins 规范的插件可以在 Cursor 中加载。Cursor 不会在 `mcp.json` 中展开该标准的 `${PLUGIN_ROOT}` 和 `${PLUGIN_DATA}` 变量；插件根目录请使用 `${CURSOR_PLUGIN_ROOT}`。参见参考文档中的 [MCP 服务器](https://cursor.com/docs/reference/plugins.md#mcp-servers)。Cursor Plugins 与该标准并行继续发展，因此 Cursor 专有组件和 Marketplace 功能会保持目前的工作方式。

更多内容见 [agent-plugins.org](https://agent-plugins.org)，或阅读 [GitHub 上的规范](https://github.com/agentplugins/agent-plugins-spec)。

## Cursor Plugin Canvas

Plugins 现在附带预置的 **Canvas**：团队可以打开并复用的共享设置模板。

- **Hex Canvas** — 构建数据可视化。在 Cursor，我们用 Hex Canvas 探索并分享分析数据。
- **Atlassian Canvas** — 实时查看来自 Jira 和 Confluence 的议题、项目和文档。

在 Customize 中从已安装的插件打开 Canvas，即可获得一个有引导的起点，而不必从零配置一切。

## Marketplace

[Cursor Marketplace](/marketplace) 是你发现并安装官方 Cursor Plugins 的地方。Plugins 以 Git 仓库的形式分发，并通过 Cursor 团队提交。

每个插件在上架前都会经过[人工审核](https://cursor.com/help/security-and-privacy/marketplace-security.md)。在 [cursor.com/marketplace](https://cursor.com/marketplace) 浏览官方插件，或在 **Customize** 中按关键词搜索。社区插件和 MCP 服务器可在 [cursor.directory](https://cursor.directory) 浏览。

## 团队 Marketplace

团队 Marketplace 在 Teams 和 Enterprise 方案中可用。它们可以通过同一个 Marketplace 分发 Agent Plugins 和 Cursor Plugins。

- Teams 方案：最多 1 个团队 Marketplace
- Enterprise 方案：不限数量的团队 Marketplace

在 Teams 方案中，[默认团队 Marketplace](https://cursor.com/docs/plugins.md#default-team-marketplace) 不计入已导入 Marketplace 的数量上限，因此团队还可以再导入一个团队 Marketplace。

[联系销售](https://cursor.com/contact-sales?source=docs-plugins)以获取不限数量的团队 Marketplace 和 Enterprise 管理控制。

打开 **Dashboard -> Plugins & MCPs** 以管理 Team Marketplaces。

在 Enterprise 方案中，只有管理员可以从 **Dashboard -> Plugins & MCPs** 添加团队 Marketplace。

### 默认团队 Marketplace

**Default** 团队 Marketplace 在 Cursor 中连接共享的 Plugins 和 MCP 服务器。管理员可以添加已经可供 Cloud Agents 使用的 Team MCP 服务器，然后让队友在 Agent Window、IDE 和 CLI 中安装并配置同一批服务器。

Default Marketplace 不能重命名或删除。如果旧版 Default Marketplace 使用了不同的名称，它仍保留 Default 角色。

把 Team MCP 服务器添加到 Default Marketplace，并不会为每位开发者安装或启用它。管理员仍然控制 Marketplace 访问权限和插件安装模式。每位开发者可能还需要向 MCP 提供方进行身份验证。

### 迁移现有的 Team MCP

管理员可以把独立的 Team MCP 服务器关联到 Default Marketplace：

1. 打开 **Dashboard -> Plugins & MCPs**。
2. 找到 **Team MCP Servers**。
3. 在迁移提示中选择 **Add to Team Marketplace**。
4. 在同一页面查看 Default Marketplace、其访问权限和插件安装模式。

Cursor 会在需要时创建 Default Marketplace，并把现有 MCP 服务器关联到它。这些服务器对 Cloud Agents 仍然可用，同时队友可以选择在本地安装并配置它们。

从 Marketplace 中移除已关联的 MCP 插件，或删除该 Marketplace，可能会删除 Team MCP 服务器。这会把它从本地用户和 Cloud Agents 中移除。继续之前请查看确认信息。

### Marketplace 访问权限

团队 Marketplace 默认对其团队中的所有人可用。在 **Marketplace Settings -> Marketplace Access** 下，管理员可以把某个 Marketplace 限制为选定的 [Organization Groups](https://cursor.com/docs/enterprise/organization-groups.md)。只有该 Marketplace 所属团队中、且属于所选组的成员才能获得访问权限。团队管理员仍保留访问权限。

### SCIM 如何工作？

Organization Groups 可以通过 [SCIM](https://cursor.com/docs/account/teams/scim.md) 从你的身份提供方同步成员关系。在身份提供方中管理成员，Cursor 会把这些更新同步到 Organization Group。

使用团队级 SCIM 目录组的现有 Marketplace 会保留该配置。Cursor 不会自动迁移这些分配。没有 Organization Groups 的组织会继续使用 SCIM 目录组。

### 插件安装模式

设置 Marketplace 访问权限之后，选择每个插件如何分发给该受众：

- **Default Off**：开发者可以找到该插件，并选择是否安装。
- **Default On**：插件默认安装，但开发者可以选择不使用。
- **Required**：插件始终安装，且不能卸载。

### 从插件的 Marketplace 页面添加

团队管理员可以从 [Cursor Marketplace](/marketplace) 中该插件的页面，把插件添加到团队 Marketplace：

1. 打开插件页面并点击 **Add to Cursor**。
2. 选择 **Add to Team**。
3. 在 **Available Marketplaces** 下，选择要添加到的团队 Marketplace。
4. 在 **Access Settings** 下，将 **Plugin Installation** 设为 **Default Off**、**Default On** 或 **Required**。
5. 点击 **Add Plugin**。

只有当你的团队至少有一个团队 Marketplace 时，才会出现 **Add to Team**。不是团队管理员的成员只能为自己添加该插件。

### 允许成员发布

在 **Default** Marketplace 上，管理员控制成员是否可以发布个人 Skills：

1. 打开 [Dashboard → Plugins & MCPs](https://cursor.com/dashboard/plugins)。
2. 打开 **Default** 团队 Marketplace，然后打开 **Marketplace Settings**。
3. 打开或关闭 **Allow Members to Publish**。

该设置默认开启。关闭后，只有团队管理员可以发布新的 Skills。成员已经发布的 Skills 仍然可用，这些作者仍可以更新或取消发布它们。

参见[向团队发布 Skill](https://cursor.com/docs/plugins.md#publish-a-skill-to-your-team)。

### 从 Cursor 提供 Marketplace

对于从 GitHub 导入的团队 Marketplace，管理员可以在 **Marketplace Settings** 下打开 **Serve marketplace from Cursor**。Cursor 会保留该仓库的一份同步副本，并从中提供 Plugins，因此团队成员无需具备源仓库的 GitHub 访问权限即可使用该 Marketplace。访问权限由 [Marketplace 访问权限](https://cursor.com/docs/plugins.md#marketplace-access) 控制。

首次同步进行期间，该设置显示 **Syncing** 并保持锁定。如果仓库同步无法完成，访问会继续从 GitHub 提供。

此设置适用于从 GitHub 导入的 Marketplace。从 GitLab、Bitbucket 或 Azure DevOps 导入的 Marketplace 继续从源仓库提供。

## 添加团队 Marketplace

使用此流程把仓库导入为团队 Marketplace。**Import from Repo** 接受 GitHub、GitLab、Bitbucket 和 Azure DevOps 的 URL。如果 Cursor 要求，请连接该提供方。

1. 前往 **Dashboard -> Plugins & MCPs**。
2. 在 **Team Marketplaces** 中，点击 **Add Marketplace**。
3. 按照说明从头创建 Marketplace，或使用 **Import from Repo** 并粘贴仓库 URL。
4. 使用 "Add to Marketplace" 添加并审查插件。
5. 在 **Marketplace Settings** 下设置 **Marketplace Access**，可选启用 Auto Refresh，然后保存。

可以试用的示例仓库：

- [fieldsphere/cursor-team-marketplace-template](https://github.com/fieldsphere/cursor-team-marketplace-template)

## 让 Plugins 保持最新

首次导入仓库时，Plugins 会被索引。你可以用两种方式刷新它们：

- **自动**：对于 GitHub 导入，打开 **Enable Auto Refresh**，以便每当有更改推送到该 Marketplace 所跟踪的分支时更新 Plugins。这要求该仓库安装了 [Cursor GitHub App](https://cursor.com/docs/integrations/github.md)。Cursor 对一个 Marketplace 重新索引最多每 10 分钟一次，并把密集推送合并到最新的 commit。
- **手动**：点击 **Refresh**，从仓库更新。

正在运行的 Cloud Agent，包括 [Project](https://cursor.com/docs/agent/projects.md) 中的每个 Agent，都会保留它启动时所用的插件版本。要获取更新，请启动一个新的 Agent。

对于用 "Import from Repo" 创建的 Marketplace，每次推送时 Auto Refresh 都会重新读取完整清单，因此添加到仓库中的新插件会被自动收录。

对于插件是逐个添加的 Marketplace，Auto Refresh 只更新已有插件。重新导入仓库 URL，以收录新添加的插件。

## 开发者在哪里找到团队 Marketplace

开发者可以在 Customize 中找到团队 Marketplace。

- 在侧边栏中打开 **Customize**
- 查找来自你的团队 Marketplace 的插件。
- 直接从该面板安装 Default Off 插件。
- Default On 插件会自动安装，但开发者可以选择不使用。
- Required 插件会自动安装，且不能卸载。
- 安装并配置 Marketplace 的 MCP 服务器，以便在 Agent Window、IDE 和 CLI 中使用。

## 向团队发布 Skill

在 Teams 和 Enterprise 方案中，成员可以把 `~/.cursor/skills/` 中的个人 Skill 发布到团队的 **Default** Marketplace。

1. 打开 **Customize** 并前往 **Skills**。
2. 打开你想分享的个人 Skill。
3. 选择 **Publish** 并确认。

Cursor 会把该 Skill 打包成一个插件，在为你的团队托管的仓库中保存一份副本，并把它添加到 Default Marketplace。此后该 Skill 从该插件加载，而不是从你的本地文件夹加载。

发布之后：

- **安装需要主动选择。** 队友在团队 Marketplace 中找到该 Skill 并自行安装。发布不会为其他任何人安装它。作者会自动获得它。
- **作者保持控制。** 发布会把该 Skill 从 `~/.cursor/skills/` 移到你机器上该插件的安装文件夹。要更新它，从 **Customize → Skills** 打开该 Skill，在那里编辑，然后从该 Skill 的菜单选择 **Sync changes**，或从打开的 `SKILL.md` 上的 **Published** 菜单选择 **Sync**。**Unpublish** 会把该 Skill 移回你的机器。
- **一个 Skill，一个插件。** 插件以该 Skill 命名。发布不会捆绑该已发布 Skill 所引用的其他 Skills。

访问权限遵循你现有的 [Marketplace 访问权限](https://cursor.com/docs/plugins.md#marketplace-access) 设置。管理员可以用 [Allow Members to Publish](https://cursor.com/docs/plugins.md#allow-members-to-publish) 关闭成员发布。

若要在不分享的情况下把个人 Skills 用于你自己的 Cloud Agents，请改为[同步它们](https://cursor.com/docs/skills.md#use-personal-skills-with-cloud-agents)。

## 安装插件

从 Marketplace 安装 Agent Plugins 和 Cursor Plugins。Cursor 根据插件清单检测格式，因此两者的安装流程相同：

1. 在侧边栏中打开 **Customize**。
2. 找到你想使用的插件。
3. 选择 **Install**，并选择项目或用户作用域。

Agent Plugin 在其根目录有 `plugin.json` 清单。Cursor Plugin 有 `.cursor-plugin/plugin.json` 清单。团队 Marketplace 可以用上文所述的相同访问权限和安装模式分发任一格式。

你也可以从 `~/.cursor/plugins/local` 开发任一格式。文件夹布局以及 Cursor 何时加载它，参见[在本地测试插件](https://cursor.com/docs/plugins.md#test-plugins-locally)。

### MCP Apps 深链接

使用安装链接分享 MCP 服务器配置：

```text
cursor://anysphere.cursor-deeplink/mcp/install?name=$NAME&config=$BASE64_ENCODED_CONFIG
```

生成这些链接的细节，见 [MCP 安装链接](https://cursor.com/docs/mcp/install-links.md)。

## 管理已安装的插件

在侧边栏中打开 **Customize**，即可在一个页面中管理已安装的 Agent Plugins、Cursor Plugins、MCP 服务器、Rules 和 Skills。按用户、工作区或团队作用域筛选，查看已安装的内容。

### MCP 服务器

在 Customize 中打开或关闭个人的以及团队分发的 MCP 服务器：

1. 在侧边栏中打开 **Customize**
2. 找到你想更改的 MCP 服务器
3. 使用开关启用或禁用它

已禁用的服务器不会加载，也不会出现在聊天中。

### Rules 和 Skills

在 Customize 中管理 Rules 和 Skills。在 **Always**、**Agent Decides** 和 **Manual** 模式之间切换单条规则。Skills 出现在 **Agent Decides** 部分，可以在聊天中用 `/skill-name` 手动调用。

## 使用 workspaceOpen hook

`workspaceOpen` hook 可以返回要在工作区打开时加载的插件路径。当插件集合取决于工作区本身时，这很有用。

### Hooks 参考

从 `workspaceOpen` hook 脚本注册插件路径

## 创建插件

插件是包含清单及其组件的目录。当你想打包可移植的 Skills 和 MCP 服务器时，选择 Agent Plugins。当你还需要 Rules、Agents、Commands、Hooks 或变量等 Cursor 专有组件时，选择 Cursor Plugins。

### Agent Plugin

```text
my-plugin/
├── plugin.json
├── skills/
│   └── code-reviewer/
│       └── SKILL.md
└── mcp.json
```

Agent Plugins 需要根目录的 `plugin.json`，并带有该标准的 schema 标识符：

```json
{
  "$schema": "https://agent-plugins.org/schemas/1.0.0/plugin.schema.json",
  "name": "my-plugin",
  "description": "Portable code review tools",
  "version": "1.0.0",
  "author": { "name": "Your Name" }
}
```

### Cursor Plugin

```text
my-plugin/
├── .cursor-plugin/
│   └── plugin.json
├── rules/
│   └── coding-standards.mdc
├── skills/
│   └── code-reviewer/
│       └── SKILL.md
└── mcp.json
```

Cursor Plugin 清单只需要 `name`。组件从其默认目录中被发现，你也可以在清单中指定自定义路径。

```json
{
  "name": "my-plugin",
  "description": "Custom development tools",
  "version": "1.0.0",
  "author": { "name": "Your Name" }
}
```

从 [Cursor Plugin 模板仓库](https://github.com/cursor/plugin-template)开始，或阅读 [Agent Plugins 编写指南](https://agent-plugins.org/plugin-authors)来创建 Agent Plugin。

### 在本地测试插件

发布之前，把任一插件格式放到 `~/.cursor/plugins/local`：

1. 为你的插件创建一个文件夹：
   `~/.cursor/plugins/local/my-plugin`
2. 把插件文件复制到该文件夹。Agent Plugin 需包含根目录的 `plugin.json`，Cursor Plugin 需包含 `.cursor-plugin/plugin.json`。
3. 重启 Cursor，或运行 **Developer: Reload Window**。
4. 打开 **Customize**，确认你期望的插件组件，例如 Rules、Skills 或 MCP 服务器。

重新加载后，如果允许本地插件导入，Cursor 会发现此文件夹中的插件。

在 Enterprise 中，管理员通过 **Dashboard -> Settings -> Security & Identity -> Marketplace and Plugins** 下的 **Allow Local Plugin Imports** 控制此项。该设置在 Enterprise 上默认关闭，它也控制 **Customize** 中的 **Import from Disk**。第一方插件允许列表位于同一个仅限 Enterprise 的部分。如果已经安装了同名的 Marketplace 插件，则该安装优先于本地副本。

`~/.cursor/plugins/local` 中的符号链接只有在目标解析为该文件夹内的目录时才会加载。如果符号链接指向磁盘上其他位置的插件仓库，Cursor 会跳过它。

插件准备好后，在 [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish) 提交审核。Cursor Plugins 可以使用 `.cursor-plugin/marketplace.json` 支持多插件仓库。

完整的清单 schema、组件格式和提交检查清单，参见 [Plugins 参考](https://cursor.com/docs/reference/plugins.md)。

### 团队与 Enterprise Marketplace

升级以获得私有团队 Marketplace 和组织范围的插件分发。

## 常见问题

### Marketplace 插件会经过安全审核吗？

会。每个插件在上架前都会经过人工审核。所有插件必须开源，并且我们会在发布前审核每次更新。审查、更新审核以及如何报告问题的细节，见 [Marketplace 安全](https://cursor.com/help/security-and-privacy/marketplace-security.md)。

### 如何创建插件？

对于可移植的 Agent Plugin，添加根目录 `plugin.json` 清单，并打包 Skills 或 MCP 服务器。对于 Cursor Plugin，添加 `.cursor-plugin/plugin.json` 清单，以及你需要的任何 Cursor 组件。两种格式的示例见 [Plugins 参考](https://cursor.com/docs/reference/plugins.md)。

### Cursor Plugins 与 Agent Plugins 标准是什么关系？

[Agent Plugins](https://agent-plugins.org) 是一项开放、与供应商无关的规范，用于把 Skills 和 MCP 服务器打包成可移植的插件。Cursor 支持该标准，因此符合规范的插件可以在 Cursor 中加载。Cursor 不会在 `mcp.json` 中展开该标准的 `${PLUGIN_ROOT}` 和 `${PLUGIN_DATA}` 变量；插件根目录请使用 `${CURSOR_PLUGIN_ROOT}`。Cursor Plugins 并行开发，并增加 Rules、Agents、Commands、Hooks 和变量等 Cursor 专有组件。

### 如何把个人 Skill 发布给我的团队？

打开 **Customize → Skills**，打开 `~/.cursor/skills/` 中的个人 Skill，并选择 **Publish**。Cursor 会把它添加到你团队的 Default Marketplace。参见[向团队发布 Skill](https://cursor.com/docs/plugins.md#publish-a-skill-to-your-team)。

### 发布 Skill 会为所有人安装它吗？

不会。队友选择是否从 Marketplace 安装它。作者会自动获得它。管理员之后可以把该插件改为 Default On 或 Required。

## 相关

- [Plugins 帮助](https://cursor.com/help/customization/plugins.md)
- [Skills](https://cursor.com/docs/skills.md)

---

## 站点地图

[所有文档页面概览](/llms.txt)
