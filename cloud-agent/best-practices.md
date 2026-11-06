> 原文：https://cursor.com/docs/cloud-agent/best-practices

# 最佳实践

使用这些建议，让 Cloud Agent 的运行更可靠。

## 先设置环境

使用 [Cloud Agent 环境设置](https://cursor.com/docs/cloud-agent/setup.md)，让 Cursor 的环境配置就绪。就像人类开发者一样，环境设置正确时，Cursor 会做得更好。

## 确保 Agent 能访问它需要的东西

在运行 Cloud Agent 之前，请核对这些前提条件：

- **密钥**：确保 Agent 可以通过仪表盘中的 [Secrets 标签页](https://cursor.com/dashboard/cloud-agents)访问所需密钥（API 密钥、数据库凭证等）。对于云角色，优先使用 [OIDC token](https://cursor.com/docs/cloud-agent/identity.md)，而不是长期有效的访问密钥。
- **出站控制**：如果启用了[网络访问](https://cursor.com/docs/cloud-agent/security-network.md)限制，请确保本地开发所需的全部 URL 都已加入允许列表。
- **本地可测试性**：你的仓库应当能够在本地顺利运行，而不依赖从虚拟机无法到达的外部服务。如果人类开发者很难在本地测试，Agent 也会很难。

## 使用 Skills 和 agents.md 配置你的 Agent

如果 Cloud Agent 难以测试它的改动，我们建议使用 [Skills](https://cursor.com/docs/skills.md) 和 agents.md 来配置你的 Agent。把项目 Skills 提交到仓库，为你自己的 Cloud Agents [同步个人 Skills](https://cursor.com/docs/skills.md#use-personal-skills-with-cloud-agents)，或[发布一个 Skill](https://cursor.com/docs/plugins.md#publish-a-skill-to-your-team)，以便队友安装。

把 Agent 看作一位聪明、但上下文很少的人类开发者。确保它做对事情的最好办法，是给它理解该做什么所需的上下文。

例如，在 Cursor，我们的 agents.md 列出了在单体仓库中运行和调试最常用微服务的技巧。我们还有大量关于如何测试和调试关键服务的 Skills，每个都清楚地说明了何时使用该 Skill。

这些 Skills 包含深入的细节，例如如何调试某个特定微服务，或在测试需要时如何设置第三方依赖。

## 使用 Rules 强制执行约定

Cloud Agents 可以在三个层级读取并遵循 [Rules](https://cursor.com/docs/rules.md)：

- **用户 Rules**：在 Cursor Settings 中设置，适用于你在所有仓库中的会话。最适合只想对自己生效的规则。
- **团队 Rules**：在 [Rules、Commands、Hooks 仪表盘](https://cursor.com/dashboard/team-content)中设置，适用于每个仓库中的所有团队成员。最适合全组织的约定。
- **仓库 Rules**：提交到仓库的 `.cursor/rules/*.mdc` 文件，适用于使用该仓库的所有 Agent。最适合仓库或项目特定的约定。

## 给 Agent 它需要的工具

我们经常发现，Agent 受限于它们能够访问的工具。我们建议使用 MCP 并创建自定义工具，使 Agent 能够访问人类开发者所能访问的相同系统。

## 让工具适合 Agent

重要的是创建 Agent 擅长使用的工具。我们建议创建工具，并根据观察到的 Agent 使用方式进行迭代。

例如，在 Cursor，我们创建了一个自定义 CLI，供模型在我们的代码库中运行微服务。我们发现，在运行自定义开发命令时，例如来自 `package.json` 文件的命令，有些模型会忘记参数，或者 Agent 会被嘈杂的构建日志分散注意力，而人类开发者知道应该忽略这些日志。

---

## 站点地图

[所有文档页面概览](/llms.txt)
