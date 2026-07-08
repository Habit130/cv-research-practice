# 第二阶段计划 —— 项目化实践闭环

依据:指南 §7(结语)。围绕一个小型 CV 任务,把五类能力串成闭环:
论文选读 → 代码复现 → 实验记录 → 对比分析 → 报告写作。

## 任务决定 —— 待确认

推荐:**Loop A —— 图像分类,ResNet-18 + CIFAR-10。**

- 指南的模型能力一章以 ResNet 为入口(§5.1、Fig. 3);分类是五要素最简的问题形态(Table 2),闭环每个环节都足够小、能做完。
- 单张普通 GPU 或 Apple Silicon MPS 即可完成,业余时间约 2–3 周。
- 可选后续:**Loop B —— 小型目标检测**,对应指南贯穿全文的例子(校园道路骑车人/行人检测,Table 2),在 Loop A 完成后做,例如微调一个小型检测器。

## Phase 0 —— 环境(指南 §6.2、Table 6)

- 新建 conda 环境,固定 Python / PyTorch 版本。
- 退出标准:PyTorch 能看到 GPU 或 MPS;冒烟测试的前向传播跑通。

## Phase 1 —— 论文选读(指南 §4.1–4.3、Table 4)

- 种子论文:ResNet(He et al., 2016)。向后追引文(AlexNet、VGG),向前找若干 forward citation。
- 检索结果按三层分级(必读 / 背景 / 暂存 → 入 Zotero 并标注暂存原因)。
- 每篇必读论文用 [papers/paper-notes-template.md](papers/paper-notes-template.md) 做一份笔记,三轮阅读。
- 退出标准:完成 1–2 份必读笔记;不翻 PDF 也能说出种子论文的五要素(数据 / 任务 / 模型 / loss / 指标)。

## Phase 2 —— 代码复现(指南 §6.3、§3.5)

- 克隆一个参考实现到 `repro/<name>/`,按 [repro/repro-checklist.md](repro/repro-checklist.md) 逐级爬梯,不许跳级。
- 退出标准:完整训练的指标接近参考数字,剩余差距能解释(数据、schedule、硬件还是随机种子)。

## Phase 3 —— 实验与记录(指南 §6.4、§6.6、Table 7)

- 每次运行**先**从 [experiments/experiment-log-template.md](experiments/experiment-log-template.md) 建记录,再启动。
- 最小集合:1 个 baseline + 2 个 ablation,遵守 fair comparison 约束。候选 ablation:去掉 shortcut connection(指南自己的 ResNet 切入点)、深度 18 → 34、更换 LR schedule —— 每次只改一个因素。
- 退出标准:每份记录以条件化判断收尾,写清结果支持什么、不支持什么。

## Phase 4 —— 报告(指南 §4.4–4.5)

- 在 `report/` 写 LaTeX 报告,按 §4.4 的叙事线:问题为什么重要 → 已有方法差在哪 → 做了什么 → 实验是否支持。附 baseline / ablation 对照表。
- 退出标准:报告能编译;报告里每个数字都能追溯到一份实验记录。

## 进度

- 2026-07-07 —— 第二阶段脚手架完成(计划 + 模板);仓库语言切换为中文。任务选择待确认。
