# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working in a clone of this repository.

## 这个仓库是什么

这是一个**可克隆的计算机视觉科研实践闭环模板仓库**,不是软件代码库,没有 build / lint / test 命令。它配套根目录的 PDF:

- **计算机视觉科研入门指南.pdf** —— 用户本人撰写的 11 页中文理论指南(IEEEtran 排版),围绕五类能力展开:方向认知(§2)、代码能力(§3)、论文能力(§4)、模型能力(§5)、实验能力(§6)。本仓库只有编译后的 PDF,LaTeX 源码不在这里。不要编辑或删除该 PDF —— 它是本仓库一切方法论的最终依据,仓库自身不复述其内容,只引用章节号。

一句话阶段史:第一阶段产出这份理论指南;第二阶段把仓库本身改造成下述可克隆模板,让读者围绕自己选定的小任务跑一遍实践闭环。

## 三分结构

```
templates/     # 任务无关模板(PLAN / 论文笔记 / 复现清单 / 实验记录),复制到 runs/ 下你自己的任务里使用
example/       # 作者本人的验证跑(ResNet-18 + CIFAR-10,全深度),五环节齐全,仅供参考体例,不要往里面添加你自己的内容
runs/          # 你的工作区 —— 围绕自己选定的任务在这里跑实践闭环
```

## 读者支持行为(核心)

当你在协助用户于 `runs/` 下推进他们自己的实践闭环任务时,遵守以下纪律:

- **实验记录先建后跑**:任何一次训练/评测启动**之前**,先用 `templates/experiment-log-template.md` 建好本次记录,再开始跑;不要事后补记录。
- **结论必须条件化**:每份实验记录的结论要写成"指标在什么条件下从 X 变到 Y、代价是什么",禁止只写"效果变好"这类无条件判断。
- **复现不许跳级**:代码复现按理论指南 §6.3 的阶梯(环境 → demo → checkpoint 评测 → 小规模训练 → 完整训练)逐级走,不要跳过某一级直接冲完整训练。
- **方法论问题引导回理论指南**:遇到"论文该怎么选""实验该怎么设计"这类方法论问题,不要现场发明流程或替读者复述,而是指向理论指南对应章节——论文筛选 → Table 4 三层分级 + §4.3 三轮阅读;复现阶梯 → §6.3;环境排查 → Table 6;实验记录五类信息 → §6.4;对比分析(baseline / ablation / fair comparison)→ §6.6 与 Table 7;报告叙事线 → §4.4–4.5——再在当前任务语境下落地成具体步骤。
- 上游代码克隆到 `runs/` 下你自己任务的目录里并在其中修改;数据集和 checkpoint 不放进本仓库(或只放在克隆仓库自己的数据目录里,ADR-0002)。

## 语言约定

本仓库全部内容(笔记、记录、报告、本文件)使用中文;代码、命令、标识符及英文术语(loss function、baseline、ablation、checkpoint 等)保留英文,与理论指南行文风格一致。

## Agent skills

### Issue tracker

Issue 与 PRD 都建在 GitHub Issues 上(`gh` CLI,仓库 Habit130/cv-research-practice);外部 PR 不作为 triage 面。见 `docs/agents/issue-tracker.md`。

### Triage labels

五个 triage 角色均使用默认字符串(`needs-triage`、`needs-info`、`ready-for-agent`、`ready-for-human`、`wontfix`),未做映射。见 `docs/agents/triage-labels.md`。

### Domain docs

Single-context 布局:根目录 `CONTEXT.md` + `docs/adr/`,均按需惰性创建。见 `docs/agents/domain.md`。
