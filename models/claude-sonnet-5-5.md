> 原文：https://cursor.com/docs/models/claude-sonnet-5-5

Claude Sonnet 5.5 是 Anthropic 最新的中档模型，也是 [Sonnet 5](https://cursor.com/docs/models/claude-sonnet-5.md) 的后继模型。它在 max 强度下于 [CursorBench](https://cursor.com/cursorbench) 得分为 55.5%，仅次于 [Opus 5.5](https://cursor.com/docs/models/claude-opus-5-5.md)。它保持 Sonnet 5 的输入和输出费率，并把缓存读取费率减半。可以从 **Cursor Settings > Models** 把它加入模型选择器。

## 优势

- 以 Sonnet 的价格取得前沿水平的 CursorBench 结果。它与 Opus 5.5 处在同一条性价比前沿上。
- 随强度扩展。分数从 low 强度的 35.8% 升至 max 的 55.5%，因此在更难的任务上你可以用 tokens 换取质量。
- 支持 Thinking 模式，以及最高 1M tokens 的上下文窗口。

## 限制

- 若要在最难的任务上取得最高质量，[Opus 5.5](https://cursor.com/docs/models/claude-opus-5-5.md) 仍然是更强的选择。
- 更高的强度级别会使用多得多的 tokens。在 CursorBench 上，max 强度每个任务的成本大约比默认的 high 强度高 6 倍。

## 工具

在 Cursor 中使用时，Sonnet 5.5 可以使用全部 Agent 工具，包括：

进一步了解[工具如何工作](https://cursor.com/docs/agent/overview.md#tools)以及[工具调用基础](https://cursor.com/learn/tool-calling.md)。

## 定价

Sonnet 5.5 是第三方模型，因此不属于 Cursor [方案](https://cursor.com/docs/models-and-pricing.md#third-party-models)包含的用量。在 The X Subscription 上，按下面的费率从你的预付额度计费。在旧版 Cursor 方案上，它从 **Other Models** 用量池扣减。所有价格均按每百万 tokens 计算。

Sonnet 5.5 按每百万输入 tokens $2、每百万输出 tokens $10 计费，与 Sonnet 5 相同。提示缓存读取为每百万 tokens $0.10，是 Sonnet 5 的 $0.20 的一半。

全球端点使用上述费率。仅限美国的端点价格高 10%，为输入 $2.20/M、输出 $11/M tokens。数据驻留详情见[隐私与数据治理](https://cursor.com/docs/enterprise/privacy-and-data-governance.md)。

所有 Sonnet 5.5 提示词均按上表的基础每 token 费率计费，包括上下文超过 200k 时。Sonnet 5.5 没有单独的长上下文倍率。

可以使用 Thinking 变体进行更深的推理。

---

## 站点地图

[所有文档页面概览](/llms.txt)
