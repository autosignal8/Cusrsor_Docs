> 原文：https://cursor.com/docs/models/grok-4-5

我们建议使用 [Grok 4.7](https://cursor.com/docs/models/grok-4-7.md)，以获得更强的自我验证和更长时间运行的工作能力。Grok 4.5 仍然可用。

Grok 4.5 是 Cursor 与 SpaceXAI 的联合模型，面向软件工程和知识工作中的长时间运行任务。它在 Grok 底座上，继续用数万亿 tokens 的 Cursor 数据训练，并针对困难、贴近实际的问题做强化学习。

## 优势

- 能处理需要使用工具、检查结果、从错误中恢复并调整方法的长时间运行任务。
- 把广泛的知识用到软件工程、数据科学、金融、研究、法律工作，以及其他基于计算机的任务上。
- 解决多步骤任务所用的步骤不到同类前沿模型的一半。

## 强度级别

Grok 4.5 支持三个强度级别：high（默认）、medium 和 low。更高的强度会给模型更多时间来思考困难任务。最难的工作用 high；想在更简单的任务上得到更快、更便宜的响应时，降到 medium 或 low。

在 Plus 以及旧版 [Start 方案](https://cursor.com/docs/account/pricing/legacy-cursor-plans.md#start-india-only)（仅印度）上，Grok 4.5 固定为 medium 强度的非 Fast 模式。强度切换和 Fast mode 在 Premium 及以上，以及旧版 Pro、Pro+ 和 Ultra 方案上可用。

## 工具

在 Cursor 中使用时，Grok 4.5 可以使用全部 Agent 工具，包括：

进一步了解[工具如何工作](https://cursor.com/docs/agent/overview.md#tools)以及[工具调用基础](https://cursor.com/learn/tool-calling.md)。

## 定价

Grok 4.5 从每个付费方案都包含的 [Cursor 模型](https://cursor.com/docs/models-and-pricing.md#cursor-models)每周用量池中扣减，与 Grok 4.7、Grok 4.6 和 Composer 2.5 一起。在[旧版 Cursor 方案](https://cursor.com/docs/account/pricing/legacy-cursor-plans.md)上，它从每月的 Cursor Models 用量池中扣减。

超出用量池、按标准速度计的额外用量价格为：输入 $2/M tokens、输出 $6/M tokens。Fast 变体的价格为输入 $4/M tokens、输出 $18/M tokens。所有价格均按每百万 tokens 计算。

---

## 站点地图

[所有文档页面概览](/llms.txt)
