> 原文：https://cursor.com/docs/cli/reference/terminal-setup

# 终端设置

配置你的终端，以获得最好的 Cursor CLI 体验。本指南涵盖多行输入的键绑定、Vim 模式和主题同步。

## 快速开始

如果 Shift+Enter 在你的终端中不能用于换行，运行 `/setup-terminal` 以获取配置替代方案的指导：

```bash
/setup-terminal
```


此命令会检测你的终端，并提供将 Option+Enter 配置为插入换行的替代方式的说明。

## 通用选项

这些方法在**所有终端**中都有效，包括 tmux、screen 和 SSH 会话：

| 方法     | 说明                                                     |
| :------- | :------------------------------------------------------- |
| \\+Enter | 输入反斜杠，然后按 Enter 以插入换行                      |
| Ctrl+J   | 换行的标准控制字符（ASCII 换行符）                       |

如果你在 tmux 中，或其他键绑定有问题，Ctrl+J 是最可靠的选项。

## 终端支持

### 原生 Shift+Enter 支持

这些终端开箱即支持用 Shift+Enter 换行：

- **iTerm2**（macOS）
- **Ghostty**
- **Kitty**
- **Warp**
- **Zed**（集成终端）

### 需要 `/setup-terminal`

这些终端需要 `/setup-terminal` 来配置 Option+Enter 换行：

- **Apple Terminal**（macOS）
- **Alacritty**
- **VS Code**（集成终端）

### 终端复用器

**tmux** 和 **screen** 会在 Shift+Enter 到达应用程序之前拦截它。请改用通用选项：

- Ctrl+J — 在所有复用器会话中可靠工作
- \\+Enter — 同样通用

你可以把外层终端（例如 iTerm2）配置为支持 Shift+Enter，但该键绑定无法穿过 tmux。使用通用选项可以获得最一致的体验。

## Vim 模式

在 CLI 输入区域启用 Vim 键绑定，用于导航和编辑。

### 用斜杠命令切换

```bash
/vim
```


这会为当前会话打开或关闭 Vim 模式，并保存该偏好。

### 在设置中配置

添加到 `~/.cursor/cli-config.json`：

```json
{
  "version": 1,
  "editor": { "vimMode": true },
  "permissions": { "allow": [], "deny": [] }
}
```


### 模式

Vim 模式使用模式化编辑：

- **普通模式** — 导航并执行命令（启用 Vim 模式时的默认模式）
- **插入模式** — 正常输入文本

按 Esc 从插入模式返回普通模式。

### 导航

| 按键    | 说明                                              |
| :------ | :------------------------------------------------ |
| h, l    | 向左 / 向右移动                                   |
| j, k    | 向下 / 向上移动                                   |
| w, b    | 下一个 / 上一个单词                               |
| e       | 单词末尾                                          |
| W, B, E | 与上面相同，但针对 WORD（非空白字符序列）         |
| 0, $    | 行首 / 行尾                                       |

### 编辑

| 按键       | 说明                                    |
| :--------- | :-------------------------------------- |
| x          | 删除光标下的字符                        |
| X          | 删除光标前的字符                        |
| d + motion | 删除范围（例如 `dw` 删除单词）          |
| dd         | 删除整行                                |
| D          | 删除到行尾                              |
| s          | 替换字符（删除 + 插入模式）             |
| S, cc      | 更改整行                                |
| C          | 更改到行尾                              |

### 进入插入模式

| 按键 | 说明           |
| :--- | :------------- |
| i    | 在光标处插入   |
| a    | 在光标后插入   |
| I    | 在行首插入     |
| A    | 在行尾插入     |
| o    | 在下方打开新行 |
| O    | 在上方打开新行 |

### 计数

在命令前加数字以重复它们。例如，`3w` 向前移动 3 个单词，`2dd` 删除 2 行。

Vim 模式只影响输入区域。在聊天历史和其他 UI 元素中导航使用标准键绑定。

## 终端主题

Cursor CLI 会自动检测终端的配色方案并调整外观。

