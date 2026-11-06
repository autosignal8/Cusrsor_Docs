> 原文：https://cursor.com/docs/origin/apps

# Origin Apps

Origin 目前以早期 beta 发布。你可以创建仓库，用 git 推送和拉取，从 GitHub 镜像，浏览并搜索代码，打开并合并 pull request，并与你的 Cursor 团队共享。

从 [Origin App Marketplace](https://cursor.com/codebase/apps) 把第三方 app 加到你的代码库，或构建一个调用 [Origin API](https://cursor.com/docs/api/origin.md) 的私有 app。

你在[代码库设置](https://cursor.com/docs/origin/settings.md#codebase-settings)的 **Apps** 页面安装和管理 app，地址是 [cursor.com/codebase/settings/apps](https://cursor.com/codebase/settings/apps)。要查看仓库上安装了哪些 app，打开其[仓库设置](https://cursor.com/docs/origin/settings.md#apps)中的 **Apps** 标签页。

## 第三方 app

[Origin App Marketplace](https://cursor.com/codebase/apps) 包含这些 app。要在你的代码库上安装一个，登录 Cursor 并打开该 app 的列表页面。

| 类别 | App |
| --- | --- |
| 部署 | [Cloudflare](https://cursor.com/codebase/apps/public/cloudflare), [Netlify](https://cursor.com/codebase/apps/public/netlify), [Render](https://cursor.com/codebase/apps/public/render), [Spacelift](https://cursor.com/codebase/apps/public/spacelift-deploy-us), [Vercel](https://cursor.com/codebase/apps/public/vercel) |
| CI | [Buildkite](https://cursor.com/codebase/apps/public/buildkite), [CircleCI](https://cursor.com/codebase/apps/public/circleci), [CloudBees](https://www.cloudbees.com/blog/enterprise-jenkins-now-builds-from-cursor-origin), [Depot](https://cursor.com/codebase/apps/public/depot), [EngFlow](https://cursor.com/codebase/apps/public/engflow), [Namespace](https://cursor.com/codebase/apps/public/namespacelabs), [RWX](https://cursor.com/codebase/apps/public/rwx), [Tuist](https://cursor.com/codebase/apps/public/tuist) |
| 代码审查 | [CodeRabbit](https://cursor.com/codebase/apps/public/coderabbitai), [Greptile](https://cursor.com/codebase/apps/public/greptile), [Tenki](https://cursor.com/codebase/apps/public/tenki) |
| 安全 | [Artemis Security](https://cursor.com/codebase/apps/public/artemis-security), [Semgrep](https://cursor.com/codebase/apps/public/semgrep), [Socket Security](https://cursor.com/codebase/apps/public/socket-security), [Upwind](https://cursor.com/codebase/apps/public/upwind-security) |
| 监控 | [Sentry](https://cursor.com/codebase/apps/public/sentry) |
| 项目管理 | [Linear](https://cursor.com/codebase/apps/public/linear) |

Origin app 只在 Origin 托管的仓库上工作，不在[从 GitHub 镜像](https://cursor.com/docs/origin/mirror-github.md)的仓库上工作。镜像仓库的 CI 和集成留在 GitHub。

## 构建你自己的 app

要调用 Origin API，为你的团队[构建一个私有 app](https://cursor.com/docs/origin/apps/build-app.md)。App 用 app JWT 和 installation access token 进行身份验证。[Origin API 参考](https://cursor.com/docs/api/origin.md)涵盖 base URL、身份验证、scope、webhook 和端点。

应用市场接受提交。要让每个 Origin 团队都能看到你的 app，把它设为公开并[提交审核](https://cursor.com/docs/origin/apps/publish.md)。


---

## 站点地图

[所有文档页面概览](/llms.txt)
