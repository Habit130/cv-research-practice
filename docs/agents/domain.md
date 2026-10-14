# 领域文档

`CONTEXT.md` 记录仓库职责、非目标、关联仓库与已确认的领域用语。`docs/adr/` 记录耐久决定及其理由。

只加载与当前任务相关的上下文。保持 `CONTEXT.md` 中的规范术语和所有权边界。发现与已接受 ADR 冲突时,明确指出冲突,不静默覆盖。

## Layout

本仓库是 single-context,没有 monorepo 边界,不使用 `CONTEXT-MAP.md`。

- 根 [CONTEXT.md](../../CONTEXT.md)
- [docs/adr/](../adr/),当前为 [ADR-0001](../adr/0001-第二阶段形态-模板仓库而非第二份指南.md) 与 [ADR-0002](../adr/0002-仓库只含流程文本.md)
- 实践流程以 [README.md](../../README.md) 为准
- 方法论指回[理论指南](../../计算机视觉科研入门指南.pdf),不得编辑或删除该 PDF

修改相关区域前阅读对应 ADR。需要改变既有决定时,先说明具体决策、冲突与理由,交由 habit 决定。仅在决定落定时新增 ADR。
