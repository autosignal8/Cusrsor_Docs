> 原文：https://cursor.com/docs/integrations/xcode

# Xcode

Xcode 26.3+ 提供一个内置的 [MCP](https://cursor.com/docs/mcp.md) 服务器，让 Cursor 可以直接访问你的 Xcode 项目。Cursor 的 Agent 可以读取和编辑文件、触发构建、运行测试、捕获 SwiftUI preview，并搜索 Apple 的文档；这一切都不必离开你的编辑器。

这通过 `xcrun mcpbridge` 实现。它是 Apple 随 Xcode 提供的二进制文件，把 MCP 协议消息转换到 Xcode 内部的 XPC 层。你只需配置一次，Cursor 就会把 Xcode 的 20 个内置工具当作其他 MCP 服务器一样使用。

## 前提条件

- 已安装 Xcode 26.3 或更高版本的 macOS
- 付费的 [Cursor 方案](https://cursor.com/docs/models-and-pricing.md)
- 在 Xcode 中打开一个 Xcode 项目（Xcode 必须正在运行）

### 在 Xcode 中启用 MCP

在 Cursor 能够连接之前，请打开 Xcode 的 MCP bridge：

### 打开 Xcode 设置

前往 **Xcode > Settings > Intelligence**。

### 启用 MCP

在 **Model Context Protocol** 下，打开 **Xcode Tools**。

## 设置 Cursor

选择适合你工作流的方式。

### 选项 1：MCP 设置界面

### 打开 MCP 设置

前往 **Customize** > **MCPs**。

### 添加服务器

点击 **Add New MCP Server**。把传输方式设为 **stdio**，命名为 `xcode-tools`，并把命令填为 `xcrun mcpbridge`。

### 选项 2：`mcp.json`

在你的 [MCP 配置文件](https://cursor.com/docs/mcp.md#configuration-locations)中添加一项：

```json title="~/.cursor/mcp.json"
{
  "mcpServers": {
    "xcode-tools": {
      "command": "xcrun",
      "args": ["mcpbridge"]
    }
  }
}
```

### 选项 3：Cursor CLI

如果你使用 [Cursor CLI](https://cursor.com/docs/cli/overview.md)，请从终端注册该服务器：

```bash
agent mcp add xcode-tools -- xcrun mcpbridge
```

CLI 与编辑器共享同一份 MCP 配置，因此该服务器会同时出现在两边。

## 可用工具

Xcode 在五个类别中提供 20 个 MCP 工具：

### 文件操作

- **XcodeRead**：读取文件内容（每次调用最多 600 行；较大的文件可使用 offset/limit）
- **XcodeWrite**：创建或覆盖文件
- **XcodeUpdate**：对已有文件应用有针对性的编辑
- **XcodeGrep**：用正则表达式搜索文件内容
- **XcodeGlob**：按模式查找文件
- **XcodeLS**：列出目录内容
- **XcodeMakeDir**：创建目录
- **XcodeRM**：删除文件或目录
- **XcodeMV**：移动或重命名文件

### 构建与测试

- **BuildProject**：构建当前 scheme
- **GetBuildLog**：获取构建日志，可按严重程度、正则表达式或文件 glob 筛选
- **RunAllTests**：运行完整测试套件
- **RunSomeTests**：运行指定的测试类或方法
- **GetTestList**：列出可用的测试

### 诊断

- **XcodeListNavigatorIssues**：显示 Issue Navigator 中的警告和错误
- **XcodeRefreshCodeIssuesInFile**：重新检查某个文件中的代码问题

### 智能

- **RenderPreview**：截取 SwiftUI preview 的屏幕截图
- **DocumentationSearch**：在 Apple 文档和 WWDC 文稿中进行语义搜索
- **ExecuteSnippet**：运行一段 Swift 代码

### 工作区

- **XcodeListWindows**：列出打开的 Xcode 窗口和标签页

## 示例工作流

典型的 Cursor + Xcode 工作流如下：

1. 在 Cursor 和 Xcode 中都打开你的项目
2. 让 Cursor 的 Agent 添加一项功能或修复一个 bug
3. Agent 使用 **XcodeRead** 和 **XcodeGrep** 来理解你的代码
4. 它用 **XcodeWrite** 或 **XcodeUpdate** 编辑文件
5. 它运行 **BuildProject** 检查错误，并用 **GetBuildLog** 读取结果
6. 它用 **RunSomeTests** 运行测试，以验证这次更改
7. 它用 **RenderPreview** 捕获 SwiftUI preview，以确认界面

整个过程你都留在 Cursor 中。Xcode 在后台处理编译、测试和 preview。

## Cursor CLI 与 Xcode

[Cursor CLI](https://cursor.com/docs/cli/overview.md) 也可以使用 Xcode 的 MCP 工具。这对无头工作流、CI 流水线，或以终端为主的开发者很有用。

```bash
# Run agent with Xcode tools available
agent "Add unit tests for the NetworkManager class"
```

Agent 会从你的配置中找到 `xcode-tools` MCP 服务器，并使用编辑器中可用的同一组工具。

## 排查问题

### Cursor 找不到 xcode-tools 服务器

请确保 Xcode 正在运行，并且已打开一个项目。`xcrun mcpbridge` 进程需要一个活动的 Xcode 会话才能通信。

### 工具报错，提示缺少 tabIdentifier

有些 Xcode MCP 工具需要工作区上下文。请确认你在 Xcode 中打开的是项目或工作区，而不是一个空窗口。

### 构建或测试工具超时

大型项目的构建时间更长。请直接查看 Xcode 中的构建进度。MCP bridge 会等待 Xcode 的响应，因此超时通常意味着底层操作仍在进行。

### Xcode 设置中没有 MCP 开关

你需要 Xcode 26.3 或更高版本。在 **Xcode > About Xcode** 中查看你的版本，并通过 Mac App Store 或 [Apple Developer 下载](https://developer.apple.com/download/)更新。

### xcrun: error: unable to find utility "mcpbridge"

你的系统指向的是 Command Line Tools，而不是完整的 Xcode 安装。运行以下命令进行修复：

```bash
sudo xcode-select -s /Applications/Xcode.app/Contents/Developer
sudo xcodebuild -runFirstLaunch
```

然后确认该 bridge 可用：

```bash
xcrun --find mcpbridge
```

这应当返回一个文件路径，而不是错误。一旦如此，请打开 Xcode 并载入你的项目，前往 **Settings > Intelligence > Model Context Protocol**，启用 **Allow external agents**。然后在 Cursor 设置中重新打开 Xcode MCP 服务器。你应当会在 Xcode 中看到一个确认此次连接的权限对话框。

## 相关内容

### MCP 概览

涵盖设置、配置和身份验证的完整 MCP 指南

### iOS 与 macOS（Swift）

结合 Cursor、Sweetpad 和 Xcode Build Server 的 Swift 开发工作流

### Cursor CLI

从终端使用 Cursor 的 Agent

### CLI MCP 命令

从命令行管理 MCP 服务器

---

## 站点地图

[所有文档页面概览](/llms.txt)
