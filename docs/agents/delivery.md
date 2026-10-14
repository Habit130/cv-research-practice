# 交付协议

本文件定义 issue 范围的工作如何从冻结契约走到经核对的完成。Issue 是唯一的验收契约正文。

## 角色

### Orchestration

Orchestration 会话拥有一组明确列入和排除的 issue。一个 issue 不能同时属于两个活动中的 orchestration 范围。

Orchestration 可以写治理产物:issue 与 tracker 状态、规格、ADR、路线图、AGENTS 与 `docs/agents/`、冻结 handoff,以及验收记录。它不实现或修复产品代码。

分发前,Orchestration:

1. 刷新 issue、依赖、assignee、分支、PR、评论与共享状态;
2. 冻结完整的 issue 契约;
3. 决定 `Independent acceptance: required` 或 `not required`,并记录理由;
4. 分配一条 branch/worktree,以及每一项机器级共享资源;
5. 写入不可变的 Execution handoff,并在 issue 上记录路径与 SHA-256。

替换 Orchestration 需要一份 `ORCH-<scope>-<timestamp>.md` handoff,包含当前前沿、issue、PR、尝试、发现与资源分配。接替者行动前先刷新实时状态。

### Execution

Execution 拥有一个 issue、一条 branch/worktree、一个 PR 和唯一写者。编辑前把 issue 指派给自己,并评论已确认的契约 ID、分支、worktree 与尝试次数。

Execution 在冻结契约内选择实现、工作顺序和测试设计。它把已授权工作做到实现、适用验证、检查,以及修复本次交付造成的失败,常规步骤不反复请示。产品、范围或验收变更需要新契约版本,之后相关工作才能继续;不受影响的已授权工作可以继续。只暂停受缺失决定、权限或前提影响的工作。允许只读助手;写助手不共享这份交付。

Execution 交回:

- 已确认的契约 ID,以及确切的分支、PR 与 head;
- 每条 BASE 与 ticket 准则的 `Criterion ID -> primary evidence -> Pass/Fail`;
- 运行过的命令与关键输出;
- 推迟的工作与契约外发现;
- 契约有意留给实现选择的决定。

Execution 让 issue 和 PR 保持打开,并释放写所有权。

### Acceptance

Acceptance 是 habit 启动的新会话。它可以跑检查,并写 issue/PR 验收记录。它不修改交付分支、产品代码或治理文件。

Acceptance 阅读冻结的 issue 契约、每一个 PR commit、diff、直接受影响的交互边界、Execution 的证据矩阵、验证输出,以及 Codex review。Execution 的推理和结论只是线索,不是事实。

Acceptance 给出自己的完整准则/证据 Pass/Fail 矩阵,并处置每一条 Codex 或新观察到的发现。Pass 是给 habit 的技术门,不授权 Acceptance 会话合并。

## Issue 契约

bootstrap 之后的每个 PR 关联一个可执行 issue。Issue 包含:

- Goal;
- 列入与排除的 Scope;
- Dependencies;
- Independent acceptance 决定与理由;
- 全部六条展开的 BASE 准则;
- 带稳定 ID 和预期证据的 ticket 准则。

从冻结 handoff 启动 Execution,即确认该契约版本。实质性澄清在 issue 中建立新版本,并写一份新的不可变 handoff。Acceptance 只使用活动 Execution 已确认的版本。

## 仓库基线

每个可执行 issue 全文展开这些定义:

- **BASE-EVIDENCE**: Execution 把每条准则映射到主要证据和 Pass/Fail。触发独立 Acceptance 时,Acceptance 提供自己的完整映射。
- **BASE-VERIFY**: issue 点名的每条 build、lint、test、typecheck、packaging 或专项命令按要求的顺序成功,输出被标为证据。
- **BASE-SEVERITY**: 本 PR 不引入经独立确认的 P0/P1。Codex 发现必须按下面的窄分类,用证据明确确认或拒绝。
- **BASE-SCOPE**: 列入范围之外的工作保持未改,或明确记为推迟;排除范围不被实现。
- **BASE-DOCS**: 行为、接口、工作流或约束的变更,在同一次交付中更新权威文档。
- **BASE-PR**: PR 关联本 issue,并写明 Motivation、Changes 与 Verification。

