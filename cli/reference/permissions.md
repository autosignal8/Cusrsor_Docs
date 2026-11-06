> 原文：https://cursor.com/docs/cli/reference/permissions

# 权限

使用 CLI 配置中的权限 token 来配置 Agent 被允许做的事。权限设置在 `~/.cursor/cli-config.json`（全局）或 `<project>/.cursor/cli.json`（项目级）。

## 权限类型

### Shell 命令

**格式：** `Shell(commandBase)`

控制对 shell 命令的访问。`commandBase` 是命令行中的第一个 token。支持 glob 模式，以及可选的 `command:args` 语法以进行更精细的控制。

| 示例            | 说明                                           |
| --------------- | ---------------------------------------------- |
| `Shell(ls)`     | 允许运行 `ls` 命令                             |
| `Shell(git)`    | 允许任意 `git` 子命令                          |
| `Shell(npm)`    | 允许 npm 包管理器命令                          |
| `Shell(curl:*)` | 允许带任意参数的 `curl`                        |
| `Shell(rm)`     | 拒绝破坏性的文件删除（通常放在 `deny` 中）     |

### 文件读取

**格式：** `Read(pathOrGlob)`

控制对文件和目录的读取访问。支持 glob 模式。

| 示例                | 说明                             |
| ------------------- | -------------------------------- |
| `Read(src/**/*.ts)` | 允许读取 `src` 中的 TypeScript 文件 |
| `Read(**/*.md)`     | 允许读取任何位置的 markdown 文件 |
| `Read(.env*)`       | 拒绝读取环境文件                 |
| `Read(/etc/passwd)` | 拒绝读取系统文件                 |

### 文件写入

**格式：** `Write(pathOrGlob)`

控制对文件和目录的写入访问。支持 glob 模式。打印模式可以使用写入和 shell 工具。使用 `permissions.allow`、`permissions.deny` 和 `--force` 控制哪些操作无需提示即可运行。

| 示例                  | 说明                          |
| --------------------- | ----------------------------- |
| `Write(src/**)`       | 允许写入 `src` 下的任意文件   |
| `Write(package.json)` | 允许修改 package.json         |
| `Write(**/*.key)`     | 拒绝写入私钥文件              |
| `Write(**/.env*)`     | 拒绝写入环境文件              |

### Web 获取

**格式：** `WebFetch(domainOrPattern)`

控制 Agent 在使用 web fetch 工具时可以获取哪些域名（例如检索文档或网页）。如果没有 allowlist 条目，每次获取都会提示批准。把域名加入 `allow`，即可自动批准来自可信来源的获取。

| 示例                        | 说明                                      |
| --------------------------- | ----------------------------------------- |
| `WebFetch(docs.github.com)` | 允许从 `docs.github.com` 获取             |
| `WebFetch(*.example.com)`   | 允许从 `example.com` 的任意子域名获取     |
| `WebFetch(*)`               | 允许从任意域名获取（请谨慎使用）          |

**域名模式匹配：**

- `*` 匹配所有域名
- `*.example.com` 匹配子域名（例如 `docs.example.com`、`api.example.com`）
- `example.com` 只匹配该精确域名

### MCP 工具

**格式：** `Mcp(server:tool)`

控制 Agent 可以运行哪些 MCP (Model Context Protocol) 工具。使用 `server`（来自 `mcp.json`）和 `tool` 名称，用 `*` 作为通配符。

| 示例             | 说明                                    |
| ---------------- | --------------------------------------- |
| `Mcp(datadog:*)` | 允许来自 Datadog MCP 服务器的所有工具   |
| `Mcp(*:search)`  | 允许任意服务器的 `search` 工具          |
| `Mcp(*:*)`       | 允许所有 MCP 工具（请谨慎使用）         |

## 配置

把权限添加到 CLI 配置文件中的 `permissions` 对象：

```json
{
  "permissions": {
    "allow": [
      "Shell(ls)",
      "Shell(git)",
      "Read(src/**/*.ts)",
      "Write(package.json)",
      "WebFetch(docs.github.com)",
      "WebFetch(*.github.com)",
      "Mcp(datadog:*)"
    ],
    "deny": [
      "Shell(rm)",
      "Read(.env*)",
      "Write(**/*.key)",
      "WebFetch(malicious-site.com)"
    ]
  }
}
```

## 模式匹配

- Glob 模式使用 `**`、`*` 和 `?` 通配符
- 相对路径的范围限定为当前工作区
- 绝对路径可以指向项目之外的文件
- Deny 规则优先于 allow 规则
- 使用 `command:args`（例如 `curl:*`）用 glob 同时匹配命令和参数

---

## 站点地图

[所有文档页面概览](/llms.txt)
