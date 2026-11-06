> 原文：https://cursor.com/docs/models/gpt-5-6-sol

GPT-5.6 Sol 是 OpenAI 在 Cursor 中的旗舰 GPT-5.6 模型。它是 Sol、Terra 和 Luna 这一族里最强的：能在长时间运行的 Agent 工作上持续推进，速度足以用于交互式会话，并且沟通明显简洁。

## 优势

- 在 GPT-5.6 系列中，面对有挑战的编程和推理任务时智能最高。
- 在长时间运行的工作上持续性很强；它会在长达数小时的 Agent 会话中继续做下去，而不是提前停下。
- 沟通干净、直接，注释和最终消息里的多余内容比许多 Claude 模型更少。
- 简洁、便于扫读；作为规划和调试时的橡皮鸭搭档很合适。
- 相对同等智能的模型，速度有竞争力。

## 限制

- 每 token 价格高于 Terra 和 Luna。
- 在中等规模的任务上可能会过度使用 Subagents。
- 有时在同意反馈之后，会等待一句明确的「就这么做」，而不是立刻执行。
- 在某些 Agent 行为评测上，指令遵循可能落后于最强的 Claude 模型。

## 工具

在 Cursor 中使用时，GPT-5.6 Sol 可以使用全部 Agent 工具，包括：

进一步了解[工具如何工作](https://cursor.com/docs/agent/overview.md#tools)以及[工具调用基础](https://cursor.com/learn/tool-calling.md)。

## 定价

GPT-5.6 Sol 是第三方模型，因此不属于 Cursor [方案](https://cursor.com/docs/models-and-pricing.md#third-party-models)包含的用量。在 The X Subscription 上，按下面的费率从你的预付额度计费。在旧版 Cursor 方案上，它从 **Other Models** 用量池扣减。所有价格均按每百万 tokens 计算。

提供 **Fast mode** 档位（`gpt-5.6-sol-fast`），用于优先处理，价格为标准费率的 2 倍。

当输入超过 272k tokens（长上下文）时，输入价格为标准费率的 2 倍，输出价格为标准费率的 1.5 倍。Fast mode 适用相同的倍数：长上下文请求按 Fast 输入、缓存写入和缓存读取费率的 2 倍计费，输出按 Fast 输出费率的 1.5 倍计费。

---

## 站点地图

[所有文档页面概览](/llms.txt)
