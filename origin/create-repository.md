> 原文：https://cursor.com/docs/origin/create-repository

# 创建 Origin 仓库

Origin 目前以早期 beta 发布。你可以创建仓库，用 git 推送和拉取，从 GitHub 镜像，浏览并搜索代码，打开并合并 pull request，并与你的 Cursor 团队共享。

从 Web UI 创建一个空的 Origin 仓库，或让 Cursor Agent 为你创建。然后克隆它并用 git 推送。如果代码已经在 GitHub 上，请改为[镜像该仓库](https://cursor.com/docs/origin/mirror-github.md)。

## 在 UI 中创建

在 [cursor.com/codebase](https://cursor.com/codebase)：

1. 选择 **New**
2. 在 **New repo** 对话框中输入 **Repo Name**。如果仓库将归团队代码库所有，还要选择 **Internal** 或 **Private** 可见性。个人账号拥有的仓库始终创建为私有，因此不显示可见性选择器。
3. 选择 **Create Repo**

创建之后，打开仓库，从绿色的 **Code** 按钮复制克隆 URL，再从你的机器推送。

## 从 GitHub 同步

要把已有的 GitHub 仓库复制到 Origin，在代码库主页选择 **Sync from GitHub**，而不是 **New**。你选择 GitHub 组织和仓库，然后确认同步。前提条件、会同步的内容，以及如何与 GitHub 分离，见[镜像 GitHub 仓库](https://cursor.com/docs/origin/mirror-github.md)。

## 用 Cursor Agent 创建

Cursor Agent 可以在任务中创建 Origin 仓库。在 Cursor 里让 Agent 在 Origin 上创建仓库；它可以安装 [Origin CLI](https://cursor.com/docs/origin/cli.md)、登录、创建仓库、设置远程，并推送。

Agent 使用与你的 Cursor 账号相同的权限。创建要成功，你需要有 Origin 代码存储的访问权限。[Cloud Agents](https://cursor.com/docs/origin/agents.md) 可以针对已有的 Origin 仓库工作：克隆、建分支、提交并推送。在 Origin 上创建的仓库中，它们可以打开 Origin pull request；在[从 GitHub 镜像的仓库](https://cursor.com/docs/origin/mirror-github.md)上，它们打开 GitHub pull request。

## 名称、可见性与共享

在 **New repo** 对话框中输入 **Repo Name**。接下来取决于仓库归团队代码库所有，还是归你的个人账号所有。

### 团队拥有的仓库

- 在 **New repo** 对话框中选择可见性：
  - **Internal**：对团队中有权访问该代码库的任何人可见
  - **Private**：仅对直接被授予访问权限，或通过代码库权限获得访问的成员可见
- 团队访问遵循你的 Cursor 团队和代码库访问权限
- 创建之后，从仓库的 **Settings** 标签页管理可见性和按仓库的访问权限。见[设置](https://cursor.com/docs/origin/settings.md)。

### 个人仓库

- 在个人账号下创建的仓库始终为 **Private**，默认仅你可见。个人仓库的 **New repo** 对话框不显示可见性选择器。
- 要共享个人仓库，打开仓库，进入 **Settings**，然后进入 **Collaborators**，邀请你想授予访问权限的人。

## 推送第一次提交

在 UI 中创建空仓库后，初始化本地项目并推送：

```bash
git clone https://origin.cursor.com/{owner}/{repo}.git
cd {repo}
# add your files
git add .
git commit -m "Initial commit"
git push -u origin main
```

如果本地已有项目：

```bash
cd your-project
git remote add origin https://origin.cursor.com/{owner}/{repo}.git
git push -u origin main
```


---

## 站点地图

[所有文档页面概览](/llms.txt)
