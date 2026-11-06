> 原文：https://cursor.com/docs/models/muse-wonder

在 Meta 可用之前配置过[模型访问](https://cursor.com/docs/enterprise/model-and-integration-management.md#model-access-control)的 Teams 和 Enterprise 管理员，需要先启用 Meta 提供商，Muse Wonder 才会对其成员显示。

Muse Wonder 是 Meta 的旗舰模型，也是 Cursor 中 [Muse Spark 1.3](https://cursor.com/docs/models/muse-spark-1-3.md) 的后继模型。它面向 Agent 编程：长的工具使用链、多步骤调试，以及大型仓库上的工作。

## 优势

- 六个强度级别：minimal、low、medium、high（默认）、extra high 和 max。更高的强度每一步会消耗更多推理 tokens，并在 [CursorBench](https://cursor.com/cursorbench) 上得到更高分数；更简单的任务可以降到 low 或 minimal，以得到更快、更便宜的响应。
- 上下文窗口为 300k tokens；在 Max Mode 下为 1M，每 token 费率相同。
- 支持图像输入，包括工具返回的截图。

## 工具

在 Cursor 中使用时，Muse Wonder 可以使用全部 Agent 工具，包括：

进一步了解[工具如何工作](https://cursor.com/docs/agent/overview.md#tools)以及[工具调用基础](https://cursor.com/learn/tool-calling.md)。

## 定价

Muse Wonder 是第三方模型，因此不属于 Cursor [方案](https://cursor.com/docs/models-and-pricing.md#third-party-models)包含的用量。在 The X Subscription 上，按下面的费率从你的预付额度计费。在旧版 Cursor 方案上，它从 **Other Models** 用量池扣减。所有价格均按每百万 tokens 计算。

提示词按上面的基础每 token 费率计费，包括 Max Mode 下超过 300k tokens 时。缓存输入按每百万 tokens $0.20 计费；没有长上下文倍率，也没有缓存写入费用。

## 数据保留

Muse Wonder 按 Cursor 的标准提供商条款运行。见 Meta 的[零数据保留政策](https://dev.meta.ai/help/policies-and-privacy/zero-data-retention)和 Cursor 的[数据使用概览](https://cursor.com/data-use)。

---

## 站点地图

[所有文档页面概览](/llms.txt)
