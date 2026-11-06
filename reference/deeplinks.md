> 原文：https://cursor.com/docs/reference/deeplinks

# Deeplinks

Deeplink 让你可以与他人分享提示词、命令和规则，从而在团队和社区之间协作并共享知识。

链接也可以通过 [cursor.com](https://cursor.com) 打开。把路径和 URL 参数附加到 URL 末尾，例如：[cursor.com/link/prompt?text=...](https://cursor.com/link/prompt?text=Research+and+find+one+bug+in+this+codebase)

分享之前，请务必检查你的提示词和命令，确保其中不包含 API key、密码或专有代码等敏感信息。

## 提示词

分享提示词，让其他人可以针对特定任务或工作流快速上手。当有人点击提示词 Deeplink 时，会打开 Cursor，并在聊天中预填该提示词。用户必须先查看并确认该提示词，然后才会执行。Deeplink 从不会触发自动执行。

研究并找出这个代码库中的一个 bug

### 演练场

### TypeScript

```typescript
const IS_WEB = false; // Set to true for web format

function generatePromptDeeplink(promptText: string): string {
  const baseUrl = IS_WEB
    ? 'https://cursor.com/link/prompt'
    : 'cursor://anysphere.cursor-deeplink/prompt';
  const url = new URL(baseUrl);
  url.searchParams.set('text', promptText);
  return url.toString();
}

const deeplink = generatePromptDeeplink("Create a React component for user authentication");
console.log(deeplink);
```

### Python

```python
from urllib.parse import urlencode, urlparse, urlunparse

IS_WEB = False  # Set to True for web format

def generate_prompt_deeplink(prompt_text: str) -> str:
    base_url = "https://cursor.com/link/prompt" if IS_WEB else "cursor://anysphere.cursor-deeplink/prompt"
    params = {"text": prompt_text}
    query_string = urlencode(params)
    return f"{base_url}?{query_string}"

deeplink = generate_prompt_deeplink("Create a React component for user authentication")
print(deeplink)
```

## 命令

分享其他人可以在自己的 Cursor 环境中直接执行的命令。命令 Deeplink 让你可以分享定义在 `.cursor/commands` 目录中的自定义命令。当有人点击命令 Deeplink 时，会打开 Cursor，并用指定的名称和内容创建一个新命令。用户必须先查看并确认该命令，然后才会执行。

debug-api

添加 console.log 语句来调试 API 响应

### 演练场

### TypeScript

```typescript
const IS_WEB = false; // Set to true for web format

function generateCommandDeeplink(commandName: string, commandContent: string): string {
  const baseUrl = IS_WEB
    ? 'https://cursor.com/link/command'
    : 'cursor://anysphere.cursor-deeplink/command';
  const url = new URL(baseUrl);
  url.searchParams.set('name', commandName);
  url.searchParams.set('text', commandContent);
  return url.toString();
}

const deeplink = generateCommandDeeplink("debug-api", "Add console.log statements to debug API responses");
console.log(deeplink);
```

### Python

```python
from urllib.parse import urlencode, urlparse, urlunparse

IS_WEB = False  # Set to True for web format

def generate_command_deeplink(command_name: str, command_content: str) -> str:
    base_url = "https://cursor.com/link/command" if IS_WEB else "cursor://anysphere.cursor-deeplink/command"
    params = {"name": command_name, "text": command_content}
    query_string = urlencode(params)
    return f"{base_url}?{query_string}"

deeplink = generate_command_deeplink("debug-api", "Add console.log statements to debug API responses")
print(deeplink)
```

## 规则

分享其他人可以添加到自己的 Cursor 环境中的规则。规则 Deeplink 让你可以分享定义在 `.cursor/rules` 目录中的自定义规则。当有人点击规则 Deeplink 时，会打开 Cursor，并用指定的名称和内容创建一条新规则。用户必须先查看并确认该规则，然后才会添加。

typescript-strict

始终使用严格的 TypeScript 类型，并避免 'any'

### 演练场

### TypeScript

```typescript
const IS_WEB = false; // Set to true for web format

function generateRuleDeeplink(ruleName: string, ruleContent: string): string {
  const baseUrl = IS_WEB
    ? 'https://cursor.com/link/rule'
    : 'cursor://anysphere.cursor-deeplink/rule';
  const url = new URL(baseUrl);
  url.searchParams.set('name', ruleName);
  url.searchParams.set('text', ruleContent);
  return url.toString();
}

const deeplink = generateRuleDeeplink("typescript-strict", "Always use strict TypeScript types and avoid 'any'");
console.log(deeplink);
```

### Python

```python
from urllib.parse import urlencode, urlparse, urlunparse

IS_WEB = False  # Set to True for web format

def generate_rule_deeplink(rule_name: str, rule_content: str) -> str:
    base_url = "https://cursor.com/link/rule" if IS_WEB else "cursor://anysphere.cursor-deeplink/rule"
    params = {"name": rule_name, "text": rule_content}
    query_string = urlencode(params)
    return f"{base_url}?{query_string}"

deeplink = generate_rule_deeplink("typescript-strict", "Always use strict TypeScript types and avoid 'any'")
print(deeplink)
```

## 常见问题

### Deeplink URL 的最大长度是多少？

Deeplink URL 的最大长度为 10,000 个字符。以编程方式生成 Deeplink 时，请确保内容在经过 URL 编码后不超过此限制。上方的交互式生成器会在你输入时显示当前 URL 长度以及剩余字符数。

### 如何在网页上使用 Deeplink，而不是在 Cursor 应用中使用？

你可以把 Deeplink 协议换成网页链接，方法是把基础 URL 从 `cursor://anysphere.cursor-deeplink/` 改为 `https://cursor.com/link/`。例如：

```text
cursor://anysphere.cursor-deeplink/prompt?text=Hello%20world
```

```text
https://cursor.com/link/prompt?text=Hello%20world
```

网页链接会把用户重定向到 cursor.com，他们可以在浏览器中打开该 Deeplink，或复制后在 Cursor 中使用。

---

## 站点地图

[所有文档页面概览](/llms.txt)
