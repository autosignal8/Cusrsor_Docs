> 原文：https://cursor.com/docs/cli/github-actions

# GitHub Actions

在 GitHub Actions 和其他 CI/CD 系统中使用 Cursor CLI，自动化开发任务。

## GitHub Actions 集成

基本设置：

```yaml
- name: Install Cursor CLI
  run: |
    curl https://cursor.com/install -fsS | bash
    echo "$HOME/.cursor/bin" >> $GITHUB_PATH

- name: Run Cursor Agent
  env:
    CURSOR_API_KEY: ${{ secrets.CURSOR_API_KEY }}
  run: |
    agent -p "Your prompt here" --model gpt-5
```

对于 Windows runner，使用 PowerShell：`irm 'https://cursor.com/install?win32=true' | iex`

## Cookbook 示例

实用工作流见我们的 cookbook 示例：[更新文档](https://cursor.com/docs/cli/headless.md)和[修复 CI 问题](https://cursor.com/docs/cli/headless.md)。

## 其他 CI 系统

在任何 CI/CD 系统中使用 Cursor CLI，需要：

- **Shell 脚本执行**（bash、zsh 等）
- 用于 API key 配置的**环境变量**
- 能够访问 Cursor API 的**互联网连接**

## 自主级别

选择 Agent 的自主级别：

### 完全自主

让 Agent 完全控制 git 操作、API 调用和外部交互。设置更简单，需要更多信任。

**示例：** 在我们的[更新文档](https://cursor.com/docs/cli/headless.md) cookbook 中，第一个工作流让 Agent：

- 分析 PR 改动
- 创建并管理 git 分支
- 提交并推送改动
- 在 pull request 上发表评论
- 处理所有错误场景

```yaml
- name: Update docs (full autonomy)
  run: |
    agent -p "You have full access to git, GitHub CLI, and PR operations. 
    Handle the entire docs update workflow including commits, pushes, and PR comments."
```

### 受限自主

我们建议在生产 CI 工作流中使用这种**基于权限的限制**做法。它兼顾两面：Agent 可以智能地处理复杂分析和文件修改，而关键操作保持确定且可审计。

限制 Agent 的操作，同时在单独的工作流步骤中处理关键步骤。控制力和可预测性更好。

**示例：** 同一 cookbook 中的第二个工作流把 Agent 限制为只修改文件：

```yaml
- name: Generate docs updates (restricted)
  run: |
    agent -p "IMPORTANT: Do NOT create branches, commit, push, or post PR comments. 
    Only modify files in the working directory. A later workflow step handles publishing."

- name: Publish docs branch (deterministic)
  run: |
    # Deterministic git operations handled by CI
    git checkout -B "docs/${{ github.head_ref }}"
    git add -A
    git commit -m "docs: update for PR"
    git push origin "docs/${{ github.head_ref }}"

- name: Post PR comment (deterministic)
  run: |
    # Deterministic PR commenting handled by CI
    gh pr comment ${{ github.event.pull_request.number }} --body "Docs updated"
```

### 基于权限的限制

使用[权限配置](https://cursor.com/docs/cli/reference/permissions.md)在 CLI 层面强制限制：

```json
{
  "permissions": {
    "allow": [
      "Read(**/*.md)",
      "Write(docs/**/*)",
      "Shell(grep)",
      "Shell(find)"
    ],
    "deny": ["Shell(git)", "Shell(gh)", "Write(.env*)", "Write(package.json)"]
  }
}
```

## 身份验证

### 生成 API key

首先，从 Cursor 仪表板[生成 API key](https://cursor.com/docs/cli/reference/authentication.md#api-key-authentication)。

### 配置仓库密钥

使用 GitHub CLI 把 Cursor API key 安全地存储在仓库中：

```bash
# Repository secret
gh secret set CURSOR_API_KEY --repo OWNER/REPO --body "$CURSOR_API_KEY"

# Organization secret (all repos)
gh secret set CURSOR_API_KEY --org ORG --visibility all --body "$CURSOR_API_KEY"
```

也可以使用 GitHub UI：进入你的仓库 → **Settings** → **Secrets and variables** → **Actions** → **New repository secret**

### 在工作流中使用

设置 `CURSOR_API_KEY` 环境变量：

```yaml
env:
  CURSOR_API_KEY: ${{ secrets.CURSOR_API_KEY }}
```

---

## 站点地图

[所有文档页面概览](/llms.txt)
