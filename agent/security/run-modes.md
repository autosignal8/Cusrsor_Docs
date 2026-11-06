> 原文：https://cursor.com/docs/agent/security/run-modes

# Run Modes

Run Modes 控制 Cursor Agent 如何运行工具调用，以及 Cursor 何时打断你以请求批准。

用它们决定 Agent 在 shell 命令、MCP 工具和 Fetch 调用上有多大自主权。对大多数人来说，最安全又实用的设置是 **Auto-review**。它会运行已知安全的调用，在可以时把 shell 命令放进沙箱，并让分类器审查其余内容。

## 选择模式

在桌面应用中，前往 **Settings > Agents > Approvals & Execution**。

| 模式 | 无需询问即可运行的内容 | 沙箱 | 分类器 | 适用场景 |
| :----------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------ | :--------------------------- | :--------- | :-------------------------------------------------------------------------- |
| **Auto-review** | 允许列表中的调用立即运行。其他 shell 命令在可能时于沙箱中运行。不使用沙箱的调用会交给 Auto-review 分类器。 | 是，适用于 shell 命令 | 是 | 你希望提示更少，并在更高风险的调用运行前先做安全审查。 |
| **Allowlist** | 允许列表中的操作无需批准即可运行。启用沙箱后，受支持的 shell 命令可以在沙箱中运行。 | 可选，适用于 shell 命令 | 否 | 你希望用一小套受信任、会重复执行的操作获得确定性行为。 |
| **Run Everything** | 每次工具调用都自动运行。 | 否 | 否 | 你接受风险，并且希望没有任何提示。 |

## Auto-review 如何工作

Auto-review 适用于 shell、MCP 和 Fetch 工具调用。Cursor 按以下顺序检查每次调用：

![Auto-review 模式下 Agent 操作的执行生命周期。在你的本地机器上，已加入允许列表的 shell、Fetch 和 MCP 调用在沙箱外运行。其他 shell 命令在可以时于沙箱中运行。无法使用沙箱的 shell 命令、在沙箱中失败的命令以及其他调用会送到 Cursor 后端的 LLM 分类器。分类器可以在你的机器上运行只读的 ReadFile、Grep、Glob 和 ListDir 调用。被允许的调用在沙箱外运行。对于被阻止的调用，Agent 会选择另一种方法，或请你批准该操作。](/docs-static/images/agent/auto-review-lifecycle-light.svg)

当 shell 命令在沙箱的文件和网络限制下仍能工作时，它就「可以在沙箱中运行」。需要更多访问权限的命令，例如完整的网络访问、工作区外的写入或特权操作，不能使用沙箱，因此会改为交给分类器。

如果沙箱中的命令因沙箱限制失败（例如权限错误），Agent 可以在沙箱外重新运行它。分类器会审查这次重新运行。

沙箱是 Run Modes 之上、针对 shell 命令的一层。它控制受支持的终端命令在哪里运行，而不是该模式是否使用 Auto-review 分类器。

分类器在 Cursor 后端运行。为了判断一次调用，它可以在你的机器上发起只读的 `ReadFile`、`Grep`、`Glob` 和 `ListDir` 调用，例如读取命令所运行的脚本。

当分类器阻止一次调用时，Cursor 可以尝试另一种方法。如果 Agent 认为尽管分类器这么说，该操作仍然合理，Cursor 会向你显示批准提示。

### Auto-review 不是安全边界

分类器可能会出错。它可能放行你本会阻止的调用，或阻止你本会放行的调用。

### Auto-review 分类器要求

