> 原文：https://cursor.com/docs/agent-sdk/quickstart

# 用 Agent SDK 构建 PR 审查者

你将构建一个审查者，把每个 pull request 评为 `trivial`、`moderate` 或 `large`，然后批准安全的改动，或请求人工审查。

部署之后，你在 [Deployed Agents](https://cursor.com/dashboard/deployed-agents) 中管理它。

## 用编程 Agent 构建

把这段提示词粘贴到你的编程 Agent 中，即可根据本页构建审查者。后面的各节会手工走一遍相同的步骤。

```text
Build the PR reviewer from https://cursor.com/docs/agent-sdk/quickstart.md
in ./pr-reviewer. Fetch that page and follow each step in order.

- If agent-sdk isn't on PATH, install it with
  npm install --global @grok/agent-sdk. Wait while I finish agent-sdk login.
- Use the code from the page as written. Ask me for a GitHub repository and
  a pull request I didn't author, and use them in place of acme/checkout#42.
- Run both tool-call checks, and confirm the complexity each one reports.
- Ask me before any step that posts a review on GitHub. Skip the deploy step.

Finish with agent-sdk validate --dir . and the project's TypeScript check,
then tell me what passed.
```

## 前提条件

模型轮次需要 Node 22.13 或更高版本，以及 Cursor 登录：

```bash
npm install -g @grok/agent-sdk
agent-sdk login
```

## 创建并运行项目

```bash
agent-sdk init ./pr-reviewer
cd pr-reviewer
agent-sdk dev
```

在第二个终端中，确认 Agent 会回复：

```bash
agent-sdk run --dir . --message "Introduce yourself in one sentence."
```

## 添加审查指令

替换 `bot/instructions.md`：

```md
# PR reviewer

Call `inspect_pr` before you judge a pull request, then act on the
complexity it reports:

- `trivial`: approve with `submit_review` if the diff matches the title
  and nothing looks risky.
- `moderate`: approve only when you understand every patch and see no
  risk. Otherwise request a human review and name the files that worry you.
- `large`: request a human review right away and point the reviewer at
  the biggest files.

Never approve a draft. End with one sentence that gives the verdict and why.
```

## 共享 URL 解析器

两个工具都要解析 pull request URL，因此把解析器放在 `bot/lib/github.ts`。Agent SDK 不会在那里查找工具。

```ts
export function parsePrUrl(prUrl: string) {
  const [owner, repo, pulls, number] = new URL(prUrl).pathname.split("/").filter(Boolean);
  const pull_number = Number.parseInt(number ?? "", 10);
  if (pulls !== "pull" || owner === undefined || repo === undefined || Number.isNaN(pull_number)) {
    throw new Error(`Not a pull request URL: ${prUrl}`);
  }
  return { owner, repo, pull_number };
}
```

## 添加检查工具

创建 `bot/tools/inspect_pr.ts`。它对大型 pull request 跳过补丁，并将其余补丁限制在 3,000 个字符。

```ts
import { defineTool } from "@grok/agent-sdk/tools";
import { z } from "zod";
import { parsePrUrl } from "../lib/github.js";

function rateComplexity(linesChanged: number, changedFiles: number) {
  if (linesChanged <= 25 && changedFiles <= 2) {
    return "trivial";
  }
  if (linesChanged <= 400 && changedFiles <= 15) {
    return "moderate";
  }
  return "large";
}

export default defineTool({
  description: "Fetch a pull request's title, patches, and complexity.",
  inputSchema: z.object({ prUrl: z.string().describe("GitHub pull request URL") }),
  async execute({ prUrl }, ctx) {
    const pull = parsePrUrl(prUrl);
    const octokit = await ctx.host.github.getOctokit();
    const { data: pr } = await octokit.rest.pulls.get(pull);
    const { data: files } = await octokit.rest.pulls.listFiles({ ...pull, per_page: 100 });
    const complexity = rateComplexity(pr.additions + pr.deletions, pr.changed_files);

    return {
      title: pr.title,
      draft: pr.draft ?? false,
      complexity,
      files: files.map((file) => ({
        path: file.filename,
        additions: file.additions,
        deletions: file.deletions,
        patch: complexity === "large" ? undefined : file.patch?.slice(0, 3000),
      })),
    };
  },
});
```

## 验证复杂度分类

在没有模型轮次的情况下调用该工具：

```bash
# Expect "complexity": "trivial"
agent-sdk tool call inspect_pr --dir . --input '{"prUrl":"https://github.com/facebook/react/pull/35623"}'

# Expect "complexity": "large" and no patch fields
agent-sdk tool call inspect_pr --dir . --input '{"prUrl":"https://github.com/facebook/react/pull/25229"}'
```

## 添加审查工具

创建 `bot/tools/submit_review.ts`：

```ts
import { defineTool } from "@grok/agent-sdk/tools";
import { z } from "zod";
import { parsePrUrl } from "../lib/github.js";

export default defineTool({
  description: "Approve the pull request, or comment asking for a human review.",
  inputSchema: z.object({
    prUrl: z.string().describe("GitHub pull request URL"),
    verdict: z.enum(["approve", "request_human_review"]),
    summary: z.string().describe("One or two sentences on why."),
  }),
  async execute({ prUrl, verdict, summary }, ctx) {
    const approve = verdict === "approve";
    const octokit = await ctx.host.github.getOctokit();
    await octokit.rest.pulls.createReview({
      ...parsePrUrl(prUrl),
      event: approve ? "APPROVE" : "COMMENT",
      body: approve ? summary : `Human review needed. ${summary}`,
    });
    return { posted: true, verdict };
  },
});
```

你需要 GitHub 写权限，以及一个不是你编写的 pull request。

## 审查一个 pull request

```bash
agent-sdk run --dir . --message "Review https://github.com/acme/checkout/pull/42"
```

确认 `inspect_pr` 在 `submit_review` 之前运行，然后在 GitHub 上检查该审查。

## 从 GitHub 触发审查

创建 `bot/channels/github.ts`：

```ts
import { defaultGitHubAuth, githubChannel } from "@grok/agent-sdk/channels/github";

const REVIEW_ACTIONS = new Set(["opened", "reopened", "ready_for_review"]);

export default githubChannel({
  botName: "agent-sdk-pr-reviewer",
  cursorAccount: { repos: ["acme/checkout"] },
  onPullRequest: (ctx, pr) => {
    if (!REVIEW_ACTIONS.has(pr.action) || pr.draft) {
      return null;
    }

    return {
      auth: defaultGitHubAuth(ctx),
      title: `Review ${ctx.repository.fullName}#${pr.number}`,
      context: [`Review ${pr.url}.`],
    };
  },
});
```

在 `agent-sdk dev` 运行时，重放一个打开的 pull request。拉取权限就够了：

```bash
agent-sdk github replay https://github.com/acme/checkout/pull/42 --dir . --action opened
```

打开 playground，确认 `Review acme/checkout#42` 同时显示两次工具调用和结论。

## 部署审查者

按照[部署](https://cursor.com/docs/agent-sdk/deployment.md)运行 `agent-sdk deploy`，然后在 [Deployed Agents](https://cursor.com/dashboard/deployed-agents) 中管理审查者。

## 接下来去哪里

- 用 [Evals](https://cursor.com/docs/agent-sdk/evals.md) 把这两个 PR 变成回归检查。
- 在[工具](https://cursor.com/docs/agent-sdk/reference/tools.md)中阅读类型化工具。
- 用 [GitHub 指南](https://cursor.com/docs/agent-sdk/guides/github.md)设置 fixture 和转发。

---

## 站点地图

[所有文档页面概览](/llms.txt)
