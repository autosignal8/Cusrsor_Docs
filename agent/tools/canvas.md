> 原文：https://cursor.com/docs/agent/tools/canvas

# Canvases

Canvas 让 Cursor 创建可在聊天旁边渲染的交互式产物。你不必滚动浏览很长的 markdown 表格或代码块，而是得到一个独立视图，用分区、统计数据和表格排版，并且可以重新打开、编辑和迭代。

向 Agent 要求仪表盘、分析、审计或报告，当 Canvas 更合适时，Cursor 会在 Canvas 中打开结果。[Cloud Agents](https://cursor.com/docs/cloud-agent.md) 也可以在工作过程中构建 Canvas。

## 工作原理

1. Cursor 判断你的任务能从可视化或交互式视图中受益，或者你直接提出了这样的要求。
2. Cursor 构建 Canvas，并在聊天中插入对它的引用。
3. 你查看渲染后的视图，切换到源码进行调整，或让 Cursor 修改它。
4. Cursor 保存 Canvas，以便你稍后用新数据重新打开并重新运行。

每个 Canvas 都会出现在工作区的 Canvas 列表中，因此你可以跳回过去的 Canvas，而无需重新运行它们。

## 打开 Canvas

- **从 Cursor**：当 Cursor 创建 Canvas 时，回复末尾会出现一张卡片。点击它即可打开。
- **命令面板**：从命令面板运行 **Open Canvas**，它列在 View 下。
- **Agents Window**：从 [Agents Window](https://cursor.com/docs/agent/agents-window.md) 的新标签页菜单直接打开 Canvas 标签页。

## 分享 Canvas

分享 Canvas 会给队友一个指向该视图实时快照的链接：相同的布局、图表和表格，无需重新运行 Agent 或翻找聊天记录。使用 Canvas 工具栏中的 **Publish** 发布或刷新分享，然后从工具栏复制链接，发送给需要它的队友。团队中的任何人都可以在浏览器中以只读方式打开该链接。

[仪表盘](https://cursor.com/dashboard/shared-canvases)上的 **Shared Canvases** 页面列出你自己发布的 Canvas，因此你可以重新打开、复制或删除自己的分享。它不会列出队友发布的 Canvas。要打开队友的 Canvas，请向他们索取分享链接。

共享 Canvas 在付费方案上可用（Premium 及以上、Teams、Enterprise，以及旧版 Pro、Pro+ 和 Ultra）。Free 账户不能创建分享。因为只有你团队的成员才能打开分享，所以你需要在一个团队中；同时属于某个团队的个人付费方案成员也可以分享。分享还要求使用允许数据存储的 Privacy Mode（Legacy Privacy Mode 会阻止分享）。

团队管理员可以从 [Team settings](https://cursor.com/dashboard/settings#shared-canvases) 的 **Shared Canvases** 下为组织关闭共享 Canvas。

## 迭代 Canvas

Canvas 设计为易于细化。

- 如果布局不对，告诉 Cursor 要改什么，而不是手工编辑。
- 如果数字看起来过时或不对，让 Cursor 重新运行底层查询，或展示它的工作过程。
- 对于较大的返工，还原后用更详细的说明再次提示 Cursor。这通常比通过小的后续消息一点点推动更快。
- 对于小的调整，你也可以手动编辑源代码。

## 打包进 Skills

常见的 Canvas 工作流可以打包为 [Skills](https://cursor.com/docs/skills.md)，这样每次你提出要求时，Cursor 都会生成一致的布局。

一个 Canvas Skill 通常包括：

- **触发描述**，让 Cursor 知道何时使用它，例如「季度收入报告」或「依赖审计」。
- **布局说明**，定义 Canvas 应包含的分区、统计数据和表格。
- **数据源和查询**，Cursor 应运行它们来填充视图，例如 SQL 查询、API 调用或 shell 命令。
- **格式规则**，如单位、日期范围或排序顺序。

Skill 就位后，一句简短的提示词就足以用新数据重新生成 Canvas，并且每个使用该 Skill 的队友都会得到相同的输出形态。

## 相关

- [Agents Window](https://cursor.com/docs/agent/agents-window.md)
- [Skills](https://cursor.com/docs/skills.md)
- [给 Agent 写提示](https://cursor.com/docs/agent/prompting.md)


---

## 站点地图

[所有文档页面概览](/llms.txt)
