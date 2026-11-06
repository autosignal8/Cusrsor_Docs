> 原文：https://cursor.com/docs/hooks

# Hooks

Hooks 让你用自定义脚本观察、控制和扩展 Agent 循环。在项目级或用户级的 `hooks.json` 文件里定义 Hooks，或通过插件从 **Customize** 安装。Hooks 是拉起的进程，双向通过 stdio 用 JSON 通信。它们在 Agent 循环的既定阶段之前或之后运行，可以观察、阻止或修改行为。

[媒体](/docs-static/images/agent/hooks.mp4)

有了 Hooks，你可以：

- 编辑之后运行格式化器
- 为事件加上分析
- 扫描个人身份信息（PII）或密钥
- 拦住有风险的操作（例如 SQL 写入）
- 控制 Subagent（Task 工具）的执行
- 在会话开始时注入上下文

在找可以直接用的集成？参见[合作伙伴集成](https://cursor.com/docs/hooks.md#partner-integrations)，那里有来自生态合作伙伴的安全、治理和密钥管理方案。

Cursor 支持加载来自 Claude Code 等第三方工具的 Hooks。兼容性和配置详见[第三方 Hooks](https://cursor.com/docs/reference/third-party-hooks.md)。

## Hook 类别

Hooks 按触发方式分成三类：

**Agent Hooks（Agent Chat）** 在 Agent 会话期间触发：

- `sessionStart` / `sessionEnd` - 会话生命周期管理
- `preToolUse` / `postToolUse` / `postToolUseFailure` - 通用的工具使用 Hooks（对所有工具触发）
- `subagentStart` / `subagentStop` - Subagent（Task 工具）的生命周期
- `beforeShellExecution` / `afterShellExecution` - 控制 shell 命令
- `beforeMCPExecution` / `afterMCPExecution` - 控制 MCP 工具的使用
- `beforeReadFile` / `afterFileEdit` - 控制文件访问和编辑
- `beforeSubmitPrompt` - 提交前校验提示词
- `preCompact` - 观察上下文窗口压缩
- `stop` - 处理 Agent 完成
- `afterAgentResponse` / `afterAgentThought` - 跟踪 Agent 的响应

**Tab Hooks（行内补全）** 在自主的 Tab 操作时触发：

- `beforeTabFileRead` - 控制 Tab 补全的文件访问
- `afterTabFileEdit` - 对 Tab 的编辑做后处理

**应用生命周期 Hooks** 在任何 Agent 会话之外触发：

- `workspaceOpen` - Cursor 打开工作区时触发，工作区文件夹每次变化时也会触发。可以返回额外的插件路径，供当前工作区加载。

这几套分开的 hook 面，让你可以对自主的 Tab 操作、由用户主导的 Agent 操作，以及工作区启动，分别套用不同的策略。

## Cloud Agent 支持

Cloud Agents 会运行仓库里基于命令的 Hooks。如果项目根目录的 `.cursor/hooks.json` 里定义了 Hooks，Cloud Agents 会在工作期间加载并运行。

在 Enterprise 套餐上，Cloud Agents 还会运行通过 [Web 仪表板](https://cursor.com/dashboard/team-content?section=hooks) 配置的团队 Hooks 和企业管理的 Hooks。

Cloud Agents 有时会在只读环境里开始早期的探索轮次。这些轮次期间 Hooks 不运行。等 Agent 有了可写环境，它们才开始运行。

### 支持的 Hooks

下列 Hooks 会在 Cloud Agents 中运行：

| Hook | 是否支持 |
| ---------------------- | --------- |
| `beforeShellExecution` | 是 |
| `afterShellExecution` | 是 |
| `beforeReadFile` | 是 |
| `afterFileEdit` | 是 |
| `preToolUse` | 是 |
| `postToolUse` | 是 |
| `postToolUseFailure` | 是 |
| `subagentStart` | 是 |
| `subagentStop` | 是 |
| `beforeSubmitPrompt` | 是 |
| `preCompact` | 是 |
| `afterAgentResponse` | 是 |
| `afterAgentThought` | 是 |
| `stop` | 是 |

HTTP MCP 服务器跑在 Cursor 的后端，而不是 Agent 的 VM 上。对它们的工具，`preToolUse` 和 `postToolUse` 触发时 `tool_name` 设为 `MCP:<tool_name>`，并有这些限制：

- `preToolUse` 可以拒绝这次调用，但 Cursor 会忽略 `updated_input`。
- `postToolUse` 收到的 `tool_output` 是 `{"redacted": true}`（已知时带上 `isError`），而不是工具结果，也不能用 `updated_mcp_tool_output` 替换结果。

### Cloud Agents 中不可用的 Hooks

有些 Hooks 不适用于 Cloud Agents，因为执行环境不一样：

| Hook | 原因 |
| ------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `sessionStart` | 暂缓支持，因为 Cloud Agents 仍可能在只读环境里启动。那里不会加载 Hooks，所以云端的 `sessionStart` 会触发得太晚（在第一次写入之后），而不是在会话真正开始时。 |
| `sessionEnd` | Cloud Agents 没有与编辑器生命周期绑定的会话边界。`sessionEnd` 绑的是 IDE 会话，不是 Cloud Agent 对话。 |
| `beforeMCPExecution` / `afterMCPExecution` | 暂缓支持，因为 Cloud Agents 仍可能在只读环境里启动，那里不会加载 Hooks，MCP hook 的触发时机也不明确。 |
| `beforeTabFileRead` / `afterTabFileEdit` | Tab 补全是 IDE 功能，不会在 Cloud Agents 里运行。 |
| `workspaceOpen` | 这是 IDE 生命周期 hook，不适用于 Cloud Agents。 |

### 配置来源

Cloud Agents 从这些来源加载 Hooks：

- **项目 Hooks**（仓库里的 `.cursor/hooks.json`）：在 Cloud Agent 工作期间加载并运行。
- **团队 Hooks**（Enterprise）：从仪表板分发，并在 Cloud Agents 中运行。
- **Enterprise Hooks**（Enterprise）：系统级的受管 Hooks，在 Cloud Agents 中运行。

用户级 Hooks（`~/.cursor/hooks.json`）在 Cloud Agents 里不可用。Cloud Agent 的 VM 访问不到你本地主目录里的配置。

[Self-Hosted Machines](https://cursor.com/docs/cloud-agent/self-hosted.md) 的 worker（Pools 和 My Machines）会运行同样的、基于命令的项目 Hooks；在 Enterprise 上还会运行团队和企业管理的 Hooks。在这些 worker 上，会话认领 worker 时触发 `sessionStart`，认领释放时触发 `sessionEnd`。参见 [Pools 上的 Hooks](https://cursor.com/docs/cloud-agent/self-hosted/pool.md#hooks)。

### 执行类型限制

Cloud Agents 只运行**基于命令的 Hooks**。基于提示词的 Hooks 需要在 hook 和 Agent 循环之间接上认证，云端执行环境里没有这套接线。

## 快速开始

创建一个 `hooks.json` 文件。可以建在项目级（`<project>/.cursor/hooks.json`），或建在主目录（`~/.cursor/hooks.json`）。项目级 Hooks 只对该项目生效，主目录里的 Hooks 全局生效。

### 用户 Hooks（\~/.cursor/）

要做全局生效的用户级 Hooks，创建 `~/.cursor/hooks.json`：

```json
{
  "version": 1,
  "hooks": {
    "afterFileEdit": [{ "command": "./hooks/format.sh" }]
  }
}
```

把 hook 脚本创建在 `~/.cursor/hooks/format.sh`：

```bash
#!/bin/bash
# Read input, do something, exit 0
cat > /dev/null
exit 0
```

赋予可执行权限：

```bash
chmod +x ~/.cursor/hooks/format.sh
```

### 项目 Hooks（.cursor/）

要做只对某个仓库生效的项目级 Hooks，创建 `<project>/.cursor/hooks.json`：

```json
{
  "version": 1,
  "hooks": {
    "afterFileEdit": [{ "command": ".cursor/hooks/format.sh" }]
  }
}
```

注意：项目 Hooks 从**项目根目录**运行，所以用 `.cursor/hooks/format.sh`（而不是 `./hooks/format.sh`）。

把 hook 脚本创建在 `<project>/.cursor/hooks/format.sh`：

```bash
#!/bin/bash
# Read input, do something, exit 0
cat > /dev/null
exit 0
```

赋予可执行权限：

```bash
chmod +x .cursor/hooks/format.sh
```

Cursor 会监视 Hooks 配置文件并自动重新加载。每次文件编辑之后，你的 hook 都会运行。

## Hook 类型

Hooks 支持两种执行类型：基于命令（默认）和基于提示词（由 LLM 评估）。

### 基于命令的 Hooks

命令 Hooks 执行 shell 脚本：从 stdin 接收 JSON 输入，从 stdout 返回 JSON 输出。在 Windows 上，桌面应用通过 PowerShell 运行 hook 命令，并把 JSON 通过管道送进命令，因此 PowerShell 脚本从 `$input` 读取，其他程序从 stdin 读取。

```json
{
  "version": 1,
  "hooks": {
    "beforeShellExecution": [
      {
        "command": "./scripts/approve-network.sh",
        "timeout": 30,
        "matcher": "curl|wget|nc"
      }
    ]
  }
}
```

**退出码行为：**

- 退出码 `0` - Hook 成功，使用 JSON 输出。对于权限类 Hooks（`beforeShellExecution`、`beforeMCPExecution`、`beforeReadFile`、`beforeTabFileRead`、`subagentStart`、`preToolUse`），无效 JSON 或与该 hook schema 不符的响应会阻止该操作。
- 退出码 `2` - 阻止该操作（等同于返回 `permission: "deny"`）
- 其他退出码 - Hook 失败，操作继续（默认 fail-open）

### 基于提示词的 Hooks

提示词 Hooks 用 LLM 评估一段自然语言条件。适合在不写自定义脚本的情况下执行策略。

```json
{
  "version": 1,
  "hooks": {
    "beforeShellExecution": [
      {
        "type": "prompt",
        "prompt": "Does this command look safe to execute? Only allow read-only operations.",
        "timeout": 10
      }
    ]
  }
}
```

**特性：**

- 返回结构化的 `{ ok: boolean, reason?: string }` 响应
- 用一个更快的模型做快速评估
- `$ARGUMENTS` 占位符会自动替换为 hook 输入 JSON
- 如果没有 `$ARGUMENTS`，hook 输入会自动追加到末尾
- 可选的 `model` 字段用来覆盖默认的 LLM 模型

## 示例

下面的例子使用 `./hooks/...` 路径，适用于**用户 Hooks**（`~/.cursor/hooks.json`），脚本从 `~/.cursor/` 运行。**项目 Hooks**（`<project>/.cursor/hooks.json`）请改用 `.cursor/hooks/...` 路径，因为脚本从项目根目录运行。

```json title="hooks.json"
{
  "version": 1,
  "hooks": {
    "sessionStart": [
      {
        "command": "./hooks/session-init.sh"
      }
    ],
    "sessionEnd": [
      {
        "command": "./hooks/audit.sh"
      }
    ],
    "beforeShellExecution": [
      {
        "command": "./hooks/audit.sh"
      },
      {
        "command": "./hooks/block-git.sh"
      }
    ],
    "beforeMCPExecution": [
      {
        "command": "./hooks/audit.sh"
      }
    ],
    "afterShellExecution": [
      {
        "command": "./hooks/audit.sh"
      }
    ],
    "afterMCPExecution": [
      {
        "command": "./hooks/audit.sh"
      }
    ],
    "afterFileEdit": [
      {
        "command": "./hooks/audit.sh"
      }
    ],
    "beforeSubmitPrompt": [
      {
        "command": "./hooks/audit.sh"
      }
    ],
    "preCompact": [
      {
        "command": "./hooks/audit.sh"
      }
    ],
    "stop": [
      {
        "command": "./hooks/audit.sh"
      }
    ],
    "beforeTabFileRead": [
      {
        "command": "./hooks/redact-secrets-tab.sh"
      }
    ],
    "afterTabFileEdit": [
      {
        "command": "./hooks/format-tab.sh"
      }
    ]
  }
}
```

```sh title="audit.sh"
#!/bin/bash

# audit.sh - Hook script that writes all JSON input to /tmp/agent-audit.log
# This script is designed to be called by Cursor's hooks system for auditing purposes

# Read JSON input from stdin
json_input=$(cat)

# Create timestamp for the log entry
timestamp=$(date '+%Y-%m-%d %H:%M:%S')

# Create the log directory if it doesn't exist
mkdir -p "$(dirname /tmp/agent-audit.log)"

# Write the timestamped JSON entry to the audit log
echo "[$timestamp] $json_input" >> /tmp/agent-audit.log

# Exit successfully
exit 0
```

```sh title="block-git.sh"
#!/bin/bash

# Hook to block git commands and redirect to gh tool usage
# This hook implements the beforeShellExecution hook from the Cursor Hooks Spec

# Initialize debug logging
echo "Hook execution started" >> /tmp/hooks.log

# Read JSON input from stdin
input=$(cat)
echo "Received input: $input" >> /tmp/hooks.log

# Parse the command from the JSON input
command=$(echo "$input" | jq -r '.command // empty')
echo "Parsed command: '$command'" >> /tmp/hooks.log

# Check if the command contains 'git' or 'gh'
if [[ "$command" =~ git[[:space:]] ]] || [[ "$command" == "git" ]]; then
    echo "Git command detected - blocking: '$command'" >> /tmp/hooks.log
    # Block the git command and provide guidance to use gh tool instead
    cat << EOF
{
  "continue": true,
  "permission": "deny",
  "user_message": "Git command blocked. Please use the GitHub CLI (gh) tool instead.",
  "agent_message": "The git command '$command' has been blocked by a hook. Instead of using raw git commands, please use the 'gh' tool which provides better integration with GitHub and follows best practices. For example:\n- Instead of 'git clone', use 'gh repo clone'\n- Instead of 'git push', use 'gh repo sync' or the appropriate gh command\n- For other git operations, check if there's an equivalent gh command or use the GitHub web interface\n\nThis helps maintain consistency and leverages GitHub's enhanced tooling."
}
EOF
elif [[ "$command" =~ gh[[:space:]] ]] || [[ "$command" == "gh" ]]; then
    echo "GitHub CLI command detected - asking for permission: '$command'" >> /tmp/hooks.log
    # Ask for permission for gh commands
    cat << EOF
{
  "continue": true,
  "permission": "ask",
  "user_message": "GitHub CLI command requires permission: $command",
  "agent_message": "The command '$command' uses the GitHub CLI (gh) which can interact with your GitHub repositories and account. Please review and approve this command if you want to proceed."
}
EOF
else
    echo "Non-git/non-gh command detected - allowing: '$command'" >> /tmp/hooks.log
    # Allow non-git/non-gh commands
    cat << EOF
{
  "continue": true,
  "permission": "allow"
}
EOF
fi
```

### TypeScript 的 stop 自动化 hook

需要类型化 JSON、持久的文件 I/O，以及在同一个 hook 里发 HTTP 调用时，选 TypeScript。这个由 Bun 驱动的 `stop` hook 会在磁盘上记录每个对话的失败次数，把结构化遥测转发到内部 API，并在 Agent 连续失败两次时自动安排重试。

```json title="hooks.json"
{
  "version": 1,
  "hooks": {
    "stop": [
      {
        "command": "bun run .cursor/hooks/track-stop.ts --stop"
      }
    ]
  }
}
```

```ts title=".cursor/hooks/track-stop.ts"
import { mkdir, readFile, writeFile } from 'node:fs/promises';
import { stdin } from 'bun';

type StopHookInput = {
  conversation_id: string;
  generation_id: string;
  model: string;
  model_id?: string;
  model_params?: Array<{ id: string; value: string }>;
  status: 'completed' | 'aborted' | 'error';
  loop_count: number;
};

type StopHookOutput = {
  followup_message?: string;
};

type MetricsEntry = {
  lastStatus: StopHookInput['status'];
  errorCount: number;
  lastUpdatedIso: string;
};

type MetricsStore = Record<string, MetricsEntry>;

const STATE_DIR = '.cursor/hooks/state';
const METRICS_PATH = `${STATE_DIR}/agent-metrics.json`;
const TELEMETRY_URL = Bun.env.AGENT_TELEMETRY_URL;

async function parseHookInput<T>(): Promise<T> {
  const text = await stdin.text();
  return JSON.parse(text) as T;
}

async function readMetrics(): Promise<MetricsStore> {
  try {
    return JSON.parse(await readFile(METRICS_PATH, 'utf8')) as MetricsStore;
  } catch {
    return {};
  }
}

async function writeMetrics(store: MetricsStore) {
  await mkdir(STATE_DIR, { recursive: true });
  await writeFile(METRICS_PATH, JSON.stringify(store, null, 2), 'utf8');
}

async function sendTelemetry(payload: StopHookInput, entry: MetricsEntry) {
  if (!TELEMETRY_URL) return;
  await fetch(TELEMETRY_URL, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      conversationId: payload.conversation_id,
      generationId: payload.generation_id,
      model: payload.model,
      modelId: payload.model_id,
      modelParams: payload.model_params,
      status: payload.status,
      errorCount: entry.errorCount,
      loopCount: payload.loop_count,
      timestamp: entry.lastUpdatedIso
    })
  });
}

async function main() {
  const payload = await parseHookInput<StopHookInput>();
  const metrics = await readMetrics();
  const entry =
    metrics[payload.conversation_id] ?? {
      lastStatus: payload.status,
      errorCount: 0,
      lastUpdatedIso: ''
    };

  entry.lastStatus = payload.status;
  entry.lastUpdatedIso = new Date().toISOString();
  entry.errorCount = payload.status === 'error' ? entry.errorCount + 1 : 0;

  metrics[payload.conversation_id] = entry;
  await writeMetrics(metrics);
  await sendTelemetry(payload, entry);

  const response: StopHookOutput = {};
  if (entry.errorCount >= 2 && payload.loop_count < 4) {
    response.followup_message =
      'Automated retry triggered after two failures. Double-check credentials before running again.';
  }

  process.stdout.write(JSON.stringify(response) + '\n');
}

main().catch(error => {
  console.error('[stop hook] failed', error);
  process.stdout.write('{}\n');
});
```

把 `AGENT_TELEMETRY_URL` 设为应接收运行摘要的内部端点。

### Python 清单防护 hook

需要丰富的解析库时，Python 很合适。这个 hook 用 `pyyaml` 在 `kubectl apply` 运行前检查 Kubernetes 清单；Bash 很难安全地解析多文档 YAML。

```json title="hooks.json"
{
  "version": 1,
  "hooks": {
    "beforeShellExecution": [
      {
        "command": "python3 .cursor/hooks/kube_guard.py"
      }
    ]
  }
}
```

```python title=".cursor/hooks/kube_guard.py"
#!/usr/bin/env python3
import json
import shlex
import sys
from pathlib import Path

import yaml

SENSITIVE_NAMESPACES = {"prod", "production"}

def main() -> None:
    payload = json.load(sys.stdin)
    command = payload.get("command", "")
    cwd = Path(payload.get("cwd") or ".")
    response = {"continue": True, "permission": "allow"}

    try:
        args = shlex.split(command)
    except ValueError:
        print(json.dumps(response))
        return

    if len(args) < 2 or args[0] != "kubectl" or args[1] != "apply" or "-f" not in args:
        print(json.dumps(response))
        return

    f_index = args.index("-f")
    if f_index + 1 >= len(args):
        print(json.dumps(response))
        return

    manifest_arg = args[f_index + 1]
    manifest_path = (cwd / manifest_arg).resolve()

    if not manifest_path.exists():
        print(json.dumps(response))
        return

    cli_namespace = None
    for i, arg in enumerate(args):
        if arg in ("-n", "--namespace") and i + 1 < len(args):
            cli_namespace = args[i + 1]
        elif arg.startswith("--namespace="):
            cli_namespace = arg.split("=", 1)[1]
        elif arg.startswith("-n="):
            cli_namespace = arg.split("=", 1)[1]

    try:
        documents = list(yaml.safe_load_all(manifest_path.read_text()))
    except (OSError, yaml.YAMLError) as exc:
        sys.stderr.write(f"Failed to read/parse {manifest_path}: {exc}\n")
        print(json.dumps(response))
        return

    if cli_namespace in SENSITIVE_NAMESPACES or any(
        (doc or {}).get("metadata", {}).get("namespace") in SENSITIVE_NAMESPACES
        for doc in documents
    ):
        response.update(
            {
                "permission": "ask",
                "user_message": "kubectl apply to prod requires manual approval.",
                "agent_message": f"{manifest_path.name} includes protected namespaces; confirm with your team before continuing.",
            }
        )

    print(json.dumps(response))

if __name__ == "__main__":
    main()
```

在 hook 脚本运行的环境里安装 PyYAML（例如 `pip install pyyaml`），解析器的 import 才能成功。

## 合作伙伴集成

我们与已经为 Cursor 做好 Hooks 支持的生态厂商合作。这些集成覆盖安全扫描、治理、密钥管理等。

### MCP 治理与可见性

| 合作伙伴 | 说明 |
| --------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| [MintMCP](https://www.mintmcp.com/blog/mcp-governance-cursor-hooks) | 建立 MCP 服务器的完整清单，监控工具使用模式，并在敏感数据到达 AI 模型之前扫描响应。 |
| [Oasis Security](https://www.oasis.security/blog/cursor-oasis-governing-agentic-access) | 对 AI Agent 的操作执行最小权限策略，并在企业系统中保留完整的审计轨迹。 |
| [Runlayer](https://www.runlayer.com/blog/cursor-hooks) | 包装 MCP 工具，并接入他们的 MCP broker，对 Agent 与工具之间的交互做集中控制和可见性。 |

### 代码安全与最佳实践

| 合作伙伴 | 说明 |
| ---------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| [Corridor](https://corridor.dev/blog/corridor-cursor-hooks/) | 在代码编写过程中，对代码实现和安全设计决策给出实时反馈。 |
| [Semgrep](https://semgrep.dev/blog/2025/cursor-hooks-mcp-server) | 自动扫描 AI 生成的代码中的漏洞，并用实时反馈重新生成代码，直到安全问题解决。 |

### 依赖安全

| 合作伙伴 | 说明 |
| ------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| [Endor Labs](https://www.endorlabs.com/learn/bringing-malware-detection-into-ai-coding-workflows-with-cursor-hooks) | 拦截软件包安装并扫描恶意依赖，在供应链攻击进入代码库之前加以阻止。 |

### Agent 安全与防护

| 合作伙伴 | 说明 |
| ---------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| [Snyk](https://snyk.io/blog/evo-agent-guard-cursor-integration/) | 用 Evo Agent Guard 实时审查 Agent 的操作，检测并阻止提示词注入和危险工具调用之类的问题。 |

### 密钥管理

| 合作伙伴 | 说明 |
| ----------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [1Password](https://marketplace.1password.com/integration/cursor-hooks) | 在 shell 命令执行前，校验来自 1Password Environments 的环境文件已经正确挂载，从而在不把凭据写到磁盘的情况下实现即时的密钥访问。 |

关于 Hooks 合作伙伴的更多细节，见博客文章[面向安全与平台团队的 Hooks](/blog/hooks-partners)。

## 配置

在 `hooks.json` 文件里定义 Hooks。配置可以存在于多个层级。每个来源里所有匹配的 Hooks 都会运行，Cursor 会合并它们的响应：不论来源，任何 `deny` 都优先于 `ask`，`ask` 优先于 `allow`。`user_message` 和 `agent_message` 的值会拼接在一起。对于其他字段，例如 `followup_message`，以后到的响应为准。响应按下面的优先级顺序合并，因此对这些字段来说，优先级更低的来源会覆盖优先级更高的来源：

```sh
~/.cursor/
├── hooks.json
└── hooks/
    ├── audit.sh
    └── block-git.sh
```

- **Enterprise**（由 MDM 管理，系统级）：
  - macOS：`/Library/Application Support/Cursor/hooks.json`
  - Linux/WSL：`/etc/cursor/hooks.json`
  - Windows：`C:\\ProgramData\\Cursor\\hooks.json`
- **Team**（云端分发，仅 Enterprise）：
  - 在 [Web 仪表板](https://cursor.com/dashboard/team-content?section=hooks) 中配置，并自动同步到全体团队成员
- **Project**（特定项目）：
  - `<project-root>/.cursor/hooks.json`
  - 项目 Hooks 在任何受信任的工作区里运行，并随项目一起提交进版本控制
- **User**（特定用户）：
  - `~/.cursor/hooks.json`

优先级（从高到低）：Enterprise → Team → Project → User

已安装 [Plugins](https://cursor.com/docs/plugins.md) 的 Hooks 排在上述全部之后。插件 Hooks 只运行命令 Hooks。

`hooks` 对象把 hook 名称映射到 hook 定义数组。每个定义目前支持 `command` 属性，它可以是 shell 字符串、绝对路径或相对路径。工作目录取决于 hook 来源：

- **项目 Hooks**（仓库里的 `.cursor/hooks.json`）：从**项目根目录**运行
- **用户 Hooks**（`~/.cursor/hooks.json`）：从 `~/.cursor/` 运行
- **Enterprise Hooks**（系统级配置）：从 enterprise 配置目录运行
- **团队 Hooks**（云端分发）：从受管 hooks 目录运行
- **插件 Hooks**：从插件的安装目录运行，`CURSOR_PLUGIN_ROOT` 指向该目录。插件的 `stop` 和 `subagentStop` Hooks 从工作区根目录运行。

项目 Hooks 要用类似 `.cursor/hooks/script.sh` 的路径（相对于项目根目录），不要用 `./hooks/script.sh`（那会去找 `<project>/hooks/script.sh`）。

### 配置文件

下面的例子是用户级 hooks 文件（`~/.cursor/hooks.json`）。项目级 Hooks 要把 `./hooks/script.sh` 这类路径改成 `.cursor/hooks/script.sh`：

```json
{
  "version": 1,
  "hooks": {
    "sessionStart": [{ "command": "./session-init.sh" }],
    "sessionEnd": [{ "command": "./audit.sh" }],
    "preToolUse": [
      {
        "command": "./hooks/validate-tool.sh",
        "matcher": "Shell|Read|Write"
      }
    ],
    "postToolUse": [{ "command": "./hooks/audit-tool.sh" }],
    "subagentStart": [{ "command": "./hooks/validate-subagent.sh" }],
    "subagentStop": [{ "command": "./hooks/audit-subagent.sh" }],
    "beforeShellExecution": [{ "command": "./script.sh" }],
    "afterShellExecution": [{ "command": "./script.sh" }],
    "afterMCPExecution": [{ "command": "./script.sh" }],
    "afterFileEdit": [{ "command": "./format.sh" }],
    "preCompact": [{ "command": "./audit.sh" }],
    "stop": [{ "command": "./audit.sh", "loop_limit": 10 }],
    "beforeTabFileRead": [{ "command": "./redact-secrets-tab.sh" }],
    "afterTabFileEdit": [{ "command": "./format-tab.sh" }],
    "workspaceOpen": [{ "command": "./register-workspace-plugins.sh" }]
  }
}
```

Agent Hooks（`sessionStart`、`sessionEnd`、`preToolUse`、`postToolUse`、`postToolUseFailure`、`subagentStart`、`subagentStop`、`beforeShellExecution`、`afterShellExecution`、`beforeMCPExecution`、`afterMCPExecution`、`beforeReadFile`、`afterFileEdit`、`beforeSubmitPrompt`、`preCompact`、`stop`、`afterAgentResponse`、`afterAgentThought`）适用于 Agent Chat 操作。Tab Hooks（`beforeTabFileRead`、`afterTabFileEdit`）专门用于行内 Tab 补全。应用生命周期 hook（`workspaceOpen`）在工作区打开时、以及工作区文件夹变化时触发，与任何 Agent 会话无关。

### 全局配置选项

| 选项 | 类型 | 默认值 | 说明 |
| --------- | ------ | -------- | ------------------------------------------------------------ |
| `version` | number | 必填 | 配置 schema 的版本。必须是正整数（使用 `1`）。 |

### 按脚本的配置选项

| 选项 | 类型 | 默认值 | 说明 |
| ------------ | ------------------------- | ---------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `command` | string | 必填 | 脚本路径或命令 |
| `type` | `"command"` \| `"prompt"` | `"command"` | Hook 的执行类型 |
| `timeout` | number | 平台默认值 | 执行超时，单位为秒 |
| `loop_limit` | number \| null | `5` | stop/subagentStop Hooks 的单脚本循环上限。`null` 表示没有上限。Cursor Hooks 的默认值是 `5`，Claude Code Hooks 的默认值是 `null`。 |
| `failClosed` | boolean | `false` | 为 `true` 时，hook 失败（崩溃、超时、非零退出码、没有输出）会阻止操作，而不是放行。权限类 Hooks 即使此项为 `false`，遇到无效 JSON 或无效响应也会阻止。适合对安全性要求高的 Hooks。 |
| `matcher` | string | - | 用来过滤 hook 何时运行的正则。空字符串或 `"*"` 匹配全部。 |

### Matcher 配置

Matcher 用来过滤 hook 何时运行。Matcher 是一个正则字符串；空字符串或 `"*"` 匹配全部。正则拿去比对的值取决于 hook：

```json
{
  "version": 1,
  "hooks": {
    "preToolUse": [
      {
        "command": "./validate-shell.sh",
        "matcher": "Shell"
      }
    ],
    "subagentStart": [
      {
        "command": "./validate-explore.sh",
        "matcher": "explore|shell"
      }
    ],
    "beforeShellExecution": [
      {
        "command": "./approve-network.sh",
        "matcher": "curl|wget|nc "
      }
    ]
  }
}
```

- **subagentStart**：matcher 比对的是 **Subagent 类型**（例如 `explore`、`shell`、`generalPurpose`）。用它让 hook 只在启动某一种 Subagent 时运行。上面的例子只对 explore 或 shell Subagent 运行 `validate-explore.sh`。
- **beforeShellExecution**：matcher 比对的是 **shell 命令**字符串。用它让 hook 只在命令匹配某种模式时运行（例如网络调用、删除文件）。上面的例子只在命令包含 `curl`、`wget` 或 `nc ` 时运行 `approve-network.sh`。

**按 hook 可用的 matcher：**

- **preToolUse / postToolUse / postToolUseFailure**：按工具类型过滤。取值包括 `Shell`、`Read`、`Write`、`Grep`、`Delete`、`Task`，以及使用 `MCP:<tool_name>` 格式的 MCP 工具。
- **subagentStart / subagentStop**：按 Subagent 类型过滤（`generalPurpose`、`explore`、`shell` 等）。
- **beforeShellExecution / afterShellExecution**：按 shell 命令文本过滤；matcher 比对的是完整命令字符串。
- **beforeReadFile**：比对的值是 `Read`。
- **afterFileEdit**：比对的值是 `Write`。
- **beforeTabFileRead**：比对的值是 `TabRead`。
- **afterTabFileEdit**：比对的值是 `TabWrite`。
- **beforeSubmitPrompt**：比对的值是 `UserPromptSubmit`。
- **stop**：比对的值是 `Stop`。
- **afterAgentResponse**：比对的值是 `AgentResponse`。
- **afterAgentThought**：比对的值是 `AgentThought`。

## 团队分发

可以用项目 Hooks（通过版本控制）、MDM 工具，或 Cursor 的云端分发系统，把 Hooks 分发给团队成员。

### 项目 Hooks（版本控制）

项目 Hooks 是和团队共享 Hooks 的最简单办法。把 `hooks.json` 放在 `<project-root>/.cursor/hooks.json`，并提交到仓库。团队成员在受信任的工作区里打开项目时，Cursor 会自动加载并运行项目 Hooks。

Cloud Agents 在云端处理你的仓库时，也会加载这些项目 Hooks。

项目 Hooks：

- 与代码一起放在版本控制里
- 在受信任的工作区里为全体团队成员自动加载
- 可以按项目区分（例如，为某个代码库强制执行格式化标准）
- 出于安全考虑，工作区必须受信任才会运行

### MDM 分发

用移动设备管理（MDM）工具在整个组织里分发 Hooks。把 `hooks.json` 文件和 hook 脚本放到每台机器上的目标目录。

**用户主目录**（按用户分发）：

- `~/.cursor/hooks.json`
- `~/.cursor/hooks/`（hook 脚本）

**全局目录**（系统级分发）：

- macOS：`/Library/Application Support/Cursor/hooks.json`
- Linux/WSL：`/etc/cursor/hooks.json`
- Windows：`C:\\ProgramData\\Cursor\\hooks.json`

注意：基于 MDM 的分发完全由你的组织管理。Cursor 不会通过你的 MDM 方案部署或管理文件。请确保内部 IT 或安全团队按照组织的策略处理配置、部署和更新。

### 云端分发（仅 Enterprise）

Enterprise 团队可以用 Cursor 自带的云端分发，把 Hooks 自动同步给全体团队成员。在 [Web 仪表板](https://cursor.com/dashboard/team-content?section=hooks) 里配置 Hooks。团队成员登录时，Cursor 会把配置好的 Hooks 自动下发到所有客户端机器。

云端分发提供：

- 自动同步到全体团队成员（每 30 分钟）
- 按操作系统定向，用于特定平台的 Hooks
- 通过仪表板集中管理

仪表板上的每个 hook 在一个 hook 步骤上只跑一个脚本或一条提示词。仪表板 Hooks 不接受按 hook 设置的选项，例如 `failClosed` 或 `matcher`。

桌面应用把每个命令 hook 写到 `~/.cursor/managed/team_<team ID>/hooks/`，并在每次同步时重写该文件夹。在 Windows 上，没有 `.ps1`、`.bat` 或 `.cmd` 扩展名的脚本名会被加上 `.ps1`。

默认情况下，桌面应用在发送提示词之前只会短暂等待 Hooks 加载。在仪表板的 hook 设置里打开 **Block until hooks are ready**，它就会一直等到 enterprise、团队、项目和用户 Hooks 都加载完，没有超时，这样每次提示词都会跑这些 Hooks，包括重启后的第一条。

Enterprise 管理员可以在仪表板上创建、编辑和管理团队 Hooks，不需要接触具体的机器。

[联系销售](https://cursor.com/contact-sales?source=docs-hooks-cloud)以获取 Enterprise 的云端 hook 分发。

## 参考

### 通用 schema

#### 输入（所有 Hooks）

除了各自专有的字段，所有 Hooks 都会收到一组基础字段：

```json
{
  "conversation_id": "string",
  "generation_id": "string",
  "model": "string",
  "model_id": "string",
  "model_params": [{ "id": "string", "value": "string" }],
  "hook_event_name": "string",
  "cursor_version": "string",
  "workspace_roots": ["<path>"],
  "user_email": "string | null",
  "transcript_path": "string | null"
}
```

| 字段 | 类型 | 说明 |
| ----------------- | ----------------- | --------------------------------------------------------------------------------------------------------- |
| `conversation_id` | string | 跨多轮保持稳定的对话 ID |
| `generation_id` | string | 当前这一代，每次用户消息都会变 |
| `model` | string | 触发该 hook 的 composer 所配置的旧版模型 slug |
| `model_id` | string（可选） | 所选模型的结构化 ID（如果有） |
| `model_params` | array（可选） | 所选模型参数，例如 thinking、context 或 effort。每一项都有 `id` 和 `value`。 |
| `hook_event_name` | string | 正在运行的是哪个 hook |
| `cursor_version` | string | Cursor 应用版本（例如 "1.7.2"） |
| `workspace_roots` | string\[] | 工作区里的根文件夹列表（通常只有一个，多根工作区可以有多个） |
| `user_email` | string \| null | 已认证用户的邮箱地址（如果有） |
| `transcript_path` | string \| null | 主对话记录文件的路径（禁用记录时为 null） |

应用生命周期 Hooks（`workspaceOpen`）在任何 Agent 会话之外触发，因此请求里不含 `conversation_id`、`generation_id`、`model`、`session_id` 和 `transcript_path`。它们仍然会收到 `hook_event_name`、`cursor_version`、`workspace_roots` 和 `user_email`。

### Hook 事件

#### preToolUse

在任何工具执行之前调用。这是一个通用 hook，对所有工具类型都会触发（Shell、Read、Write、MCP、Task 等）。用 matcher 按具体工具过滤。

```json
// Input
{
  "tool_name": "Shell",
  "tool_input": { "command": "npm install", "working_directory": "/project" },
  "tool_use_id": "abc123",
  "cwd": "/project",
  "model": "claude-opus-4-7-thinking-max",
  "model_id": "claude-opus-4-7",
  "model_params": [
    { "id": "thinking", "value": "true" },
    { "id": "context", "value": "1m" },
    { "id": "effort", "value": "max" }
  ],
  "agent_message": "Installing dependencies..."
}

// Output
{
  "permission": "allow" | "deny",
  "user_message": "<message shown in client when denied>",
  "agent_message": "<message sent to agent when denied>",
  "updated_input": { "command": "npm ci" }
}
```

| 输出字段 | 类型 | 说明 |
| --------------- | ----------------- | ------------------------------------------------------------------------------------------------------------------- |
| `permission` | string | `"allow"` 表示继续，`"deny"` 表示阻止。schema 接受 `"ask"`，但 `preToolUse` 目前并不执行它。 |
| `user_message` | string（可选） | 操作被拒绝时显示给用户的消息 |
| `agent_message` | string（可选） | 操作被拒绝时反馈给 Agent 的消息 |
| `updated_input` | object（可选） | 改用这份修改后的工具输入 |

#### postToolUse

工具成功执行之后调用。适合审计、分析和注入上下文。

```json
// Input
{
  "tool_name": "Shell",
  "tool_input": { "command": "npm test" },
  "tool_output": "{\"exitCode\":0,\"stdout\":\"All tests passed\"}",
  "tool_use_id": "abc123",
  "cwd": "/project",
  "duration": 5432,
  "model": "claude-opus-4-7-thinking-max",
  "model_id": "claude-opus-4-7",
  "model_params": [
    { "id": "thinking", "value": "true" },
    { "id": "context", "value": "1m" },
    { "id": "effort", "value": "max" }
  ]
}

// Output
{
  "updated_mcp_tool_output": { "modified": "output" },
  "additional_context": "Test coverage report attached."
}
```

| 输入字段 | 类型 | 说明 |
| ------------- | ------ | --------------------------------------------------------------------- |
| `duration` | number | 执行时间，单位毫秒 |
| `tool_output` | string | 工具返回的结果载荷，已经 JSON 字符串化（不是终端原文） |

| 输出字段 | 类型 | 说明 |
| ------------------------- | ----------------- | ------------------------------------------------------------------ |
| `updated_mcp_tool_output` | object（可选） | 仅 MCP 工具：替换模型看到的工具输出 |
| `additional_context` | string（可选） | 在工具结果之后注入对话的额外上下文 |

#### postToolUseFailure

工具失败、超时或被拒绝时调用。适合错误跟踪和恢复逻辑。

```json
// Input
{
  "tool_name": "Shell",
  "tool_input": { "command": "npm test" },
  "tool_use_id": "abc123",
  "cwd": "/project",
  "error_message": "Command timed out after 30s",
  "failure_type": "timeout" | "error" | "permission_denied",
  "duration": 5000,
  "is_interrupt": false
}

// Output
{
  "additional_context": "Tests time out on CI runners. Retry with --maxWorkers=2."
}
```

| 输入字段 | 类型 | 说明 |
| --------------- | ------- | ----------------------------------------------------------------- |
| `error_message` | string | 失败说明 |
| `failure_type` | string | 失败类型：`"error"`、`"timeout"` 或 `"permission_denied"` |
| `duration` | number | 到失败发生为止的时间，单位毫秒 |
| `is_interrupt` | boolean | 这次失败是否由用户中断或取消引起 |

| 输出字段 | 类型 | 说明 |
| -------------------- | ----------------- | ------------------------------------------------------------------------- |
| `additional_context` | string（可选） | 在失败的工具结果之后注入对话的额外上下文 |

#### subagentStart

在生成 Subagent（Task 工具）之前调用。可以允许或拒绝创建 Subagent。

```json
// Input
{
  "subagent_id": "abc-123",
  "subagent_type": "generalPurpose",
  "task": "Explore the authentication flow",
  "parent_conversation_id": "conv-456",
  "tool_call_id": "tc-789",
  "subagent_model": "claude-sonnet-4-20250514",
  "is_parallel_worker": false,
  "git_branch": "feature/auth"
}

// Output
{
  "permission": "allow" | "deny",
  "user_message": "<message shown when denied>"
}
```

| 输入字段 | 类型 | 说明 |
| ------------------------ | ----------------- | ------------------------------------------------------------ |
| `subagent_id` | string | 这个 Subagent 实例的唯一标识符 |
| `subagent_type` | string | Subagent 类型：`generalPurpose`、`explore`、`shell` 等 |
| `task` | string | 交给 Subagent 的任务描述 |
| `parent_conversation_id` | string | 父 Agent 会话的对话 ID |
| `tool_call_id` | string | 触发该 Subagent 的工具调用 ID |
| `subagent_model` | string | Subagent 将使用的模型 |
| `is_parallel_worker` | boolean | 这个 Subagent 是否作为并行 worker 运行 |
| `git_branch` | string（可选） | Subagent 将在其上操作的 Git 分支（如果适用） |

| 输出字段 | 类型 | 说明 |
| -------------- | ----------------- | ----------------------------------------------------------------------------------------------------------------- |
| `permission` | string | `"allow"` 表示继续，`"deny"` 表示阻止。`subagentStart` 不支持 `"ask"`，会把它当成 `"deny"`。 |
| `user_message` | string（可选） | Subagent 被拒绝时显示给用户的消息 |

#### subagentStop

Subagent 完成、出错或被中止时调用。可以触发后续动作。

```json
// Input
{
  "subagent_type": "generalPurpose",
  "status": "completed" | "error" | "aborted",
  "task": "Explore the authentication flow",
  "description": "Exploring auth flow",
  "summary": "<subagent output summary>",
  "duration_ms": 45000,
  "message_count": 12,
  "tool_call_count": 8,
  "loop_count": 0,
  "modified_files": ["src/auth.ts"],
  "agent_transcript_path": "/path/to/subagent/transcript.txt"
}

// Output
{
  "followup_message": "<auto-continue with this message>"
}
```

| 输入字段 | 类型 | 说明 |
| ----------------------- | -------------- | ------------------------------------------------------------------------------------------------ |
| `subagent_type` | string | Subagent 类型：`generalPurpose`、`explore`、`shell` 等 |
| `status` | string | `"completed"`、`"error"` 或 `"aborted"` |
| `task` | string | 交给 Subagent 的任务描述 |
| `description` | string | Subagent 用途的简短描述 |
| `summary` | string | Subagent 的输出摘要 |
| `duration_ms` | number | 执行时间，单位毫秒 |
| `message_count` | number | Subagent 会话期间交换的消息数 |
| `tool_call_count` | number | Subagent 发起的工具调用次数 |
| `loop_count` | number | 这个 Subagent 已经因 `subagentStop` 的后续消息触发过的次数（从 0 开始） |
| `modified_files` | string\[] | Subagent 修改过的文件 |
| `agent_transcript_path` | string \| null | Subagent 自己的记录文件路径（与父对话分开） |

| 输出字段 | 类型 | 说明 |
| ------------------ | ----------------- | ------------------------------------------------------------------------------ |
| `followup_message` | string（可选） | 用这条消息自动继续。仅当 `status` 为 `"completed"` 时才会被使用。 |

`followup_message` 字段让 Subagent 完成后触发下一轮，从而做成循环式流程。后续消息与 `stop` hook 使用同一个可配置的循环上限（默认 5，通过 `loop_limit` 配置）。

`subagentStop` 在 Subagent 每次结束时都会触发，因此一个带着后续消息继续的 Subagent 会再次触发它，并且 `loop_count` 加一。当一次 `subagentStop` 调用没有返回 `followup_message`，或达到循环上限时，这个 Subagent 才算结束。

#### beforeShellExecution / beforeMCPExecution

在任何 shell 命令或 MCP 工具执行之前调用。返回一个权限决定。

无效的 JSON，或与该 hook schema 不符的响应，会阻止该操作。崩溃、超时，以及除 `2` 以外的非零退出码，默认 fail-open：Cursor 记下失败并放行该操作。在 hook 定义上设置 `failClosed: true`，这些失败也会被阻止。对安全性要求高的 `beforeMCPExecution` Hooks，建议这么做。

```json
// beforeShellExecution input
{
  "command": "<full terminal command>",
  "cwd": "<current working directory>",
  "sandbox": false
}

// beforeMCPExecution input
{
  "tool_name": "<tool name>",
  "tool_input": "<json params>",
  "mcp_server_name": "<server name from mcp.json>"
}
// Plus either (HTTP/SSE servers):
{ "url": "<server url>", "mcp_server_url": "<server url>" }
// Or (stdio servers):
{ "command": "<launch command and args>" }

// Output
{
  "permission": "allow" | "deny" | "ask",
  "user_message": "<message shown in client>",
  "agent_message": "<message sent to agent>"
}
```

| 字段 | 类型 | 说明 |
| ----------------- | ------ | ---------------------------------------------------------------------------------------------------- |
| `tool_name` | string | 即将运行的 MCP 工具名称 |
| `tool_input` | string | 将传给该工具的 JSON 参数字符串 |
| `mcp_server_name` | string | 该服务器在其 `mcp.json` 中的键（例如 `linear`）。用它识别某一台具体的服务器。 |
| `mcp_server_url` | string | 服务器 URL，仅 HTTP/SSE 服务器有 |
| `url` | string | 与 `mcp_server_url` 相同；仅 HTTP/SSE 服务器有 |
| `command` | string | stdio 启动命令和参数，用空格拼在一起；仅 stdio 服务器有 |

用 `mcp_server_name`（以及 `tool_name`）判断一次调用是不是打到你的服务器。`command` 是服务器配置里的启动字符串，不同安装之间可能不一样：相对路径、`${CURSOR_PLUGIN_ROOT}` 展开，或者 HTTP 传输（这种根本没有 `command`）。如果一个 hook 会放行它认不出来的东西，就应该把缺失或不符合预期的 `mcp_server_name` 当成拒绝。

#### afterShellExecution

shell 命令执行之后触发；适合审计，或从命令输出收集指标。

```json
// Input
{
  "command": "<full terminal command>",
  "output": "<full terminal output>",
  "duration": 1234,
  "sandbox": false
}
```

| 字段 | 类型 | 说明 |
| ---------- | ------- | ---------------------------------------------------------------------------------------- |
| `command` | string | 已执行的完整终端命令 |
| `output` | string | 从终端捕获的完整输出 |
| `duration` | number | 执行该 shell 命令所花的毫秒数（不含等待批准的时间） |
| `sandbox` | boolean | 命令是否在沙箱环境中运行 |

#### afterMCPExecution

MCP 工具执行之后触发；包含工具的输入参数和完整的 JSON 结果。

```json
// Input
{
  "tool_name": "<tool name>",
  "tool_input": "<json params>",
  "mcp_server_name": "<server name from mcp.json>",
  "result_json": "<tool result json>",
  "duration": 1234
}
```

| 字段 | 类型 | 说明 |
| ----------------- | ------ | ----------------------------------------------------------------------------------- |
| `tool_name` | string | 已执行的 MCP 工具名称 |
| `tool_input` | string | 传给该工具的 JSON 参数字符串 |
| `mcp_server_name` | string | 该服务器在其 `mcp.json` 中的键 |
| `mcp_server_url` | string | 服务器 URL，仅 HTTP/SSE 服务器有 |
| `result_json` | string | 工具响应的 JSON 字符串 |
| `duration` | number | 执行该 MCP 工具所花的毫秒数（不含等待批准的时间） |

#### afterFileEdit

Agent 编辑文件之后触发；适合格式化器，或统计 Agent 写下的代码。

```json
// Input
{
  "file_path": "<absolute path>",
  "edits": [{ "old_string": "<search>", "new_string": "<replace>" }]
}
```

#### beforeReadFile

在 Agent 读取文件之前调用。用于访问控制，阻止敏感文件被发给模型。

无效的 JSON，或与该 hook schema 不符的响应，会阻止这次读取。崩溃、超时，以及除 `2` 以外的非零退出码会被记下，读取默认放行。在 hook 定义上设置 `failClosed: true`，这些失败也会阻止读取。

```json
// Input
{
  "file_path": "<absolute path>",
  "content": "<file contents>",
  "attachments": [
    {
      "type": "file" | "rule",
      "file_path": "<absolute path>"
    }
  ]
}

// Output
{
  "permission": "allow" | "deny",
  "user_message": "<message shown when denied>"
}
```

| 输入字段 | 类型 | 说明 |
| ------------- | ------ | ----------------------------------------------------------------------------------------------------------------- |
| `file_path` | string | 正在读取的文件的绝对路径 |
| `content` | string | 文件的全部内容 |
| `attachments` | array | 与提示词关联的上下文附件。每一项有 `type`（`"file"` 或 `"rule"`）和 `file_path`。 |

| 输出字段 | 类型 | 说明 |
| -------------- | ----------------- | --------------------------------------- |
| `permission` | string | `"allow"` 表示继续，`"deny"` 表示阻止 |
| `user_message` | string（可选） | 拒绝时显示给用户的消息 |

#### beforeTabFileRead

在 Tab（行内补全）读取文件之前调用。在 Tab 访问文件内容之前做脱敏或访问控制。

**与 `beforeReadFile` 的主要差别：**

- 只由 Tab 触发，不是 Agent
- 不含 `attachments` 字段（Tab 不用提示词附件）
- 适合对自主的 Tab 操作套用不同的策略

```json
// Input
{
  "file_path": "<absolute path>",
  "content": "<file contents>"
}

// Output
{
  "permission": "allow" | "deny"
}
```

#### afterTabFileEdit

Tab（行内补全）编辑文件之后调用。适合格式化器，或审计 Tab 写下的代码。

**与 `afterFileEdit` 的主要差别：**

- 只由 Tab 触发，不是 Agent
- 包含详细的编辑信息：`range`、`old_line` 和 `new_line`，便于精确跟踪编辑
- 适合对 Tab 编辑做细粒度的格式化或分析

```json
// Input
{
  "file_path": "<absolute path>",
  "edits": [
    {
      "old_string": "<search>",
      "new_string": "<replace>",
      "range": {
        "start_line_number": 10,
        "start_column": 5,
        "end_line_number": 10,
        "end_column": 20
      },
      "old_line": "<line before edit>",
      "new_line": "<line after edit>"
    }
  ]
}

// Output
{
  // No output fields currently supported
}
```

#### beforeSubmitPrompt

用户按下发送之后、后端请求之前立刻调用。可以阻止提交。

```json
// Input
{
  "prompt": "<user prompt text>",
  "attachments": [
    {
      "type": "file" | "rule",
      "file_path": "<absolute path>"
    }
  ]
}

// Output
{
  "continue": true | false,
  "user_message": "<message shown to user when blocked>"
}
```

| 输出字段 | 类型 | 说明 |
| -------------- | ----------------- | ---------------------------------------------------- |
| `continue` | boolean | 是否允许继续提交提示词 |
| `user_message` | string（可选） | 提示词被阻止时显示给用户的消息 |

#### afterAgentResponse

Agent 完成一条助手消息之后调用。

```json
// Input
{
  "text": "<assistant final text>"
}
```

#### afterAgentThought

Agent 完成一个思考块之后调用。适合观察 Agent 的推理过程。

```json
// Input
{
  "text": "<fully aggregated thinking text>",
  "duration_ms": 5000
}

// Output
{
  // No output fields currently supported
}
```

| 字段 | 类型 | 说明 |
| ------------- | ----------------- | ------------------------------------------------------ |
| `text` | string | 这个已完成思考块的完整聚合文本 |
| `duration_ms` | number（可选） | 该思考块的时长，单位毫秒 |

#### stop

Agent 循环结束时调用。可以选择自动提交一条后续用户消息，让它继续迭代。

```json
// Input
{
  "status": "completed" | "aborted" | "error",
  "loop_count": 0
}
```

```json
// Output
{
  "followup_message": "<message text>"
}
```

- 可选的 `followup_message` 是字符串。提供了且非空时，Cursor 会自动把它作为下一条用户消息提交。这样就能做循环式流程（例如一直迭代，直到达成目标）。
- `loop_count` 字段表示这个 stop hook 已经为该对话触发过多少次自动后续消息（从 0 开始）。每个脚本默认最多 5 次自动后续，可用 `loop_limit` 选项配置。把 `loop_limit` 设为 `null` 即取消上限。`subagentStop` 的后续消息使用同一上限。

#### sessionStart

创建新的 composer 对话时调用。这个 hook 按即发即忘的方式运行；Agent 循环不会等待，也不会强制要求一个阻塞式响应。用它设置本次会话专用的环境变量，或注入额外上下文。

```json
// Input
{
  "session_id": "<unique session identifier>",
  "is_background_agent": true | false,
  "composer_mode": "agent" | "ask" | "edit"
}
```

```json
// Output
{
  "env": { "<key>": "<value>" },
  "additional_context": "<context to add to conversation>"
}
```

| 输入字段 | 类型 | 说明 |
| --------------------- | ----------------- | ------------------------------------------------------------------- |
| `session_id` | string | 本次会话的唯一标识符（与 `conversation_id` 相同） |
| `is_background_agent` | boolean | 这是后台 Agent 会话，还是交互式会话 |
| `composer_mode` | string（可选） | composer 启动时所处的模式（例如 "agent"、"ask"、"edit"） |

| 输出字段 | 类型 | 说明 |
| -------------------- | ----------------- | ------------------------------------------------------------------------------------------ |
| `env` | object（可选） | 为本次会话设置的环境变量。之后的全部 hook 执行都能用到 |
| `additional_context` | string（可选） | 要加到对话初始系统上下文中的额外上下文 |

schema 也接受 `continue` 和 `user_message` 字段，但目前的调用方并不执行它们。即使 `continue` 为 `false`，会话创建也不会被阻止。

#### sessionEnd

composer 对话结束时调用。这是一个即发即忘的 hook，适合日志、分析或清理。响应会被记录，但不会被使用。

```json
// Input
{
  "session_id": "<unique session identifier>",
  "reason": "completed" | "aborted" | "error" | "window_close" | "user_close",
  "duration_ms": 45000,
  "is_background_agent": true | false,
  "final_status": "<status string>",
  "error_message": "<error details if reason is 'error'>"
}
```

```json
// Output
{
  // No output fields - fire and forget
}
```

| 输入字段 | 类型 | 说明 |
| --------------------- | ----------------- | ----------------------------------------------------------------------------------------- |
| `session_id` | string | 正在结束的会话的唯一标识符 |
| `reason` | string | 会话如何结束："completed"、"aborted"、"error"、"window\_close" 或 "user\_close" |
| `duration_ms` | number | 会话总时长，单位毫秒 |
| `is_background_agent` | boolean | 这是否是后台 Agent 会话 |
| `final_status` | string | 会话的最终状态 |
| `error_message` | string（可选） | reason 为 "error" 时的错误消息 |

#### preCompact

在上下文窗口压缩或摘要发生之前调用。这是一个观察型 hook，不能阻止或修改压缩行为。适合在压缩发生时记日志，或通知用户。

```json
// Input
{
  "trigger": "auto" | "manual",
  "context_usage_percent": 85,
  "context_tokens": 120000,
  "context_window_size": 128000,
  "message_count": 45,
  "messages_to_compact": 30,
  "is_first_compaction": true | false
}
```

```json
// Output
{
  "user_message": "<message to show when compaction occurs>"
}
```

| 输入字段 | 类型 | 说明 |
| ----------------------- | ------- | ---------------------------------------------------------- |
| `trigger` | string | 是什么触发了压缩："auto" 或 "manual" |
| `context_usage_percent` | number | 当前上下文窗口用量的百分比（0-100） |
| `context_tokens` | number | 当前上下文窗口的 token 数 |
| `context_window_size` | number | 上下文窗口的最大 token 数 |
| `message_count` | number | 对话中的消息数 |
| `messages_to_compact` | number | 将被摘要的消息数 |
| `is_first_compaction` | boolean | 这是否是该对话的第一次压缩 |

| 输出字段 | 类型 | 说明 |
| -------------- | ----------------- | -------------------------------------------------- |
| `user_message` | string（可选） | 发生压缩时显示给用户的消息 |

#### workspaceOpen

Cursor 打开工作区时触发一次，之后每次工作区文件夹变化再触发一次。窗口里没有任何工作区文件夹时会跳过。在 Cursor 桌面应用和 CLI 中运行。

```json
// Input
{
  "hook_event_name": "workspaceOpen",
  "cursor_version": "string",
  "workspace_roots": ["<absolute path>"],
  "user_email": "string | null"
}

// Output
{
  "pluginPaths": ["<absolute path>", "..."]
}
```

| 输出字段 | 类型 | 说明 |
| ------------- | -------------------- | ----------------------------------------------------------------------- |
| `pluginPaths` | string\[]（可选） | 要为当前工作区加载的插件目录的绝对路径。 |

## 环境变量

Hook 脚本执行时会收到这些环境变量：

| 变量 | 说明 | 是否总是存在 |
| ------------------------ | ------------------------------------------------------------- | ---------------------- |
| `CURSOR_PROJECT_DIR` | 工作区根目录 | 是 |
| `CURSOR_VERSION` | Cursor 版本字符串 | 是 |
| `CURSOR_USER_EMAIL` | 已认证用户的邮箱 | 已登录时 |
| `CURSOR_TRANSCRIPT_PATH` | 对话记录文件的路径 | 启用记录时 |
| `CURSOR_CODE_REMOTE` | 在远程工作区中运行时设为字符串 `"true"` | 远程工作区 |
| `CURSOR_PLUGIN_ROOT` | 提供该 hook 的插件的安装目录 | 插件 Hooks |
| `CLAUDE_PROJECT_DIR` | 项目目录的别名（Claude 兼容） | 是 |

`sessionStart` Hooks 设置的、作用域限于本次会话的环境变量，会传给该会话中之后的每一次 hook 执行。

## 故障排除

**如何确认 Hooks 已生效**

**Customize** 里有一个 Hooks 标签页，以及一个 Hooks 输出通道，用来调试已配置和已执行的 Hooks，并查看错误。

**如果 Hooks 没有工作**

- Cursor 会监视 `hooks.json` 文件，并在保存时重新加载。如果 Hooks 仍然没有加载，重启 Cursor。
- 检查相对路径对你的 hook 来源是否正确：
  - **项目 Hooks** 的路径相对于**项目根目录**（例如 `.cursor/hooks/script.sh`）
  - **用户 Hooks** 的路径相对于 `~/.cursor/`（例如 `./hooks/script.sh` 或 `hooks/script.sh`）

**退出码阻止**

命令 Hooks 的退出码 `2` 会阻止该操作（等同于返回 `permission: "deny"`）。这与 Claude Code 的行为一致，以便兼容。

### Enterprise Hooks 与分发

云端分发和全团队的 hook 管理在 Enterprise 上可用。

---

## 站点地图

[所有文档页面概览](/llms.txt)
