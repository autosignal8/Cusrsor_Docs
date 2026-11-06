> 原文：https://cursor.com/docs/cli/reference/parameters

# 参数

## 全局选项

全局选项可以与任何命令一起使用：

| 选项                       | 说明                                                                                                       |
| -------------------------- | ---------------------------------------------------------------------------------------------------------- |
| `-v, --version`            | 输出版本号                                                                                                 |
| `--api-key <key>`          | 用于身份验证的 API key（也可以使用 `CURSOR_API_KEY` 环境变量）                                            |
| `-H, --header <header>`    | 向 Agent 请求添加自定义标头（格式：`Name: Value`，可多次使用）                                            |
| `-p, --print`              | 将响应打印到控制台（用于脚本或非交互使用）。可以访问所有工具，包括写入和 shell。                          |
| `--output-format <format>` | 输出格式（仅与 `--print` 一起有效）：`text`、`json` 或 `stream-json`（默认：`text`）                      |
| `--stream-partial-output`  | 将部分输出作为单独的文本增量流式输出（仅与 `--print` 和 `stream-json` 格式一起有效）                      |
| `--resume [chatId]`        | 恢复聊天会话                                                                                               |
| `--continue`               | 继续上一个会话（`--resume=-1` 的别名）                                                                    |
| `--model <model>`          | 要使用的模型                                                                                               |
| `--mode <mode>`            | 设置 Agent 模式：`plan` 或 `ask`（未指定模式时默认为 agent）                                               |
| `--plan`                   | 以 plan 模式启动（`--mode=plan` 的简写）                                                                  |
| `--list-models`            | 列出所有可用模型                                                                                           |
| `-f, --force`              | 强制允许命令，除非被明确拒绝                                                                               |
| `--yolo`                   | `--force` 的别名                                                                                           |
| `--sandbox <mode>`         | 设置沙箱模式：`enabled` 或 `disabled`                                                                      |
| `--approve-mcps`           | 自动批准所有 MCP 服务器                                                                                    |
| `--trust`                  | 不经提示信任工作区（仅无头模式）                                                                           |
| `--workspace <path>`       | 要使用的工作区目录                                                                                         |
| `--plugin-dir <path>`      | 加载本地插件目录（可多次指定）                                                                             |
| `-w, --worktree [name]`    | 在 `~/.cursor/worktrees/<reponame>/<name>` 下的新 Git worktree 中运行。如果省略，会生成一个名称。         |
| `--worktree-base <branch>` | 新 worktree 所基于的分支或 ref（默认：当前 HEAD）                                                          |
| `--skip-worktree-setup`    | 跳过运行 `.cursor/worktrees.json` 中的 worktree 设置脚本                                                   |
| `-h, --help`               | 显示命令帮助                                                                                               |

## 命令

| 命令                          | 说明                                                         | 用法                                |
| ----------------------------- | ------------------------------------------------------------ | ----------------------------------- |
| `agent [prompt...]`           | 以 agent 模式启动（默认）                                    | `agent agent "fix the tests"`       |
| `login`                       | 使用 Cursor 进行身份验证                                     | `agent login`                       |
| `logout`                      | 登出并清除已存储的身份验证                                   | `agent logout`                      |
| `status` \| `whoami`          | 查看身份验证状态                                             | `agent status`                      |
| `about`                       | 显示版本、系统和账户信息                                     | `agent about`                       |
| `models`                      | 列出此账户可用的模型                                         | `agent models`                      |
| `mcp`                         | 管理 MCP 服务器                                              | `agent mcp`                         |
| `sandbox`                     | 配置沙箱模式，或在沙箱中运行一条命令（隐藏）                 | `agent sandbox enable`              |
| `worker`                      | 启动一个私有云 worker，在你的环境中运行 Agent                | `agent worker start`                |
| `acp`                         | 启动 ACP 服务器模式（高级，隐藏命令）                        | `agent acp`                         |
| `update`                      | 将 Cursor Agent 更新到最新版本                               | `agent update`                      |
| `ls`                          | 打开先前的聊天并选择一个恢复                                 | `agent ls`                          |
| `resume`                      | 恢复最近的聊天会话                                           | `agent resume`                      |
| `create-chat`                 | 创建一个新的空聊天并返回其 ID                                | `agent create-chat`                 |
| `generate-rule` \| `rule`     | 通过交互式提示词生成一条新的 Cursor 规则                     | `agent generate-rule`               |
| `install-shell-integration`   | 将 shell 集成安装到 `~/.zshrc`                               | `agent install-shell-integration`   |
| `uninstall-shell-integration` | 从 `~/.zshrc` 移除 shell 集成                                | `agent uninstall-shell-integration` |
| `help [command]`              | 显示命令帮助                                                 | `agent help [command]`              |

`agent acp` 用于自定义 ACP 客户端和高级集成。它默认不出现在命令帮助输出中。

未指定命令时，Cursor Agent 默认以交互式 agent 模式启动。

## MCP

管理为 Cursor Agent 配置的 MCP 服务器。

| 子命令                    | 说明                                                                             | 用法                                |
| ------------------------- | -------------------------------------------------------------------------------- | ----------------------------------- |
| `login <identifier>`      | 使用 `.cursor/mcp.json` 或 `~/.cursor/mcp.json` 中配置的 MCP 服务器进行身份验证 | `agent mcp login <identifier>`      |
| `list`                    | 列出已配置的 MCP 服务器及其状态                                                  | `agent mcp list`                    |
| `list-tools <identifier>` | 列出特定 MCP 的可用工具及其参数名                                                | `agent mcp list-tools <identifier>` |
| `enable <identifier>`     | 将 MCP 服务器加入本地已批准列表                                                  | `agent mcp enable <identifier>`     |
| `disable <identifier>`    | 禁用 MCP 服务器，使其不会加载或提示批准                                          | `agent mcp disable <identifier>`    |