Ticket 准则补充有限的行为与证据。不使用宽泛的“没有 bug”或“所有边界情况”条款。

## 严重度

- **P0**: 本 PR 引入的灾难性缺陷,影响是系统级或大体不可逆,例如大范围无法恢复的数据丢失、会导致未授权访问的凭据暴露,或几乎所有受支持用法都完全失去主要产品。合并前没有合理的遏制办法。
- **P1**: 本 PR 在受支持路径上引入的具体缺陷,破坏核心操作、损坏规范或已持久化的数据,或违反既定安全或隐私边界;影响重大,且受影响路径没有可接受的变通办法。
- **P2/P3**: 更低严重度的缺陷、可维护性问题、可选重构、更强测试建议,以及契约外的改进。记为不阻塞的后续工作。

Codex 的 P0/P1 只有在 Acceptance 独立确认其触发条件、影响、主要证据和严重度之后才阻塞。普通的 override 评论不能豁免已确认的 P0/P1。改变底层产品或安全边界需要新的 issue/ADR 和新契约。

## 有条件的独立 Acceptance

Orchestration 在分发前决定是否需要独立 Acceptance。它依据影响面、可逆性、跨边界效应、实现判断,以及确定性验证的强度,把决定和理由写入 issue。

出现下列情况时,独立 Acceptance 为 required:

- Orchestration 预先声明;
- Codex 报告 P0/P1;
- Codex review 不可用或未完成;
- 仓库没有 GitHub review 路径。

当 Acceptance 为 `not required` 时,Execution 的完整自检,加上一次没有 P0/P1 的已完成 Codex review,就是技术上的合并建议。

Acceptance 不能因为新偏好的实现、更强的测试或未列出的场景,而判定交付失败。ticket 准则之外的发现,除非是 BASE-SEVERITY 下已确认的 P0/P1,否则不阻塞。

## 审查边界

Acceptance 审查契约、全部 PR commit、diff,以及 diff 改变的直接交互边界。它不审计无关的未改代码。

修复之后重新打开:

- 每条失败的准则;
- 每条此前通过、但实现、证据或直接交互边界在修复 diff 中发生了变化的准则。

其他已通过准则保持关闭,除非主要证据证明发生了回归。

## 失败分流与有限修复

每条 Fail 分类为:

- **Local defect**: 路径仍然有效,有限修正不需要新的设计判断。尽可能让原 Execution 做一次聚焦修订。
- **Capability mismatch**: 该次尝试缺少必要的探索、集成或判断。启动一次新的、确实更强的 Execution 接管。
- **Specification blocker**: 继续推进需要产品、范围或契约决定。加上 `ready-for-human` 并停给 habit。
- **Execution-environment blocker**: 工具、凭据、平台或已分配的共享状态使公平尝试无法进行。暂停并解决,不消耗尝试次数。

执行链最多三次尝试:初次、一次聚焦修订、一次新的升级接管。最后一次可用尝试仍失败时,把 issue 标为 `ready-for-human`,附上证据和建议的决定。

## Handoff

冻结 handoff 放在 `docs/orchestration/handoffs/`,并被 gitignore。Issue 记录相对仓库的路径、契约版本、尝试次数、角色与 SHA-256。

使用:

```text
AC-<issue>-v<contract>-a<attempt>-execution.md
AC-<issue>-v<contract>-a<attempt>-acceptance.md
ORCH-<scope>-<timestamp>.md
```

发布 digest 之后不得覆盖该 handoff。

## 完成

Acceptance Pass 表示 PR 可以交给 habit。habit squash-merge。PR 使用 `Closes #<issue>`,但 Orchestration 只有在写远端默认分支上核对到确切交付之后,才记录 Completed。然后清理本地 worktree 和分支。
