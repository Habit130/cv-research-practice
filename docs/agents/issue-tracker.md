# Issue 与 PR 协作:GitHub

本仓库的 Issue 与 PRD 建在 GitHub Issues 上,通过 `gh` CLI 操作。仓库为 `Habit130/cv-research-practice`;在克隆目录中由 remote 自动识别,跨目录操作时显式使用 `--repo Habit130/cv-research-practice`。

## Issue 操作

- 建 Issue:`gh issue create --title "..." --body-file <正文文件>`。
- 读 Issue:`gh issue view <number> --comments`。
- 列 Issue:`gh issue list --state open --json number,title,labels`,按需增加过滤条件。
- 评论:`gh issue comment <number> --body-file <正文文件>`。
- 加/删标签:`gh issue edit <number> --add-label "..."` / `--remove-label "..."`,映射见 [triage-labels.md](triage-labels.md)。
- 关闭:`gh issue close <number>`,确认任务已完成后操作。

多行正文写入临时文件再用 `--body-file` 传入,保留真实换行,避免 shell 展开正文中的命令或变量。当工作流要求“发布到 issue tracker”或“读取相关 ticket”时,分别创建或读取 GitHub Issue。

## PR 与交接

分支与审查原则以根目录 [AGENTS.md](../../AGENTS.md) 为准。推送任务分支后,可用 `gh pr create --base main --head <任务分支> --title "..." --body-file <正文文件>` 创建 PR。

PR 描述写清问题、最终改动、实际检查环境与结果、未验证项;已有 Issue 时关联其编号。跨环境交接补充分支、commit hash、剩余步骤与所需硬件。禁止把 CPU 检查描述为 MPS/CUDA 验证。

**外部 PR 不作为需求 triage 面**,需求仍在 Issues 中评估;这不影响通过 PR 审查变更。GitHub 访问受限时如实说明,保留本地分支与提交,不声称已发布。
