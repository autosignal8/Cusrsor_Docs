> 原文：https://cursor.com/docs/models/grok-4-7

Grok 4.7 是 Cursor 与 SpaceXAI 的前沿模型，用于编程和知识工作。它在 Grok 4.6 的基础上扩大了底座规模，针对更难的、长达数小时的任务做了更长时间的训练，并加强了自我验证。

## 优势

- 能持续处理困难的、长时间运行的工作，并且比 Grok 4.6 更仔细地检查自己的结果。
- 更可靠地管理更长的上下文，标准窗口为 256k，长上下文为 500k。
- 在复杂编程和长达数小时的知识工作上更强。

## 强度级别

Grok 4.7 支持四个强度级别：xhigh、high（默认）、medium 和 low。更高的强度会给模型更多时间来处理困难任务。强度级别之间的间隔比 Grok 4.6 更大，因此更难的任务会花更多时间思考。

在 Premium 及以上方案，以及旧版 Pro、Pro+ 和 Ultra 方案上，Fast 是默认速度档。在 Plus 以及旧版 [Start 方案](https://cursor.com/docs/account/pricing/legacy-cursor-plans.md#start-india-only)（仅印度）上，Grok 4.7 固定为 medium 强度和标准速度。

## 工具

在 Cursor 中使用时，Grok 4.7 可以使用全部 Agent 工具，包括：

进一步了解[工具如何工作](https://cursor.com/docs/agent/overview.md#tools)以及[工具调用基础](https://cursor.com/learn/tool-calling.md)。

## 定价

Grok 4.7 从每个付费方案都包含的 [Cursor 模型](https://cursor.com/docs/models-and-pricing.md#cursor-models)每周用量池中扣减，与 Grok 4.6、Grok 4.5 和 Composer 2.5 一起。在[旧版 Cursor 方案](https://cursor.com/docs/account/pricing/legacy-cursor-plans.md)上，它从每月的 Cursor Models 用量池中扣减。

超出用量池、按标准速度计的额外用量价格为：输入 $2/M tokens、缓存输入 $0.50/M tokens、输出 $6/M tokens。Fast 为输入 $4/M、缓存 $1/M、输出 $12/M。当输入超过 256k tokens 时，标准请求按这些费率的 2 倍计费，Fast 请求按标准费率的 3 倍计费，上下文窗口最高 500k。长上下文计费只看输入长度：如果输入不超过 256k tokens，整个请求使用标准或 Fast 费率。所有价格均按每百万 tokens 计算。

---

## 站点地图

[所有文档页面概览](/llms.txt)
