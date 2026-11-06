> 原文：https://cursor.com/docs/models/claude-fable-5-1

Claude Fable 5.1 是 Anthropic 最新的 Mythos 级模型，并取代 [Fable 5](https://cursor.com/docs/models/claude-fable-5.md)。它面向长时间运行、复杂且异步的编程与知识工作。输入和输出费率与 Fable 5 相同。提示缓存读取低 75%。

## 启用 Fable 5.1

由于 Anthropic 处理数据保留的方式，Fable 5.1 与其他 Fable 模型有相同的隐私考量。如果你的账号、团队或组织启用了 Privacy Mode，需要先在 Cursor Dashboard 中批准 Fable 5.1 的数据保留政策，然后才能使用该模型。你可以在 [Cursor Dashboard](https://cursor.com/dashboard/restricted_models/claude-fable-5-1) 中查看该政策。

对于 Teams 账号，批准该模型政策会作用于整个 Team。

对于 Enterprise 账号，模型可用性在组织级别批准，但仍通过[模型访问控制](https://cursor.com/docs/enterprise/model-and-integration-management.md#model-access-control)在团队和组级别继续管控。

前往 [Team Settings](https://cursor.com/dashboard/restricted_models/claude-fable-5-1)，为你的 Team、组织或账号启用 Fable 5.1。

## 优势

- 在长时间运行的 Agent 会话中，沟通比 Fable 5 更清晰。
- 面向自主的多步骤工作。它能在长会话中保持意图，并以更少的中途确认把任务推进到完成。
- 深度推理。对于值得额外推敲的问题，可以使用 Thinking 变体。

## 限制

- 与其他 Fable 模型一样，需要选择加入 Anthropic 的数据保留。Privacy Mode 和 Enterprise 客户在使用前需要管理员批准。
- 输入和输出费率大约是 [Claude Opus 5.5](https://cursor.com/docs/models/claude-opus-5-5.md) 的 2.5 倍，而后者目前在 [CursorBench](https://cursor.com/cursorbench) 上的分数更高。

## 数据保留

Anthropic 针对 Fable 模型的政策在[此处](https://privacy.claude.com/en/articles/15425996-data-retention-practices-for-covered-models)有详细说明，要点如下：

- 保留的客户数据会在 30 天后自动且永久删除，除非涉及安全调查或法律保全
- 保留的客户数据默认任何人都不可读取
- 保留的企业数据不会用于训练 Claude
- 保留的客户数据不会与其他客户共享

启用并使用 Fable 5.1 不会改变你的 Privacy Mode 设置，也不会改变 Cursor 自身对你数据的保留。

由于 Fable 5.1 处理数据的方式，它在每一个启用了 Cursor [Privacy Mode](https://cursor.com/docs/enterprise/privacy-and-data-governance.md) 的 Team 中默认关闭，对每一位 Enterprise 客户也同样默认关闭，不论 Cursor Privacy Mode 的设置如何。

对于每一个关闭了 Cursor Privacy Mode 的个人客户或 Team 账号，Fable 5.1 默认开启。

对于 Enterprise 方案上的客户，或启用了 Cursor Privacy Mode 的客户，需要明确选择加入；在管理员明确启用之前，该模型不可用。启用前需要了解：

- **数据保留。** 使用 Fable 5.1 时，无论你的 Cursor [Privacy Mode](https://cursor.com/docs/enterprise/privacy-and-data-governance.md) 设置如何，Anthropic 都会存储 Agent 的输入和输出数据，以支持自动化和人工的伤害预防流程。这些数据不会用于训练或改进 Anthropic 的模型或产品。
- **选择加入适用于整个团队。** 启用 Fable 5.1 需要选择加入 Anthropic 的数据保留条款，并且这适用于你的整个团队。Enterprise 管理员可以通过[模型访问控制](https://cursor.com/docs/enterprise/model-and-integration-management.md#model-access-control)限制哪些用户组可以使用该模型。

## 自动回退到 Opus

Fable 5.1 带有严格的安全护栏。当某个请求触发其中一条护栏时，Cursor 会自动把它路由到 Claude Opus，让你的工作继续而不会报错。你不需要自己重试或切换模型。

## 工具

在 Cursor 中使用时，Fable 5.1 可以使用全部 Agent 工具，包括：

进一步了解[工具如何工作](https://cursor.com/docs/agent/overview.md#tools)以及[工具调用基础](https://cursor.com/learn/tool-calling.md)。

## 定价

Fable 5.1 是第三方模型，因此不属于 Cursor [方案](https://cursor.com/docs/models-and-pricing.md#third-party-models)包含的用量。在 The X Subscription 上，按下面的费率从你的预付额度计费。在旧版 Cursor 方案上，它从 **Other Models** 用量池扣减。所有价格均按每百万 tokens 计算。

Fable 5.1 按每百万输入 tokens $10、每百万输出 tokens $50 计费，与 Fable 5 相同。提示缓存读取为每百万 tokens $0.25，比 Fable 5 的 $1.00 缓存读取费率低 75%。缓存写入仍为每百万 tokens $12.50。

Fable 5.1 支持 Thinking 变体，用于更深的推理。我们建议使用 high Thinking 变体，以获得最强的结果。

---

## 站点地图

[所有文档页面概览](/llms.txt)
