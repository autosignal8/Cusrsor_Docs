> 原文：https://cursor.com/docs/agent/tools/terminal

# 终端

Cursor 会直接在你的终端中运行 shell 命令。[Run Mode](https://cursor.com/docs/agent/security/run-modes.md) 控制命令何时运行、何时由 Cursor 询问，以及终端命令何时进入沙箱。

## 沙箱

沙箱在受限环境中运行终端命令，阻止未经授权的文件访问和网络活动。关于平台要求、网络模式、环境变量和 `sandbox.json` 配置，请阅读 [Run Modes > 沙箱](https://cursor.com/docs/agent/security/run-modes.md#sandboxing)。

## 故障排除

某些 shell 主题（例如 Powerlevel9k/Powerlevel10k）会干扰内联终端输出。如果命令输出看起来被截断或格式错乱，请在 Cursor 运行时禁用该主题，或改用更简单的提示符。

### 为 Cursor 会话禁用繁重的提示符

在 shell 配置中使用 `CURSOR_AGENT` 环境变量，检测 Cursor 何时正在运行，并跳过初始化花哨的提示符和主题。

```zsh
# ~/.zshrc - disable Powerlevel10k when Cursor runs
if [[ -n "$CURSOR_AGENT" ]]; then
  # Skip theme initialization for better compatibility
else
  [[ -r ~/.p10k.zsh ]] && source ~/.p10k.zsh
fi
```

```bash
# ~/.bashrc - fall back to a simple prompt in Cursor sessions
if [[ -n "$CURSOR_AGENT" ]]; then
  PS1='\u@\h \W \$ '
fi
```

## 相关

- [终端帮助](https://cursor.com/help/ai-features/terminal.md)


---

## 站点地图

[所有文档页面概览](/llms.txt)
