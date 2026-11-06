> 原文：https://cursor.com/docs/origin

# Origin

Origin 目前以早期 beta 发布。你可以创建仓库，用 git 推送和拉取，从 GitHub 镜像，浏览并搜索代码，打开并合并 pull request，并与你的 Cursor 团队共享。

Origin 是 Cursor 用来存储和共享代码的 git 托管平台。用它托管仓库、从 GitHub 同步项目，并在浏览器中浏览团队的 Origin 仓库。

[媒体](/docs-static/images/origin/create-and-push.mp4)

## 你能做什么

在早期 beta 中，你可以：

- [创建 Origin 仓库](https://cursor.com/docs/origin/create-repository.md)，包括通过 Cursor Agent 创建
- 用标准 git [克隆、推送和拉取](https://cursor.com/docs/origin/git.md)
- [把 GitHub 仓库镜像](https://cursor.com/docs/origin/mirror-github.md)到 Origin
- [打开、审查并合并 pull request](https://cursor.com/docs/origin/pull-requests.md)，并用 [Code Tours](https://cursor.com/docs/origin/code-tours.md) 引导审查者逐一查看
- 在 [cursor.com/codebase](https://cursor.com/codebase) [浏览并搜索](https://cursor.com/docs/origin/browse.md)代码
- 管理[仓库设置](https://cursor.com/docs/origin/settings.md#repository-settings)和[代码库设置](https://cursor.com/docs/origin/settings.md#codebase-settings)
- 从代码库设置安装 [Origin Apps](https://cursor.com/docs/origin/apps.md)
- 创建[私有 Origin Apps](https://cursor.com/docs/origin/apps/build-app.md)，通过我们的[公共 API](https://cursor.com/docs/api/origin.md)在 Origin 上构建集成
- 把[自动化和 Cloud Agents](https://cursor.com/docs/origin/agents.md)连接到 Origin 仓库
- [安装 Origin CLI](https://cursor.com/docs/origin/cli.md)，用于终端工作流

## 谁可以访问

Origin 代码存储适用于 Premium、Super、Ultra、Teams 和 Enterprise，以及旧版 Start、Pro、Pro+ 和 Ultra 方案。Free 和 Plus 不可用。

## 隐私

Origin 遵循命名空间所有者的 [Privacy Mode](https://cursor.com/help/security-and-privacy/privacy.md)：拥有该仓库的团队或个人。

## 启用 Origin

团队使用 Origin 之前，需要有人认领代码库名称。代码库名称是仓库所在的命名空间，也就是 `https://cursor.com/codebase/{owner}/{repo}` 中的 `{owner}`。

在 beta 期间，命名空间认领之后不能更改或更新。请谨慎选择代码库名称。

- 任何团队成员都可以认领代码库名称：打开 [cursor.com/codebase](https://cursor.com/codebase)，选择 **Get Started**，并按设置流程操作
- 管理员可以随时从仪表盘为团队禁用 Origin
- 使用旧版隐私模式的团队无法启用 Origin，因此若要访问，请先切换到 [Privacy Mode](https://cursor.com/help/security-and-privacy/privacy.md#how-do-i-enable-privacy-mode)

代码库名称认领后，管理员可以创建仓库，并从[代码库设置](https://cursor.com/docs/origin/settings.md#permissions)向团队其余成员授予仓库访问权限。

## 仓库列表

启用 Origin 后，[cursor.com/codebase](https://cursor.com/codebase) 会显示你团队的仓库。

- 用 **Find repo...** 搜索
- 用 **New** 创建仓库，或用 **Sync from GitHub** 迁入已有仓库
- 打开仓库即可浏览代码、pull request 和设置

仓库图标表明每个仓库的托管方式：Origin 托管的仓库，以及从 GitHub 同步的仓库。对于 Origin 托管的仓库，Origin 是事实来源。对于[已同步的仓库](https://cursor.com/docs/origin/mirror-github.md)，GitHub 仍是事实来源，Origin 是镜像。

## 后续步骤

- [安装 Origin CLI](https://cursor.com/docs/origin/cli.md)
- [创建仓库](https://cursor.com/docs/origin/create-repository.md)
- [镜像 GitHub 仓库](https://cursor.com/docs/origin/mirror-github.md)
- [Pull requests](https://cursor.com/docs/origin/pull-requests.md)
- [浏览与搜索](https://cursor.com/docs/origin/browse.md)
- [仓库设置](https://cursor.com/docs/origin/settings.md#repository-settings)
- [代码库设置](https://cursor.com/docs/origin/settings.md#codebase-settings)
- [Agents 与自动化](https://cursor.com/docs/origin/agents.md)


---

## 站点地图

[所有文档页面概览](/llms.txt)
