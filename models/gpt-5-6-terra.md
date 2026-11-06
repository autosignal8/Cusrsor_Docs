> 原文：https://cursor.com/docs/models/gpt-5-6-terra

GPT-5.6 Terra 是 OpenAI GPT-5.6 系列的中档模型。它在智能和价格上位于 Sol 与 Luna 之间，因此很适合日常的 Agent 编程：你想要比 Luna 更强的能力，同时不想承担 Sol 的成本。

## 优势

- 多步骤编程和工具使用扎实，每 token 价格明显低于 Sol。
- 对于常规 Agent 工作流，比 Sol 更快、更便宜。
- 同样的 GPT-5.6 系列行为：推理、工具调用和 Agent 循环。

## 限制

- 不是 GPT-5.6 系列的顶峰。对于最难的长时间运行任务，[GPT-5.6 Sol](https://cursor.com/docs/models/gpt-5-6-sol.md) 是更强的选择。
- 对开放式推理链的容量小于 Sol。

## 工具

在 Cursor 中使用时，GPT-5.6 Terra 可以使用全部 Agent 工具，包括：

进一步了解[工具如何工作](https://cursor.com/docs/agent/overview.md#tools)以及[工具调用基础](https://cursor.com/learn/tool-calling.md)。

## 定价

GPT-5.6 Terra 是第三方模型，因此不属于 Cursor [方案](https://cursor.com/docs/models-and-pricing.md#third-party-models)包含的用量。在 The X Subscription 上，按下面的费率从你的预付额度计费。在旧版 Cursor 方案上，它从 **Other Models** 用量池扣减。所有价格均按每百万 tokens 计算。

提供 **Fast mode** 档位（`gpt-5.6-terra-fast`），用于优先处理，价格为标准费率的 2 倍。

当输入超过 272k tokens（长上下文）时，输入价格为标准费率的 2 倍，输出价格为标准费率的 1.5 倍。Fast mode 适用相同的倍数：长上下文请求按 Fast 输入、缓存写入和缓存读取费率的 2 倍计费，输出按 Fast 输出费率的 1.5 倍计费。

---

## 站点地图

[所有文档页面概览](/llms.txt)
