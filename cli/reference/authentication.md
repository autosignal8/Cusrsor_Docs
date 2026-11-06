> 原文：https://cursor.com/docs/cli/reference/authentication

# 身份验证

Cursor CLI 支持两种身份验证方法：基于浏览器的登录（推荐）和 API key。

## 浏览器身份验证（推荐）

使用浏览器流程可以获得最简单的身份验证体验：

```bash
# Log in using browser flow
agent login

# Check authentication status
agent status

# Log out and clear stored authentication
agent logout
```

登录命令会打开你的默认浏览器，并提示你使用 Cursor 账户进行身份验证。设置 `NO_OPEN_BROWSER=1` 可以打印登录 URL，而不打开浏览器。完成后，你的凭据会安全地存储在本地。

## API key 身份验证

对于自动化、脚本或 CI 环境，使用 API key 身份验证：

### 步骤 1：生成 API key

从 [Cursor Dashboard → API Keys](https://cursor.com/dashboard/api) 生成用户 API key。

### 步骤 2：设置 API key

你可以用两种方式提供 API key：

**方式 1：环境变量（推荐）**

```bash
export CURSOR_API_KEY=your_api_key_here
agent "implement user authentication"
```

**方式 2：命令行标志**

```bash
agent --api-key your_api_key_here "implement user authentication"
```

## 身份验证状态

检查你当前的身份验证状态：

```bash
agent status
```

此命令会显示：

- 你是否已通过身份验证
- 你的账户信息
- 当前端点配置

## 故障排除

- **「Not authenticated」错误：** 运行 `agent login`，或确认你的 API key 已正确设置
- **浏览器没有打开：** 运行 `NO_OPEN_BROWSER=1 agent login`，并手动打开打印出的 URL

---

## 站点地图

[所有文档页面概览](/llms.txt)
