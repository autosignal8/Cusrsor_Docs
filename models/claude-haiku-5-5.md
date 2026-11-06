> 原文：https://cursor.com/docs/models/claude-haiku-5-5

Claude Haiku 5.5 是 Anthropic Claude 5.5 系列中最快的模型，也是 [Claude 4.5 Haiku](https://cursor.com/docs/models/claude-4-5-haiku.md) 的后继模型。它在 max 强度下于 [CursorBench](https://cursor.com/cursorbench) 得分为 48.4%，与 [Sonnet 5.5](https://cursor.com/docs/models/claude-sonnet-5-5.md) 和 [Opus 5.5](https://cursor.com/docs/models/claude-opus-5-5.md) 处在同一条性价比曲线上；对于输入不超过 100k tokens 的请求，每 token 价格是 Haiku 4.5 的十分之一。可以从 **Cursor Settings > Models** 把它加入模型选择器。

## 优势

- 适合大批量工作的低成本。输入不超过 100k tokens 的请求按输入 $0.10/M、输出 $0.50/M 计费。
- 第一款具备强度级别的 Haiku。开启 Thinking 后，CursorBench 分数从 low 强度的 30.9% 升至 max 的 48.4%。
- 在 low、medium 和 high 强度下可以关闭 Thinking，以得到更快、更便宜的响应。
- 支持最高 1M tokens 的上下文窗口。

## 限制

- 输入超过 100k tokens 的请求按标准费率的 5 倍计费。上下文很大的长时间 Agent 会话一旦越过这条线，每 token 成本就会更高。
- 对于更难的任务，[Sonnet 5.5](https://cursor.com/docs/models/claude-sonnet-5-5.md) 和 [Opus 5.5](https://cursor.com/docs/models/claude-opus-5-5.md) 在 CursorBench 上的分数更高。
- 关闭 Thinking 会降低质量。在 CursorBench 上，关闭 Thinking 时分数为 22.1% 至 26.2%，而在相同强度级别下开启 Thinking 时为 30.9% 至 42.3%。

## 工具

在 Cursor 中使用时，Haiku 5.5 可以使用全部 Agent 工具，包括：

进一步了解[工具如何工作](https://cursor.com/docs/agent/overview.md#tools)以及[工具调用基础](https://cursor.com/learn/tool-calling.md)。

## 定价

Haiku 5.5 是第三方模型，因此不属于 Cursor [方案](https://cursor.com/docs/models-and-pricing.md#third-party-models)包含的用量。在 The X Subscription 上，按下面的费率从你的预付额度计费。在旧版 Cursor 方案上，它从 **Other Models** 用量池扣减。所有价格均按每百万 tokens 计算。

输入不超过 100k tokens 的请求按输入 $0.10/M、输出 $0.50/M 计费。提示缓存写入为 $0.125/M，缓存读取为 $0.01/M。

输入超过 100k tokens 的请求（长上下文）按这些费率的 5 倍计费：输入 $0.50/M、缓存写入 $0.625/M、缓存读取 $0.05/M、输出 $2.50/M。

可以使用 Thinking 变体进行更深的推理。

---

## 站点地图

[所有文档页面概览](/llms.txt)
