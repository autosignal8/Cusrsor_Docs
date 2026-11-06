> 原文：https://cursor.com/docs/origin/clonekit-ci

# 在 CI 中用 `origin repo clone-fast` 设置 CloneKit

Origin 目前以早期 beta 发布。你可以创建仓库，用 git 推送和拉取，从 GitHub 镜像，浏览并搜索代码，打开并合并 pull request，并与你的 Cursor 团队共享。

`origin repo clone-fast` 在 CI 作业中替代 `git clone`。它不是从头克隆，而是下载一个预先构建的 clone kit，也就是 Cursor 为每个开启了 CloneKit 的仓库构建的打包快照，然后只 fetch 自该 kit 构建以来新增的对象。本页说明如何把短时 Origin token 放到 runner 上，并在 [Buildkite](https://cursor.com/docs/origin/clonekit-ci.md#buildkite)、[GitHub Actions](https://cursor.com/docs/origin/clonekit-ci.md#github-actions) 和[其他 CI 系统](https://cursor.com/docs/origin/clonekit-ci.md#other-ci-systems)中运行该命令。

## 工作原理

每次运行 `origin repo clone-fast {owner}/{repo} [DIR]` 都会执行以下操作：

1. 用 `CURSOR_AUTH_TOKEN` 中的 token 从 Origin 获取 kit 清单。清单列出 kit 的产物及其顶端的提交。
2. 从 `gitcdn.origin.cursor.com` 下载 pack 产物并检查其大小。加上 `--verify` 时，还会在下载过程中检查其哈希。
3. 把它们安装到目标目录，该目录必须为空。
4. 补齐克隆：fetch 自 kit 构建以来新增的对象，并检出默认分支的当前顶端。加上 `--no-top-up` 时，检出停留在 kit 的顶端。`--bare` 创建一个没有工作树的裸仓库。

kit 路径成功时，命令在 stdout 打印 `mode: clone-kit`。如果任一步骤失败，默认的 `--fallback auto` 会改为运行普通的 `git clone`，并打印 `mode: git-clone`。传入 `--fallback never` 则会以状态 1 退出。

token 和检出在每个 CI 系统上的行为都相同：

- **Token 最多 15 分钟后过期，且不能刷新。** 在克隆之前立即签发一个。
- **CLI 从 `CURSOR_AUTH_TOKEN` 读取 token。** 在运行 `origin` 的 shell 中导出它。
- **克隆结束在默认分支的顶端。** 要构建特定提交，之后运行 `git fetch origin SHA` 和 `git checkout SHA`。Origin 按 SHA 提供任何可达的提交。

## 前提条件

CloneKit 适用于 Enterprise 方案，并按仓库开启；没有自助开关。联系你的 Cursor 客户团队，为你要克隆的每个仓库开启它。`origin repo clone-fast --help` 列出该命令的选项。

- runner 上的 Origin CLI。用 `curl -fsSL https://downloads.cursor.com/origin/install.sh | sh` 安装，二进制位于 `$HOME/.local/bin/origin`。Linux runner 在 x64 或 arm64 上需要 glibc；不支持 Alpine 和其他 musl 镜像。支持 macOS runner。见[安装 Origin CLI](https://cursor.com/docs/origin/cli.md)。
- bash、git、jq、curl 7.55 或更高版本，以及 openssl 1.1.1 或更高版本。`clone-fast` 用 `curl` 下载。
- 到这些主机的网络出站：

| 主机 | 用途 |
| -------------------------- | ---------------------------------------------------------- |
| `downloads.cursor.com` | 安装 CLI |
| `origin.cursor.com` | kit 清单和 git 操作 |
| `gitcdn.origin.cursor.com` | kit 下载 |
| `api.cursor.com` | 签发 token，以及来自 Origin App 的镜像同步请求 |

## 选择一条路径

| CI 系统 | token 的来源 | 章节 |
| ----------------------------------------------------------------------------------------- | --------------------------------------------- | ---------------------------------------------------------------------------------- |
| Buildkite，托管或自托管 agent，且 Origin 已连接为仓库提供方 | Buildkite Agent API，位于 `checkout` hook 中 | [Buildkite](https://cursor.com/docs/origin/clonekit-ci.md#buildkite) |
| GitHub Actions | 你的 Origin App，位于工作流步骤中 | [GitHub Actions](https://cursor.com/docs/origin/clonekit-ci.md#github-actions) |
| GitLab CI、Jenkins、CircleCI 和自托管机群 | 你的 Origin App，位于作业中 | [其他 CI 系统](https://cursor.com/docs/origin/clonekit-ci.md#other-ci-systems) |

## Buildkite

Buildkite 会为你签发 token。把 agent 默认的 git checkout 替换为一个 `checkout` hook，该 hook 向 Buildkite Agent API 请求 token 并运行 `origin repo clone-fast`。

连接 Origin 时你必须是 Buildkite 组织管理员，安装 Buildkite app 时你必须是 Origin 管理员。

### 把 Origin 连接到 Buildkite

在 Buildkite 中，选择 **Settings** > **Repository Providers** > **Add Provider** > **Origin**，或在 New Pipeline 页面选择 **Connect Origin account**。选择所有者和仓库，然后安装 Buildkite app。Buildkite 请求对仓库内容和 pull request 的读取权限，以及对 checks 的读写权限。详情见 Buildkite 文档中的 [Origin](https://buildkite.com/docs/pipelines/source-control/origin)。

### 在 agent 上安装 Origin CLI

遵循[前提条件](https://cursor.com/docs/origin/clonekit-ci.md#prerequisites)，并使 `curl`、`git` 和 `jq` 位于 `PATH` 上。

### 保持检出目录为空

如果 agent 在多次构建之间保留构建目录，请在 hook 运行之前清空 `$BUILDKITE_BUILD_CHECKOUT_PATH`。`clone-fast` 需要空目录，发现其中有文件时会回退到 `git clone`。

### 添加 checkout hook

在自托管 agent 上，把下面的脚本保存为 agent 的 `--hooks-path` 目录中的 `checkout`。在 Buildkite 托管 agent 上，把它作为非内置插件的 `checkout` hook 交付，或在步骤上设置 `checkout: { skip: true }`，并在步骤的 `command` 中运行相同命令。仓库 hook 不能定义 `checkout`，因为仓库此时尚未检出。见 Buildkite 文档中的 [Agent hooks](https://buildkite.com/docs/agent/hooks) 和 [Git checkout](https://buildkite.com/docs/pipelines/configure/git-checkout)。

该 hook 请求一个限定于该流水线仓库的 token，把它导出为 `CURSOR_AUTH_TOKEN`，用 `clone-fast` 克隆，用 `origin auth setup-git` 注册 git credential helper，并检出 `$BUILDKITE_COMMIT`。agent token 通过 stdin 传给 `curl`，因此它绝不会出现在命令行上：

```bash
#!/usr/bin/env bash
set -euo pipefail

body=$(printf '{"repo_url":"%s"}' "$BUILDKITE_REPO")
token=$(printf 'Authorization: Token %s\n' "$BUILDKITE_AGENT_ACCESS_TOKEN" \
  | curl -fsS -X POST -H @- \
      -H 'Content-Type: application/json' -H 'Accept: application/json' \
      --data "$body" \
      "${BUILDKITE_AGENT_ENDPOINT%/}/jobs/${BUILDKITE_JOB_ID}/cursor_origin_access_token" \
  | jq -er '.token')

# clone-fast takes owner/repo, not the clone URL.
repo=${BUILDKITE_REPO#https://origin.cursor.com/}
repo=${repo#git/}
repo=${repo%.git}

export CURSOR_AUTH_TOKEN="$token"
origin repo clone-fast "$repo" "$BUILDKITE_BUILD_CHECKOUT_PATH" --verify
origin auth setup-git
cd "$BUILDKITE_BUILD_CHECKOUT_PATH"
git fetch origin "$BUILDKITE_COMMIT"
git checkout -q "$BUILDKITE_COMMIT"
```

当构建在没有提交的情况下启动时，`BUILDKITE_COMMIT` 为 `HEAD`。这时两行 `git` 会 fetch 远程 `HEAD`，并把工作树留在 `clone-fast` 已经检出的默认分支顶端。

- **原样发送 `$BUILDKITE_REPO`。** `repo_url` 必须与流水线上登记的仓库 URL 完全一致，包括 `.git`。不同的写法会返回 HTTP 400。
- **该 token 是只读的，并限定于该流水线的仓库。** 它带有 `repository:contents:read`，并在 15 分钟后过期。后续作业，或签发超过 15 分钟后的 git 操作，需要通过同一请求获取新 token。
- **遇到 503 时重试。** 如果 Agent API 返回 HTTP 503，等待其 `Retry-After` 头再重试，与 Buildkite 自己的 credential helper 相同。

## GitHub Actions 和其他 CI 提供方

GitHub Actions 和其他 CI 系统自行签发 token。你创建一次 Origin App，把它安装到你要克隆的仓库上，并把该 app 的私钥和两个 id 交给每个作业。作业签署一个短时 app JWT，并把它交换为 installation token。完整字段参考见 [App JWT](https://cursor.com/docs/api/origin/reference/app-jwt.md) 和 [Create Installation Access Token](https://cursor.com/docs/api/origin/reference/create-installation-access-token.md)。

### 创建 Origin App

[构建 Origin App](https://cursor.com/docs/origin/apps/build-app.md)

#### 保存凭据

把私钥作为密钥保存在 CI 系统中，并以环境变量 `ORIGIN_APP_PRIVATE_KEY` 暴露给作业，内容为完整 PEM 文本。把 App ID 和 installation id 存为普通变量 `ORIGIN_APP_ID` 和 `ORIGIN_INSTALLATION_ID`。如果 CI 系统把密钥挂载为文件，先用 `ORIGIN_APP_PRIVATE_KEY=$(cat /path/to/origin-app-private.pem)` 加载密钥。

#### 在作业中签发 token

下面的函数签署 app JWT，并把它交换为 installation token。JWT 使用 `alg` `EdDSA`，把 `iss` 和 `kid` 设为 App ID，把 `aud` 设为 `origin-apps`，并把 `exp` 设为 15 分钟之后。[App JWT](https://cursor.com/docs/api/origin/reference/app-jwt.md) 参考建议生命周期约为五分钟；因为 installation token 的寿命绝不会超过签发它的 JWT，此做法签署 15 分钟的 JWT，使 token 获得完整的 15 分钟。请求申请 `repository:contents:read`，覆盖克隆、fetch 和拉取。推送需要 `repository:contents:write`，且该 installation 必须已授予该 scope。在请求体中加入 `"repositoryIds":[...]`，可以把 token 收窄到该 installation 的部分仓库。

私钥通过文件描述符传给 `openssl`，bearer 头通过 stdin 传给 `curl`，因此二者都不会出现在命令行上，runner 上的其他进程也就读不到。该函数直接设置并导出 `CURSOR_AUTH_TOKEN`，因此 token 绝不会写入文件，签发失败时在 `set -e` 下会停止作业。它需要 bash、openssl 1.1.1 或更高版本、curl 7.55 或更高版本，以及 jq：

```bash
origin_app_token() {
  b64url() { openssl base64 -A | tr '+/' '-_' | tr -d '='; }
  now=$(date +%s)
  header=$(printf '{"alg":"EdDSA","kid":"%s","typ":"JWT"}' "$ORIGIN_APP_ID" | b64url)
  claims=$(printf '{"iss":"%s","aud":"origin-apps","iat":%d,"exp":%d}' \
    "$ORIGIN_APP_ID" "$now" "$((now + 900))" | b64url)
  # openssl -rawin needs a seekable input; the signing input holds no secret.
  signing_input=$(mktemp)
  trap 'rm -f "$signing_input"' EXIT
  printf '%s.%s' "$header" "$claims" > "$signing_input"
  signature=$(openssl pkeyutl -sign -rawin -in "$signing_input" \
    -inkey <(printf '%s\n' "$ORIGIN_APP_PRIVATE_KEY") | b64url)
  app_jwt="$header.$claims.$signature"
  CURSOR_AUTH_TOKEN=$(printf 'Authorization: Bearer %s\n' "$app_jwt" \
    | curl -fsS -X POST -H @- -H 'Content-Type: application/json' \
        --data '{"scopes":["repository:contents:read"]}' \
        "https://api.cursor.com/v1/origin/app/installations/${ORIGIN_INSTALLATION_ID}/access_tokens" \
    | jq -er '.token')
  export CURSOR_AUTH_TOKEN
}
```

在克隆到空目录之前立即调用它，然后注册 git credential helper，以便后续 git 命令在 `CURSOR_AUTH_TOKEN` 保持导出时完成身份验证。把 `acme/widgets` 换成你的仓库，把 `COMMIT_SHA` 换成 CI 系统为该构建暴露的提交：

```bash
set -euo pipefail

origin_app_token
origin repo clone-fast acme/widgets . --verify
origin auth setup-git
git fetch origin "$COMMIT_SHA"
git checkout -q "$COMMIT_SHA"
```

### GitHub Actions

GitHub Actions 使用 [Origin App](https://cursor.com/docs/origin/clonekit-ci.md#create-an-origin-app) 路径，私钥放在 Actions secret 中，两个 id 放在仓库变量中。因为工作流由 GitHub 触发，仓库位于 GitHub，Origin 以[镜像](https://cursor.com/docs/origin/mirror-github.md)方式持有它。工作流让 Origin 拉取该构建的提交，用 `clone-fast` 克隆到空的 `$GITHUB_WORKSPACE`，然后检出该提交。它从不调用 GitHub，因此不需要 `GITHUB_TOKEN` 权限，也不使用 `actions/checkout`。

### 让 app 同步镜像

工作流调用 Sync Mirror，这需要 **Sync mirrors from upstream** 权限（`repository:mirror:sync`）。在 Origin app 设置中该 app 页面的 **Permissions** 下选择它。如果 app 已经安装，打开该 installation 的页面并选择 **Review & Update**，以接受新权限。

### 添加密钥

在你的仓库中，选择 **Settings** > **Secrets and variables** > **Actions**，然后添加密钥 `ORIGIN_APP_PRIVATE_KEY`，内容为完整的私钥 PEM。

### 添加变量

在同一页面，添加仓库变量 `ORIGIN_APP_ID` 和 `ORIGIN_INSTALLATION_ID`。

### 添加工作流

把下面的工作流保存为 `.github/workflows/ci.yml`，把 `ORIGIN_REPO` 设为该镜像在 Origin 上的 `{owner}/{repo}`，并在克隆步骤之后添加你的构建步骤。

该工作流安装 CLI、签发 token、等待 Origin 镜像该提交，然后克隆它：

```yaml
name: ci

on:
  push:
    branches: [main]
  pull_request:

permissions: {}

jobs:
  build:
    runs-on: ubuntu-latest
    defaults:
      run:
        shell: bash
    steps:
      - name: Install the Origin CLI
        run: |
          curl -fsSL https://downloads.cursor.com/origin/install.sh | sh
          echo "$HOME/.local/bin" >> "$GITHUB_PATH"

      - name: Clone from Origin with clone-fast
        env:
          ORIGIN_APP_ID: ${{ vars.ORIGIN_APP_ID }}
          ORIGIN_INSTALLATION_ID: ${{ vars.ORIGIN_INSTALLATION_ID }}
          ORIGIN_APP_PRIVATE_KEY: ${{ secrets.ORIGIN_APP_PRIVATE_KEY }}
          ORIGIN_REPO: acme/widgets
          BUILD_BRANCH: ${{ github.head_ref || github.ref_name }}
          BUILD_SHA: ${{ github.event.pull_request.head.sha || github.sha }}
        run: |
          set -euo pipefail

          origin_app_token() {
            b64url() { openssl base64 -A | tr '+/' '-_' | tr -d '='; }
            now=$(date +%s)
            header=$(printf '{"alg":"EdDSA","kid":"%s","typ":"JWT"}' "$ORIGIN_APP_ID" | b64url)
            claims=$(printf '{"iss":"%s","aud":"origin-apps","iat":%d,"exp":%d}' \
              "$ORIGIN_APP_ID" "$now" "$((now + 900))" | b64url)
            signing_input=$(mktemp)
            trap 'rm -f "$signing_input"' EXIT
            printf '%s.%s' "$header" "$claims" > "$signing_input"
            signature=$(openssl pkeyutl -sign -rawin -in "$signing_input" \
              -inkey <(printf '%s\n' "$ORIGIN_APP_PRIVATE_KEY") | b64url)
            app_jwt="$header.$claims.$signature"
            echo "::add-mask::$app_jwt"
            CURSOR_AUTH_TOKEN=$(printf 'Authorization: Bearer %s\n' "$app_jwt" \
              | curl -fsS -X POST -H @- -H 'Content-Type: application/json' \
                  --data '{"scopes":["repository:contents:read","repository:mirror:sync"]}' \
                  "https://api.cursor.com/v1/origin/app/installations/${ORIGIN_INSTALLATION_ID}/access_tokens" \
              | jq -er '.token')
            echo "::add-mask::$CURSOR_AUTH_TOKEN"
            export CURSOR_AUTH_TOKEN
          }

          origin_app_token

          # Origin mirrors GitHub with a delay. Wait up to about two minutes for this commit.
          body=$(printf '{"ref":"refs/heads/%s","sha":"%s","wait":true}' "$BUILD_BRANCH" "$BUILD_SHA")
          printf 'Authorization: Bearer %s\n' "$CURSOR_AUTH_TOKEN" \
            | curl -fsS -X POST -H @- -H 'Content-Type: application/json' --data "$body" \
                "https://api.cursor.com/v1/origin/repos/${ORIGIN_REPO}:syncMirror" \
            | jq -e '.synced' > /dev/null \
            || { echo "Origin has not mirrored $BUILD_SHA from $BUILD_BRANCH yet" >&2; exit 1; }

          origin repo clone-fast "$ORIGIN_REPO" . --verify
          origin auth setup-git
          git fetch origin "$BUILD_SHA"
          git checkout -q "$BUILD_SHA"
```

- **在 `pull_request` 上，工作流构建的是 pull request 的 head。** `github.sha` 是只存在于 GitHub 的合并提交，因此 `BUILD_SHA` 和 `BUILD_BRANCH` 改用该 pull request 的 head 提交和分支。
- **同步请求是 [Sync Mirror](https://cursor.com/docs/api/origin/reference/sync-mirror.md)。** 它接受 installation token，需要 `repository:mirror:sync`，并在约两分钟的等待预算之后返回 `200` 且 `"synced": true`，或返回 `202` 且 `"synced": false`。如果 Origin 是事实来源而 GitHub 是镜像，请删除该同步请求：提交已经在 Origin 上，而且 Origin 会拒绝对不从上游来源拉取的仓库发出的同步请求。
- **两份凭据都会被掩码。** `::add-mask::` 会在作业日志中隐藏 app JWT 和 installation token。
- **后续步骤会再次签发。** 需要 Origin git 访问的后续步骤会再次定义并调用该函数。token 绝不会写入 `$GITHUB_ENV` 或 `$GITHUB_OUTPUT`，它们是磁盘上的文件。
- **来自 fork 的 pull request 不会被镜像。** 它们的 head 分支不在你的仓库中。这些构建请从 GitHub 进行。

### 其他 CI 系统

GitLab CI、Jenkins、CircleCI 和自托管机群直接使用 [Origin App](https://cursor.com/docs/origin/clonekit-ci.md#create-an-origin-app) 路径。按[保存凭据](https://cursor.com/docs/origin/clonekit-ci.md#store-the-credentials)中的说明保存私钥和两个 id，然后在 `origin repo clone-fast` 之前立即运行[在作业中签发 token](https://cursor.com/docs/origin/clonekit-ci.md#mint-a-token-in-the-job)里的函数，并 fetch 和检出 CI 系统为该构建暴露的提交。

## 验证第一次运行

第一次作业使用 `--fallback never`。此时缺少 kit 会使作业以退出状态 1 失败，而不是静默运行一次缓慢的 `git clone`。在 kit 路径成功之前保持该标志。

成功的运行会在 stderr 打印 `clone-kit: manifest=...`、每个产物一行下载信息、一个 `clone-kit timings:` 块，以及 `clone-kit: ready DIR (head SHA)`，然后在 stdout 打印 `mode: clone-kit`，并以 0 退出。计时块把这次运行拆成 manifest、download、verify 和 checkout 阶段。要衡量收益，把它的总计与同一 runner 上同一仓库的普通 `git clone` 比较。

kit 路径未完成时，stderr 会显示一行机器可读的信息，`clone-kit-result: status=fallback phase=PHASE` 或 `clone-kit-result: status=failed phase=PHASE`，后面是原因。使用默认的 `--fallback auto` 时，stdout 随后显示 `mode: git-clone`。

## 故障排除

每条都以作业日志中显示的那一行开头。

### 清单获取返回 404

`clone-kit-result: status=fallback phase=manifest`，并带有 `HTTP 404`。该仓库还没有 clone kit；联系你的 Cursor 客户团队以开启 CloneKit。如果消息中的 URL 包含两次 `https://`，说明命令收到的是克隆 URL，而不是 `{owner}/{repo}`。

### 清单获取返回 401

`clone-kit manifest fetch failed: HTTP 401`。Origin 拒绝了该 token：它已过期、从未有效，或其 installation 已被移除。在克隆之前立即签发新 token。使用默认的 `--fallback auto` 时，回退的 `git clone` 会以相同代码和退出状态 128 失败，因此日志会显示两个错误。

### 清单获取返回 403

`clone-kit manifest fetch failed: HTTP 403`。该 installation 或 Buildkite 流水线 token 不覆盖此仓库。重新安装 app，或重新选择它覆盖的仓库。与 401 相同，回退的 `git clone` 会以相同代码和退出状态 128 失败。

### Git 要求输入用户名

`fatal: could not read Username for 'https://origin.cursor.com'`。要么 CLI 已过期，要么运行 `git` 的 shell 中没有导出 `CURSOR_AUTH_TOKEN`。运行 `origin update`，或导出该变量。

### 克隆在 auth 阶段回退

`clone-kit-result: status=fallback phase=auth`，并带有 `Not authenticated`。`CURSOR_AUTH_TOKEN` 没有导出到运行 `origin` 的进程中。在 `origin` 命令之前，于同一 shell 中导出它。

### 签发返回 401 Issuer is not authorized

token 端点返回 `401 {"code":16,"message":"Issuer is not authorized"}`。`iss` claim 不是已注册的 App ID，或签名密钥没有登记在该 app 上。确认 `ORIGIN_APP_ID` 与该 app 一致，且该 app 列出了该签名密钥。

### origin auth status 报告 token 有效但克隆失败

`origin auth status` 根据 `CURSOR_AUTH_TOKEN` 内部的过期 claim 得出 `Token: valid` 或 `Token: expired`。它不会询问 Origin 是否接受该 token，因此 `valid` 只表示 token 尚未过期。CLI 会在过期 claim 之前五分钟把 token 视为已过期。`origin` 命令在发出任何请求之前会拒绝已过期的 token，而 `clone-fast` 会记录 `clone-kit-result: status=fallback phase=auth`，并带有 `The injected CURSOR_AUTH_TOKEN session has expired`。签发新 token 并重试。

如果 kit 路径仍然失败，把带有 `clone-kit-result` 行的作业日志、仓库，以及 `origin --version` 的输出发给你的 Cursor 客户团队。


---

## 站点地图

[所有文档页面概览](/llms.txt)
