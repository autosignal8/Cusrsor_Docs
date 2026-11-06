> 原文：https://cursor.com/docs/cli/headless

# 使用无头 CLI

在脚本和自动化工作流中使用 Cursor CLI，完成代码分析、生成和重构任务。

## 工作原理

使用[打印模式](https://cursor.com/docs/cli/using.md#non-interactive-mode)（`-p, --print`）进行非交互式脚本和自动化。

### 在脚本中修改文件

将 `--print` 与 `--force`（或 `--yolo`）结合，在脚本中修改文件：

```bash
# Enable file modifications in print mode
agent -p --force "Refactor this code to use modern ES6+ syntax"

# Without --force, changes are only proposed, not applied
agent -p "Add JSDoc comments to this file"  # Won't modify files

# Batch processing with actual file changes
find src/ -name "*.js" | while read file; do
  agent -p --force "Add comprehensive JSDoc comments to $file"
done
```

`--force` 标志允许 Agent 直接修改文件，无需确认

## 设置

完整设置细节见[安装](https://cursor.com/docs/cli/installation.md)和[身份验证](https://cursor.com/docs/cli/reference/authentication.md)。

```bash
# Install Cursor CLI (macOS, Linux, WSL)
curl https://cursor.com/install -fsS | bash

# Install Cursor CLI (Windows PowerShell)
irm 'https://cursor.com/install?win32=true' | iex

# Set API key for scripts
export CURSOR_API_KEY=your_api_key_here
agent -p "Analyze this code"
```

## 示例脚本

针对不同的脚本需求使用不同的输出格式。细节见[输出格式](https://cursor.com/docs/cli/reference/output-format.md)。

### 搜索代码库

默认情况下，`--print` 使用 `text` 格式，只给出干净的最终答案：

```bash
#!/bin/bash
# Simple codebase question - uses text format by default

agent -p "What does this codebase do?"
```

### 自动化代码审查

使用 `--output-format json` 进行结构化分析：

```bash
#!/bin/bash
# simple-code-review.sh - Basic code review script

echo "Starting code review..."

# Review recent changes
agent -p --force --output-format text \
  "Review the recent code changes and provide feedback on:
  - Code quality and readability
  - Potential bugs or issues
  - Security considerations
  - Best practices compliance

  Provide specific suggestions for improvement and write to review.txt"

if [ $? -eq 0 ]; then
  echo "✅ Code review completed successfully"
else
  echo "❌ Code review failed"
  exit 1
fi
```

### 实时进度跟踪

使用 `--output-format stream-json` 按消息跟踪进度，或加上 `--stream-partial-output` 以增量流式输出 delta：

```bash
#!/bin/bash
# stream-progress.sh - Track progress in real-time

echo "🚀 Starting stream processing..."

# Track progress in real-time
accumulated_text=""
tool_count=0
start_time=$(date +%s)

agent -p --force --output-format stream-json --stream-partial-output \
  "Analyze this project structure and create a summary report in analysis.txt" | \
  while IFS= read -r line; do
    
    type=$(echo "$line" | jq -r '.type // empty')
    subtype=$(echo "$line" | jq -r '.subtype // empty')
    
    case "$type" in
      "system")
        if [ "$subtype" = "init" ]; then
          model=$(echo "$line" | jq -r '.model // "unknown"')
          echo "🤖 Using model: $model"
        fi
        ;;
        
      "assistant")
        # Only process streaming deltas (timestamp_ms present, no model_call_id).
        # Skip buffered flushes before tool calls and at end of turn.
        has_ts=$(echo "$line" | jq 'has("timestamp_ms")')
        has_mc=$(echo "$line" | jq 'has("model_call_id")')
        if [ "$has_ts" = "true" ] && [ "$has_mc" = "false" ]; then
          content=$(echo "$line" | jq -r '.message.content[0].text // empty')
          accumulated_text="$accumulated_text$content"
          printf "\r📝 Generating: %d chars" ${#accumulated_text}
        fi
        ;;

      "tool_call")
        if [ "$subtype" = "started" ]; then
          tool_count=$((tool_count + 1))

          # Extract tool information
          if echo "$line" | jq -e '.tool_call.writeToolCall' > /dev/null 2>&1; then
            path=$(echo "$line" | jq -r '.tool_call.writeToolCall.args.path // "unknown"')
            echo -e "\n🔧 Tool #$tool_count: Creating $path"
          elif echo "$line" | jq -e '.tool_call.readToolCall' > /dev/null 2>&1; then
            path=$(echo "$line" | jq -r '.tool_call.readToolCall.args.path // "unknown"')
            echo -e "\n📖 Tool #$tool_count: Reading $path"
          fi

        elif [ "$subtype" = "completed" ]; then
          # Extract and show tool results
          if echo "$line" | jq -e '.tool_call.writeToolCall.result.success' > /dev/null 2>&1; then
            lines=$(echo "$line" | jq -r '.tool_call.writeToolCall.result.success.linesCreated // 0')
            size=$(echo "$line" | jq -r '.tool_call.writeToolCall.result.success.fileSize // 0')
            echo "   ✅ Created $lines lines ($size bytes)"
          elif echo "$line" | jq -e '.tool_call.readToolCall.result.success' > /dev/null 2>&1; then
            lines=$(echo "$line" | jq -r '.tool_call.readToolCall.result.success.totalLines // 0')
            echo "   ✅ Read $lines lines"
          fi
        fi
        ;;

      "result")
        duration=$(echo "$line" | jq -r '.duration_ms // 0')
        end_time=$(date +%s)
        total_time=$((end_time - start_time))

        echo -e "\n\n🎯 Completed in ${duration}ms (${total_time}s total)"
        echo "📊 Final stats: $tool_count tools, ${#accumulated_text} chars generated"
        ;;
    esac
  done
```

## 处理图像

要把图像、媒体文件或其他二进制数据发送给 Agent，在提示词中包含文件路径。Agent 可以通过工具调用读取任何文件，包括图像、视频和其他格式。

### 在提示词中包含文件路径

直接在提示词文本中引用文件路径。Agent 会在需要时自动读取这些文件：

```bash
# Analyze an image
agent -p "Analyze this image and describe what you see: ./screenshot.png"

# Process multiple media files
agent -p "Compare these two images and identify differences: ./before.png ./after.png"

# Combine file paths with text instructions
agent -p "Review the code in src/app.ts and the design mockup in designs/homepage.png. Suggest improvements to match the design."
```

### 工作原理

当你在提示词中包含文件路径时：

1. Agent 收到带有文件路径引用的提示词
2. Agent 使用工具调用自动读取这些文件
3. 图像会被自动处理
4. 你可以使用相对路径或绝对路径引用文件

### 示例：图像分析脚本

```bash
#!/bin/bash
# analyze-image.sh - Analyze images using the headless CLI

IMAGE_PATH="./screenshots/ui-mockup.png"

agent -p --output-format json \
  "Analyze this image and provide a detailed description: $IMAGE_PATH" | \
  jq -r '.result'
```

### 示例：批量媒体处理

```bash
#!/bin/bash
# process-media.sh - Process multiple media files

for image in images/*.png; do
  echo "Processing $image..."
  agent -p --output-format text \
    "Describe what's in this image: $image" > "${image%.png}.description.txt"
done
```

文件路径可以相对于当前工作目录，也可以是绝对路径。Agent 会通过工具调用读取文件，因此请确保文件存在，并且从你运行命令的位置可以访问。

---

## 站点地图

[所有文档页面概览](/llms.txt)