Auto-review 的分类器在 Cursor 管理的小型模型上运行。目前它使用 [Gemini 3.5 Flash Lite](https://cursor.com/docs/models/gemini-3-5-flash-lite.md)，并以 Haiku 作为回退。

Enterprise 的[模型访问控制](https://cursor.com/docs/enterprise/model-and-integration-management.md#model-access-control)对两者都适用。请为团队保持允许 Claude 4.5 Haiku。允许它之后，Auto-review 会在 **Settings > Agents > Approvals & Execution** 中保持可用；当 Gemini 3.5 Flash Lite 被阻止时，Auto-review 会用它进行每一次审查。阻止 Claude 4.5 Haiku 可能会在那里禁用 Auto-review，即使团队的 Run Modes 包含它。成员随后会改用 Allowlist。

如果 Auto-review 显示为灰色，请在 [Team Settings → Models](https://cursor.com/dashboard/team-settings/models) 中启用 Claude 4.5 Haiku，完全退出并重新打开 Cursor，然后再次检查 Approvals & Execution。

Gemini 3.5 Flash Lite 在 Cursor 中仅作为 Auto-review 所用的模型提供。因此，为组织启用它并不会让写代码的 Agent 可以使用它。

### 配置 Auto-review

Auto-review 要工作良好并不需要配置。如果有些特定操作你始终希望手动审查，用通俗的话描述它们。

最简单的设置方式是让 Cursor Agent 来做。告诉它类似「我希望每条 AWS CLI 命令都先经过批准」，它会为你编辑 `permissions.json`。

你也可以自己编辑该文件。Auto-review 从两个位置读取 `permissions.json`：

| 位置 | 范围 |
| :--------------------------------------- | :------------------------------------------------------------------------------------------- |
| `~/.cursor/permissions.json` | 适用于你机器上的所有项目目录。 |
| `<project-dir>/.cursor/permissions.json` | 适用于一个项目目录。当项目应共享相同的指导时，把它提交进去。 |

如果两个文件都存在，Cursor 会合并它们。你的个人说明和项目说明都会生效。

团队也可以在仪表盘中定义全局 Auto-review 配置。定义了团队配置后，它优先，Cursor 会忽略用户级和项目级文件。

两个本地文件使用相同的 schema。每条说明都是一句通俗的话，因此「我希望每条 AWS CLI 命令都先经过批准」这样的请求会直接对应到 `block_instructions`：

```json
{
  "autoRun": {
    "allow_instructions": [],
    "block_instructions": [
      "Every AWS CLI command should go through approval first.",
      "Every command that modifies Kubernetes resources should go through approval first."
    ]
  }
}
```

- `allow_instructions` 描述 Auto-review 应倾向于允许的操作。
- `block_instructions` 描述 Auto-review 应倾向于阻止的操作，以便 Agent 可以选择另一条路径或请你批准。

关于策略设计的更多内容，阅读[用 Auto-review 治理 Agent 自主权](https://cursor.com/blog/agent-autonomy-auto-review)。

## 沙箱

沙箱让 Cursor 运行终端命令时不必给予完整的机器访问权限。沙箱中的命令可以在你的项目中工作，但不能自由读取受保护的文件、在已批准的路径之外写入，或联系任意网络目的地。

工程上的深入说明，见[为本地 Agent 实现安全沙箱](https://cursor.com/blog/agent-sandboxing)。

### permissions.json 与 sandbox.json 各司其职

`permissions.json` 引导 Auto-review 自动运行哪些调用、审查哪些调用。`sandbox.json` 控制沙箱中的命令可以触及什么，例如网络域名以及额外的可读或可写路径。开始使用时，这两个文件你都不需要。

| 访问 | 终端命令的默认沙箱行为 |
| :------------------ | :----------------------------------------------------------------------------------------------------------------------- |
| **工作区文件** | 工作区内的读写访问。`.cursorignore` 可以让 Agent 看不到某些文件。 |
| **受保护路径** | Cursor 会保护 `.git/config`、`.git/hooks`、`.vscode`、`.cursorignore` 以及敏感的 Cursor 配置文件等路径。 |
| **网络** | 默认阻止，然后由你的网络模式和 [`sandbox.json`](https://cursor.com/docs/reference/sandbox.md) 放开。 |
| **临时文件** | `/tmp` 和平台临时目录默认可写，除非在 `sandbox.json` 中禁用。 |

有些命令需要完整的系统访问权限，并会绕过沙箱。当命令在沙箱外运行时，Cursor 会标明，并请求你的批准。

### 沙箱配置

用 `sandbox.json` 文件自定义沙箱行为：

| 位置 | 范围 |
| :----------------------------------- | :------------------------------------------------------------------------------------------------ |
| `~/.cursor/sandbox.json` | 适用于你机器上的所有项目目录。 |
| `<project-dir>/.cursor/sandbox.json` | 适用于一个项目目录。当项目应共享相同的沙箱规则时，把它提交进去。 |

如果两个文件都存在，Cursor 会合并它们，项目级文件优先。团队管理员策略和 Cursor 硬编码的安全规则叠在其上，因此本地文件不能削弱这些保护。

使用 `sandbox.json` 控制网络策略、额外的可读或可写路径、临时目录写入以及共享的构建缓存。完整 schema 见 [`sandbox.json` 参考](https://cursor.com/docs/reference/sandbox.md)。

### 读取访问

读取访问控制 Agent 是否可以在不先询问你的情况下读取工作区之外的文件。它涵盖 Agent 的文件读取和搜索，以及在沙箱中运行的终端命令。

读取访问需要 Cursor 3.23 或更高版本。

在 **Settings > Agents > Approvals & Execution > Read Access** 中设置：

| 模式 | 行为 |
| :------------ | :-------------------------------------------------------------------------------------------------------------------- |
| **System** | Agent 可以在不经批准的情况下读取工作区之外的文件。这是默认值。 |
| **Workspace** | Agent 在工作区内自由读取。工作区之外的任何内容都需要你的批准，除非它在你的 Read Allowlist 上。 |

选择 **Workspace** 后：

- **工作区之外的文件读取会先询问。** 批准卡片会显示完整路径以及 Cursor 询问的原因。点击 **Allow** 以放行该读取。
- **搜索保持在边界内。** Grep 结果会跳过工作区之外、也不在你的 Read Allowlist 上的文件。
- **沙箱中的命令能看到的机器内容更少。** 在 macOS 和 Linux 上，沙箱中的命令可以读取工作区、你的 Read Allowlist，以及常见工具运行所需的系统路径，例如系统库、工具链和证书存储。命令获得的是该项目的私有临时目录，而不是主机临时目录。

工作区边界还包括 `~/.cursor/projects` 下的本项目文件夹，以及 `~/.cursor` 中的 skills、rules 和 plugins 文件夹。

读取访问不适用于 **Run Everything** 模式。该模式会在不做检查的情况下运行每次调用，因此这项设置在那里是隐藏的。

#### Read Allowlist

把 Agent 可以在工作区之外读取的路径添加到同一设置区域中的 **Read Allowlist**。在 Auto-review 中，展开 **Allowlist Options** 即可找到它。该列表仅在 Read Access 为 **Workspace** 时生效。

- **文件夹。** 绝对文件夹路径（如 `/opt/shared/design-tokens`）允许其下的所有内容。
- **Glob。** 带 `*` 的模式（如 `~/notes/*.md`）允许它匹配的文件。沙箱中的命令无法匹配 glob，因此它们获得对该模式父文件夹的读取访问。
- **主目录。** `~` 会展开为你的主目录。

#### 在 `sandbox.json` 中配置读取访问

在 `sandbox.json` 中设置 `readBoundary` 和 `additionalReadPaths`，按机器或按项目配置读取访问：

```json
{
  "readBoundary": "workspace",
  "additionalReadPaths": ["/opt/shared/design-tokens", "~/notes/*.md"]
}
```

`sandbox.json` 中的值会替换 Cursor 设置中的值，并且项目文件优先于 `~/.cursor/sandbox.json`。设置界面会显示是哪个文件在控制该值。

在 [CLI](https://cursor.com/docs/cli/overview.md) 中，于 [`cli-config.json`](https://cursor.com/docs/cli/reference/configuration.md) 里设置 `sandbox.readBoundary`，并把 `Read(...)` 条目加入 [`permissions.allow`](https://cursor.com/docs/cli/reference/permissions.md) 作为允许列表。`sandbox.json` 会覆盖这两者。

#### 团队读取策略

Enterprise 方案的管理员可以在 [Team Settings > Security & automation](https://cursor.com/dashboard/team-settings?view=extension-security) 中为团队设置读取策略，也可以为一个[组织组](https://cursor.com/docs/enterprise/organization-groups.md#auto-run-and-smart-auto)设置。打开 **Read Controls**，然后选择一种模式：

- **System** 把选择留给每个成员。
- **Workspace** 会为每个成员打开 Workspace。他们的设置会显示为由管理员控制，批准卡片会说明这次读取需要根据团队的读取策略获得批准。

在 **Workspace** 下，团队的 **Read Allowlist** 会追加到每个成员自己的列表。关闭 **Read Control User Extensions** 后，只应用团队列表，并忽略来自成员设置和 `sandbox.json` 的路径。

### 沙箱在你的平台上如何工作

### macOS

Cursor 通过 `sandbox-exec` 使用 Seatbelt。生成的沙箱配置文件会限制整个子进程树的文件访问、网络访问和其他进程行为。

**要求**

- Cursor v2.0 或更高版本
- 无需额外设置

### Linux

Cursor 使用 Landlock 和 seccomp。Landlock 应用文件系统限制。Seccomp 阻止不安全的系统调用。

**要求**

- **内核 6.2 或更高版本**，并支持 Landlock v3（`CONFIG_SECURITY_LANDLOCK=y`）
- 已启用**非特权用户命名空间**

如果你的内核不满足这些要求，Cursor 会回退为在运行命令前请求批准。

### AppArmor 设置（仅远程环境和 CLI）

本地桌面安装无需设置。Cursor 桌面安装包附带所需的 AppArmor 配置文件。

某些发行版通过 AppArmor 限制用户命名空间，而远程环境和独立的 [CLI](https://cursor.com/docs/cli/overview.md) 不附带该配置文件。如果在那里创建沙箱时因用户命名空间权限错误而失败，请为你的发行版安装 AppArmor 软件包。

Debian / Ubuntu:

```bash
curl -fsSL https://downloads.cursor.com/lab/enterprise/cursor-sandbox-apparmor_0.6.0_all.deb -o cursor-sandbox-apparmor.deb
sudo dpkg -i cursor-sandbox-apparmor.deb
```

RHEL / Fedora:

```bash
curl -fsSL https://downloads.cursor.com/lab/enterprise/cursor-sandbox-apparmor-0.6.0-1.noarch.rpm -o cursor-sandbox-apparmor.rpm
sudo rpm -i cursor-sandbox-apparmor.rpm
```

安装后，重启 Cursor 或你的 CLI 会话，沙箱即可工作。

### 环境变量

Cursor 会向每个沙箱子进程注入环境变量。在沙箱内运行的脚本、构建工具和自动化都可以使用它们。

| 变量 | 平台 | 说明 |
| :------------------------------- | :----------- | :--------------------------------------------------------------------------------------------------------------------------- |
| `CURSOR_SANDBOX` | macOS、Linux | 当进程在沙箱内运行时，设为 `"seatbelt"`（macOS）或 `"native"`（Linux）。 |
| `CURSOR_ORIG_UID` | macOS、Linux | 启动 Cursor 的用户的 UID，在沙箱应用任何命名空间或身份变更之前捕获。 |
| `CURSOR_ORIG_GID` | macOS、Linux | 启动 Cursor 的用户的 GID，在沙箱身份变更之前捕获。 |
| `CURSOR_SANDBOX_LANDLOCK_STATUS` | Linux | 报告当前生效的沙箱后端：`fully_enforced`（Landlock）、`bubblewrap`（Bubblewrap 回退）。用于诊断。 |

### Linux：沙箱内的 UID 可能与你的真实用户不一致

在 Linux 上，沙箱会创建用户命名空间，并把进程重映射为该命名空间内的 UID 0（root）。这意味着沙箱命令中的 `id -u` 和 `$UID` 返回 0，而不是你的主机用户 ID。如果你的脚本或自动化需要主机用户 ID，例如设置文件所有权或向 Docker 传递 `--user`，请改为读取 `CURSOR_ORIG_UID` 和 `CURSOR_ORIG_GID`。

#### Docker 与容器自动化

自动化规则和脚本中的一种常见模式，是运行需要与主机用户身份匹配的 Docker 容器。因为沙箱在 Linux 上会重映射 UID，依赖 `$(id -u)` 会得到错误的值。请改用 `CURSOR_ORIG_*` 变量：

```bash
docker run --rm \
  --user "${CURSOR_ORIG_UID:-$(id -u)}:${CURSOR_ORIG_GID:-$(id -g)}" \
  -v "$PWD:/work" -w /work \
  my-image build
```

`${CURSOR_ORIG_UID:-$(id -u)}` 这一回退确保命令在沙箱外也能工作，因为沙箱外没有设置这些变量。

### 网络访问

选择沙箱中的终端命令如何访问网络：

| 模式 | 行为 |
| :-------------------------- | :------------------------------------------------------------------------------------------------------------------ |
| **sandbox.json Only** | 网络仅限于你的 `sandbox.json` 允许列表中的域名。不会加入 Cursor 的默认域名。 |
| **sandbox.json + Defaults** | 你的允许列表，加上 Cursor 为常见包管理器和语言工具内置的默认域名。这是默认设置。 |
| **Allow All** | 无论 `sandbox.json` 如何，沙箱中都允许全部网络访问。 |

### 查看默认允许的域名

```text
*.cloudflarestorage.com
*.docker.com
*.docker.io
*.googleapis.com
*.githubusercontent.com
*.gvt1.com
*.public.blob.vercel-storage.com
*.yarnpkg.com
alpinelinux.org
anaconda.com
apache.org
apt.llvm.org
archive.ubuntu.com
archlinux.org
awscli.amazonaws.com
azure.com
binaries.prisma.sh
bitbucket.org
centos.org
cloudflarestorage.com
cocoapods.org
codeload.github.com
cpan.org
crates.io
debian.org
dl.google.com
docker.com
docker.io
dot.net
dotnet.microsoft.com
eclipse.org
fedoraproject.org
files.pythonhosted.org
fonts.gstatic.com
gcr.io
ghcr.io
github.com
gitlab.com
golang.org
google.com
goproxy.io
gradle.org
haskell.org
hashicorp.com
hex.pm
index.crates.io
java.com
java.net
json-schema.org
json.schemastore.org
k8s.io
launchpad.net
maven.org
mcr.microsoft.com
metacpan.org
microsoft.com
mise.run
nodejs.org
npm.duckdb.org
npmjs.com
npmjs.org
nuget.org
oracle.com
packagecloud.io
packages.microsoft.com
packagist.org
pkg.go.dev
playwright.azureedge.net
ppa.launchpad.net
proxy.golang.org
pub.dev
public.blob.vercel-storage.com
public.ecr.aws
pypa.io
pypi.org
pypi.python.org
pythonhosted.org
quay.io
registry.npmjs.org
registry.yarnpkg.com
repo.maven.apache.org
ruby-lang.org
rubygems.org
rubyonrails.org
rustup.rs
rvm.io
security.ubuntu.com
sh.rustup.rs
sourceforge.net
spring.io
static.crates.io
static.rust-lang.org
sum.golang.org
swift.org
ubuntu.com
visualstudio.com
yarnpkg.com
ziglang.org
```

## 其他保护

Run Modes 和沙箱并不是仅有的安全控制。即使某个模式本来会自动运行，这些保护仍可能要求批准：

| 保护 | 作用 |
| :--------------------------- | :------------------------------------------------------------------------------------------------- |
| **Browser Protection** | 阻止 Agent 自动运行浏览器工具。 |
| **File-Deletion Protection** | 阻止 Agent 自动删除文件，包括 `rm` 命令。 |
| **External-File Protection** | 阻止 Agent 自动创建、修改或删除工作区之外的文件。 |

## 团队控制

管理员可以覆盖用户可用哪些模式，配置终端命令的沙箱网络规则，设置[团队读取策略](https://cursor.com/docs/agent/security/run-modes.md#team-read-policy)，以及更多内容。所有这些设置都在 Web 仪表盘中。

团队设置优先于个人和项目配置。当你希望为所有人设定一致的基线时使用它们。如果为团队启用 Auto-review，请在[模型访问控制](https://cursor.com/docs/enterprise/model-and-integration-management.md#model-access-control)下保持允许[分类器需要的模型](https://cursor.com/docs/agent/security/run-modes.md#auto-review-model-requirements)之一。

## 更新日志

| Cursor 版本 | 日期 | 变更 |
| :------------- | :----------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **3.23** | Oct 1, 2026 | [读取访问](https://cursor.com/docs/agent/security/run-modes.md#read-access)增加了 Workspace 模式：在 Agent 读取工作区之外的文件之前会询问，并带有 Read Allowlist 和团队读取策略。 |
| **3.6** | May 29, 2026 | [Auto-review](/changelog/auto-review) 作为推荐的默认设置发布。 |
| **3.5** | May 22, 2026 | **Ask Every Time** 已弃用。新用户不能选择它。使用空允许列表的 **Allowlist** 可获得相同行为。**Run in Sandbox** 已并入启用了沙箱的 **Allowlist**。 |

### Cloud Agents 不使用 Run Modes

Run Modes 适用于本地 Agent。Cloud Agents 在自己的专用机器中运行，因此 Agent 从不会请你批准某个操作。


---

## 站点地图

[所有文档页面概览](/llms.txt)