所有 MCP 命令都支持 `-h, --help`，以获取该命令的帮助。

## Sandbox

配置沙箱模式，或在沙箱中运行一条命令。

| 子命令                | 说明                                     | 用法                    |
| --------------------- | ---------------------------------------- | ----------------------- |
| `enable`              | 为命令执行启用沙箱模式                   | `agent sandbox enable`  |
| `disable`             | 禁用沙箱模式并使用 allowlist 模式        | `agent sandbox disable` |
| `reset`               | 将沙箱配置重置为默认值                   | `agent sandbox reset`   |
| `run <cmd> [args...]` | 在具备工作区读/写权限的沙箱中运行命令    | `agent sandbox run ls`  |
| `help [command]`      | 显示命令帮助                             | `agent sandbox help`    |

| 命令          | 选项                            | 说明                                                       |
| ------------- | ------------------------------- | ---------------------------------------------------------- |
| `sandbox run` | `--allow-paths <paths>`         | 额外读/写路径的逗号分隔列表                               |
| `sandbox run` | `--readonly-paths <paths>`      | 额外只读路径的逗号分隔列表                                 |
| `sandbox run` | `--blocked-patterns <patterns>` | 要阻止的 gitignore 风格模式的逗号分隔列表                 |
| `sandbox run` | `--sandbox`                     | 使用工作区读/写沙箱策略运行（默认：`true`）               |
| `sandbox run` | `--network`                     | 在沙箱中启用网络访问（默认：`false`）                     |
| `sandbox run` | `--sb-debug`                    | 将沙箱调试日志写入临时文件夹并打印路径                     |

所有 sandbox 命令都支持 `-h, --help`，以获取该命令的帮助。

## Worker

启动一个私有云 worker，连接到 Cursor 并在你的环境中运行 Agent。

| 子命令           | 说明                                                     | 用法                 |
| ---------------- | -------------------------------------------------------- | -------------------- |
| `start`          | 启动 worker 并连接到 Cursor                              | `agent worker start` |
| `debug`          | 运行私有 worker 的预检诊断，覆盖身份验证、隐私和路由     | `agent worker debug` |
| `help [command]` | 显示命令帮助                                             | `agent worker help`  |

| 命令           | 选项                               | 说明                                                                                                                                                                                          |
| -------------- | ---------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `worker`       | `--auth-token-file <path>`         | 包含 worker 身份验证 token 的文件路径                                                                                                                                                         |
| `worker`       | `--worker-dir <path>`              | 向 Agent 暴露的工作区根目录。可重复。第一个值是分配身份。                                                                                                                                     |
| `worker`       | `--management-addr <address>`      | `/healthz`、`/readyz` 和 `/metrics` 的监听地址                                                                                                                                                |
| `worker`       | `--label <key=value>`              | 添加一个 worker 标签。可多次使用。不能与 `--labels-file` 一起使用。                                                                                                                           |
| `worker`       | `--labels-file <path>`             | JSON 或 TOML 标签文件的路径。也可以使用 `CURSOR_WORKER_LABELS_FILE`。                                                                                                                         |
| `worker`       | `--idle-release-timeout <seconds>` | worker 变为空闲后可以保持连接的秒数。默认 `0` 会禁用基于空闲的释放。                                                                                                                          |
| `worker`       | `--pool`                           | 注册以接受池分配。一次由一个 Cloud Agent 认领该 worker。                                                                                                                                      |
| `worker`       | `--single-use`                     | `--pool` 的旧别名                                                                                                                                                                             |
| `worker`       | `--pool-name <name>`               | 池 worker 的池标签。需要 `--pool` 或 `--single-use`。默认为 `default`。                                                                                                                       |
| `worker`       | `--name <name>`                    | 自定义显示名称。默认为机器主机名。                                                                                                                                                            |
| `worker`       | `--data-dir <path>`                | 日志、产物和录制数据的基目录                                                                                                                                                                  |
| `worker`       | `--debug`                          | 在启动 bridge 模式之前打印 worker 调试诊断                                                                                                                                                    |
| `worker`       | `--computer-use`                   | 让 Agent 驱动这台机器的桌面。在 macOS 上，如有需要会安装 Cursor Computer Use 辅助应用。见[计算机使用](https://cursor.com/docs/cloud-agent/self-hosted/computer-use.md)。                   |
| `worker`       | `--display <display>`              | 仅 Linux。`--computer-use` 所要求的现有 X11 显示，例如 `:0`。                                                                                                                                 |
| `worker`       | `--share-desktop [mode]`           | 仅 Linux。让已授权的查看者观看或控制 Agent 桌面：`view` 或 `view_and_control`。与 computer use 相互独立。                                                                                     |
| `worker start` | `--verbose`                        | 启用详细启动日志                                                                                                                                                                              |
| `worker debug` | `--json`                           | 以 JSON 输出调试报告                                                                                                                                                                          |

## 特定命令的选项

| 命令               | 选项                | 说明                                              |
| ------------------ | ------------------- | ------------------------------------------------- |
| `status`, `whoami` | `--format <format>` | 输出格式：`text` 或 `json`（默认：`text`）        |
| `about`            | `--format <format>` | 输出格式：`text` 或 `json`（默认：`text`）        |

## 实参

以 agent 模式启动时（默认行为），你可以提供初始提示词：

**实参：**

- `prompt` — Agent 的初始提示词

## 获取帮助

所有命令都支持全局 `-h, --help` 选项，以显示该命令的帮助。

---

## 站点地图

[所有文档页面概览](/llms.txt)
