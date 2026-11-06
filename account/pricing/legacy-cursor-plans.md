> 原文：https://cursor.com/docs/account/pricing/legacy-cursor-plans

# 旧版 Cursor 方案

旧版 Cursor 方案是 Cursor 在 [The X Subscription](https://cursor.com/docs/models-and-pricing.md) 之前销售的方案：Hobby、Start、Pro、Pro+、Ultra，以及带有 Standard 和 Premium 席位的 Teams。它们会保持原样继续可用。新客户从 The X Subscription 开始；本页写给仍在旧版方案上、想知道它如何运作、又如何对比的人。

切换到 The X Subscription 是可选的，而且一旦切换即永久生效。除非你更改或取消，否则旧版方案、价格和功能都会继续。资格以及如何切换，见[什么是 The X Subscription？](https://cursor.com/help/the-x-subscription/overview.md)。

## 哪些方案是旧版 Cursor 方案？

| 旧版方案 | 价格 | The X Subscription 上的对应方案 |
| :--- | :--- | :--- |
| **Hobby** | Free | Free |
| **Start**（仅印度） | ₹649/mo，含税 | Plus ($8/mo) |
| **Pro** | $20/mo | Premium ($30/mo) |
| **Pro+** | $60/mo | Super ($100/mo) |
| **Ultra** | $200/mo | Ultra ($200/mo) |
| **Teams Standard** | $40/user/mo | Teams，Premium 席位（$30/user/mo） |
| **Teams Premium** | $120/user/mo | Teams，Super 席位（$100/user/mo） |

对应指最接近的档位，并不是一模一样的一档。The X Subscription 上每个付费方案所含的、使用 Cursor 模型的编码用量，至少是它所取代的旧版方案的 2 倍，并且增加了 Grok 和 X 的权益。它也取消了包含在内的第三方模型额度；第三方模型单独计费。切换前请把两边都对比一下。Start 对应 Plus 只是类比：付费 Cursor 账号切换时，Unify 流程提供的是 Premium 及以上；Pro、Pro+、Teams Standard 和 Teams Premium 会默认到表中所示的档位。

按请求计费的方案更早一代，当时用量按请求而不是 tokens 计数。见[按请求计费（旧版）](https://cursor.com/docs/account/pricing/request-based-legacy.md)。

## 旧版方案与 The X Subscription 有何不同

|  | 旧版 Cursor 方案 | The X Subscription |
| :--- | :--- | :--- |
| 包含用量 | 两个每月用量池：**Cursor Models** 和 **Other Models** | 一个 Cursor 模型每周用量池，与 Grok 共享 |
| 用量重置 | 每月，随你的账单周期 | 每周 |
| 第三方模型 | 在 Pro、Pro+、Ultra 和 Teams 上，通过 Other Models 用量池包含 | 不包含。在 Premium 及以上按 API 标价从预付额度计费 |
| 额外用量 | **按需用量**，每月后付费 | 增加预付额度的**充值**，可选 Auto Top-up |
| 支出控制 | 按需支出限额 | 每月额外用量上限（支出上限） |
| Grok 和 X | 不包含 | 包含 Grok 应用和 X Premium 权益 |
| 账单管理位置 | [cursor.com/dashboard/billing](https://cursor.com/dashboard/billing) | [grok.com/dashboard/billing](https://grok.com/dashboard/billing) |

## 旧版方案上的用量池

旧版方案把包含用量分成两个池。两者都随你的每月账单周期重置，并且都可以在编辑器设置和[用量仪表板](https://cursor.com/dashboard/usage)中看到。

- **Cursor Models。** Grok 4.7、Grok 4.6、Grok 4.5 和 Composer 2.5 的包含用量。每个旧版方案都包含这个用量池。
- **Other Models。** 第三方模型的包含用量，按每个模型的 API 价格扣减。Pro、Pro+、Ultra 和 Teams 包含这个用量池。Start 和 Hobby 不包含。

模型选择会影响用量池消耗的速度。当你选择第三方模型，或 [Auto](https://cursor.com/docs/cursor-router.md) 路由到第三方模型时，该请求按该模型的标价从 Other Models 扣减。[Subagents](https://cursor.com/docs/subagents.md#parent-model-and-usage-pools) 中明确指定的第三方模型也会从 Other Models 扣减，即使父聊天用的是 Auto、Grok 或 Composer。

## 旧版方案上的按需用量

按需用量让你在某个包含用量池用完之后继续工作。它按月后付费，每模型费率与包含用量相同，质量和速度不变。

- **个人方案（Pro、Pro+、Ultra）。** 默认关闭，直到你在 [cursor.com/dashboard/spending](https://cursor.com/dashboard/spending) 的 **Spending** 下打开它。在那里设置每月支出限额。
- **Teams。** 默认开启。管理员可以从团队仪表板关闭它，或设置全团队的支出限额。符合条件的第三方请求还会收取 [Cursor Token 费率](https://cursor.com/docs/models-and-pricing.md#cursor-token-rate)。如果第三方模型在每一个定价档位上的缓存读取价格都严格低于每百万 tokens $0.10，则可豁免。
- **Start 和 Hobby。** 不可用。AI 功能会暂停，直到用量池重置。

限额和额度如何运作，见[按用量计费](https://cursor.com/help/account-and-billing/overages.md)和[支出限额](https://cursor.com/help/account-and-billing/spend-limits.md)。

## 方案详情

### Hobby

Hobby 是免费的旧版方案。它包含用量有限的 Agent、Chat 和 Tab，不包含 Cloud Agents、Bugbot 或 Origin。

### Start（仅印度）

Start 的价格为每月 ₹649，含税，按月以 INR 通过 UPI、信用卡或借记卡计费。它包含 Cursor Models 用量池、[Cloud Agents](https://cursor.com/docs/cloud-agent.md)、[Cursor for iOS](https://cursor.com/docs/cloud-agent/mobile.md)、Plugins、MCP 服务器、Hooks 和 Skills。

在 Start 上，Grok 4.7、Grok 4.6 和 Grok 4.5 以固定的 medium 强度和标准速度运行，Composer 2.5 以标准速度运行。你不能更改强度级别，也不能开启 Fast mode。Start 不包含 Other Models 用量池、按需用量、Bugbot、Auto、Automations 或 Cursor SDK。只有当你的账号处于免费方案时才能购买 Start，因此不能从另一个付费方案直接切回 Start。资格与付款见 [Cursor Start](https://cursor.com/help/account-and-billing/cursor-start.md)。

### Pro、Pro+ 和 Ultra

Pro、Pro+ 和 Ultra 包含两个用量池、无限的 Tab 补全、Bugbot、Cloud Agents、Automations、Origin 和 Cursor SDK。Pro+ 和 Ultra 的每个用量池都比 Pro 更多。这三档都可以使用 Fast mode 和 Grok 强度级别。

这些方案之间的升级立即生效；降级在账单周期结束时生效。在 [cursor.com/dashboard/billing](https://cursor.com/dashboard/billing) 管理它们。见[定价与方案](https://cursor.com/help/account-and-billing/pricing.md#how-do-i-change-plans-on-a-legacy-cursor-plan)。

### Teams Standard 和 Premium

旧版 Teams 席位有两种。Standard（$40/user/mo）包含两个用量池的标准 Teams 额度，Premium（$120/user/mo）包含 Standard 额度的 5 倍。用量按席位分配，不能在成员之间转移，并随团队账单周期重置。现有团队仍然可以添加席位或更改席位类型，而不必切换方案。见[团队定价](https://cursor.com/docs/account/teams/pricing.md#legacy-teams-seats)。

## 切换后会有什么变化

- 符合条件的 Cursor 和 Grok 订阅会在结账时取消，未使用的时间会按比例退款。在 iOS 或 Android 应用中购买的 Grok Bot 订阅也会取消，并按未使用时间退款。只有通过 Apple App Store 计费的 Cursor 订阅，需要你自己在 iPhone 或 iPad 的 Settings 里取消。
- 切换时，尚未开票的按需用量会计入一张最终发票；要等这张发票付清，切换才会继续。
- 如果你的 Grok 账号中的 Grok Bot 数据多到目前还无法全部转移，Unify 会请你稍后再试。
- 你的用量会从两个每月用量池变为一个 Cursor 模型每周用量池，并与 Grok 共享。
- 第三方模型不再包含在内。在 Premium 及以上，它们仍可在 Cursor 中使用，按 API 标价从你的预付额度计费。
- 按需用量及其支出限额，会被充值和每月额外用量上限取代。
- 账单从 cursor.com 转到 [grok.com/dashboard/billing](https://grok.com/dashboard/billing)。

先从[什么是 The X Subscription？](https://cursor.com/help/the-x-subscription/overview.md)看起，然后个人方案前往 [cursor.com/unify](https://cursor.com/unify)，团队前往 [cursor.com/unify/team](https://cursor.com/unify/team)。

## 相关

- [模型与定价](https://cursor.com/docs/models-and-pricing.md)
- [团队定价](https://cursor.com/docs/account/teams/pricing.md)
- [定价与方案](https://cursor.com/help/account-and-billing/pricing.md)
- [用量与限制](https://cursor.com/help/models-and-usage/usage-limits.md)
- [按请求计费（旧版）](https://cursor.com/docs/account/pricing/request-based-legacy.md)

---

## 站点地图

[所有文档页面概览](/llms.txt)
