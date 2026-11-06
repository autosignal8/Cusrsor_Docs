> 原文：https://cursor.com/docs/cursor-router

# Cursor Router

Cursor Router 是 **Auto** 背后的模型路由系统。并不是每个请求都需要前沿级智能，因此路由器会把每个请求送到适合该任务的模型：简单请求送到快速、高效的模型，复杂工作送到能力最强的模型。

Cursor Router 目前仅在 Teams 和 Enterprise 方案上可用。

## 工作方式

当你在模型选择器中选择 **Auto**，并选择 **Balance** 或 **Intelligence** 时，Cursor Router 会对每个 Agent 请求运行分类器，并依据任务类型和复杂度进行路由。路由器会挑选对该请求仍能产出相当质量、同时最划算的模型。

Cursor Router 由数据驱动，并由 Cursor 管理。你不能亲手指定由哪个模型处理某个请求，模型池也会随着新模型发布而变化。你通过选择优化模式来引导路由。

在 Enterprise 方案上，Cursor Router 会遵守你团队的模型访问控制。如果某个模型对你的团队被屏蔽，路由器会改为路由到允许的模型。屏蔽过多模型会降低路由质量，并可能停用路由器。要节省成本，路由器在不调用其他前沿模型时，需要一个既强大又划算的模型可用，因此启用 [Grok 4.6](https://cursor.com/docs/models/grok-4-6.md) 是路由器能够工作的必要条件。

## 优化模式

打开模型选择器，选择 **Auto**，然后在 **Optimize For** 下挑选一种模式：

- **Cost**：使用先前的 Auto 路由逻辑。它优化 token 支出。
- **Balance**：在智能、速度和成本上做优化。
- **Intelligence**：把更难的任务路由到能力最强的模型，成本低于始终运行单一前沿模型。

Balance 和 Intelligence 比 Cost 更快消耗你的用量限额。你可以随时切换模式。

## 定价

Cost 模式按 Cursor 模型请求计费，并从每周用量池扣减，不收取 Cursor Token 费率。Balance 和 Intelligence 按每个请求所路由到的模型的标价计费。这些模式中，路由到第三方还会产生 Cursor Token 费率，除非其[在每一个定价档位上的缓存读取价格都严格低于每百万 tokens $0.10](https://cursor.com/docs/models-and-pricing.md#cursor-token-rate)。

## 团队设置

管理员从[团队仪表板](https://cursor.com/docs/account/teams/dashboard.md)配置 Cursor Router。

- **Enable Cursor Router**：打开或关闭路由。启用后，使用 Auto 的团队成员会由 Cursor Router 路由。Enterprise 团队必须手动启用路由器，因为它默认关闭。在 Enterprise 方案上，还可以按[组织组](https://cursor.com/docs/enterprise/organization-groups.md)配置路由器。
- **Routing preferences**：选择团队成员可以在 Auto 中选择哪些优化模式。你最多可以禁用 2 种模式。
- **Underlying model**：在每次响应开始时显示 Auto 路由到了哪个模型，或者保持隐藏。隐藏是默认值，也是建议值，这样会按结果本身来评判，而不是按模型名称。适用于 Balance 和 Intelligence 模式。
- **Impose Auto**：让 Auto 成为团队中每个人的默认模型。**Soft** 把每个新聊天默认设为 Auto；成员仍然可以切换模型。**Hard** 把模型选择器锁定为 Auto。两者默认都关闭。

## 通过 SDK 使用 Router

[TypeScript SDK](https://cursor.com/docs/sdk/typescript.md#cursor-router) 和 [Python SDK](https://cursor.com/docs/sdk/python.md#cursor-router) 把 Cursor Router 暴露为模型 id `auto-smart`，参数为 `optimize_for`（`cost`、`balanced` 或 `intelligence`）。在把选择硬编码之前，先调用 `Cursor.models.list()`，确认该 API 密钥所属的团队可以使用 Router。

### TypeScript

```typescript
import { Agent } from "@cursor/sdk";

await using agent = await Agent.create({
  apiKey: process.env.CURSOR_API_KEY!,
  model: {
    id: "auto-smart",
    params: [{ id: "optimize_for", value: "balanced" }],
  },
  local: { cwd: process.cwd() },
});
```

### Python

```python
import os

from cursor_sdk import Agent, LocalAgentOptions, ModelParameterValue, ModelSelection

with Agent.create(
    model=ModelSelection(
        id="auto-smart",
        params=[ModelParameterValue(id="optimize_for", value="balanced")],
    ),
    local=LocalAgentOptions(cwd=os.getcwd()),
) as agent:
    ...
```

SDK 运行的是 Cursor 的 Agent 工作流。它不是独立的 chat-completions 或原始推理 API。目录发现、单次运行的模式覆盖和故障排除，见 [TypeScript SDK 中的 Cursor Router](https://cursor.com/docs/sdk/typescript.md#cursor-router) 或 [Python SDK](https://cursor.com/docs/sdk/python.md#cursor-router)。

## 相关

- [Cursor Router 帮助](https://cursor.com/help/models-and-usage/cursor-router.md)

---

## 站点地图

[所有文档页面概览](/llms.txt)
