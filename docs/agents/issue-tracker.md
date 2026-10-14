# Issue tracker: GitHub

Issue 与规格放在 GitHub Issues,仓库为 `Habit130/cv-research-practice`。在本仓库目录中使用 `gh`;目标可能含糊时显式加上 `--repo Habit130/cv-research-practice`。

## Conventions

- 创建:`gh issue create --title "..." --body-file <path>`。
- 行动前先读:`gh issue view <number> --comments`,并取当前标签与 assignee。
- 列出:`gh issue list --state open --json number,title,labels`,按需增加过滤。
- Execution 认领:`gh issue edit <number> --add-assignee @me`,然后评论已确认的契约、branch/worktree 与尝试次数。
- 评论:`gh issue comment <number> --body-file <path>`。多行正文写入文件再传入,保留真实换行,避免 shell 展开正文中的命令或变量。
- 流转:按 [triage-labels.md](triage-labels.md) 添加或移除规范角色标签。
- 关闭:PR 使用 `Closes #<number>`。只有 habit squash-merge 之后,Orchestration 在 `origin/main` 核对到确切结果,才记录 Completed。

GitHub 的 issue 与 PR 共用编号空间。含糊的 `#<number>` 先查 PR,再查 issue。

## Pull requests as a triage surface

**PRs as a request surface: no.**

外部 PR 不作为需求 triage 面。交付 PR 仍是 `docs/agents/delivery.md` 下的审查与证据面,不进入需求队列。

## 发布与读取

技能要求“发布到 issue tracker”时,创建 GitHub Issue。技能要求“读取相关 ticket”时,运行 `gh issue view <number> --comments`。

访问 GitHub、推送或创建 PR 受限时如实交接,保留本地分支与提交,不声称远端已保存或 PR 已创建。硬件结论的写法见 [research-practice.md](research-practice.md);不得把 CPU 检查写成 MPS/CUDA 验证。

## Wayfinding operations

供已安装的 wayfinder 技能使用。本仓库目前没有 wayfinder map;需要时才创建,不在治理初始化中预建。

- **Map**: 一个带 `wayfinder:map` 标签的 issue,正文含 Notes / Decisions-so-far / Fog。`gh issue create --label wayfinder:map`。
- **Child ticket**: 用 GitHub sub-issue 连到 map(`gh api` 的 sub-issues 端点)。若 sub-issue 不可用,把 child 写进 map 正文的任务列表,并在 child 正文顶部写 `Part of #<map>`。标签为 `wayfinder:<type>`(`research` / `prototype` / `grilling` / `task`)。被认领后指派给驱动者。
- **Blocking**: 使用 GitHub 原生 issue dependencies。用 blocker 的数字 database id(`gh api repos/<owner>/<repo>/issues/<n> --jq .id`,不是 `#number` 或 `node_id`)调用 `gh api --method POST repos/<owner>/<repo>/issues/<child>/dependencies/blocked_by -F issue_id=<blocker-db-id>`。开放阻塞见 `issue_dependencies_summary.blocked_by`。dependencies 不可用时,在 child 正文顶部写 `Blocked by: #<n>, #<n>`。全部 blocker 关闭后,ticket 才算解除阻塞。
- **Frontier query**: 列出 map 的开放 child,去掉仍有开放 blocker 或已有 assignee 的项;按 map 顺序取第一个。
- **Claim**: `gh issue edit <n> --add-assignee @me`,这是该会话的第一次写操作。
- **Resolve**: `gh issue comment <n> --body-file <path>`,然后 `gh issue close <n>`,再把上下文指针追加到 map 的 Decisions-so-far。
