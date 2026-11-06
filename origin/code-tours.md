> 原文：https://cursor.com/docs/origin/code-tours

# Code Tours

Origin 目前以早期 beta 发布。你可以创建仓库，用 git 推送和拉取，从 GitHub 镜像，浏览并搜索代码，打开并合并 pull request，并与你的 Cursor 团队共享。

Code Tours 按适合审查的顺序带你走读 [pull request](https://cursor.com/docs/origin/pull-requests.md)。Cursor 读取 diff，把相关改动分组，并在代码旁边解释每一组。Origin 上的每个 pull request 在其 **Changes** 标签页上都有一次导览。

下面是一次把 webhook 重试移到队列上的 pull request 导览：

![一次 webhook 重试改动的 Code Tour：先是概览，然后是重试路径，并在其 diff 上方标出超时风险](/docs-static/images/origin/code-tour-guided.png)

## 打开导览

### 打开 pull request

在 [cursor.com/codebase](https://cursor.com/codebase)，打开仓库，选择 **Pull Requests**，并打开一个 pull request。

### 切换到 Tour

在 **Changes** 标签页，在 **Diff** / **Tour** 控件中选择 **Tour**，或按 T。再按一次 T 回到 diff。

本页大部分内容适用于在 Origin 上创建的仓库中的 pull request。[从 GitHub 镜像的仓库](https://cursor.com/docs/origin/mirror-github.md)中的 GitHub pull request 也会获得导览，但功能更少。

## 导览提供什么

每次导览都是为其 pull request 撰写的。小修复可能只有几句话，而大改动会对其各个部分做更长的走读。

大多数导览会先说明改动做了什么以及为什么，面向还没在代码库那一部分工作过的人来写。然后导览按组走读改动，并在代码旁边解释每一组。沿途它会指出值得细看的地方，例如其他代码所依赖的行为。想了解更多时，就导览的任何部分询问 Cursor，或切换到 **Diff** 只看文件改动。

你可能不认识的术语带有点状下划线。悬停在其上，或用键盘聚焦它，即可看到简短定义。pull request 描述中的截图和录屏，或撰写它的 [Cloud Agent](https://cursor.com/docs/cloud-agent.md) 中的截图和录屏，可能出现在有帮助的地方。当 Agent 撰写 pull request 时，导览还可能使用该 Agent 关于为何采取该做法、需要留意什么，以及它测试了什么的笔记。

## 找到方向

- **跳到某一节。** 选择导览左上角的短行以列出各节，然后选一节。
- **分享一节。** 选择节标题旁的链接图标，以复制指向它的链接。
- **把一节标为已审查。** 勾选节旁边的框，把其文件标为已查看。**Diff** 视图保持同一进度。
- **展开文件。** 测试文件一开始是折叠的，以便先呈现正在审查的代码。已删除、生成的和二进制文件也可能一开始是折叠的。

## 自定义导览

要获得不同的导览，打开导览标题旁的 **Generate custom tour** 菜单。从那里你可以：

- 从要点开始
- 选择导览覆盖哪些文件
- 设置深度
- 添加你自己的说明

对于镜像 GitHub 仓库中的 GitHub pull request，除要点之外的各项都可用。

要导览 pull request 历史的一部分，在文件上方的版本选择器中选择一个版本、一次提交或一个范围，导览会随之变化。选择 **Changes since your last review**，只看自你上次审查以来的变化。

## 就导览询问 Cursor

选择节标题旁的 **+**，把该节加入页面底部的 **Ask Cursor** 框，然后输入你的问题。要询问某一段，选中文本并选择 **Add to chat**，或按 Cmd I。pull request 页面上聊天可用时，你会看到这两者。

正在处理该 pull request 的 [Cloud Agent](https://cursor.com/docs/cloud-agent.md) 也可以编辑导览。让它重写一节、添加该改动的截图或录屏，或用不同说明把整次导览再写一遍。

## 导览结束时

在超过三个文件的 pull request 上，最后一节之后可能出现 **What next?** 卡片。它会建议几个后续导览，例如 **Changes since your last review**、**Files you own**，或 **Detailed version of this tour**。选择 **Something else…** 来描述另一种。该卡片也可能提供 **Add media to this tour**，它会让 Cloud Agent 把截图、录屏、示意图或表格加入导览。

导览加载完成后，**Is this tour helpful?** 让你点赞或点踩，也可以留下评论。

## 导览何时撰写

Cursor 只为在 Origin 上创建且由团队拥有的仓库提前撰写导览。在这些仓库中，pull request 打开时（包括草稿）以及推送新提交时，它会在后台撰写导览。通常在第一位审查者查看之前就已就绪。

如果你打开时导览尚未就绪，Cursor 会当场撰写，每一节出现时你就可以阅读。你请求的导览，例如不同深度或版本范围，会在你请求时撰写。

## 新提交

有人向 pull request 推送时，Cursor 会在后台为新提交撰写导览。你继续阅读当前这一次，**Updating…** 会出现在 **Diff** / **Tour** 控件旁边。如果你在最新版本上，新导览就绪后会替换旧的。如果你在看较早的版本，想切换时选择 **See latest**。如果你有未保存的评论，Cursor 会先向你确认。

在较早的版本上，针对不在该版本中的代码的评论会标为 **Not in this version**。

## 大型 pull request

有些 pull request 太大，无法生成导览。你会看到 **Too large for a tour**，以及一个把你带回 diff 的 **View files** 按钮。较小的 pull request 各自有自己的导览。

## 用量与计费

Cursor 提前撰写的导览不计入你的用量。当你打开或请求导览而导致 Cursor 撰写时，它会计入你的 [Cursor 用量](https://cursor.com/docs/models-and-pricing.md)，方式与一次 Agent 请求相同。阅读已经写好的导览不消耗用量。当 Cloud Agent 编辑或重写导览时，这部分工作计入该 Agent 的用量。

要让 Cursor 在你打开或请求时撰写导览，你需要付费的 Cursor 方案。在 Free 方案上，或仍处于试用期的个人方案上，你可以阅读已经存在的导览。

## 隐私

Code Tours 遵循拥有该仓库的团队或个人的 [Privacy Mode](https://cursor.com/help/security-and-privacy/privacy.md)，与 Origin 的其余部分一样。要撰写导览，Cursor 会把 pull request 的 diff 和描述发送给模型。导览与 pull request 一起保存，任何能看到该 pull request 的人都可以阅读它。

## FAQ

### 为什么导览会显示 “Too large for a tour”？

该 pull request 大于 Cursor 能为之撰写导览的规模。选择 **View files** 在 diff 中审查它。如果你把工作拆成更小的 pull request，每一个都会有自己的导览。

### 导览失败或中途停止了。我该怎么办？

在 **Couldn't generate the tour** 下选择 **Retry**。如果持续失败，请确认你使用的是付费方案。见[用量与计费](https://cursor.com/docs/origin/code-tours.md#usage-and-billing)。

### 为什么导览里看不到最新的提交？

Cursor 仍在为最新提交撰写导览。**Updating…** 消失后，导览会自行切换。如果没有，选择 **See latest**。

### 我能得到更短的导览，或只关于我的文件的导览吗？

可以。打开 **Generate custom tour** 菜单来设置深度，或选择导览覆盖哪些文件。见[自定义导览](https://cursor.com/docs/origin/code-tours.md#customize-a-tour)。

## 相关

- [Pull requests](https://cursor.com/docs/origin/pull-requests.md) - 打开、审查并合并 pull request
- [浏览与搜索](https://cursor.com/docs/origin/browse.md) - 浏览文件、搜索代码并查看提交历史
- [Origin](https://cursor.com/docs/origin.md) - 概览、访问权限和隐私


---

## 站点地图

[所有文档页面概览](/llms.txt)
