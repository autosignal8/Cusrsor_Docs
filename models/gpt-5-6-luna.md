> 原文：https://cursor.com/docs/models/gpt-5-6-luna

GPT-5.6 Luna 是 OpenAI GPT-5.6 系列中最小、成本最低的模型。适合大批量、对延迟敏感或对成本敏感的 Agent 工作，也就是你并不需要 Sol 或 Terra 那么强能力的场合。

## 优势

- GPT-5.6 系列中价格最低；适合原型设计、Subagents 和高吞吐循环。
- 相对更大的 GPT-5.6 变体，响应更快。
- 与 Sol 和 Terra 一样，具备同系列的工具调用和 Agent 支持。

## 限制

- 不是前沿模型。对于更看重峰值智能的更难问题，[GPT-5.6 Sol](https://cursor.com/docs/models/gpt-5-6-sol.md) 或 [GPT-5.6 Terra](https://cursor.com/docs/models/gpt-5-6-terra.md) 是更好的选择。
- 对长的、开放式推理链，容量更小。

## 工具

在 Cursor 中使用时，GPT-5.6 Luna 可以使用全部 Agent 工具，包括：

进一步了解[工具如何工作](https://cursor.com/docs/agent/overview.md#tools)以及[工具调用基础](https://cursor.com/learn/tool-calling.md)。

## 定价

GPT-5.6 Luna 是第三方模型，因此不属于 Cursor [方案](https://cursor.com/docs/models-and-pricing.md#third-party-models)包含的用量。在 The X Subscription 上，按下面的费率从你的预付额度计费。在旧版 Cursor 方案上，它从 **Other Models** 用量池扣减。所有价格均按每百万 tokens 计算。

提供 **Fast mode** 档位（`gpt-5.6-luna-fast`），用于优先处理，价格为标准费率的 2 倍。

当输入超过 272k tokens（长上下文）时，输入价格为标准费率的 2 倍，输出价格为标准费率的 1.5 倍。Fast mode 适用相同的倍数：长上下文请求按 Fast 输入、缓存写入和缓存读取费率的 2 倍计费，输出按 Fast 输出费率的 1.5 倍计费。

---

## 站点地图

[所有文档页面概览](/llms.txt)
