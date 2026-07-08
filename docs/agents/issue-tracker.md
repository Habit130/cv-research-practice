# Issue Tracker:GitHub

> 2026-07-07 讨论后从本地 markdown 切换至 GitHub Issues(仓库:Habit130/cv-research-practice)。

本仓库的 issue 与 PRD 都建在 GitHub Issues 上,一切操作走 `gh` CLI。

## 约定

- **建 issue**:`gh issue create --title "..." --body "..."`,多行正文用 heredoc。
- **读 issue**:`gh issue view <number> --comments`,需要时配 `jq` 过滤评论并取标签。
- **列 issue**:`gh issue list --state open --json number,title,body,labels,comments --jq '[.[] | {number, title, body, labels: [.labels[].name], comments: [.comments[].body]}]'`,按需加 `--label` / `--state` 过滤。
- **评论**:`gh issue comment <number> --body "..."`
- **加/删标签**:`gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- **关闭**:`gh issue close <number> --comment "..."`

仓库从 `git remote -v` 推断——在克隆目录内 `gh` 会自动识别。

## PR 是否作为 triage 面

**否。**(若日后把外部 PR 视为 feature request,改为 `yes`;`/triage` 读取此开关。)

## 当技能说"publish to the issue tracker"

建一个 GitHub issue。

## 当技能说"fetch the relevant ticket"

运行 `gh issue view <number> --comments`。