### 自动检测

CLI 使用标准转义序列查询终端的背景色。大多数现代终端都支持：

- **深色终端** → CLI 使用深色主题
- **浅色终端** → CLI 使用浅色主题

### 支持自动检测的终端

这些终端会正确报告其配色方案：

- iTerm2
- Ghostty
- Kitty
- Alacritty
- Apple Terminal
- Windows Terminal
- VS Code 集成终端

### 强制指定主题

如果自动检测不起作用，可以用环境变量覆盖：

```bash
# Force dark theme
export COLORFGBG="15;0"

# Force light theme
export COLORFGBG="0;15"
```


把这行加入你的 shell 配置文件（`.bashrc`、`.zshrc` 等），使其永久生效。

### 主题问题故障排除

**颜色看起来不对：**

- 确保终端支持 256 色或真彩色
- 检查 `TERM` 是否设置正确（例如 `xterm-256color`）
- 尝试显式设置 `COLORFGBG`

**tmux 用户：**

- 在 `.tmux.conf` 中添加以下内容以透传颜色检测：
  ```
  set -g default-terminal "tmux-256color"
  set -ag terminal-overrides ",xterm-256color:RGB"
  ```
- 更改后重启 tmux

## 手动配置

如果 `/setup-terminal` 对你的终端不起作用，可以手动配置键绑定。

### 用 Option+Enter 换行

Option+Enter 会发送 Cursor CLI 识别为换行的特殊转义序列。将终端配置为在按下 Option+Enter 时发送 `\x1b\r`（Escape 后跟回车）。

**iTerm2：**

1. 打开 **Preferences** → **Profiles** → **Keys** → **Key Mappings**
2. 点击 **+** 添加新映射
3. 将 **Keyboard Shortcut** 设为 Option+Enter
4. 将 **Action** 设为 "Send Escape Sequence"
5. 输入 `\r` 作为转义序列

**Alacritty：**

添加到 `alacritty.toml`：

```toml
[keyboard]
bindings = [
  { key = "Return", mods = "Alt", chars = "\u001b\r" }
]
```


**Kitty：**

添加到 `kitty.conf`：

```
map alt+enter send_text all \x1b\r
```


### Shift+Enter

Shift+Enter 支持取决于终端是否正确报告按键修饰符。大多数现代终端会自动处理，但有些可能需要配置。

**VS Code 终端：**

VS Code 的集成终端可能无法正确传递 Shift+Enter。添加到 `keybindings.json`：

```json
{
  "key": "shift+enter",
  "command": "workbench.action.terminal.sendSequence",
  "args": { "text": "\u001b[13;2u" },
  "when": "terminalFocus"
}
```


## 故障排除

**键绑定不起作用：**

- 使用 `cat` 或 `showkey` 验证终端是否正确检测到按键
- 检查终端复用器（tmux/screen）是否拦截了按键
- 使用 Ctrl+J 作为可靠的回退

**tmux 用户：**

- Shift+Enter 和 Option+Enter 无法穿过 tmux
- 改用 Ctrl+J 或 \\+Enter
- 这些通用选项在任何地方都有效，包括嵌套的 tmux 会话

**SSH 会话：**

- 远程终端能力取决于你的本地终端模拟器
- Ctrl+J 通过 SSH 可靠工作
- \\+Enter 是另一个可靠选项

## 摘要

| 键绑定       | 适用                              | 说明                                                       |
| :----------- | :-------------------------------- | :--------------------------------------------------------- |
| Ctrl+J       | 所有终端                          | 最可靠，到处可用                                           |
| \\+Enter     | 所有终端                          | 通用替代                                                   |
| Shift+Enter  | iTerm2、Ghostty、Kitty、Warp、Zed | 原生支持，无需配置                                         |
| Option+Enter | 运行 `/setup-terminal` 之后       | Apple Terminal、Alacritty、VS Code 的换行替代              |

---

## 站点地图

[所有文档页面概览](/llms.txt)
