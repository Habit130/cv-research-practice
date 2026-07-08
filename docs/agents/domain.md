# 领域文档(Domain Docs)

> 默认脚手架(2026-07-07):single-context 布局;`CONTEXT.md` 与 `docs/adr/` 均按需惰性创建,细节待讨论。

工程技能在探索本仓库前,应按以下方式消费领域文档。

## 探索前先读

- 仓库根目录的 **`CONTEXT.md`**;或
- 若存在根目录的 **`CONTEXT-MAP.md`**,它会指向各 context 自己的 `CONTEXT.md`,按主题读相关的那几份。
- **`docs/adr/`** —— 读与即将动手的区域相关的 ADR。多 context 仓库还要看 `src/<context>/docs/adr/` 里的局部决策。

以上文件不存在时,**静默继续**。不要提示缺失,也不要主动建议先创建它们。`/domain-modeling` 技能(经 `/grill-with-docs` 与 `/improve-codebase-architecture` 触达)会在术语或决策真正落定时惰性创建。

## 文件结构

Single-context 仓库(本仓库当前采用):

```
/
├── CONTEXT.md
├── docs/adr/
│   ├── 0001-<决策>.md
│   └── 0002-<决策>.md
├── templates/
├── example/
└── runs/
```

Multi-context 仓库(标志是根目录出现 `CONTEXT-MAP.md`):

```
/
├── CONTEXT-MAP.md
├── docs/adr/                          ← 全局决策
└── src/
    ├── <context-a>/
    │   ├── CONTEXT.md
    │   └── docs/adr/                  ← 该 context 的局部决策
    └── <context-b>/
        ├── CONTEXT.md
        └── docs/adr/
```

## 使用词汇表里的词

产出中出现领域概念时(issue 标题、重构提案、假设、测试名),按 `CONTEXT.md` 中的定义用词,不要漂移到词汇表明确避开的同义词。

需要的概念不在词汇表里,本身就是信号:要么你在发明项目不用的语言(重新考虑),要么确有空缺(记下来交给 `/domain-modeling`)。

## 与 ADR 冲突时要明说

产出与既有 ADR 矛盾时,显式指出而不是悄悄覆盖:

> _与 ADR-0007(<决策名>)矛盾 —— 但值得重开,因为……_
