> 原文：https://cursor.com/docs/models-and-pricing

# 模型与定价

Cursor 支持来自 OpenAI、Anthropic、Google、SpaceXSI 等的前沿模型。Cursor 作为 [The X Subscription](https://cursor.com/help/the-x-subscription/overview.md) 的一部分销售，一份订阅覆盖 Grok、Cursor 和 X。付费方案包含 Cursor 模型的每周用量池，在 Grok 与 Cursor 之间共享。第三方模型不包含在内；在 Premium 及以上，你可以按 API 标价使用它们，费用从预付额度中支付。

正在使用 Pro、Pro+、Ultra、Start，或 Teams 的 Standard 或 Premium 席位？这些是[旧版 Cursor 方案](https://cursor.com/docs/account/pricing/legacy-cursor-plans.md)。它们会保持原样继续可用，带有两个每月用量池和按需用量。

## 方案

| 方案 | 价格 | 包含用量 | 第三方模型 |
| :--- | :--- | :--- | :--- |
| **Free** | $0 | 有限 | 不可用 |
| **Plus** | $8/mo | 基础 | 不可用 |
| **Premium** | $30/mo | 4 倍 Plus | API 标价，从预付额度支付 |
| **Super** | $100/mo | 15 倍 Plus | API 标价，从预付额度支付 |
| **Ultra** | $200/mo | 50 倍 Plus | API 标价，从预付额度支付 |
| **Teams** | $30, $100, or $200 per seat/mo | 与席位档位一致 | API 标价，外加符合条件模型的 [Cursor Token 费率](https://cursor.com/docs/models-and-pricing.md#cursor-token-rate) |
| **Enterprise** | 定制 | 共享 | API 标价，外加符合条件模型的 Cursor Token 费率 |

可以按年付费：Plus 为 $80/yr，Premium 为 $300/yr，Super 为 $1,000/yr，Ultra 为 $2,000/yr。价格不含税。

- **Free。** 可使用 Agent 和 Chat，用量有限。
- **Plus。** 可使用 Agent 与 Cursor 自有模型（Grok 4.7、Grok 4.6、Grok 4.5 和 Composer 2.5），设置固定：Fast 关闭，强度为 medium，Grok 4.7 使用 256k 上下文窗口。还包括 Grok Chat、Voice、Imagine、Build 和 Grok API 访问。不含 Auto、Cloud Agents、Automations、Bugbot、Origin、第三方模型，也不支持你自己的 API 密钥。
- **Premium。** 包含 Plus 的全部内容，用量为 Plus 的 4 倍。新增 Cloud Agents 与 Automations、在 [Origin](https://cursor.com/docs/origin.md) 上托管代码、用 [Bugbot](https://cursor.com/docs/bugbot.md) 做代码审查、按 API 标价使用第三方模型，以及 Grok Bot。
- **Super。** 包含 Premium 的全部内容，用量为 Plus 的 15 倍，并在高峰时段享有优先模型访问。
- **Ultra。** 包含 Super 的全部内容，用量为 Plus 的 50 倍，并可最早使用新产品和模型。

Premium 适合刚开始使用 Agent 的用户，Super 适合每天使用 Agent 的用户，Ultra 适合同时运行多个 Agent 或 Automations 的重度用户。完整功能对比（包括 Grok 和 X 的权益）见 [cursor.com/pricing](https://cursor.com/pricing)。

## 包含用量

每个付费方案都包含一个 Cursor 模型用量池。该用量池在你订阅中的各项之间共享：编辑器中的 Agent、CLI、Cloud Agents、Grok Bot，以及 Grok 应用。用量池每周刷新，因此某一天用量很高，也不会把整月额度用完。

不同模型从用量池扣减的速率不同。剩余用量和下一次重置日期显示在编辑器设置中，以及 [grok.com/dashboard](https://grok.com/dashboard) 的 **Usage** 下。

### Cursor 模型

用量池覆盖 Grok 4.7、Grok 4.6、Grok 4.5 和 Composer 2.5。在 Teams 和 Enterprise 方案上，[Cursor Router](https://cursor.com/docs/cursor-router.md) 会根据你的优化模式，为每个 Balance 或 Intelligence 请求在 Cursor 模型与第三方模型之间做出选择；路由到第三方模型时，按[第三方用量](https://cursor.com/docs/models-and-pricing.md#third-party-models)计费。

| 模型 | 提供商 | 输入 | 缓存写入 | 缓存读取 | 输出 | 美国数据驻留 | 说明 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| [Grok 4.7](https://x.ai/news/grok-4-7) | Cursor | $2 | - | $0.5 | $6 | 是 | 由 Cursor 与 SpaceXAI 联合训练；长上下文（输入 tokens >256k）按标准费率的 2 倍计费，最高 500k；可使用 Fast mode，价格为 2 倍；长上下文（>256k）的 Fast mode 按标准费率的 3 倍计费 |
| [Grok 4.7 (Fast)](https://x.ai/news/grok-4-7) | Cursor | $4 | - | $1 | $12 | 是 | 由 Cursor 与 SpaceXAI 联合训练；长上下文（输入 tokens >256k）按标准费率的 2 倍计费，最高 500k；可使用 Fast mode，价格为 2 倍；长上下文（>256k）的 Fast mode 按标准费率的 3 倍计费 |
| [Grok 4.7 500k](https://x.ai/news/grok-4-7) | Cursor | $4 | - | $1 | $12 | 是 | 由 Cursor 与 SpaceXAI 联合训练；长上下文（输入 tokens >256k）按标准费率的 2 倍计费，最高 500k；可使用 Fast mode，价格为 2 倍；长上下文（>256k）的 Fast mode 按标准费率的 3 倍计费 |
| [Grok 4.7 500k (Fast)](https://x.ai/news/grok-4-7) | Cursor | $6 | - | $1.5 | $18 | 是 | 由 Cursor 与 SpaceXAI 联合训练；长上下文（输入 tokens >256k）按标准费率的 2 倍计费，最高 500k；可使用 Fast mode，价格为 2 倍；长上下文（>256k）的 Fast mode 按标准费率的 3 倍计费 |
| Grok 4.6 | Cursor | $2 | - | $0.5 | $6 | 是 | 由 Cursor 与 SpaceXAI 联合训练 |
| Grok 4.6 (Fast) | Cursor | $4 | - | $1 | $12 | 是 | 由 Cursor 与 SpaceXAI 联合训练 |
| Grok 4.5 | Cursor | $2 | - | $0.5 | $6 | 是 | 由 Cursor 与 SpaceXAI 联合训练 |
| Grok 4.5 (Fast) | Cursor | $4 | - | $1 | $18 | 是 | 由 Cursor 与 SpaceXAI 联合训练 |
| [Composer 2.5](https://cursor.com/blog/composer-2-5) | Cursor | $0.5 | - | $0.2 | $2.5 | 是 | - |
| [Composer 2.5 (Fast)](https://cursor.com/blog/composer-2-5) | Cursor | $3 | - | $0.5 | $15 | 是 | - |

超出每周用量池后购买的额外用量，按这些费率计费。

## 第三方模型

Claude、GPT 和 Gemini 等第三方模型不属于包含的用量池。在 Premium 及以上，从第一次请求起就可以在 Cursor 中按该模型的 API 标价使用它们，费用从你的[预付额度](https://cursor.com/docs/models-and-pricing.md#top-ups-and-spending-caps)中支付。Plus 和 Free 不包含第三方模型。

### 模型定价

所有价格均按每百万 tokens 计算：

| 模型 | 提供商 | 输入 | 缓存写入 | 缓存读取 | 输出 | 美国数据驻留 | 说明 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| [Claude 4 Sonnet](https://www.anthropic.com/claude/sonnet) | Anthropic | $3 | $3.75 | $0.3 | $15 | 否 | 默认隐藏；在旧版定价中，Thinking 变体按 2 次请求计费 |
| [Claude 4 Sonnet 1M](https://www.anthropic.com/claude/sonnet) | Anthropic | $6 | $7.5 | $0.6 | $22.5 | 否 | 默认隐藏；在旧版定价中，Thinking 变体按 2 次请求计费；由于上下文窗口很大，此模型可能非常昂贵；当输入超过 200k tokens 时，费用为 2 倍 |
| [Claude 4.5 Haiku](https://www.anthropic.com/claude/haiku) | Anthropic | $1 | $1.25 | $0.1 | $5 | 否 | 默认隐藏；Bedrock/Vertex：区域端点加收 10%；缓存：写入 1.25 倍，读取 0.1 倍 |
| [Claude 4.5 Opus](https://www.anthropic.com/claude/opus) | Anthropic | $5 | $6.25 | $0.5 | $25 | 否 | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode |
| [Claude 4.5 Sonnet](https://www.anthropic.com/claude/sonnet) | Anthropic | $3 | $3.75 | $0.3 | $15 | 否 | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode；扩展上下文最多支持 1M tokens，按相同的每 token 费率计费（无长上下文附加费） |
| [Claude 4.6 Opus](https://www.anthropic.com/claude/opus) | Anthropic | $5 | $6.25 | $0.5 | $25 | 是 | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode；扩展上下文最多支持 1M tokens，按相同的每 token 费率计费（无长上下文附加费） |
| [Claude 4.6 Sonnet](https://www.anthropic.com/claude/sonnet) | Anthropic | $3 | $3.75 | $0.3 | $15 | 是 | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode；扩展上下文最多支持 1M tokens，按相同的每 token 费率计费（无长上下文附加费） |
| [Claude 4.7 Opus](https://www.anthropic.com/claude/opus) | Anthropic | $5 | $6.25 | $0.5 | $25 | 是 | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode；扩展上下文最多支持 1M tokens，按相同的每 token 费率计费（无长上下文附加费） |
| [Claude Fable 5](https://www.anthropic.com/claude/fable) | Anthropic | $10 | $12.5 | $1 | $50 | 是 | 默认隐藏；Enterprise 客户，以及启用了 Privacy Mode 的 Teams 与个人客户，需要数据保留审批；Anthropic 会存储 Agent 的输入与输出数据，用于伤害预防流程；这些数据不会用于训练或改进 Anthropic 的模型或产品；触发安全护栏的请求会自动路由到 Claude Opus；费用大约是 Claude Opus 5 的 2 倍；在旧版按请求计费的方案上需要 Max Mode |
| [Claude Fable 5.1](https://www.anthropic.com/claude/fable) | Anthropic | $10 | $12.5 | $0.25 | $50 | 是 | Enterprise 客户，以及启用了 Privacy Mode 的 Teams 与个人客户，需要数据保留审批；Anthropic 会存储 Agent 的输入与输出数据，用于伤害预防流程；这些数据不会用于训练或改进 Anthropic 的模型或产品；触发安全护栏的请求会自动路由到 Claude Opus；提示缓存读取为 $0.25/M，比标准缓存读取费率低 75%；输入与输出费用大约是 Claude Opus 5.5 的 2.5 倍；在旧版按请求计费的方案上需要 Max Mode |
| [Claude Haiku 5.5](https://www.anthropic.com/claude/haiku) | Anthropic | $0.1 | $0.125 | $0.01 | $0.5 | 是 | 在旧版按请求计费的方案上需要 Max Mode；输入超过 100k tokens 的请求按标准费率的 5 倍计费（输入 $0.50/M，缓存写入 $0.625/M，缓存读取 $0.05/M，输出 $2.50/M）；对于输入不超过 100k tokens 的请求，输入与输出比 Claude 4.5 Haiku 便宜 90% |
| [Claude Opus 4.7 (fast mode)](https://www.anthropic.com/claude/opus) | Anthropic | $30 | $37.5 | $3 | $150 | 是 | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode；有限的研究预览；扩展上下文最多 1M tokens，按与较短上下文相同的每 token 费率计费 |
| [Claude Opus 4.8](https://www.anthropic.com/claude/opus) | Anthropic | $5 | $6.25 | $0.5 | $25 | 是 | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode；Fast mode（\`claude-opus-4-8-fast\`）在旧版按请求计费的方案上需要 Max Mode；Fast mode 的每 token 定价比 Opus 4.7 的 fast mode 低 3 倍；扩展上下文最多支持 1M tokens，按相同的每 token 费率计费（无长上下文附加费） |
| [Claude Opus 5](https://www.anthropic.com/claude/opus) | Anthropic | $5 | $6.25 | $0.5 | $25 | 是 | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode；Fast mode（\`claude-opus-5-fast\`）在旧版按请求计费的方案上需要 Max Mode；扩展上下文最多支持 1M tokens，按相同的每 token 费率计费（无长上下文附加费） |
| [Claude Opus 5.5](https://www.anthropic.com/claude/opus) | Anthropic | $4 | $5 | $0.2 | $20 | 是 | 在旧版按请求计费的方案上需要 Max Mode；Fast mode（\`claude-opus-5-5-fast\`）在旧版按请求计费的方案上需要 Max Mode；输入与输出比 Claude Opus 5 便宜 20%；提示缓存读取为 $0.20/M（输入价格的 0.05 倍），低于 Claude Opus 5 上输入价格的 0.10 倍；区域端点与仅限美国的端点价格高 10%（输入 $4.40/M，输出 $22/M）；扩展上下文最多支持 1M tokens，按相同的每 token 费率计费（无长上下文附加费） |
| [Claude Sonnet 5](https://www.anthropic.com/claude/sonnet) | Anthropic | $2 | $2.5 | $0.2 | $10 | 是 | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode；扩展上下文最多支持 1M tokens，按相同的每 token 费率计费（无长上下文附加费）；使用了更新后的分词器，因此相同输入可能会对应更多 tokens |
| [Claude Sonnet 5.5](https://www.anthropic.com/claude/sonnet) | Anthropic | $2 | $2.5 | $0.1 | $10 | 是 | 在旧版按请求计费的方案上需要 Max Mode；输入与输出费率与 Claude Sonnet 5 相同，缓存读取为 $0.10/M，而非 $0.20/M；仅限美国的端点价格高 10%（输入 $2.20/M，输出 $11/M）；扩展上下文最多支持 1M tokens，按相同的每 token 费率计费（无长上下文附加费） |
| [DeepSeek V4.1 Flash 1017](https://huggingface.co/deepseek-ai/DeepSeek-V4.1-Flash-1017) | DeepSeek | $0.3 | - | $0.006 | $1.2 | 是 | 默认隐藏 |
| [Gemini 2.5 Flash](https://developers.googleblog.com/en/start-building-with-gemini-25-flash/) | Google | $0.3 | - | $0.03 | $2.5 | 是 | 默认隐藏 |
| [Gemini 3 Flash](https://ai.google.dev/gemini-api/docs) | Google | $0.5 | - | $0.05 | $3 | 否 | 默认隐藏 |
| [Gemini 3 Pro](https://ai.google.dev/gemini-api/docs) | Google | $2 | - | $0.2 | $12 | 否 | 默认隐藏 |
| [Gemini 3 Pro Image Preview](https://ai.google.dev/gemini-api/docs) | Google | $2 | - | $0.2 | $12 | 否 | 默认隐藏；原生图像生成模型，针对速度、灵活性与上下文理解做了优化；文本输入与输出价格与 Gemini 3 Pro 相同；图像输出：$120/1M tokens（约每张 1K/2K 图像 $0.134，约每张 4K 图像 $0.24）；预览模型在变为稳定版本之前可能会变化，并且速率限制更严格 |
| [Gemini 3.1 Pro](https://ai.google.dev/gemini-api/docs) | Google | $2 | - | $0.2 | $12 | 否 | - |
| [Gemini 3.5 Flash](https://ai.google.dev/gemini-api/docs) | Google | $1.5 | - | $0.15 | $9 | 是 | 默认隐藏 |
| [Gemini 3.5 Flash Lite](https://ai.google.dev/gemini-api/docs/models/gemini-3.5-flash-lite) | Google | $0.3 | - | $0.03 | $2.5 | 是 | 默认隐藏；仅用于 Auto-review 分类器。不能作为聊天或 Agent 模型选择 |
| [Gemini 3.6 Flash](https://ai.google.dev/gemini-api/docs) | Google | $1.5 | - | $0.15 | $7.5 | 否 | 默认隐藏 |
| [Gemini 3.7 Flash](https://ai.google.dev/gemini-api/docs) | Google | $0.75 | - | $0.075 | $3.75 | 否 | 默认隐藏 |
| [Gemini 3.8 Flash](https://ai.google.dev/gemini-api/docs) | Google | $0.75 | - | $0.075 | $3.75 | 否 | - |
| [Gemini 4 Argon](https://ai.google.dev/gemini-api/docs) | Google | $2 | - | $0.1 | $10 | 否 | - |
| [GLM 5.2](https://z.ai) | Z.ai | $1.4 | - | $0.26 | $4.4 | 是 | 默认隐藏 |
| [GLM 5.3](https://z.ai) | Z.ai | $1.4 | - | $0.26 | $4.4 | 是 | 默认隐藏；每 token 费率与 GLM 5.2 相同 |
| [GLM 5.3 Flash](https://z.ai) | Z.ai | $0.15 | - | $0.029 | $0.5 | 是 | 默认隐藏 |
| [GPT-5](https://openai.com/index/gpt-5/) | OpenAI | $1.25 | - | $0.125 | $10 | 是 | 默认隐藏；具备 Agent 与推理能力；可用的推理强度变体为 gpt-5-high |
| [GPT-5 Fast](https://openai.com/index/gpt-5/) | OpenAI | $2.5 | - | $0.25 | $20 | 是 | 默认隐藏；速度更快，但价格为 2 倍；可用的推理强度变体为 gpt-5-high-fast、gpt-5-low-fast |
| [GPT-5 Mini](https://openai.com/index/gpt-5/) | OpenAI | $0.25 | - | $0.025 | $2 | 否 | 默认隐藏 |
| [GPT-5-Codex](https://platform.openai.com/docs/models/gpt-5-codex) | OpenAI | $1.25 | - | $0.125 | $10 | 是 | 默认隐藏；具备 Agent 与推理能力 |
| [GPT-5.1 Codex](https://platform.openai.com/docs/models/gpt-5-codex) | OpenAI | $1.25 | - | $0.125 | $10 | 是 | 默认隐藏；具备 Agent 与推理能力 |
| [GPT-5.1 Codex Max](https://platform.openai.com/docs/models/gpt-5-codex) | OpenAI | $1.25 | - | $0.125 | $10 | 是 | 默认隐藏 |
| [GPT-5.1 Codex Mini](https://platform.openai.com/docs/models/gpt-5-codex) | OpenAI | $0.25 | - | $0.025 | $2 | 是 | 默认隐藏；具备 Agent 与推理能力；速率限制是 GPT-5.1 Codex 的 4 倍 |
| [GPT-5.2](https://openai.com/index/gpt-5/) | OpenAI | $1.75 | - | $0.175 | $14 | 是 | 默认隐藏；具备 Agent 与推理能力；可用的推理强度变体为 gpt-5.2-high |
| [GPT-5.2 Codex](https://platform.openai.com/docs/models/gpt-5-codex) | OpenAI | $1.75 | - | $0.175 | $14 | 是 | 默认隐藏；具备 Agent 与推理能力 |
| [GPT-5.3 Codex](https://platform.openai.com/docs/models/gpt-5-codex) | OpenAI | $1.75 | - | $0.175 | $14 | 否 | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode；具备 Agent 与推理能力；可用的推理强度变体为 gpt-5.3-codex-high |
| [GPT-5.4](https://developers.openai.com/api/docs/models/gpt-5.4) | OpenAI | $2.5 | - | $0.25 | $15 | 是 | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode；具备 Agent 与推理能力；缓存输入 tokens 享有 90% 折扣；Fast mode 速度快 15%，价格为 2 倍；长上下文最多支持 1M tokens，输入价格为 2 倍 |
| [GPT-5.4 Mini](https://developers.openai.com/api/docs/models/gpt-5.4-mini) | OpenAI | $0.75 | - | $0.075 | $4.5 | 是 | 默认隐藏；GPT-5.4 更小、更快的变体；缓存输入 tokens 享有 90% 折扣 |
| [GPT-5.4 Nano](https://developers.openai.com/api/docs/models/gpt-5.4-nano) | OpenAI | $0.2 | - | $0.02 | $1.25 | 是 | 默认隐藏；最小的 GPT-5.4 变体，针对成本优化；缓存输入 tokens 享有 90% 折扣 |
| [GPT-5.5](https://developers.openai.com/api/docs/models/gpt-5.5) | OpenAI | $5 | - | $0.5 | $30 | 是 | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode；具备 Agent 与推理能力；在同类任务上比 GPT-5.4 更节省 tokens；在长时间运行的任务上持续性更好；可使用 Fast mode，费率更高；长上下文最多支持 1M tokens，输入价格为 2 倍 |
| [GPT-5.6 Luna](https://openai.com/index/previewing-gpt-5-6-sol/) | OpenAI | $0.2 | $0.25 | $0.02 | $1.2 | 是 | 在旧版按请求计费的方案上需要 Max Mode；最小的 GPT-5.6 变体，针对成本与速度优化；具备 Agent 与推理能力；可使用 Fast mode，价格为 2 倍；长上下文最多支持 1M tokens，输入价格为 2 倍；长上下文（>272k）也可使用 Fast mode，按 Fast 输入价格的 2 倍计费；缓存写入按未缓存输入费率的 1.25 倍计费 |
| [GPT-5.6 Sol](https://openai.com/index/previewing-gpt-5-6-sol/) | OpenAI | $4 | $5 | $0.4 | $20 | 是 | 在旧版按请求计费的方案上需要 Max Mode；具备 Agent 与推理能力；可使用 Fast mode，价格为 2 倍；长上下文最多支持 1M tokens，输入价格为 2 倍；长上下文（>272k）也可使用 Fast mode，按 Fast 输入价格的 2 倍计费；缓存写入按未缓存输入费率的 1.25 倍计费；促销价格持续至 2026 年 11 月 21 日 |
| [GPT-5.6 Terra](https://openai.com/index/previewing-gpt-5-6-sol/) | OpenAI | $2 | $2.5 | $0.2 | $12 | 是 | 在旧版按请求计费的方案上需要 Max Mode；介于 Sol 与 Luna 之间的中档 GPT-5.6 变体；具备 Agent 与推理能力；可使用 Fast mode，价格为 2 倍；长上下文最多支持 1M tokens，输入价格为 2 倍；长上下文（>272k）也可使用 Fast mode，按 Fast 输入价格的 2 倍计费；缓存写入按未缓存输入费率的 1.25 倍计费 |
| Kimi K2.7 Code | Moonshot | $0.95 | - | $0.19 | $4 | 是 | 默认隐藏 |
| [Kimi K3](https://www.moonshot.ai) | Moonshot | $3 | - | $0.3 | $15 | 是 | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode；扩展上下文最多支持 1M tokens，按相同的每 token 费率计费（无长上下文附加费）；无单独的缓存写入费用 |
| [Muse Spark 1.3](https://dev.meta.ai/docs/models) | Meta | $1.25 | - | $0.15 | $4.25 | 否 | 默认隐藏；在旧版按请求计费的方案上需要 Max Mode；在 Max Mode 下最多 1M tokens，按相同的每 token 费率计费（无长上下文附加费）；缓存输入按每百万 tokens $0.15 计费，无单独的缓存写入费用 |
| [Muse Wonder](https://dev.meta.ai/docs/models) | Meta | $4 | - | $0.2 | $20 | 否 | 在 Max Mode 下最多 1M tokens，按相同的每 token 费率计费（无长上下文附加费）；缓存输入按每百万 tokens $0.20 计费，无单独的缓存写入费用 |

选择区域数据驻留后，符合条件的模型定价上浮 10%。支持的区域、模型、功能以及数据驻留政策，见[隐私与数据治理](https://cursor.com/docs/enterprise/privacy-and-data-governance.md)。

## 充值与支出上限

充值会为你的订阅增加预付额度。额度用于支付两类额外用量：

- Cursor 中的**第三方模型**，从第一次请求起按 API 标价计费。
- **超出每周用量池的 Cursor 模型**，按上表费率计费。

你可以自行选择何时购买额度，也可以开启 **Auto Top-up**，在余额低于你设定的阈值时自动补充。**每月额外用量上限**会限制你每个月可以消耗多少付费额外用量，与你持有多少额度无关。这三项都在 [grok.com/dashboard/billing](https://grok.com/dashboard/billing) 的 **Additional usage credits** 下管理。在 Teams 上，管理员管理全团队共享的预付余额，以及一个适用于整个团队的上限。

### 达到上限后会怎样？

每周用量池用完后，你可以：

- **等待重置。** 用量池每周刷新。
- **使用预付额度。** 通过充值或 Auto Top-up，按相同的每模型费率继续使用。
- **升级方案。** 升到更高档位，获得更大的每周用量池。

请求的质量和速度绝不会被降级。如何查看用量，以及上限通知的含义，见[用量与限制](https://cursor.com/help/models-and-usage/usage-limits.md)。

## Teams

Teams 按席位定价。每位成员获得 Premium、Super 或 Ultra 席位，价格和每周用量与对应的个人方案相同，你可以在同一个团队中混用不同席位档位。团队不提供 Plus。

团队方案增加集中账单与管理、用于内部 Rules、Skills 和 Plugins 的团队市场、用量分析、全团队强制 Privacy Mode、自定义 Chat 数据保留，以及 SAML/OIDC SSO。Enterprise 增加跨 Grok 与 Cursor 的共享用量、发票、SCIM、细粒度支出控制，以及高级安全控制。

我们建议自助服务即可满足的客户使用 Teams，需要优先支持、共享用量、发票、SCIM 或高级安全控制的客户使用 [Enterprise](https://cursor.com/contact-sales?source=docs-models-pricing)。进一步了解[团队定价](https://cursor.com/docs/account/teams/pricing.md)。

## Cursor Token 费率

在 Teams 和 Enterprise 方案上，符合条件的第三方模型请求会加收 Cursor Token 费率，为每百万 tokens $0.25。该费率叠加在模型 API 定价之上，适用于额外用量和 BYOK 用量。个人方案不支付该费率。

直接选择符合条件的第三方模型时适用该费率；Cursor Router（Balance 或 Intelligence）路由到这类模型时也同样适用。它适用于输入 tokens、输出 tokens 和缓存 tokens。

Cursor Token 费率不适用于包括 Grok 和 Composer 在内的 Cursor 模型，也不适用于 Cost 模式请求。

对于在每一个定价档位上缓存读取价格都严格低于每百万 tokens $0.10 的第三方模型，Cursor Token 费率同样不适用。使用你自己的 API 密钥时，这项豁免仍然适用。

## Auto 模式

Auto 有三种模式：Cost、Balance 和 Intelligence。这些模式如何工作，见 [Cursor Router](https://cursor.com/docs/cursor-router.md)。

Cost 模式（经典 Auto）按 Cursor 模型请求计费，无论实际服务于该请求的是哪个模型，都从你的每周用量池扣减。在已开启 Cursor Router 的 Teams 和 Enterprise 上，Balance 和 Intelligence 按所路由模型的标价计费：路由到 Cursor 模型时从每周用量池扣减，路由到第三方模型时属于第三方用量，从预付额度支付。个人方案只有 Cost 模式。各模型费率见[模型定价](https://cursor.com/docs/models-and-pricing.md#model-pricing)。在 Teams 和 Enterprise 上，Balance 或 Intelligence 路由到第三方模型时，还会产生 [Cursor Token 费率](https://cursor.com/docs/models-and-pricing.md#cursor-token-rate)，除非该模型在每一个定价档位上的缓存读取价格都严格低于每百万 tokens $0.10。Cost 模式不产生该费率。

## 旧版 Cursor 方案

Hobby、Start、Pro、Pro+、Ultra，以及 Teams 的 Standard 与 Premium 席位，对现有客户继续有效。它们包含两个每月用量池：Cursor Models 和 Other Models，并使用按需用量，而不是充值。它们如何运作，以及如何对应到 The X Subscription，见[旧版 Cursor 方案](https://cursor.com/docs/account/pricing/legacy-cursor-plans.md)。

## 旧版按请求计费

### Max Mode

Max Mode 仅在旧版按请求计费的方案上可用。它把模型的上下文窗口扩展到默认上限之外，并按该模型的 API 费率加 20% 计费。详情见[旧版方案上的 Max Mode](https://cursor.com/help/ai-features/max-mode.md)。

## 常见问题

### 我的每周用量池与 Grok 共享吗？

是的。Grok Chat、Imagine、Voice、Grok Bot 和 Cursor 都从同一个 Cursor 模型每周用量池扣减。各产品的用量速率不同。Grok API 单独计费，不从该用量池扣减。

### 我可以在 Plus 上使用第三方模型吗？

不可以。Plus 只包含 Cursor 自有模型（Grok 4.7、Grok 4.6、Grok 4.5 和 Composer 2.5），且设置是固定的。升级到 Premium 或更高档位后，可以使用第三方模型（按 API 标价从预付额度计费），并可以选择 Fast mode 和强度级别。

### 哪些模型不收取 Cursor Token 费率？

在 Teams 和 Enterprise 上，在每一个定价档位上缓存读取价格都严格低于每百万 tokens $0.10 的第三方模型均可豁免，包括你使用自己的 API 密钥时。Cursor 模型和 Cost 模式请求同样豁免。个人方案不支付该费率。

### 模型托管在哪里？

模型由模型提供商、可信合作伙伴或 Cursor 托管。详情见[子处理方](https://trust.cursor.com/subprocessors)列表。

### 在哪里可以找到定价条款？

企业定价详情、账单条款和费用计算，见[定价政策](https://cursor.com/terms/pricing)。

---

## 站点地图

[所有文档页面概览](/llms.txt)
