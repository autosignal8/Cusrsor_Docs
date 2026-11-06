> 原文：https://cursor.com/docs/enterprise/endpoint-security

# 端点安全配置

Cursor 在启动时加载 JavaScript 模块并执行文件 I/O。拦截文件操作或注入进程的端点安全软件，可能使启动慢过内部超时，导致 Agent 等功能失败。本页说明如何配置排除项，以便 Cursor 与你的安全栈一起工作。

## 要排除什么

把以下进程和路径加入安全产品的排除列表。

### Windows

**进程：** 每个进程都有用户安装路径和系统安装路径。添加与你的安装类型匹配的路径。

| 进程 | 安装类型 | 路径 |
| ------------------ | ------------ | ------------------------------------------------------------------------------------------------ |
| `Cursor.exe` | 用户 | `%LOCALAPPDATA%\Programs\cursor\Cursor.exe` |
| `Cursor.exe` | 系统 | `%ProgramFiles%\cursor\Cursor.exe` |
| `rg.exe` | 用户 | `%LOCALAPPDATA%\Programs\cursor\resources\app\node_modules\@vscode\ripgrep\bin\rg.exe` |
| `rg.exe` | 系统 | `%ProgramFiles%\cursor\resources\app\node_modules\@vscode\ripgrep\bin\rg.exe` |
| `inno_updater.exe` | 用户 | `%LOCALAPPDATA%\Programs\cursor\resources\app\node_modules\cursor-inno-updater\inno_updater.exe` |
| `inno_updater.exe` | 系统 | `%ProgramFiles%\cursor\resources\app\node_modules\cursor-inno-updater\inno_updater.exe` |

**路径：**

| 路径 | 说明 |
| --------------------------------- | --------------------------------------------------------- |
| `%LOCALAPPDATA%\Programs\cursor\` | 应用程序二进制文件和捆绑模块（用户安装） |
| `%ProgramFiles%\cursor\` | 应用程序二进制文件和捆绑模块（系统安装） |
| `%APPDATA%\Cursor\` | 用户数据、设置和工作区存储 |

### macOS

**进程：** `Cursor.app`

**路径：**

| 路径 | 说明 |
| --------------------------- | ------------------ |
| `/Applications/Cursor.app/` | 应用程序包 |

## 为什么可能需要排除

Cursor 的扩展宿主在启动时从自己的安装目录读取 JavaScript 文件。当安全软件增加逐文件扫描延迟时，累积延迟可能超过 Cursor 的启动超时。

这主要影响启动。模块载入内存之后，持续的文件操作并不频繁，不太可能造成问题。

Cursor 自己的文件是经过代码签名的二进制文件和捆绑的 JavaScript，不是用户生成的内容。把它们排除在实时扫描之外风险较低，也不会降低对用户文件或网络流量的保护。

可能同时需要**进程排除**和**路径排除**。一些产品使用内核级微筛选器驱动，无论哪个进程在读取，都会扫描所有文件 I/O。仅排除进程可能不够——请同时为 Cursor 安装目录添加路径排除。

## 识别正在运行的安全软件

这些命令可以帮助识别正在运行的产品，从而知道可能需要在哪里配置排除。在 Windows 上，在 **Administrator PowerShell** 窗口中运行：

```powershell
# Registered AV products
Get-CimInstance -Namespace root/SecurityCenter2 -ClassName AntiVirusProduct |
  Select-Object displayName, pathToSignedProductExe

# Kernel-level filesystem filter drivers
fltmc

# Check for EDR process injection via environment variables
[System.Environment]::GetEnvironmentVariables() |
  Where-Object { $_.Keys -match "BPP|COR_PROFILER|COMPLUS|__COMPAT" }

# Windows Defender status
Get-MpComputerStatus |
  Select-Object IsTamperProtected, RealTimeProtectionEnabled, AMRunningMode
```

**如何阅读 `fltmc` 输出：** 可以忽略的标准 Windows 驱动包括 `WdFilter`、`storqosflt`、`wcifs`、`CldFlt`、`bfs`、`FileCrypt`、`luafv`、`Wof`、`FileInfo`、`npsvctrig`、`bindflt` 和 `UnionFS`。其他驱动很可能来自第三方安全软件。

**如何阅读环境变量输出：** 如果返回任何结果，说明有 EDR 产品正在向机器上的每个新进程注入代码，可能需要排除项。

## 验证排除是否生效

应用排除后，重启 Cursor，并验证 Agent 功能不会超时。如果你之前看到空的 Extension Host 日志（Cmd/Ctrl+Shift+P → “Output” → “Extension Host”），它们现在应显示正常的启动输出。

## 故障排除清单

1. 运行[上面的识别命令](https://cursor.com/docs/enterprise/endpoint-security.md#identifying-active-security-software)，确定正在运行哪些安全产品
2. 在这些产品的管理控制台中，同时添加进程排除和路径排除
3. 重启 Cursor 并测试 Agent——这是排除是否生效的决定性测试
4. 如果排除不能解决问题，[导出日志](https://cursor.com/help/troubleshooting/agent-issues.md#what-if-i-see-agent-execution-timed-out)，并携带诊断输出联系 Cursor 支持

---

## 站点地图

[所有文档页面概览](/llms.txt)
