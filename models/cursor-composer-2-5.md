> 原文：https://cursor.com/docs/models/cursor-composer-2-5

Composer 2.5 是 Cursor 自有的 Agent 模型。它在 [Composer 2](/blog/composer-2) 的基础上构建，长程 Agent 任务上的智能更强，强度校准、工具选择、意图理解和可靠性也更好。

## 优势

- 擅长长程任务，这来自针对[长程编程任务](/blog/self-driving-codebases)的[强化学习](/blog/real-time-rl-for-composer)。
- 交互式会话默认使用 fast 变体；standard 档进一步针对每 token 成本做了优化。
- 针对 Cursor 内的工具使用、文件编辑和终端操作做了调优。

## 工具

在 Cursor 中使用时，Composer 2.5 可以使用全部 Agent 工具，包括：

进一步了解[工具如何工作](https://cursor.com/docs/agent/overview.md#tools)以及[工具调用基础](https://cursor.com/learn/tool-calling.md)。

## 定价

还有一个智能相同的 **更快变体**，价格为输入 $3/M、输出 $15/M tokens。Fast 是产品中的默认档，并且在相近速度下，价格低于其他 fast 模型。

Composer 2.5 从每个付费方案都包含的 [Cursor 模型](https://cursor.com/docs/models-and-pricing.md#cursor-models)每周用量池中扣减，与 Grok 4.7、Grok 4.6 和 Grok 4.5 一起。在[旧版 Cursor 方案](https://cursor.com/docs/account/pricing/legacy-cursor-plans.md)上，它从每月的 Cursor Models 用量池中扣减。超出用量池的额外用量按下面的费率计费。所有价格均按每百万 tokens 计算。

---

## 站点地图

[所有文档页面概览](/llms.txt)
