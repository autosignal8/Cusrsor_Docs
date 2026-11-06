> 原文：https://cursor.com/docs/cli/reference/configuration

# 配置

使用 `cli-config.json` 文件配置 Agent CLI。

## 文件位置

| 类型 | 平台        | 路径                                       |
| :--- | :---------- | :----------------------------------------- |
| 全局 | macOS/Linux | `~/.cursor/cli-config.json`                |
| 全局 | Windows     | `$env:USERPROFILE\.cursor\cli-config.json` |
| 项目 | 全部        | `<project>/.cursor/cli.json`               |

只有权限可以在项目级配置。所有其他 CLI 设置必须全局设置。

用环境变量覆盖：

- **`CURSOR_CONFIG_DIR`**：自定义目录路径
- **`XDG_CONFIG_HOME`**（Linux/BSD）：使用 `$XDG_CONFIG_HOME/cursor/cli-config.json`

## Schema

### 必填字段

| 字段                | 类型      | 说明                                                                                       |
| :------------------ | :-------- | :----------------------------------------------------------------------------------------- |
| `version`           | number    | 配置 schema 版本（当前：`1`）                                                              |
| `editor.vimMode`    | boolean   | 启用 Vim 键绑定（默认：`false`）                                                           |
| `permissions.allow` | string\[] | 允许的操作（见[权限](https://cursor.com/docs/cli/reference/permissions.md)）              |
| `permissions.deny`  | string\[] | 禁止的操作（见[权限](https://cursor.com/docs/cli/reference/permissions.md)）              |

### 可选字段

| 字段                                  | 类型    | 说明                                                                                                               |
| :------------------------------------ | :------ | :----------------------------------------------------------------------------------------------------------------- |
| `channel`                             | string  | 用于 CLI 更新的发布通道                                                                                            |
| `model`                               | object  | 所选模型配置                                                                                                       |
| `maxMode`                             | boolean | 在模型选择器中持久化的 Max Mode 偏好                                                                               |
| `hasChangedDefaultModel`              | boolean | 由 CLI 管理的模型覆盖标志                                                                                          |
| `notifications`                       | boolean | 当 Agent 完成或需要输入时发送终端通知                                                                              |
| `hints`                               | boolean | 在 Agent 工作时显示 CLI 提示                                                                                       |
| `rewind`                              | boolean | 启用 `/rewind`，以恢复会话中更早的消息                                                                             |
| `suggestNextPrompt`                   | boolean | 在每一轮结束时建议后续提示词                                                                                       |
| `display.showLineNumbers`             | boolean | 在渲染的代码块中显示行号                                                                                           |
| `display.showThinkingBlocks`          | boolean | 在可用时渲染模型思考块                                                                                             |
| `display.showStatusIndicators`        | boolean | 启用终端标题状态指示                                                                                               |
| `display.showStatusLineRunningTime`   | boolean | 在状态行中显示已运行时间                                                                                           |
| `approvalMode`                        | string  | 批准模式：`allowlist`、`auto-review` 或 `unrestricted`                                                             |
| `sandbox.mode`                        | string  | 沙箱模式覆盖                                                                                                       |
| `sandbox.networkAccess`               | string  | 沙箱模式的网络访问设置                                                                                             |
| `sandbox.readBoundary`                | string  | [读取访问](https://cursor.com/docs/agent/security/run-modes.md#read-access)模式：`system`（默认）或 `workspace`    |
| `network.useHttp1ForAgent`            | boolean | 对 Agent 连接使用 HTTP/1.1 而不是 HTTP/2（默认：`false`）                                                          |
| `attribution.attributeCommitsToAgent` | boolean | 在 Agent 的提交中添加 "Made with Cursor" trailer（默认：`true`）                                                  |
| `attribution.attributePRsToAgent`     | boolean | 在 Agent 的 PR 中添加 "Made with Cursor" footer（默认：`true`）                                                   |

## 示例

### 最小配置

```json
{
  "version": 1,
  "editor": { "vimMode": false },
  "permissions": { "allow": ["Shell(ls)"], "deny": [] }
}
```

### 启用 Vim 模式

```json
{
  "version": 1,
  "editor": { "vimMode": true },
  "permissions": { "allow": ["Shell(ls)"], "deny": [] }
}
```

### 配置权限

```json
{
  "version": 1,
  "editor": { "vimMode": false },
  "permissions": {
    "allow": ["Shell(ls)", "Shell(echo)"],
    "deny": ["Shell(rm)"]
  }
}
```

可用的权限类型和示例见[权限](https://cursor.com/docs/cli/reference/permissions.md)。

## 故障排除

**配置错误**：把文件移到一旁并重启：

```bash
mv ~/.cursor/cli-config.json ~/.cursor/cli-config.json.bad
```

**更改没有持久化**：确保 JSON 有效且有写入权限。有些字段由 CLI 管理，可能会被覆盖。

## 说明

- 纯 JSON 格式（无注释）
- CLI 会对缺失字段进行自我修复
- 损坏的文件会备份为 `.bad` 并重新创建
- 权限条目是精确字符串（细节见[权限](https://cursor.com/docs/cli/reference/permissions.md)）

## 模型

你可以使用 `/model` 斜杠命令为 CLI 选择模型。

```bash
/model auto
/model gpt-5
/model sonnet-4-thinking
```

其他命令见[斜杠命令](https://cursor.com/docs/cli/reference/slash-commands.md)文档。

## 代理配置

如果你的网络通过代理服务器路由流量，使用环境变量和配置文件配置 CLI。

### 环境变量

在运行 CLI 之前设置这些环境变量：

```bash
export HTTP_PROXY=http://your-proxy:port
export HTTPS_PROXY=http://your-proxy:port
export NODE_USE_ENV_PROXY=1
```

如果代理执行 SSL 检查（中间人），还需要信任你组织的 CA 证书：

```bash
export NODE_EXTRA_CA_CERTS=/path/to/corporate-ca-cert.pem
```

### HTTP/1.1 回退

某些企业代理（例如 Zscaler）不支持 HTTP/2 双向流。在配置中启用 HTTP/1.1 模式：

```json
{
  "version": 1,
  "editor": { "vimMode": false },
  "permissions": { "allow": [], "deny": [] },
  "network": {
    "useHttp1ForAgent": true
  }
}
```

这会把 Agent 连接切换为带 Server-Sent Events (SSE) 的 HTTP/1.1，可与大多数企业代理配合使用。

代理测试命令和故障排除见[网络配置](https://cursor.com/docs/enterprise/network-configuration.md)。

---

## 站点地图

[所有文档页面概览](/llms.txt)
