> 原文：https://cursor.com/docs/mcp

# Model Context Protocol (MCP)

## 什么是 MCP？

[Model Context Protocol (MCP)](https://modelcontextprotocol.io/introduction) 让 Cursor 能够连接到外部工具和数据源。从 [Customize](https://cursor.com/docs/customize-cursor.md) 页面安装并管理 MCP 服务器，或在 `mcp.json` 中配置它们。

### 为什么使用 MCP？

MCP 把 Cursor 连接到外部系统和数据。不必反复解释你的项目结构，可以直接与你的工具集成。

可以用任何能够打印到 `stdout` 或提供 HTTP 端点的语言来编写 MCP 服务器，例如 Python、JavaScript、Go 等。

在 [Cursor Marketplace](/marketplace) 浏览官方插件。社区插件和 MCP 服务器可在 [cursor.directory](https://cursor.directory) 浏览。

### 它如何工作

MCP 服务器通过该协议暴露能力，从而把 Cursor 连接到外部工具或数据源。

Cursor 支持三种传输方式：

| 传输                  | 执行环境     | 部署         | 用户     | 输入             | 认证   |
| :-------------------- | :----------- | :----------- | :------- | :--------------- | :----- |
| **`stdio`**           | 本地         | Cursor 管理  | 单用户   | shell 命令       | 手动   |
| **`SSE`**             | 本地/远程    | 部署为服务器 | 多用户   | SSE 端点的 URL   | OAuth  |
| **`Streamable HTTP`** | 本地/远程    | 部署为服务器 | 多用户   | HTTP 端点的 URL  | OAuth  |

### 协议与扩展支持

Cursor 支持这些 MCP 协议能力与扩展：

| 功能                 | 支持   | 说明                                       |
| :------------------- | :----- | :----------------------------------------- |
| **Tools**            | 已支持 | 供 AI 模型执行的函数                       |
| **Prompts**          | 已支持 | 面向用户的模板化消息和工作流               |
| **Resources**        | 已支持 | 可以读取和引用的结构化数据源               |
| **Roots**            | 已支持 | 由服务器发起的、针对 URI 或文件系统边界的探查 |
| **Elicitation**      | 已支持 | 由服务器发起的、向用户索取额外信息的请求   |
| **Apps (extension)** | 已支持 | 由 MCP 工具返回的交互式 UI 视图            |

### MCP 应用

Cursor 支持 [MCP Apps 扩展](https://modelcontextprotocol.io/extensions/apps/overview)。MCP 工具可以连同标准工具输出一起返回交互式 UI。

MCP Apps 遵循渐进增强。如果宿主无法渲染应用 UI，同一工具仍可通过普通的 MCP 响应工作。

## 安装 MCP 服务器

### 一键安装

在 [Cursor Marketplace](/marketplace) 浏览官方插件，并从 **Customize** 一键安装，或用 `mcp.json` 配置自定义服务器。社区插件和 MCP 服务器可在 [cursor.directory](https://cursor.directory) 浏览。在 Marketplace 条目上点击 "Add to Cursor"，即可安装并通过 OAuth 进行身份验证。

团队管理员也可以通过[团队 Marketplace](https://cursor.com/docs/plugins.md#team-marketplaces)分发 MCP 服务器。由团队分发的服务器会在 Customize 中与个人和工作区的 MCP 服务器一起显示。

### 使用 `mcp.json`

用 JSON 文件配置自定义 MCP 服务器：

```json title="CLI Server - Node.js"
{
  "mcpServers": {
    "server-name": {
      "command": "npx",
      "args": ["-y", "mcp-server"],
      "env": {
        "API_KEY": "value"
      }
    }
  }
}
```

```json title="CLI Server - Python"
{
  "mcpServers": {
    "server-name": {
      "command": "python",
      "args": ["mcp-server.py"],
      "env": {
        "API_KEY": "value"
      }
    }
  }
}
```

```json title="Remote Server"
// MCP server using HTTP or SSE - runs on a server
{
  "mcpServers": {
    "server-name": {
      "url": "http://localhost:3000/mcp",
      "headers": {
        "API_KEY": "value"
      }
    }
  }
}
```

### 远程服务器的静态 OAuth

对于使用 OAuth 的 MCP 服务器，你可以在 `mcp.json` 中提供**静态 OAuth 客户端凭据**，以代替动态客户端注册。在以下情况下使用：

- MCP 提供方给你一个固定的 **Client ID**（以及可选的 **Client Secret**）
- 提供方要求**把重定向 URL 加入白名单**（例如 Figma、Linear）
- 提供方不支持 OAuth 2.0 Dynamic Client Registration

向使用 `url` 的远程服务器条目添加 `auth` 对象：

```json title="Remote Server with Static OAuth"
{
  "mcpServers": {
    "oauth-server": {
      "url": "https://api.example.com/mcp",
      "auth": {
        "CLIENT_ID": "your-oauth-client-id",
        "CLIENT_SECRET": "your-client-secret",
        "scopes": ["read", "write"]
      }
    }
  }
}
```

| 字段               | 必填 | 说明                                                                                                                   |
| :----------------- | :--- | :--------------------------------------------------------------------------------------------------------------------- |
| **CLIENT\_ID**     | 是   | 来自 MCP 提供方的 OAuth 2.0 Client ID                                                                                  |
| **CLIENT\_SECRET** | 否   | OAuth 2.0 Client Secret（如果提供方使用机密客户端）                                                                    |
| **scopes**         | 否   | 要请求的 OAuth scope。如果省略，Cursor 会使用 `/.well-known/oauth-authorization-server` 来发现 `scopes_supported`     |

#### 静态重定向 URL

Cursor 为 MCP 服务器使用固定的 OAuth 重定向 URL。请为用户进行身份验证的每个界面注册回调：

```text
https://www.cursor.com/agents/mcp/oauth/callback
http://localhost:8787/callback
```

- **Web 与 Cursor Agents**：`https://www.cursor.com/agents/mcp/oauth/callback`
- **桌面应用**：`http://localhost:8787/callback`

配置 MCP 提供方的 OAuth 应用时，如果用户会从 Web 和桌面两端进行身份验证，请把这两个 URL 都注册为允许的重定向 URI。服务器通过 OAuth 的 `state` 参数来识别，因此这些重定向 URL 适用于所有 MCP 服务器。

使用动态客户端注册时，桌面应用会注册这两个 URL，并且可以把 Web 回调发给提供方。该页面随后把登录交还给桌面应用。若要让 OAuth 应用只允许桌面应用的重定向 URL，请在 `auth` 中设置一个静态 `CLIENT_ID`。这样桌面应用只会把该 URL 发给提供方。

#### 与配置插值结合

`auth` 的值支持与其他字段相同的插值：

```json
{
  "mcpServers": {
    "oauth-server": {
      "url": "https://api.example.com/mcp",
      "auth": {
        "CLIENT_ID": "${env:MCP_CLIENT_ID}",
        "CLIENT_SECRET": "${env:MCP_CLIENT_SECRET}"
      }
    }
  }
}
```

请用环境变量来提供 Client ID 和 Client Secret，而不要把它们硬编码进去。

### STDIO 服务器配置

对于 STDIO 服务器（本地命令行服务器），在 `mcp.json` 中配置这些字段：

| 字段        | 必填 | 说明                                                                                             | 示例                                  |
| :---------- | :--- | :----------------------------------------------------------------------------------------------- | :------------------------------------ |
| **type**    | 是   | 服务器连接类型                                                                                   | `"stdio"`                             |
| **command** | 是   | 用于启动服务器可执行文件的命令。它必须位于系统路径上，或者包含完整路径。                         | `"npx"`, `"node"`, `"python"`, `"docker"` |
| **args**    | 否   | 传给该命令的参数数组                                                                             | `["server.py", "--port", "3000"]`     |
| **env**     | 否   | 该服务器的环境变量                                                                               | `{"API_KEY": "${env:api-key}"}`       |
| **envFile** | 否   | 用于加载更多变量的环境文件路径                                                                   | `".env"`, `"${workspaceFolder}/.env"` |

`envFile` 选项仅适用于 STDIO 服务器。远程服务器（HTTP/SSE）不支持 `envFile`。对于远程服务器，请改用[配置插值](https://cursor.com/docs/mcp.md#config-interpolation)，并把环境变量设置在你的 shell 配置文件或系统环境中。

### 使用 Extension API

若要以编程方式注册 MCP 服务器，Cursor 提供了 Extension API，无需修改 `mcp.json` 文件即可动态配置。这对企业环境和自动化的设置工作流特别有用。

### Extension API 参考

使用 `vscode.cursor.mcp.registerServer()` 以编程方式注册 MCP 服务器

### 配置位置

### 项目配置

在项目中创建 `.cursor/mcp.json`，用于项目特定的工具。

### 全局配置

在主目录中创建 `~/.cursor/mcp.json`，用于在各处可用的工具。

### 配置插值

在 `mcp.json` 的值中使用变量。Cursor 会解析这些字段中的变量：`command`、`args`、`env`、`url` 和 `headers`。

支持的语法：

- `${env:NAME}` 环境变量
- `${userHome}` 你的主文件夹路径
- `${workspaceFolder}` 项目根目录（包含 `.cursor/mcp.json` 的文件夹）
- `${workspaceFolderBasename}` 项目根目录的名称
- `${pathSeparator}` 和 `${/}` 操作系统的路径分隔符

示例

```json
{
  "mcpServers": {
    "local-server": {
      "command": "python",
      "args": ["${workspaceFolder}/tools/mcp_server.py"],
      "env": {
        "API_KEY": "${env:API_KEY}"
      }
    }
  }
}
```

```json
{
  "mcpServers": {
    "remote-server": {
      "url": "https://api.example.com/mcp",
      "headers": {
        "Authorization": "Bearer ${env:MY_SERVICE_TOKEN}"
      }
    }
  }
}
```

### 身份验证

MCP 服务器使用环境变量进行身份验证。通过配置传递 API key 和 token。

Cursor 对需要 OAuth 的服务器支持 OAuth。登录使用带 PKCE 的授权码流程。Cursor 读取服务器的受保护资源元数据（RFC 9728），以找到其授权服务器和资源指示符（RFC 8707）。在动态客户端注册中，Cursor 会注册为一个没有客户端密钥的公共客户端。

Cursor 用服务器签发的刷新 token 来续期过期的访问 token。如果服务器返回的访问 token 带有过期时间（`expires_in`）但没有刷新 token，则 token 过期时你需要重新登录。如果你的提供方只对某个 scope（例如 `offline_access`）签发刷新 token，请把该 scope 加到 [`auth.scopes`](https://cursor.com/docs/mcp.md#static-oauth-for-remote-servers)。

## Enterprise 管理控制

MCP 的分发与 MCP 策略是分开配置的。团队管理员可以分发共享的 MCP 服务器。Enterprise 管理员可以配置 MCP 策略。

### Team MCP 分发

在 **Dashboard > Plugins & MCPs** 下配置共享的 Team MCP 服务器。这些服务器可供 Cloud Agents 使用。

Team MCP 服务器的配置，以及团队插件的变量，由整个团队共享。从任何界面编辑它们，包括某个自动化的 MCP 设置，都会改变每一个使用它们的队友和自动化所看到的值。只有团队管理员可以编辑 Team MCP 服务器，或替换团队插件已有的凭据。组织管理员对其 [组织](https://cursor.com/docs/enterprise/organizations.md) 所关联的每个团队都有相同的访问权限，即使没有团队管理员角色。

要使现有的独立 Team MCP 服务器可在 Agent Window、IDE 和 CLI 中使用，请在 **Team MCP Servers** 下选择 **Add to Team Marketplace**。Cursor 会把该服务器关联到默认团队 Marketplace，且不会中断对 Cloud Agent 的访问。队友随后可以从 Customize 安装并配置它。

把 MCP 服务器关联到 Marketplace 并不会为所有人安装或启用它。在 **Dashboard > Plugins & MCPs** 下配置 **Marketplace Access** 和插件安装模式。完整流程见[迁移现有的 Team MCP](https://cursor.com/docs/plugins.md#migrate-existing-team-mcps)。

### MCP Allowlist

Enterprise 管理员可以在 Cursor 仪表板中控制用户可以运行哪些 MCP 服务器。打开 [Team Settings > MCP Configuration](https://cursor.com/dashboard/team-settings?view=mcp-configuration) 来配置团队可以运行哪些服务器和工具。Plugins & MCPs 页面也会链接到此处。加入允许列表是批准一项 MCP 配置。它不会分发或安装该服务器。

使用 MCP Allowlist 来定义已批准的服务器：

- **Command entries** 按命令模式批准本地 `stdio` MCP 服务器。
- **URL entries** 按 URL 条目模式批准远程 HTTP/SSE MCP 服务器。
- **Tool allowlists** 限制已批准服务器中的哪些工具可以自动运行。把工具允许列表留空，即允许该服务器的全部工具。

### 网络控制

远程 MCP URL 被限制在已配置的 URL 条目模式之内。

基于本地命令的 MCP 服务器使用各自的网络模式：

- **Allow all**：允许出站网络访问。
- **Allowlist**：只允许列出的目的地。
- **Deny all**：阻止出站网络访问。
- **No sandbox**：在没有命令沙箱和网络沙箱的情况下运行。

### 用户 MCP 扩展

管理员可以允许用户在管理员所定义的命令或 URL 模式之外配置自己的 MCP 服务器。对于不匹配管理员所定义模式的用户 MCP，User MCP Network Denylist 可以阻止匹配的网络目的地。

## 在聊天中使用 MCP

在相关时，Cursor 会自动使用列在 `Available Tools` 下的 MCP 工具。这也包括 [Plan Mode](https://cursor.com/docs/agent/plan-mode.md)。你可以按名称指定某个工具，或描述你的需求。在侧边栏的 **Customize** 中启用或禁用 MCP 服务器。

### 工具批准

默认情况下，Cursor 在使用 MCP 工具前会请求批准。点击工具名称旁边的箭头即可查看参数。

![工具确认提示](/docs-static/images/context/mcp/tool-confirm.png)

#### Run Mode

MCP [遵循与终端命令相同的 Run Modes](https://cursor.com/docs/agent/security/run-modes.md#run-mode)。例如，在 **Auto-review** 模式下，位于允许列表中的 MCP 工具会立即运行，其余一切都会经过分类器。

### 工具响应

Cursor 会在聊天中显示响应，并提供可展开的参数视图和响应视图：

![MCP 工具调用结果](/docs-static/images/context/mcp/tool-call.png)

### 作为上下文的图像

MCP 服务器可以返回图像，例如截图、示意图等。请把它们作为 base64 编码的字符串返回：

```js
const RED_CIRCLE_BASE64 = "/9j/4AAQSkZJRgABAgEASABIAAD/2w...";
// ^ full base64 clipped for readability

server.tool("generate_image", async (params) => {
  return {
    content: [
      {
        type: "image",
        data: RED_CIRCLE_BASE64,
        mimeType: "image/jpeg",
      },
    ],
  };
});
```

实现细节见这个[示例服务器](https://github.com/msfeldstein/mcp-test-servers/blob/main/src/image-server.js)。Cursor 会把返回的图像附加到聊天中。如果模型支持图像，它会分析这些图像。

## 安全注意事项

安装 MCP 服务器时，请考虑这些安全实践：

- **核实来源**：只安装来自可信开发者和仓库的 MCP 服务器
- **审查权限**：检查服务器将访问哪些数据和 API
- **限制 API key**：使用权限尽可能少的受限 API key
- **审计代码**：对于关键集成，审查服务器的源代码

请记住，MCP 服务器可以访问外部服务，并代表你执行代码。安装之前，请务必了解服务器会做什么。

## 实际示例

以下是 MCP 的实际示例：

- **[Xcode 集成](https://cursor.com/docs/integrations/xcode.md)** — 把 Cursor 连接到 Xcode 26.3+，用于构建、测试、SwiftUI 预览和 Apple 文档搜索
- **[Web 开发指南](https://cursor.com/for/web-development.md)** — 把 Linear、Figma 和浏览器工具集成到你的开发工作流中

## 常见问题

### MCP 服务器有什么用？

MCP 服务器把 Cursor 连接到 Google Drive、Notion 和其他服务等外部工具，从而把文档和需求带入你的编码工作流。

### 如何调试 MCP 服务器的问题？

按以下方式查看 MCP 日志：

1. 在 Cursor 中打开 Output 面板（Cmd+Shift+U）
2. 从下拉菜单中选择 "MCP Logs"
3. 检查连接错误、身份验证问题或服务器崩溃

日志会显示服务器初始化、工具调用和错误信息。

### 为什么我在 Cloud Agents 中的 MCP 登录不一样？

Cloud Agents 在 Cursor 的服务器上保留自己的 MCP 登录状态，与你机器上的登录是分开的。在本地登出并不会让 Cloud Agents 登出。要为 Cloud Agents 登出，请打开 **Customize**，并在该服务器的 **Cloud** 行上登出；对某些服务器，这一行会标为 **Cursor's servers**。

### 可以暂时禁用某个 MCP 服务器吗？

可以！无需移除即可打开或关闭服务器：

1. 在侧边栏中打开 **Customize**
2. 找到你想更改的 MCP 服务器
3. 使用开关启用或禁用它

已禁用的服务器不会加载，也不会出现在聊天中。这有助于排查问题，或减少工具过于杂乱的情况。

### 如果 MCP 服务器崩溃或超时会怎样？

如果某个 MCP 服务器失败：

- Cursor 会在聊天中显示错误信息
- 该工具调用会被标记为失败
- 你可以重试该操作，或查看日志以了解细节
- 其他 MCP 服务器会继续正常工作

Cursor 会隔离服务器故障，以防止一个服务器影响其他服务器。

### 如何更新 MCP 服务器？

对于基于 npm 的服务器：

1. 从 **Customize** 中移除该服务器
2. 清除 npm 缓存：`npm cache clean --force`
3. 重新添加该服务器以获取最新版本

对于自定义服务器，更新你的本地文件并重启 Cursor。

### 可以将 MCP 服务器用于敏感数据吗？

可以，但请遵循安全最佳实践：

- 用环境变量存放密钥，切勿硬编码
- 使用 `stdio` 传输在本地运行敏感服务器
- 把 API key 的权限限制到最低必要范围
- 在连接到敏感系统之前审查服务器代码
- 考虑在隔离环境中运行服务器

## 相关

- [MCP 帮助](https://cursor.com/help/customization/mcp.md)

---

## 站点地图

[所有文档页面概览](/llms.txt)
