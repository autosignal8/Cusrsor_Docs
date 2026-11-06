> 原文：https://cursor.com/docs/cli/reference/slash-commands

# 斜杠命令

| 命令                                   | 说明                                                                                                                   |
| -------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| `/model [filter]`                      | 选择模型。按 `Tab` 编辑。                                                                                              |
| `/run-everything [on\|off\|status]`    | 切换 Run Everything 或显示其状态。`/auto-run` 是别名。                                                                 |
| `/plan [prompt]`                       | 切换到 Plan Mode、显示当前计划，或在 Plan Mode 中提交提示词                                                            |
| `/ask`                                 | 切换 Ask Mode，用于只读提问                                                                                            |
| `/debug [prompt]`                      | 切换 Debug Mode，或在 Debug Mode 中提交提示词                                                                          |
| `/goal [objective]`                    | 给 Agent 一个长期目标，持续推进直到完全完成。正在逐步推出。                                                            |
| `/logs`                                | 显示调试日志路径并复制到剪贴板                                                                                         |
| `/update`                              | 将 Cursor Agent 更新到最新版本                                                                                         |
| `/max-mode`                            | 在旧版按请求计费的方案上切换 Max Mode                                                                                  |
| `/rename <name>`                       | 重命名当前聊天会话                                                                                                     |
| `/clear`                               | 开始新的聊天会话。`/new`、`/new-chat` 和 `/newchat` 是别名。                                                           |
| `/resume`                              | 打开最近的聊天并恢复其中一个                                                                                           |
| `/fork`                                | 将当前聊天分叉为新会话                                                                                                 |
| `/summarize`                           | 总结对话以减少上下文。`/compress` 是别名。                                                                             |
| `/rewind`                              | 跳回之前的消息                                                                                                         |
| `/vim`                                 | 切换 Vim 按键                                                                                                          |
| `/line-numbers`                        | 切换代码块中的行号                                                                                                     |
| `/show-thinking`                       | 切换思考块显示                                                                                                         |
| `/status-indicators`                   | 切换终端标题状态指示                                                                                                   |
| `/shell [command]`                     | 进入 Shell Mode。`/sh` 和 `/run` 是别名。                                                                              |
| `/about`                               | 显示 CLI 版本、系统和账户信息。同时复制到剪贴板。                                                                      |
| `/setup-terminal`                      | 配置终端换行键绑定。见[终端设置](https://cursor.com/docs/cli/reference/terminal-setup.md)。                            |
| `/help [command]`                      | 显示帮助。使用 `/help <command>` 查看命令详情。                                                                        |
| `/feedback <message>`                  | 向团队分享反馈                                                                                                         |
| `/open`                                | 在 Cursor 中打开仓库的 Git 根目录。`/cursor` 是别名。                                                                  |
| `/copy-request-id`                     | 将最近的请求 ID 复制到剪贴板                                                                                           |
| `/copy-conversation-id`                | 将当前对话 ID 复制到剪贴板                                                                                             |
| `/logout`                              | 从 Cursor 登出                                                                                                         |
| `/quit`                                | 退出                                                                                                                   |
| `/exit`                                | 退出                                                                                                                   |
| `/mcp [list\|list-tools] [identifier]` | 管理 MCP 服务器，并列出某个服务器的工具                                                                                |
| `/plugin [subcommand]`                 | 管理 Plugins 和市场                                                                                                    |
| `/config`                              | 交互式配置 CLI 设置                                                                                                    |
| `/copy`                                | 将之前的用户消息复制到剪贴板                                                                                           |
| `/sandbox`                             | 配置沙箱模式和网络访问设置                                                                                             |
| `/bedrock [subcommand]`                | 在启用 Bedrock 功能时配置 Bedrock                                                                                      |

---

## 站点地图

[所有文档页面概览](/llms.txt)
