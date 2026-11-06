> 原文：https://cursor.com/docs/agent/tools/search

# 搜索

## Instant Grep

最快的找代码方式是精确匹配：函数名、变量、错误字符串或正则表达式。当你引用特定符号时，Agent 会自动使用 grep。

Cursor 内置了 [Instant Grep](/changelog/2-1#instant-grep-beta)，这是一个自定义搜索引擎，在大型代码库上的表现优于 `ripgrep`。它自动运行，无需配置。

Instant Grep 支持完整正则表达式和词边界匹配，因此 Agent 可以构造 `import.*PaymentService` 或 `PaymentFailedError` 这样的模式，以跨文件追踪引用。

## 隐私与安全

Instant Grep 在你的机器上构建并查询索引。Cursor 不会上传文件路径或代码来构建搜索索引，也不会为搜索存储你的代码库嵌入。

当 Agent 打开一个匹配项时，该文件内容仍可能被包含在模型请求中。[数据使用](/data-use) 说明 Cursor 如何处理这些数据。

## Explore subagent

Agent 可以启动一个 [Explore subagent](https://cursor.com/docs/subagents.md)，它在自己的上下文窗口中运行，并使用更快的模型。它可以执行许多并行搜索，而不会让主对话膨胀，只返回相关发现。

当 Agent 判断某项任务能从广泛搜索中受益时，会自动使用 Explore subagent。你也可以直接要求：「用一个 subagent 找出我们验证用户输入的所有位置。」

这对上下文管理很有用。搜索大量文件会产生大量上下文。Subagent 通过总结结果而不是倾倒原始文件内容，让主对话保持聚焦。

## 常见问题

### Cursor 支持多根工作区吗？

支持。Cursor 支持[多根工作区](https://code.visualstudio.com/docs/editor/workspaces#_multiroot-workspaces)。每个工作区文件夹的上下文都可供 Agent 使用。一些依赖单个 git 根的功能（如 Worktrees）在多根工作区中会被禁用。Cloud Agents 不支持多根工作区。


---

## 站点地图

[所有文档页面概览](/llms.txt)
