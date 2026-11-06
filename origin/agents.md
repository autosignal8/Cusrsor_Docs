> 原文：https://cursor.com/docs/origin/agents

# Agents 与自动化

Origin 目前以早期 beta 发布。你可以创建仓库，用 git 推送和拉取，从 GitHub 镜像，浏览并搜索代码，打开并合并 pull request，并与你的 Cursor 团队共享。

Origin 与 Cursor 自动化和 Cloud Agents 一起工作，使 Agent 可以在你的 Origin 仓库上审查、更改并推送代码。

## 自动化

[自动化](https://cursor.com/docs/cloud-agent/automations.md)按计划运行 Cloud Agents，或在源代码管理事件触发时运行。把自动化指向 Origin 仓库的方式，与指向已连接的 GitHub 或 GitLab 仓库相同。

Origin 仓库的常见触发器：

- **Push to branch**：例如推送到 `main` 或 `master`
- **Pull request opened**、**Pull request pushed**，以及相关的 PR 事件
- **Label change**：当标签被添加到 pull request 或从中移除时

从 [cursor.com/automations](https://cursor.com/automations)、Agents Window 或 `/automate` skill 创建自动化。选择 Origin 仓库（或包含它的多仓库环境），设置触发器和提示词，然后保存并激活。

触发器的完整列表，以及[计费](https://cursor.com/docs/cloud-agent/automations.md#billing)和[共享](https://cursor.com/docs/cloud-agent/automations.md#share)，见[自动化](https://cursor.com/docs/cloud-agent/automations.md)。

## Cloud agents

[Cloud Agents](https://cursor.com/docs/cloud-agent.md) 可以针对 Origin 仓库工作：克隆、建分支、提交并推送。

在 Origin 上创建的仓库中，Agent 可以打开 Origin pull request。在[从 GitHub 镜像](https://cursor.com/docs/origin/mirror-github.md)的仓库上，Agent 会打开 GitHub pull request。

从团队代码库把 Cloud Agent 挂到 Origin 仓库的方式，与挂到其他已连接的源代码管理相同。Agent 使用你的 Cursor 账号的 Origin 访问权限。

### 本地 Agent

Cursor 中的本地 Agent 也可以用 [Origin CLI](https://cursor.com/docs/origin/cli.md) [创建 Origin 仓库](https://cursor.com/docs/origin/create-repository.md#create-with-a-cursor-agent)：安装、登录、`origin repo create`、设置远程并推送。

## 相关

- [创建仓库](https://cursor.com/docs/origin/create-repository.md)
- [Pull requests](https://cursor.com/docs/origin/pull-requests.md)
- [Origin API](https://cursor.com/docs/api/origin.md)


---

## 站点地图

[所有文档页面概览](/llms.txt)
