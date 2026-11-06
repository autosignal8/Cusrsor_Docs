> 原文：https://cursor.com/docs/models/gemini-3-8-flash

Gemini 3.8 Flash 是 Google 最新的速度档模型。它把较强的 Agent 编程表现和较低的每 token 成本结合在一起。适合能从推理和大上下文窗口中受益的高吞吐编程任务。

## 优势

- 每百万输入 tokens $0.75，每百万输出 tokens $3.75。
- 缓存输入 tokens 享有 90% 折扣（$0.075/1M）。适合重复出现的上下文，例如大型代码库。
- 1M token 的上下文窗口。单次请求可以装下仓库中相当大的一部分。
- 三个强度级别：high（默认）、medium 和 low。更简单的任务可以降到 medium 或 low，以得到更快的响应。

## 工具

在 Cursor 中使用时，Gemini 3.8 Flash 可以使用全部 Agent 工具，包括：

进一步了解[工具如何工作](https://cursor.com/docs/agent/overview.md#tools)以及[工具调用基础](https://cursor.com/learn/tool-calling.md)。

## 定价

Gemini 3.8 Flash 是第三方模型，因此不属于 Cursor [方案](https://cursor.com/docs/models-and-pricing.md#third-party-models)包含的用量。在 The X Subscription 上，按下面的费率从你的预付额度计费。在旧版 Cursor 方案上，它从 **Other Models** 用量池扣减。所有价格均按每百万 tokens 计算。

---

## 站点地图

[所有文档页面概览](/llms.txt)
