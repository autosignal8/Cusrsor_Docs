> 原文：https://cursor.com/docs/origin/pull-requests

# Pull requests

Origin 目前以早期 beta 发布。你可以创建仓库，用 git 推送和拉取，从 GitHub 镜像，浏览并搜索代码，打开并合并 pull request，并与你的 Cursor 团队共享。

在 [cursor.com/codebase](https://cursor.com/codebase) 打开仓库，选择 **Pull Requests** 标签页，即可查看打开的 pull request，并打开其中一个进行审查。

## Pull request 列表

**Pull Requests** 标签页列出该仓库的 pull request。打开一行即可审查活动、提交、检查和已更改的文件。

## 打开 pull request

把分支推送到 Origin，然后针对基分支打开 pull request：

```bash
git checkout -b my-change
git push -u origin my-change
```

在仓库的 **Code** 标签页，分支到达 Origin 后使用创建 pull request 的流程。Cursor Cloud Agents 也可以在任务中打开 pull request。在[从 GitHub 镜像的仓库](https://cursor.com/docs/origin/mirror-github.md)上，这些 Agent 会打开 GitHub pull request。

## Pull request 页面

从列表中打开 pull request 进行审查。每个 pull request 有四个标签页：

- **Activity**：按顺序显示打开、评论、审查、状态变更和其他活动
- **Commits**：pull request 中的提交；打开一个即可查看其 diff
- **Checks**：该分支的检查运行和状态；结果会在检查针对 head 提交上报时出现
- **Changes**：文件 diff；可以在 diff 上对行评论并留下审查，或切换到 **Tour**，用 [Code Tours](https://cursor.com/docs/origin/code-tours.md) 进行引导式走读

除了这些标签页，你还可以请求审查者、留下审查、在 pull request 或单行上评论，并在审查和 CI 满足后合并。Origin 会显示合并冲突，以便你在合并前解决它们。

当仓库的[规则](https://cursor.com/docs/origin/settings.md#rules-and-protections)要求审查时，拥有写权限的人给出的 **Request Changes** 审查会阻止合并。要解除阻止，该审查者批准，或拥有写权限的人驳回该审查。

## 从 GitHub 镜像的仓库

在[从 GitHub 镜像](https://cursor.com/docs/origin/mirror-github.md)的仓库上，**Pull Requests** 标签页显示 GitHub pull request。你可以在 Cursor 中审查它们。GitHub 仍是事实来源。

挂到镜像仓库的 Cloud Agents 会打开 GitHub pull request。

名为 `origin/...` 的分支留在 Origin 上，以便 GitHub 不可用时你能继续工作。它们不是 GitHub pull request 的 head。用 `origin push local` 推送它们。见[仅存在于 Origin 的分支](https://cursor.com/docs/origin/git.md#forge-local-branches-on-mirrored-repos)。

直接在 Origin 上创建的仓库中打开的 pull request 留在 Origin。它们不会被镜像到其他地方。


---

## 站点地图

[所有文档页面概览](/llms.txt)
