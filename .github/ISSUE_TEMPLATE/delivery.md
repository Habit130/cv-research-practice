---
name: Delivery contract
about: 冻结一份 issue 范围的交付及其验收证据
title: ""
labels: needs-triage
assignees: ""
---

Contract: `AC-<issue>-v1`

## Goal

<!-- 写明可观察的结果与动机。 -->

## Scope

### Included

- <!-- 有限的列入范围。 -->

### Excluded

- <!-- 明确的非目标。 -->

## Dependencies

- <!-- 阻塞的 issue、决定、仓库,或 `None`。 -->

## Independent Acceptance

- Decision: `required` | `not required`
- Rationale: <!-- 影响面、可逆性、跨边界效应、实现判断,以及确定性验证的强度。 -->

## Repository Baseline

- [ ] **BASE-EVIDENCE**: Execution 把每条准则映射到主要证据和 Pass/Fail。触发独立 Acceptance 时,Acceptance 提供自己的完整映射。
- [ ] **BASE-VERIFY**: 本 issue 点名的每条 build、lint、test、typecheck、packaging 或专项命令按要求的顺序成功,输出被标为证据。
- [ ] **BASE-SEVERITY**: 本 PR 不引入经独立确认的 P0/P1。Codex 发现必须按此分类用主要证据确认或拒绝:
  - **P0**: 本 PR 引入的灾难性缺陷,影响是系统级或大体不可逆,例如大范围无法恢复的数据丢失、会导致未授权访问的凭据暴露,或几乎所有受支持用法都完全失去主要产品。合并前没有合理的遏制办法。
  - **P1**: 本 PR 在受支持路径上引入的具体缺陷,破坏核心操作、损坏规范或已持久化的数据,或违反既定安全或隐私边界;影响重大,且受影响路径没有可接受的变通办法。
  - **P2/P3**: 更低严重度的缺陷、可维护性问题、可选重构、更强测试建议,以及契约外的改进。记为不阻塞的后续工作。
- [ ] **BASE-SCOPE**: Included 之外的工作保持未改,或明确记为推迟;Excluded 不被实现。
- [ ] **BASE-DOCS**: 行为、接口、工作流或约束的变更,在同一次交付中更新权威文档。
- [ ] **BASE-PR**: PR 关联本 issue,并写明 Motivation、Changes 与 Verification。

## Ticket Criteria

- [ ] **TICKET-1**: <!-- 要求。 -->
  - Expected evidence: <!-- 主要产物、命令或可观察结果。 -->

## Verification Commands

```text
<!-- 确切命令与要求的顺序;若无命令,写 `N/A` 并说明原因。 -->
```

## Contract Changes

<!-- 追加版本、habit 的决定、变更的准则,以及 Execution 的确认。不要改写已确认版本的历史。 -->
