# Build and Verification

本文件记录未来交付需要的可执行路径。面向读者的环境搭建仍在 README。

## Required order

无。仓库没有统一的 build、lint、test、typecheck、packaging 或 CI 命令,因此没有顺序依赖。

## Commands

无仓库级可执行验证命令。不要发明命令,也不要用训练代替文档检查。

## Operational constraints

- 文档变更核对 Markdown 引用、路径、术语,以及与 `CONTEXT.md` 和相关 ADR 的一致性。
- 任务代码的检查在对应上游实现与实际环境中完成,不在本仓库。
- 不要为文档维护启动训练。CPU 检查不得写成 MPS/CUDA 验证。
- 无 GitHub Actions,无必需状态检查。
