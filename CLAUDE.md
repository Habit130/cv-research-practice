# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 这个工作区是什么

这是一个计算机视觉科研训练工作区,不是软件代码库,没有 build / lint / test 命令。它承载一个两阶段的自我训练计划,方法论依据是根目录的 PDF:

- **计算机视觉科研入门指南.pdf** —— 用户本人撰写的 11 页中文指南(IEEEtran 排版),围绕五类能力展开:方向认知(§2)、代码能力(§3)、论文能力(§4)、模型能力(§5)、实验能力(§6)。本目录只有编译后的 PDF,LaTeX 源码不在这里。不要编辑或删除该 PDF —— 它是本工作区一切方法论的最终依据。

## 阶段路线

- **第一阶段(已完成,2026 年 7 月):** 通读指南,建立能力地图。
- **第二阶段(进行中):** 指南 §7 定义的项目化实践闭环 —— 围绕一个小型 CV 任务,完成:论文选读 → 代码复现 → 实验记录 → 对比分析 → 报告写作。计划与进度见 `stage2/PLAN.md`。

## 第二阶段目录结构

```
stage2/
├── PLAN.md            # 任务决定、阶段划分、退出标准 —— 先读这个
├── papers/            # 每篇必读论文一份笔记(paper-notes-template.md)
├── repro/             # 克隆的上游仓库 + repro-checklist.md
├── experiments/       # 每次实验一份记录(experiment-log-template.md)
└── report/            # LaTeX 报告(Phase 4 创建)
```

## 约定

- 方法论一律锚定指南章节:复现走 §6.3 的阶梯(环境 → demo → checkpoint 评测 → 小规模训练 → 完整训练);环境排查按 Table 6;实验记录必含 §6.4 的五类信息;对比遵守 §6.6 与 Table 7 的 baseline / ablation / fair comparison 纪律;论文筛选按 Table 4 三层分级 + §4.3 三轮阅读。
- 实验记录在启动运行**之前**从模板创建,不要事后补。
- 实验结论写成条件化判断(指标在什么条件下从 X 变到 Y、代价是什么),禁止只写"效果变好"。
- 上游代码克隆到 `stage2/repro/<name>/` 并在其中修改;数据集和 checkpoint 不放进本工作区(或只放在克隆仓库自己的数据目录里)。
- 工作语言:本仓库全部内容(笔记、记录、报告、本文件)使用中文;代码、命令、标识符及英文术语(loss function、baseline、ablation、checkpoint 等)保留英文,与指南行文风格一致。

## Agent skills

### Issue tracker

Issue 与 PRD 都建在 GitHub Issues 上(`gh` CLI,仓库 Habit130/cv-research-practice);外部 PR 不作为 triage 面。见 `docs/agents/issue-tracker.md`。

### Triage labels

五个 triage 角色均使用默认字符串(`needs-triage`、`needs-info`、`ready-for-agent`、`ready-for-human`、`wontfix`),未做映射。见 `docs/agents/triage-labels.md`。

### Domain docs

Single-context 布局:根目录 `CONTEXT.md` + `docs/adr/`,均按需惰性创建。见 `docs/agents/domain.md`。
