> 原文：https://cursor.com/docs/models/claude-opus-5-5

Claude Opus 5.5 是 Anthropic 最新的 Opus 模型，并取代 [Opus 5](https://cursor.com/docs/models/claude-opus-5.md)。它在 [CursorBench](https://cursor.com/cursorbench) 上创下新高，领先于 Fable 5.1，并且每 token 成本比 Opus 5 低 20%。我们建议使用 high Thinking 变体，以获得最好的结果。

## 优势

- 发布时，在 Cursor 的所有模型中 CursorBench 分数最高，价格是标准 Opus 定价，而不是 Fable 定价。
- 沟通比更早的 Opus 模型更清晰。它会说明自己做了什么、为什么这么做，又不会过度展开，因此长时间的 Agent 会话更容易跟上。
- 擅长前端和 UI 工作。早期测试者用它从零开始设计和构建完整界面。
- 很适合作为 [Subagents](https://cursor.com/docs/subagents.md) 的协调者。它委派清晰，并能在长时间运行中让父任务保持在正轨上。
- 兼容 Zero Data Retention。与更早的 Opus 模型一样，也与 Fable 5.1 不同，Opus 5.5 对一般访问没有数据保留要求。目前以 ZDR 使用 Opus 的 Teams 可以在 Opus 5.5 上保持这一设置。

## 限制

- 仍然是 Opus 档的价格。它消耗预付额度（或旧版 Cursor 方案上的 Other Models 用量池）的速度比 [Sonnet 5.5](https://cursor.com/docs/models/claude-sonnet-5-5.md) 或 Composer 更快。

## 工具

在 Cursor 中使用时，Opus 5.5 可以使用全部 Agent 工具，包括：

进一步了解[工具如何工作](https://cursor.com/docs/agent/overview.md#tools)以及[工具调用基础](https://cursor.com/learn/tool-calling.md)。

## 定价

Opus 5.5 是第三方模型，因此不属于 Cursor [方案](https://cursor.com/docs/models-and-pricing.md#third-party-models)包含的用量。在 The X Subscription 上，按下面的费率从你的预付额度计费。在旧版 Cursor 方案上，它从 **Other Models** 用量池扣减。所有价格均按每百万 tokens 计算。

Opus 5.5 按每百万输入 tokens $4、每百万输出 tokens $20 计费，低于 Opus 5 的 $5 和 $25。提示缓存读取为每百万 tokens $0.20（输入费率的 0.05 倍），低于 Opus 5 上输入费率的 0.10 倍。

发布时提供 **Fast mode** 档位（`claude-opus-5-5-fast`），用于更高优先级的输出。在旧版按请求计费的方案上，它需要 Max Mode。计费为输入 $8/M、输出 $40/M tokens。请有选择地用于时间敏感或关键的工作。

全球端点使用上述费率。区域端点和仅限美国的端点价格高 10%，为输入 $4.40/M、输出 $22/M tokens。数据驻留详情见[隐私与数据治理](https://cursor.com/docs/enterprise/privacy-and-data-governance.md)。

所有 Opus 5.5 提示词均按上表的基础每 token 费率计费，包括上下文超过 300k 时。Opus 5.5 没有单独的长上下文倍率。最高 1M tokens 的上下文窗口使用相同费率。

Opus 5.5 支持 Thinking 变体，用于更深的推理。我们建议使用 high Thinking 变体，以获得最强的结果。

---

## 站点地图

[所有文档页面概览](/llms.txt)
