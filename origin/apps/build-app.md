> 原文：https://cursor.com/docs/origin/apps/build-app

# 构建 Origin App

Origin 目前以早期 beta 发布。你可以创建仓库，用 git 推送和拉取，从 GitHub 镜像，浏览并搜索代码，打开并合并 pull request，并与你的 Cursor 团队共享。

你必须是工作区管理员才能安装该 app。

## 注册 app

### 生成 Ed25519 密钥对

只接受 Ed25519 密钥：

```bash
openssl genpkey -algorithm ED25519 -out origin-app-private.pem
openssl pkey -in origin-app-private.pem -pubout -out origin-app-public.pem
```

### 创建 app 并添加公钥

在 [Origin app 设置](https://cursor.com/codebase/settings/apps)中创建 app，并把 `origin-app-public.pem` 的内容添加为签名密钥。一个 app 最多持有 10 个有效签名密钥。在你[发布它们](https://cursor.com/docs/origin/apps/publish.md)之前，app 是私有的。

### 复制 App ID

App ID 在该 app 的页面上。App ID 以 `app_` 开头。

## 安装 app

### 把 app 安装到你的所有者上

在同一设置中该 app 的安装页面，把 app 安装到持有你要克隆的仓库的所有者上，并选择那些仓库。

### 复制 installation id

installation id 位于该 installation 页面的 URL 中，`/codebase/settings/apps/installations/{installationId}`。installation id 以 `i_` 开头。

要以编程方式读取 installation id，用 app JWT 作为 bearer 调用 [List App Installations](https://cursor.com/docs/api/origin/reference/list-app-installations.md)。响应包含每个 installation 的 `id`、`target.slug`、`scopes` 和 `repoSelectionMode`。


---

## 站点地图

[所有文档页面概览](/llms.txt)
