# Triage 标签

五个标签是工作流角色,不是 issue 类型。标签字符串不因更换 Agent 而改名。

| Canonical role | Tracker label | Meaning |
| --- | --- | --- |
| `needs-triage` | `needs-triage` | 维护者需要评估 |
| `needs-info` | `needs-info` | 等待报告者补充信息 |
| `ready-for-agent` | `ready-for-agent` | 契约已冻结,可以交给 Execution |
| `ready-for-human` | `ready-for-human` | 需要 habit 决定,或修复链已耗尽 |
| `wontfix` | `wontfix` | 不予处理 |

使用能说明谁可以推进 issue 的标签。保留无关的类型标签与领域标签,不另建同义标签。
