> 原文：https://cursor.com/docs/cloud-agent/setup

# 云环境配置

Cloud Agents 运行在隔离的 Ubuntu 机器上。请配置环境，使 Agent 拥有开发者会使用的相同仓库、工具、依赖、密钥和网络访问。

在 [Cloud Agents 仪表盘](https://cursor.com/dashboard/cloud-agents#environments)中创建新环境。

## 什么是 Cloud Agent 环境？

Cloud Agent 的开发环境与你笔记本电脑上的配置类似：已克隆的仓库、已安装的依赖、密钥、启动命令和网络访问。

有效的开发环境会向 Agent 提供代码库与组织的完整上下文，以便它们测试并验证自己的工作。

![Cloud Agent 开发环境架构](https://ptht05hbb1ssoooe.public.blob.vercel-storage.com/assets/blog/cloud-agents-architecture-light.png)

## 环境配置为什么重要？

Agent 的能力取决于它们所运行的环境。如果 Agent 能写代码，但不能运行测试、查询服务或访问 API，就无法闭环完成自己的工作。

要把工程任务从开始做到结束，Cloud Agents 需要一个配置好的开发环境，具备全部仓库、工具、依赖和上下文，才能保持自主和高效。

开发环境也会让 Agent 会话更快。[Builds](https://cursor.com/docs/cloud-agent/builds.md) 会在后台准备仓库、工具和依赖，这样 Agent 会从一台准备就绪的机器上启动。

环境配置是提升 Cloud Agents 效果最重要的一步。

## 环境配置方式

配置 Cloud Agent 环境主要有两种方式：

1. 让 Cursor 的 Agent 从 [Cloud Agents 仪表盘](https://cursor.com/dashboard/cloud-agents#environments)自行配置环境。Agent 会安装依赖、验证环境，并创建第一个 Build。
2. 用 Dockerfile 手动配置环境。如果选择这种方式，可以在 `.cursor/environment.json` 文件中指定 Dockerfile。

两种方式都可以指定安装脚本。Cursor 在创建 Build 时运行该脚本，以便在 Agent 启动前准备好依赖。

### 多仓库环境

当 Agent 需要跨多个仓库工作时，使用多仓库环境。创建环境时选择多个仓库。Cursor 会把每个选中的仓库克隆到 Agent 机器上，并在日后使用同一仓库组的 Agent 运行和自动化中复用该环境。

当前端、后端、基础设施或共享库位于不同仓库时，多仓库环境很有用。Agent 可以检查完整工作区、进行协调一致的改动、跨仓库运行测试，并在它所改动的仓库中打开 pull request。

在 [Cloud Agents 仪表盘](https://cursor.com/dashboard/cloud-agents#environments)上打开该环境的配置页面，即可查看哪个环境当前生效，以及历史上所有曾经生效的版本。

### 环境解析顺序

Cursor 按仓库或仓库组解析环境配置，采用第一个匹配项：

1. 个人保存的环境
2. 团队保存的环境
3. 仓库中的 `.cursor/environment.json`（仅限单仓库运行）

这样团队层面会有可预期的默认值，个人用户仍可以用个人环境覆盖。用户覆盖也便于在向整个团队推广之前试用新的环境配置。

已保存的环境按其内部保存的配置运行。只有当它的 **environment.json location** 指向该文件时，它才会读取仓库中的 `.cursor/environment.json`。你创建的环境，其位置初始为 **None (skip environment.json)**。要使用已提交的文件，请在创建环境时选择该文件所在的仓库。

当一次 Cloud Agent 启动为尚无环境的单个仓库创建已保存环境时，它会检查该仓库中是否有已提交的 `.cursor/environment.json`。如果文件有效，新环境的 **environment.json location** 会指向该文件，于是这次 Agent、之后的 Agent 以及 Builds 都会使用该文件。

环境配置适用于 Cursor 托管的 Cloud Agents。在[自托管机器](https://cursor.com/docs/cloud-agent/self-hosted.md#environments-on-self-hosted-machines)上的运行只使用该环境的仓库，不会读取 `.cursor/environment.json`。

### 由 Agent 驱动的配置（推荐）

Cursor 可以在不到 10 分钟内在云端配置好你的开发环境。从 [Cloud Agents 仪表盘](https://cursor.com/dashboard/cloud-agents#environments)或 Cursor 桌面应用中的 [Agents Window](https://cursor.com/docs/agent/agents-window.md)开始引导式配置。

你需要连接 GitHub、GitLab、Azure DevOps 或 Bitbucket 账号，并选择一个或多个仓库。

然后，向 Cursor 提供安装依赖和运行代码所需的环境变量与密钥。

Agent 工作时，你可以在共享的终端会话中观看进度，看它处理安装依赖等配置任务。Cursor 会在验证代码并成功完成一次 Build 之后保存该环境。

![在共享终端会话中配置云环境](https://ptht05hbb1ssoooe.public.blob.vercel-storage.com/assets/changelog/cloud-environment-setup.png)

之后的 Cloud Agents 从当前生效的 Build 启动，并可以通过运行你的软件来测试改动。把配置提交到 `.cursor/environment.json`，整个团队都能受益。

### 使用 Dockerfile 手动配置（高级）

对于高级场景，用 Dockerfile 配置环境：

- 创建 Dockerfile，用于安装系统级依赖、使用特定编译器版本、安装调试器，或切换基础操作系统镜像
- 不要 `COPY` 整个项目；Cursor 会管理工作区并检出正确的提交
- 直接编辑 `.cursor/environment.json` 来配置运行时设置
- 对私有包注册表或构建时凭证使用 build secret

下面是一个 `.cursor/environment.json` 示例，它引用 `.cursor/Dockerfile`（相对路径）和安装脚本 `custom_script.sh`：

```json
{
  "build": {
    "dockerfile": "Dockerfile",
    "context": ".."
  },
  "install": "pnpm install && ./custom_script.sh"
}
```

如果仓库需要 Docker、Tailscale 或 Cloudflare Tunnel，请参见下文的[运行 Docker](https://cursor.com/docs/cloud-agent/setup.md#running-docker)、[运行 Tailscale](https://cursor.com/docs/cloud-agent/setup.md#running-tailscale)和[运行 Cloudflare Tunnel](https://cursor.com/docs/cloud-agent/setup.md#running-cloudflare-tunnel)。

你通过 Dockerfile 配置环境，无法直接访问远程机器。

Dockerfile 构建会使用层缓存。修改 Dockerfile 后，Cursor 会重建发生变化的层，而不是从头重建每一层。

### 由 Cursor 配置的 Dockerfile（非公开测试）

对于不想从零编写 Dockerfile 的团队，Cursor 可以代为配置。配置过程中，Cursor 会检查你的仓库，识别工具和依赖，并生成一份基于 Dockerfile 的环境配置，你可以编辑它并进行版本管理。

此流程对 Enterprise 团队处于非公开测试阶段。要申请使用，请联系你的 Cursor 客户代表，或使用团队管理员账号发送邮件至 [hi@cursor.com](mailto:hi@cursor.com)。

### Dockerfile 仓库的 Computer Use 支持

基于 Debian/Ubuntu 的 Linux 发行版、并带有 Dockerfile 的仓库支持 Computer Use。如果需要支持其他 Linux 发行版，请联系支持团队。

### 资源限制

每个 Cloud Agent 都运行在内存和 CPU 有限的默认 VM 规格上。如果你使用的是 Enterprise 方案，且仓库需要更多资源，请联系支持团队，我们可以提高工作区的限制。

自助自定义资源配置即将推出。

## 从零开始

你可以在没有仓库、也未连接源代码管理提供方的情况下启动 Cloud Agent。在 [cursor.com/agents](https://cursor.com/agents) 或 [Agents Window](https://cursor.com/docs/agent/agents-window.md) 的仓库选择器中选择 **Start from scratch**，然后发送提示词。Cursor 会在后台为该 Agent 创建一个草稿 [Origin](https://cursor.com/docs/origin.md) 仓库，Agent 从第一轮对话起就在其中工作。

从零开始需要一份包含 Cloud Agents 和 Origin 的方案（Free 与 Plus 不包含）。如果团队尚未设置 Origin，Cursor 会先引导你前往 [Origin 设置](https://cursor.com/docs/origin.md#enable-origin)。如果管理员已为团队关闭 Origin，Agent 会在没有仓库的情况下启动。

### 把工作保存到仓库

当 Agent 的工作已经是你想要的结果时，在 Agent 输入框上方选择 **Create repo**。选择一个建议的名称，或选择 **Other** 并自行输入。名称可以使用字母、数字、连字符和下划线，最长 100 个字符。在团队中，选择谁可以查看代码：**Private**（仅你）或 **Internal**（团队中的任何人都可以查看和编辑）。然后选择 **Create Origin repo**。

Cursor 会以该名称发布草稿仓库，Agent 的工作会一并纳入。该仓库会出现在 [cursor.com/codebase](https://cursor.com/codebase)，你可以在那里[浏览](https://cursor.com/docs/origin/browse.md)、[克隆](https://cursor.com/docs/origin/git.md)，并更改其[可见性](https://cursor.com/docs/origin/settings.md#access)。

### 预览正在运行的应用

Cursor 会把 Agent 环境中的端口转发到你的机器，因此你可以在 Agent 构建应用的过程中打开它。在 [Agents Window](https://cursor.com/docs/agent/agents-window.md) 中，打开编辑器面板里的 **Forwarded Ports** 菜单。Agent 环境中进程正在监听的端口会显示在 **Detected** 下。打开 **Auto-Forward Ports** 即可自动转发，也可以输入端口号自行转发。在已转发的端口上选择 **Open in internal browser** 即可预览应用，并使用 [Design Mode](https://cursor.com/docs/agent/design-mode.md) 指向页面元素，从页面指挥 Agent。

### 发布到线上 URL

仓库创建之后，连接 Vercel 账号，并在 [Agents Window](https://cursor.com/docs/agent/agents-window.md) 中于 Agent 输入框上方选择 **Publish**。如果尚未添加 Vercel 插件，Cursor 会先提供 **Add Vercel**。Agent 会把仓库关联到一个 Vercel 项目，部署默认分支，并回复部署 URL。之后对默认分支的提交会自动部署。

发布需要 Vercel 账号。如果你属于 Cursor 团队，请部署到方案高于 Hobby 的 Vercel 团队。

## 安装脚本

安装脚本在仪表盘和文档中以前称为更新脚本。

Cursor 在创建 [Build](https://cursor.com/docs/cloud-agent/builds.md) 时运行安装脚本（`environment.json` 中的 `install`）。脚本在后台完成，不会拖慢每次 Agent 启动。

把 Cursor 可以提前准备的工作放在 `install` 中。例如安装依赖、生成代码、编译产物，以及预热磁盘缓存。

安装脚本可以从 Agent 使用的同一个本地套接字读取 [Agent 元数据](https://cursor.com/docs/cloud-agent/metadata.md)，并签发 [OIDC token](https://cursor.com/docs/cloud-agent/identity.md)。

### 安装脚本的幂等性

安装脚本必须幂等。它会在每个 Build 上运行，并且可能运行在先前已准备好的磁盘状态之上。

### Builds 如何使用安装脚本

Cursor 从环境的基础镜像开始，克隆其中的仓库，并运行 `install` 直到完成。成功的 Build 会捕获由此产生的磁盘状态，并成为当前生效的 Build。新的 Agent 从当前生效的 Build 启动。

确保脚本可以执行完毕。开销大的准备工作应放在 `install` 中，因为它在 Agent 请求之前运行，而不是在启动过程中运行。`pnpm install` 这类命令仍可以复用已准备的状态，只更新发生变化的依赖。

Builds 只保留磁盘状态。正在运行的进程、已导出的 shell 变量以及内存缓存不会带入 Agent 的运行。请用 `start` 或 `terminals` 启动服务。

### 环境配置恢复

失败的 Build 不会替换当前生效的 Build。在你查看失败原因并创建替代 Build 期间，Agent 会继续从最近一次成功的环境启动。

打开该环境的 **Builds** 标签页，可以查看日志、从失败的 Build 启动 Agent，或选择另一个成功的 Build。Build 的控件与调试见 [Cloud Agent Builds](https://cursor.com/docs/cloud-agent/builds.md)。

### 如何决定安装脚本中放什么

把每一个可重复的准备步骤都放进 `install`。包括完整的依赖安装、代码生成、产物编译，以及其他把可复用结果写到磁盘上的工作。

不要把长时间运行的进程放进 `install`。把 Docker、数据库、隧道和开发服务器放在启动命令中。对于 Agent 只在特定任务中才需要的服务，也可以[在 AGENTS.md 中添加说明](https://cursor.com/docs/cloud-agent/setup.md#add-cloud-specific-instructions-to-agentsmd)。

## 启动命令

Agent 从 Build 启动之后，Cursor 会先运行 `start` 命令，再运行已配置的 `terminals`。用它们运行在 Agent 工作期间需要一直保持运行的进程。

许多仓库可以不配置 `start`。如果环境依赖 Docker，请在 `start` 中加入 `sudo service docker start`。

`terminals` 用于应用代码进程。这些终端运行在你与 Agent 共享的 `tmux` 会话中。

## 向 `AGENTS.md` 添加云端专用说明

Cloud Agents 会读取 `AGENTS.md` 文件。建议添加一个专门小节，写入仅适用于云端的配置与测试说明，标题例如 `Cursor Cloud specific instructions`。

如果这一节变长，建议在其中引用其他文件，把特定任务的详细说明放在那些文件里。

更多信息见 [AGENTS.md 文档](https://cursor.com/docs/rules.md#agentsmd)。

## 环境变量和密钥

要像人类开发者一样完整运行并测试代码，Cloud Agents 通常需要环境变量和密钥，例如 API 密钥和数据库凭证。

### 推荐：使用 Cursor 设置中的 Secrets 标签页

管理密钥最简单的方式是通过 [cursor.com](https://cursor.com/dashboard/cloud-agents)。这些密钥会作为环境变量提供给 Cloud Agent。

关于不同类型的密钥，见[密钥文档](https://cursor.com/docs/cloud-agent/security-network.md#secret-protection)。要在不使用长期密钥的情况下授予云角色访问权限，见 [OIDC token](https://cursor.com/docs/cloud-agent/identity.md)。

### 环境范围的密钥

当某个凭证只应提供给使用某一个环境的 Agent 时，使用环境范围的密钥。这适用于多仓库环境、预发凭证，或访问需求各不相同的仓库组。

环境范围的密钥对该环境中的每个仓库生效。其他环境无法使用这些密钥，它们也不会进入[自托管机器](https://cursor.com/docs/cloud-agent/self-hosted.md#environments-on-self-hosted-machines)。

### 登录凭证和 2FA

如果应用需要登录，把你在本地使用的相同凭证添加为密钥，例如用户名、邮箱和密码。

如果登录流程使用基于 TOTP 的 2FA，请把 TOTP 密钥（有时称为共享密钥或根密钥）也添加为密钥。Agent 可以用 `oathtool --totp -b "$TOTP_SECRET"` 生成当前的 6 位验证码。

### 含有多个 `.env` 文件的 monorepo

如果 monorepo 中有多个 `.env.local` 文件：

- 把所有 `.env.local` 文件中的值添加到同一个 Secrets 标签页
- 键名冲突时使用互不重复的变量名，例如 `NEXTJS_*` 和 `CONVEX_*`
- 在各个应用中按需引用这些变量

如果在生成快照时包含 `.env.local` 文件，这些文件可以被保存下来，并提供给 Cloud Agents。出于安全和管理考虑，仍推荐使用 Secrets 标签页。

### 使用 AWS IAM 角色

Cursor 支持代入客户提供的 IAM 角色，以便与 AWS 进行更深入的集成。这样你可以把特定的 AWS 权限授予 Cloud Agents，而无需共享长期凭证。

1. **创建 IAM 角色**：在 AWS 账号中创建希望 Cloud Agent 代入的 IAM 角色，并记下它的 ARN（例如 `arn:aws:iam::123456789012:role/acmeRole`）。

2. **配置 IAM 角色密钥**：前往 [Cursor Dashboard → Cloud Agents](https://cursor.com/dashboard?tab=cloud-agents)，添加名为 `CURSOR_AWS_ASSUME_IAM_ROLE_ARN` 的用户或团队密钥，值设为你所创建 IAM 角色的 ARN。

3. **生成外部 ID**：必须由团队管理员在团队设置的 **Advanced** 部分完成。前往 [Cursor Dashboard → Settings → Advanced](https://cursor.com/dashboard?tab=settings)，找到 External ID 设置。如果页面上没有显示外部 ID，请在 “AWS IAM Role ARN” 字段中输入一个占位值，点击 “Validate & Save”，然后重新加载页面。这样会为团队生成一个外部 ID（例如 `cursor-xxx-yyy-zzz`）。

4. **配置 IAM 角色的信任策略**：在 AWS 账号中更新该 IAM 角色的信任策略，使其信任 Cursor 的 role assumer。信任策略应如下所示：

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowCursorAssume",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::289469326074:role/roleAssumer"
      },
      "Action": "sts:AssumeRole",
      "Condition": {
        "StringEquals": {
          "sts:ExternalId": "cursor-xxx-yyy-zzz"
        }
      }
    }
  ]
}
```

将 `cursor-xxx-yyy-zzz` 替换为团队生成的外部 ID。

**环境变量：**

配置完成后，Cursor 会设置下列环境变量，让 AWS 工具使用 `cursor-cloud-agent` 这个 profile：

- `AWS_CONFIG_FILE` 指向由 Cursor 管理的 AWS 配置文件
- `AWS_PROFILE` 设为 `cursor-cloud-agent`
- `AWS_SDK_LOAD_CONFIG` 设为 `1`

使用默认凭证链的 AWS CLI 和 AWS SDK 会在配置命令执行期间以及 Agent 运行时自动使用此 profile。无需自行导出 `AWS_ACCESS_KEY_ID`、`AWS_SECRET_ACCESS_KEY` 或 `AWS_SESSION_TOKEN`。

Cursor 使用 1 小时后过期的 STS 凭证来代入该角色。
Agent 被唤醒时，Cursor 会刷新缺失、无效，或距离过期不足 15 分钟的凭证。

要与 AWS STS 的 `AssumeRoleWithWebIdentity` 联合，或与 GCP、Azure 或其他 OIDC 验证方联合，请从 Agent VM 签发 [OIDC token](https://cursor.com/docs/cloud-agent/identity.md)，不要存储长期云密钥。

## 用 environment.json 在代码中配置

如果希望把环境配置定义在代码里，可以把 `.cursor/environment.json` 提交到仓库。已保存的环境只有在 **environment.json location** 指向该文件时才会使用它。见[环境解析顺序](https://cursor.com/docs/cloud-agent/setup.md#environment-resolution-order)。

Builds 使用该环境默认分支上的配置。如果要在功能分支上更改配置，请先提交并推送，然后在该分支上启动 Agent。Cursor 会在当前生效的 Build 之上检出所请求的分支；当该分支改变了依赖时，Agent 可以重新运行安装命令。

使用基于快照的配置的 `environment.json` 示例（快照 ID 可在仪表盘的环境页面查看）：

```json
{
  "snapshot": "snapshot-20260212-00000000-0000-0000-0000-000000000000",
  "install": "npm install"
}
```

下面是引用 `.cursor/Dockerfile`（相对路径）和安装脚本 `custom_script.sh` 的 `.cursor/environment.json` 示例：

```json
{
  "build": {
    "dockerfile": "Dockerfile",
    "context": ".."
  },
  "install": "pnpm install && ./custom_script.sh"
}
```

### 重要的路径行为

`build` 中的 `dockerfile` 和 `context` 路径相对于仓库的 `.cursor` 目录，与环境文件实际所在位置无关。已经以 `.cursor` 开头的路径（例如 `.cursor/Dockerfile`）会按原样使用。省略 `context` 时，默认值为 `.cursor`。值 `.`、`./` 和 `..` 会被特殊处理为仓库根目录，而不是 `.cursor`。因此，要用裸文件名 `COPY` 位于 `.cursor` 中的文件，请省略 `context`。`install` 命令在项目根目录中运行。

完整 schema [定义见此处](https://www.cursor.com/schemas/environment.schema.json)。

## 运行 Docker

Cloud Agents 支持 Docker 工作流。我们在内部把它用于运行多项服务的全栈仓库。

简单场景下，安装 Docker 通常就足够了。Docker 安装完成且守护进程正在运行后，`docker run hello-world` 这类命令通常可以工作。

Docker 在 Cloud Agents 中有一些边界情况，因为它运行在另一层容器之内。简单工作流通常没有问题。更复杂的配置请从下文的 `fuse-overlayfs` 和 `iptables-legacy` 配置入手。

更复杂的 Docker 配置请使用 `fuse-overlayfs` 和 `iptables-legacy`，并确保 Cloud Agent 的用户可以运行 Docker。

### 复杂 Docker 配置推荐的 Dockerfile

```docker
########################################################
# DOCKER INSTALLATION
########################################################

# Install Docker
RUN install -m 0755 -d /etc/apt/keyrings && \
    curl --retry 3 --retry-delay 5 -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg && \
    chmod a+r /etc/apt/keyrings/docker.gpg && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
    $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null && \
    apt-get update && \
    apt-get install -y \
    docker-ce=5:28.5.2-1~ubuntu.24.04~noble \
    docker-ce-cli=5:28.5.2-1~ubuntu.24.04~noble \
    containerd.io \
    docker-buildx-plugin \
    docker-compose-plugin \
    && rm -rf /var/lib/apt/lists/*

RUN apt-get update && apt-get install -y fuse-overlayfs && rm -rf /var/lib/apt/lists/*
RUN mkdir -p /etc/docker && \
    printf '%s\n' '{' \
    '  "storage-driver": "fuse-overlayfs"' \
    '}' > /etc/docker/daemon.json
RUN apt-get update && apt-get install -y iptables && rm -rf /var/lib/apt/lists/*
RUN update-alternatives --set iptables /usr/sbin/iptables-legacy && \
    update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy

########################################################
# CONFIG UBUNTU USER
########################################################

# ensure no password authentication
RUN echo 'PasswordAuthentication no\nChallengeResponseAuthentication no\nUsePAM no' > /etc/ssh/sshd_config.d/disable_password_auth.conf

# Create non-root user (only if it doesn't exist)
RUN id -u ubuntu &>/dev/null || useradd -m -s /bin/bash ubuntu
# Create docker group if it doesn't exist and add ubuntu user to it
RUN groupadd -f docker && usermod -aG docker ubuntu
RUN usermod -aG sudo ubuntu
# Configure passwordless sudo for ubuntu user
RUN echo "ubuntu ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/ubuntu
# Set a password for ubuntu user
RUN echo "ubuntu:ubuntu" | chpasswd
```

## 运行 Tailscale

Tailscale 在 Cloud Agent 虚拟机中无法使用默认网络模式。请改用用户态网络模式。

这样 Agent 可以通过 tailnet 访问私有服务和数据存储，同时这些服务不会暴露到公共互联网。

用下面的命令启动 `tailscaled`：

```bash
tailscaled --tun=userspace-networking \
  --outbound-http-proxy-listen=localhost:1054 \
  --socks5-server=localhost:1055
```

然后，在希望流量经过 Tailscale 的 shell 中导出这些代理变量：

```bash
export ALL_PROXY=socks5h://localhost:1055/
export HTTP_PROXY=http://localhost:1054/
export HTTPS_PROXY=http://localhost:1054/
```

之后按你惯用的方式运行 `tailscale up ...`。

如果需要一份可以工作的参考，一些客户成功使用了 [`tailscale-orb`](https://circleci.com/developer/orbs/orb/orbiously/tailscale#commands-connect)，因为它的 Docker 模式遵循这一模式。

用户态网络不会使这台虚拟机成为 tailnet 的出口节点。

## 运行 Cloudflare Tunnel

Cloudflare Tunnel 可以在 Cloud Agent 虚拟机中工作，因为 `cloudflared` 运行在用户态。

当 Cloud Agent 需要访问 VPC 或内网中的私有 HTTP 服务时，使用下面的模式：

- 在环境的 Dockerfile 或安装脚本中安装 `cloudflared`。
- 在私有网络内运行一个 `cloudflared` 连接器。
- 把经过认证的主机名（例如 `vpc.example.com`）经隧道路由到私有源站。
- 如果环境限制了出站访问，请把该主机名加入 Cloud Agent 的网络允许列表。
- 把 Cloudflare Access 服务令牌的值保存为 Cursor Secrets。例如使用 `CF_ACCESS_CLIENT_ID` 和 `CF_ACCESS_CLIENT_SECRET`。

然后，Cloud Agent 就可以通过普通 HTTPS，带上 `CF-Access-Client-Id` 和 `CF-Access-Client-Secret` 请求头来调用该私有服务。连接器向 Cloudflare 发起出站连接，并把请求转发到你的私有源站。服务和数据存储仍留在私有网络中，连接器也不需要开放入站端口。

对于数据库这类私有 TCP 服务，请配置 Cloudflare TCP Access 应用，并在启动命令中运行 `cloudflared access tcp`。把应用或测试命令指向 `cloudflared` 创建的本地监听地址。

请把隧道令牌和 Access 服务令牌密钥保存在 Cursor Secrets 中，不要放进仓库。如果这些密钥是为概念验证创建的，测试完成后请轮换。

---

## 站点地图

[所有文档页面概览](/llms.txt)
