> 原文：https://cursor.com/docs/origin/apps/publish

# 发布到 App Marketplace

[Origin API 参考](https://cursor.com/docs/api/origin.md)涵盖身份验证、安装、scope 和 webhook。

合作伙伴在自己的命名空间中构建 Origin app，把它设为公开，并提交审核。Cursor 批准后，该 app 会列入应用市场，供每个 Origin 团队使用。

## 概览

1. 在 [cursor.com/codebase/settings/apps](https://cursor.com/codebase/settings/apps) 的你自己的命名空间中创建一个私有 Origin app。
2. 在那里构建并测试它。安装它，并走一遍下面的[流程](https://cursor.com/docs/origin/apps/publish.md#flows)。
3. 把 app 设为 **Public**。在你的 app 设置页面，点击 **Edit App**，并把 **Visibility** 行设为 Public。现在任何 Origin 团队都可以从直接链接安装它，但它还没进入应用市场。用应用市场在该 app 列入之后所使用的同一 URL 来测试这次安装：

   ```text
   https://cursor.com/codebase/apps/install?client_id=<APP_ID>&source=app-metadata&returnTo=/codebase/settings/apps
   ```

   `client_id` 是 app ID（`app_01…`），不是 slug。
4. 录制下面[流程](https://cursor.com/docs/origin/apps/publish.md#flows)的演示视频，大约 5 到 10 分钟。然后在你的 app 设置页面，点击 **Edit App** 旁边的 **Submit to marketplace**。
5. Cursor 批准该 app 之后，它在应用市场中为 **Listed**，并且你会收到一封邮件。

## App 可见性

App 有三种可见性设置：Private、Public 和 Listed。

- **Private**：新 app 从这里开始。只有你的命名空间可以查看并安装该 app。
- **Public**：任何 Origin 团队都可以从直接链接安装该 app。在 Cursor 批准之前，它不会列入应用市场。Public app 至少需要一个默认 scope。
- **Listed**：该公开 app 出现在应用市场中。

命名空间管理员在 **Edit App** 页面的 **Visibility** 行中，在 Private 和 Public 之间切换 app。把你的 app 设为 Public，以便在你的命名空间之外测试它。

应用市场的安装 URL 是：

```text
https://cursor.com/codebase/apps/install?client_id=<APP_ID>&source=app-metadata&returnTo=/codebase/settings/apps
```

`client_id` 是 app ID（`app_01…`），不是 slug。不要添加 `scope` 或 `redirect_uri`。那些会启动[由合作伙伴发起的安装](https://cursor.com/docs/api/origin/reference/installation.md)，而不是应用市场流程。公开列表页面（`cursor.com/codebase/apps/public/<slug>`）只在 app 列入之后才可用。

取消列入 app 或把它设为 Private 会把它从应用市场隐藏。已有安装继续工作。把先前已列入的 Private app 再次设为 Public 不会重新列入它。再次提交，才能让它回到应用市场。

## 提交到应用市场

app 成为 Public 之后，命名空间管理员在该 app 设置页面点击 **Edit App** 旁边的 **Submit to marketplace**，并提供：

- **Demo video**（必填）：演示视频的链接，大约 5 到 10 分钟，涵盖下面的[流程](https://cursor.com/docs/origin/apps/publish.md#flows)。该链接必须无需登录即可观看，例如不公开列出的 YouTube 视频或公开的 Google Drive 链接。
- **App description**（必填）：对你的 app 及其主要流程的详细描述。
- **Slug**（可选）：你的 app 的唯一标识符，用于其应用市场 URL。app 列入之后不能更改。
- **Suggested tags**（可选）：逗号分隔。

提交时你会收到确认邮件，app 列入时会再收到一封邮件。Cursor 会审核每一次提交。

## 批准清单

- 该 app 是 Public，并且可以从直接链接安装。
- 演示视频大约 5 到 10 分钟，涵盖必需的[流程](https://cursor.com/docs/origin/apps/publish.md#flows)，并且无需登录即可观看。
- app 描述涵盖你的 app 及其主要流程。
- 该 app 遵循 [Origin 品牌指南](https://app.notion.com/p/3c2da74ef045815e904aece09e477803)。

## 流程

在演示视频中走一遍这些流程，目标大约 5 到 10 分钟。这里的「用户」指你的产品的用户。

### 每次演示都必需

**产品流程**

- 你的 app 的主要产品流程，例如 CI app 发布检查运行，或代码审查 app 发布审查评论。

**从 Origin 安装**

有人从 Origin 安装你的 app，并同意你的默认 scope。

- Origin 重定向到你的第一个 redirect URI。
- Origin 和你的产品都显示 Installed。
- 安装在个人命名空间和团队上都可用。
- 安装在全部仓库和选定仓库上都可用。

**卸载**

从任意一侧拆除该安装。

- 从 Origin Apps 卸载：你的产品显示已断开，并停止接收 webhook。如果这在 UI 中不可见，展示你的产品的日志或事件历史。
- 从你的产品断开：该 installation 不再出现在 Origin Apps 中。

### 如果你的 app 支持，则展示

**从你的网站安装**

用户从你的网站开始，经过 Origin 选择所有者和仓库，然后回到你的产品。

- 你的产品显示 Installed。

**你的产品的新用户和已有用户**

安装期间，已在你的产品中拥有账号的人登录，新用户则创建一个账号。

- 已有用户：系统提示他们登录，安装完成，双方都显示 Installed 且已关联。
- 新用户：他们进入你的账号创建流程，创建账号，然后完成安装。

**关联仓库**

app 安装之后，用户把 Origin 连接为项目的来源。

- 把已有项目连接到 Origin 仓库。
- 从 Origin 仓库创建新项目。

**更新 scope**

app 已经安装，并请求更多 scope。见下面的[更新 scope](https://cursor.com/docs/origin/apps/publish.md#update-scopes)。

- 同意屏幕显示 **New Permissions** 和 **Granted Permissions**，按钮文字为 **Reinstall**。
- 双方都显示 Installed，并带有新的 scope。
- 如果用户取消，旧的 scope 保持不变。

## 集成参考

### 更新 scope

把工作区管理员送回 `https://cursor.com/codebase/apps/install`，并带上额外的 `scope`。Origin 不会自行提示他们。按钮文字为 **Reinstall**。它更新同一次安装，同一页面也可以在全部仓库和选定仓库之间切换。

传入 `include_granted_scopes=true`，以保留管理员已经授予的内容，并只请求新的 scope，显示为 **New Permissions** 和 **Granted Permissions**。没有该标志时，`scope=` 会替换该授权。每个安装参数见 [Installation](https://cursor.com/docs/api/origin/reference/installation.md)。

如果没有任何变化，Origin 会显示 *No changes were made to the existing install. Redirecting to the App website.*，并且不会发送 `installation.updated`。

### 更改仓库

Origin Apps 中的 **Manage** 只更改仓库，不更改 scope。

### `installation.updated` webhook

当 scope 或仓库变化时，Origin 会向你的 app 的 webhook 发送 `installation.updated`。你不需要订阅它。

## 相关

- [Origin Apps](https://cursor.com/docs/origin/apps.md)
- [构建 Origin App](https://cursor.com/docs/origin/apps/build-app.md)
- [Origin API](https://cursor.com/docs/api/origin.md)


---

## 站点地图

[所有文档页面概览](/llms.txt)
