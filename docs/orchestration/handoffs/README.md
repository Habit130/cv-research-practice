# Orchestration Handoffs

本目录的 README 与命名约定纳入版本管理。冻结的 handoff 正文是本机产物,被 gitignore,不提交。

使用:

```text
AC-<issue>-v<contract>-a<attempt>-execution.md
AC-<issue>-v<contract>-a<attempt>-acceptance.md
ORCH-<scope>-<timestamp>.md
```

冻结文件后,在 issue 上记录相对仓库的路径与 SHA-256。digest 一经发布,该文件即不可变;需要变更时新建版本或尝试,不得覆盖。
