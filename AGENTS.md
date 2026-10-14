# AGENTS.md

Local OpenCode sessions inherit `~/.config/opencode/AGENTS.md`. This file contains only
cv-research-practice-specific boundaries, workflow rules, review invariants, and context
routing.

## Repository boundary

- 本仓库是可克隆的计算机视觉科研实践闭环模板。交付物是闭环及其记录,不是第二份指南或通用软件框架。
- 权威来源:`CONTEXT.md` 定义术语;`README.md` 定义任务梯度与各 Phase 的执行和退出标准;根目录《计算机视觉科研入门指南.pdf》是方法论最终依据,不得编辑或删除;`docs/adr/` 记录已落定决策。
- `templates/` 是任务无关模板。`example/` 只放作者验证跑,不得写入读者任务。`runs/` 是读者的实践闭环工作区。
- 仓库只收录流程文本。不得把数据集、权重、checkpoint、缓存、凭据或任务代码写入版本历史。上游实现优先克隆到仓库外,用 URL 与 commit hash 指认。放在 `runs/<任务>/repro/<仓库名>/` 时必须先确认已被 Git 排除,且不得作为嵌套仓库或 submodule 提交。
- 不得编造指标或把预期写成结果,不得把 CPU 检查写成 MPS/CUDA 验证。笔记、记录、报告与协作说明用中文;代码、命令、标识符及 baseline、ablation、checkpoint 等术语保留英文。固定标题、准则 ID、Code Review Rules 与 Conventional Commit subject 用英文。
- 没有统一的 build、lint、test 或 CI。文档变更检查引用、路径、术语与规则一致性。不要为文档维护启动训练。

## Session roles

- 每个写会话在启动提示中声明一个角色:**Orchestration**、**Execution** 或 **Acceptance**。
- **Orchestration** 拥有 issue 范围、治理产物,以及冻结的契约和 handoff。
- **Execution** 拥有一个 issue、一条 branch/worktree、一个交付面和唯一写者;在冻结契约内自主实现、验证并修复,然后交回证据。
- **Acceptance** 在需要时独立检查同一份契约,只写验收记录。产品实现与修复属于 Execution。
- habit 启动 Execution 与 Acceptance,并且是唯一的 squash-merge 权威。分发、Codex、handoff、验收与修复协议见 `docs/agents/delivery.md`。

## Git remotes and workflow

- `origin` = `https://github.com/Habit130/cv-research-practice`,写远端与 PR 目标。仓库为 public。默认分支是 `main`;本地 `main` 跟踪 `origin/main`。
- GitHub 当前没有 branch protection 或 ruleset,主机仍允许 merge、squash 与 rebase,合并后也不自动删除分支。这些主机缺口不构成绕过本文件的理由。
- 每个独立交付从最新的 `origin/main` 建立 `<type>/<kebab-slug>`。默认不使用 stacked PR。
- bootstrap 之后的每个变更都有一份冻结的 Issue 契约,通过一条 feature branch 和一个 PR 交付。
- Commit 使用英文 Conventional Commits。PR 包含 Motivation、Changes 与 Verification;说明文字用中文。
- Agent 可以建分支、提交、推送,以及创建或更新 PR。PR 保持打开。Agent 不合并、不启用 auto-merge、不改写远端历史。
- habit 执行 squash-merge。只有在 `origin/main` 核对到确切交付后,才记录完成。

## Code Review Rules

- Report a finding only when the delivery introduces a concrete P0/P1 in a supported path.
  State the trigger, impact, primary evidence, and safe path or exception. Treat style,
  optional refactors, and stronger-than-contract tests as non-blocking follow-up.
- Flag P1 when a change adds task code, a dataset, weights, a checkpoint, cache, or
  credentials to git history, or adds a nested repository or submodule. The safe path keeps
  them outside history and cites the upstream URL and commit hash. A clone under
  `runs/<task>/repro/<repo>/` is allowed only after Git exclusion is verified.
- Flag P1 when a reported metric or hardware claim is not traced to an experiment log
  created before that run, or when a CPU result is presented as MPS or CUDA verification.
  The safe path records the actual command, device, code version, and not-run, failed, or
  limited status.

## Agent skills

Load detailed guidance only when the task triggers it:

| Task | Required context |
| --- | --- |
| Issue tracker operations | `docs/agents/issue-tracker.md` |
| Triage or tracker labels | `docs/agents/triage-labels.md` |
| Planning, dispatch, handback, Acceptance, repair, or parallel delivery | `docs/agents/delivery.md` |
| Build, lint, test, packaging, or CI | `docs/agents/build.md` |
| Domain terms, boundaries, or architectural decisions | `docs/agents/domain.md`, then relevant `CONTEXT.md` and `docs/adr/` entries |
| Paper reading, reproduction, experiments, reports, or hardware claims | `docs/agents/research-practice.md` |

Follow only the rows relevant to the current task and any further pointers they contain.

## Shared-state gate

Parallel Execution uses a separate worktree per issue. Before dispatch, Orchestration
allocates exclusive ownership of every applicable machine-level shared resource:

- 本仓库没有自有服务、数据库或部署目标。
- 加速设备、conda 环境,以及仓库外的上游克隆目录,都是机器本地资源。交付实际使用时独占分配,并把实例写入 `.local/agent-context.md`。
- 不得把另一环境的 CPU 结果当作 MPS 或 CUDA 证据。
