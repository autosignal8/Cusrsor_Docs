> 原文：https://cursor.com/docs

# Cursor 文档

Cursor 是一款用于构建宏大软件的编程 Agent。用它来理解代码库、规划并构建功能、修复 bug、审查改动，并与你已经在用的工具一起工作。

![欢迎使用 Cursor，这款 AI 编辑器与编程 Agent](/docs-static/images/agent/homepage-hero.png)

## 从这里开始

### 入门

从安装走到你在 Cursor 里的第一次有用改动

### 模型与定价

比较模型、用量池和方案价格

### 更新日志

了解最新功能与改进

## 你可以用 Cursor 做什么

### 理解你的代码

理清一个仓库是如何组织在一起的，并找到合适的起点

### 规划并构建功能

划定改动范围，使用 Plan Mode，更有把握地交付更大的工作

### 查找并修复 bug

复现问题，缩小根因，并验证修复

### 审查改动

查看 diff、运行检查，在合并前发现问题

### 自定义 Cursor

在一个地方添加 Plugins、Skills、MCP 和 Rules

### 连接你的工作流

与 GitHub、GitLab、Azure DevOps、Bitbucket、JetBrains、Slack、Linear 等一起工作

## 模型

所有模型属性见[模型与定价](https://cursor.com/docs/models-and-pricing.md)页面。

| 模型 | 提供商 | 默认上下文 | 最大上下文 | 能力 | 说明 |
| --- | --- | --- | --- | --- | --- |
| [Claude 4 Sonnet](https://www.anthropic.com/claude/sonnet) | Anthropic | 200k | - | Agent, Thinking, Images | 默认隐藏；在旧版定价中，Thinking 变体按 2 次请求计费 |
| [Claude 4 Sonnet 1M](https://www.anthropic.com/claude/sonnet) | Anthropic | - | 1M | Agent, Thinking, Images | 默认隐藏；在旧版定价中，Thinking 变体按 2 次请求计费；由于上下文窗口很大，此模型可能非常昂贵；当输入超过 200k tokens 时，费用为 2 倍 |
| [Claude 4.5 Haiku](https://www.anthropic.com/claude/haiku) | Anthropic | 200k | - | Thinking, Images | 默认隐藏；Bedrock/Vertex：区域端点加收 10%；缓存：写入 1.25 倍，读取 0.1 倍 |
| [Claude 4.5 Opus](https://www.anthropic.com/claude/opus) | Anthropic | 200k | 200k | Agent, Thinking, Images | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode |
| [Claude 4.5 Sonnet](https://www.anthropic.com/claude/sonnet) | Anthropic | 200k | 1M | Agent, Thinking, Images | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode；扩展上下文最多支持 1M tokens，按相同的每 token 费率计费（无长上下文附加费） |
| [Claude 4.6 Opus](https://www.anthropic.com/claude/opus) | Anthropic | 200k | 1M | Agent, Thinking, Images | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode；扩展上下文最多支持 1M tokens，按相同的每 token 费率计费（无长上下文附加费） |
| [Claude 4.6 Sonnet](https://www.anthropic.com/claude/sonnet) | Anthropic | 200k | 1M | Agent, Thinking, Images | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode；扩展上下文最多支持 1M tokens，按相同的每 token 费率计费（无长上下文附加费） |
| [Claude 4.7 Opus](https://www.anthropic.com/claude/opus) | Anthropic | 300k | 1M | Agent, Thinking, Images | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode；扩展上下文最多支持 1M tokens，按相同的每 token 费率计费（无长上下文附加费） |
| [Claude Fable 5](https://www.anthropic.com/claude/fable) | Anthropic | 300k | 1M | Agent, Thinking, Images | 默认隐藏；Enterprise 客户，以及启用了 Privacy Mode 的 Teams 与个人客户，需要数据保留审批；Anthropic 会存储 Agent 的输入与输出数据，用于伤害预防流程；这些数据不会用于训练或改进 Anthropic 的模型或产品；触发安全护栏的请求会自动路由到 Claude Opus；费用大约是 Claude Opus 5 的 2 倍；在旧版按请求计费的方案上需要 Max Mode |
| [Claude Fable 5.1](https://www.anthropic.com/claude/fable) | Anthropic | 300k | 1M | Agent, Thinking, Images | Enterprise 客户，以及启用了 Privacy Mode 的 Teams 与个人客户，需要数据保留审批；Anthropic 会存储 Agent 的输入与输出数据，用于伤害预防流程；这些数据不会用于训练或改进 Anthropic 的模型或产品；触发安全护栏的请求会自动路由到 Claude Opus；提示缓存读取为 $0.25/M，比标准缓存读取费率低 75%；输入与输出费用大约是 Claude Opus 5.5 的 2.5 倍；在旧版按请求计费的方案上需要 Max Mode |
| [Claude Haiku 5.5](https://www.anthropic.com/claude/haiku) | Anthropic | 300k | 1M | Agent, Thinking, Images | 在旧版按请求计费的方案上需要 Max Mode；输入超过 100k tokens 的请求按标准费率的 5 倍计费（输入 $0.50/M，缓存写入 $0.625/M，缓存读取 $0.05/M，输出 $2.50/M）；对于输入不超过 100k tokens 的请求，输入与输出比 Claude 4.5 Haiku 便宜 90% |
| [Claude Opus 4.7 (fast mode)](https://www.anthropic.com/claude/opus) | Anthropic | 200k | 1M | Agent, Thinking, Images | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode；有限的研究预览；扩展上下文最多 1M tokens，按与较短上下文相同的每 token 费率计费 |
| [Claude Opus 4.8](https://www.anthropic.com/claude/opus) | Anthropic | 300k | 1M | Agent, Thinking, Images | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode；Fast mode（\`claude-opus-4-8-fast\`）在旧版按请求计费的方案上需要 Max Mode；Fast mode 的每 token 定价比 Opus 4.7 的 fast mode 低 3 倍；扩展上下文最多支持 1M tokens，按相同的每 token 费率计费（无长上下文附加费） |
| [Claude Opus 5](https://www.anthropic.com/claude/opus) | Anthropic | 300k | 1M | Agent, Thinking, Images | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode；Fast mode（\`claude-opus-5-fast\`）在旧版按请求计费的方案上需要 Max Mode；扩展上下文最多支持 1M tokens，按相同的每 token 费率计费（无长上下文附加费） |
| [Claude Opus 5.5](https://www.anthropic.com/claude/opus) | Anthropic | 300k | 1M | Agent, Thinking, Images | 在旧版按请求计费的方案上需要 Max Mode；Fast mode（\`claude-opus-5-5-fast\`）在旧版按请求计费的方案上需要 Max Mode；输入与输出比 Claude Opus 5 便宜 20%；提示缓存读取为 $0.20/M（输入价格的 0.05 倍），低于 Claude Opus 5 上输入价格的 0.10 倍；区域端点与仅限美国的端点价格高 10%（输入 $4.40/M，输出 $22/M）；扩展上下文最多支持 1M tokens，按相同的每 token 费率计费（无长上下文附加费） |
| [Claude Sonnet 5](https://www.anthropic.com/claude/sonnet) | Anthropic | 200k | 1M | Agent, Thinking, Images | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode；扩展上下文最多支持 1M tokens，按相同的每 token 费率计费（无长上下文附加费）；使用了更新后的分词器，因此相同输入可能会对应更多 tokens |
| [Claude Sonnet 5.5](https://www.anthropic.com/claude/sonnet) | Anthropic | 200k | 1M | Agent, Thinking, Images | 在旧版按请求计费的方案上需要 Max Mode；输入与输出费率与 Claude Sonnet 5 相同，缓存读取为 $0.10/M，而非 $0.20/M；仅限美国的端点价格高 10%（输入 $2.20/M，输出 $11/M）；扩展上下文最多支持 1M tokens，按相同的每 token 费率计费（无长上下文附加费） |
| [Composer 1](https://cursor.com) | Cursor | 200k | - | Agent, Images | 默认隐藏 |
| [Composer 2.5](https://cursor.com/blog/composer-2-5) | Cursor | 200k | - | Agent, Thinking, Images | - |
| [DeepSeek V4.1 Flash 1017](https://huggingface.co/deepseek-ai/DeepSeek-V4.1-Flash-1017) | DeepSeek | 1M | - | Agent, Thinking | 默认隐藏 |
| [Gemini 2.5 Flash](https://developers.googleblog.com/en/start-building-with-gemini-25-flash/) | Google | 200k | 1M | Agent, Thinking, Images | 默认隐藏 |
| [Gemini 3 Flash](https://ai.google.dev/gemini-api/docs) | Google | 200k | 1M | Agent, Thinking, Images | 默认隐藏 |
| [Gemini 3 Pro](https://ai.google.dev/gemini-api/docs) | Google | 200k | 1M | Agent, Thinking, Images | 默认隐藏 |
| [Gemini 3 Pro Image Preview](https://ai.google.dev/gemini-api/docs) | Google | 200k | 1M | Images | 默认隐藏；原生图像生成模型，针对速度、灵活性与上下文理解做了优化；文本输入与输出价格与 Gemini 3 Pro 相同；图像输出：$120/1M tokens（约每张 1K/2K 图像 $0.134，约每张 4K 图像 $0.24）；预览模型在变为稳定版本之前可能会变化，并且速率限制更严格 |
| [Gemini 3.1 Pro](https://ai.google.dev/gemini-api/docs) | Google | 200k | 1M | Agent, Thinking, Images | - |
| [Gemini 3.5 Flash](https://ai.google.dev/gemini-api/docs) | Google | 200k | 1M | Agent, Thinking, Images | 默认隐藏 |
| [Gemini 3.5 Flash Lite](https://ai.google.dev/gemini-api/docs/models/gemini-3.5-flash-lite) | Google | - | - | - | 默认隐藏；仅用于 Auto-review 分类器。不能作为聊天或 Agent 模型选择 |
| [Gemini 3.6 Flash](https://ai.google.dev/gemini-api/docs) | Google | 200k | 1M | Agent, Thinking, Images | 默认隐藏 |
| [Gemini 3.7 Flash](https://ai.google.dev/gemini-api/docs) | Google | 200k | 1M | Agent, Thinking, Images | 默认隐藏 |
| [Gemini 3.8 Flash](https://ai.google.dev/gemini-api/docs) | Google | 200k | 1M | Agent, Thinking, Images | - |
| [Gemini 4 Argon](https://ai.google.dev/gemini-api/docs) | Google | 200k | 1M | Agent, Thinking, Images | - |
| [GLM 5.2](https://z.ai) | Z.ai | 200k | - | Agent, Thinking | 默认隐藏 |
| [GLM 5.3](https://z.ai) | Z.ai | 1M | - | Agent, Thinking | 默认隐藏；每 token 费率与 GLM 5.2 相同 |
| [GLM 5.3 Flash](https://z.ai) | Z.ai | 1M | - | Agent, Thinking, Images | 默认隐藏 |
| [GPT-5](https://openai.com/index/gpt-5/) | OpenAI | 272k | - | Agent, Thinking, Images | 默认隐藏；具备 Agent 与推理能力；可用的推理强度变体为 gpt-5-high |
| [GPT-5 Fast](https://openai.com/index/gpt-5/) | OpenAI | 272k | - | Agent, Thinking, Images | 默认隐藏；速度更快，但价格为 2 倍；可用的推理强度变体为 gpt-5-high-fast、gpt-5-low-fast |
| [GPT-5 Mini](https://openai.com/index/gpt-5/) | OpenAI | 272k | - | Agent, Thinking, Images | 默认隐藏 |
| [GPT-5-Codex](https://platform.openai.com/docs/models/gpt-5-codex) | OpenAI | 272k | - | Agent, Thinking, Images | 默认隐藏；具备 Agent 与推理能力 |
| [GPT-5.1 Codex](https://platform.openai.com/docs/models/gpt-5-codex) | OpenAI | 272k | - | Agent, Thinking, Images | 默认隐藏；具备 Agent 与推理能力 |
| [GPT-5.1 Codex Max](https://platform.openai.com/docs/models/gpt-5-codex) | OpenAI | 272k | - | Agent, Thinking, Images | 默认隐藏 |
| [GPT-5.1 Codex Mini](https://platform.openai.com/docs/models/gpt-5-codex) | OpenAI | 272k | - | Agent, Thinking, Images | 默认隐藏；具备 Agent 与推理能力；速率限制是 GPT-5.1 Codex 的 4 倍 |
| [GPT-5.2](https://openai.com/index/gpt-5/) | OpenAI | 272k | - | Agent, Thinking, Images | 默认隐藏；具备 Agent 与推理能力；可用的推理强度变体为 gpt-5.2-high |
| [GPT-5.2 Codex](https://platform.openai.com/docs/models/gpt-5-codex) | OpenAI | 272k | - | Agent, Thinking, Images | 默认隐藏；具备 Agent 与推理能力 |
| [GPT-5.3 Codex](https://platform.openai.com/docs/models/gpt-5-codex) | OpenAI | 272k | - | Agent, Thinking, Images | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode；具备 Agent 与推理能力；可用的推理强度变体为 gpt-5.3-codex-high |
| [GPT-5.4](https://developers.openai.com/api/docs/models/gpt-5.4) | OpenAI | 272k | 1M | Agent, Thinking, Images | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode；具备 Agent 与推理能力；缓存输入 tokens 享有 90% 折扣；Fast mode 速度快 15%，价格为 2 倍；长上下文最多支持 1M tokens，输入价格为 2 倍 |
| [GPT-5.4 Mini](https://developers.openai.com/api/docs/models/gpt-5.4-mini) | OpenAI | 272k | - | Agent, Thinking, Images | 默认隐藏；GPT-5.4 更小、更快的变体；缓存输入 tokens 享有 90% 折扣 |
| [GPT-5.4 Nano](https://developers.openai.com/api/docs/models/gpt-5.4-nano) | OpenAI | 272k | - | Agent, Thinking, Images | 默认隐藏；最小的 GPT-5.4 变体，针对成本优化；缓存输入 tokens 享有 90% 折扣 |
| [GPT-5.5](https://developers.openai.com/api/docs/models/gpt-5.5) | OpenAI | 272k | 1M | Agent, Thinking, Images | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode；具备 Agent 与推理能力；在同类任务上比 GPT-5.4 更节省 tokens；在长时间运行的任务上持续性更好；可使用 Fast mode，费率更高；长上下文最多支持 1M tokens，输入价格为 2 倍 |
| [GPT-5.6 Luna](https://openai.com/index/previewing-gpt-5-6-sol/) | OpenAI | 272k | 1M | Agent, Thinking, Images | 在旧版按请求计费的方案上需要 Max Mode；最小的 GPT-5.6 变体，针对成本与速度优化；具备 Agent 与推理能力；可使用 Fast mode，价格为 2 倍；长上下文最多支持 1M tokens，输入价格为 2 倍；长上下文（>272k）也可使用 Fast mode，按 Fast 输入价格的 2 倍计费；缓存写入按未缓存输入费率的 1.25 倍计费 |
| [GPT-5.6 Sol](https://openai.com/index/previewing-gpt-5-6-sol/) | OpenAI | 272k | 1M | Agent, Thinking, Images | 在旧版按请求计费的方案上需要 Max Mode；具备 Agent 与推理能力；可使用 Fast mode，价格为 2 倍；长上下文最多支持 1M tokens，输入价格为 2 倍；长上下文（>272k）也可使用 Fast mode，按 Fast 输入价格的 2 倍计费；缓存写入按未缓存输入费率的 1.25 倍计费；促销价格持续至 2026 年 11 月 21 日 |
| [GPT-5.6 Terra](https://openai.com/index/previewing-gpt-5-6-sol/) | OpenAI | 272k | 1M | Agent, Thinking, Images | 在旧版按请求计费的方案上需要 Max Mode；介于 Sol 与 Luna 之间的中档 GPT-5.6 变体；具备 Agent 与推理能力；可使用 Fast mode，价格为 2 倍；长上下文最多支持 1M tokens，输入价格为 2 倍；长上下文（>272k）也可使用 Fast mode，按 Fast 输入价格的 2 倍计费；缓存写入按未缓存输入费率的 1.25 倍计费 |
| Grok 4.5 | Cursor | 256k | - | Agent, Thinking | 由 Cursor 与 SpaceXAI 联合训练 |
| Grok 4.6 | Cursor | 256k | - | Agent, Thinking | 由 Cursor 与 SpaceXAI 联合训练 |
| [Grok 4.7](https://x.ai/news/grok-4-7) | Cursor | 256k | 500k | Agent, Thinking | 由 Cursor 与 SpaceXAI 联合训练；长上下文（输入 tokens >256k）按标准费率的 2 倍计费，最高 500k；可使用 Fast mode，价格为 2 倍；长上下文（>256k）的 Fast mode 按标准费率的 3 倍计费 |
| Kimi K2.7 Code | Moonshot | 262k | - | Agent, Thinking, Images | 默认隐藏 |
| [Kimi K3](https://www.moonshot.ai) | Moonshot | 200k | 1M | Agent, Thinking, Images | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode；扩展上下文最多支持 1M tokens，按相同的每 token 费率计费（无长上下文附加费）；无单独的缓存写入费用 |
| [Muse Spark 1.3](https://dev.meta.ai/docs/models) | Meta | 300k | 1M | Agent, Thinking, Images | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode；在 Max Mode 下最多 1M tokens，按相同的每 token 费率计费（无长上下文附加费）；缓存输入按每百万 tokens $0.15 计费，无单独的缓存写入费用 |
| [Muse Wonder](https://dev.meta.ai/docs/models) | Meta | 300k | 1M | Agent, Thinking, Images | 在 Max Mode 下最多 1M tokens，按相同的每 token 费率计费（无长上下文附加费）；缓存输入按每百万 tokens $0.20 计费，无单独的缓存写入费用 |

## 更多资源

### 下载

为你的电脑获取 Cursor

### 帮助

查找常见问题与故障排除指南的答案

如有账户与账单问题，请联系支持团队

---

## 站点地图

[所有文档页面概览](/llms.txt)
