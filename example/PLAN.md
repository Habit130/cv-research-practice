# 验证跑计划 —— ResNet-18 + CIFAR-10(example)

依据:指南 §7(结语)。围绕一个小型 CV 任务,把五类能力串成闭环:
论文选读 → 代码复现 → 实验记录 → 对比分析 → 报告写作。

本次是作者本人的验证跑,目的是验证使用文档(README)的每一步可被陌生人照做。

## 任务决定

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
- 每篇必读论文用 [../templates/paper-notes-template.md](../templates/paper-notes-template.md) 做一份笔记,三轮阅读,存入 `papers/`。
- 退出标准:完成 1–2 份必读笔记;不翻 PDF 也能说出种子论文的五要素(数据 / 任务 / 模型 / loss / 指标)。

## Phase 2 —— 代码复现(指南 §6.3、§3.5)

- 克隆一个参考实现到 `repro/<name>/`,按 [../templates/repro-checklist.md](../templates/repro-checklist.md) 逐级爬梯,不许跳级。
- 退出标准:完整训练的指标接近参考数字,剩余差距能解释(数据、schedule、硬件还是随机种子)。

## Phase 3 —— 实验与记录(指南 §6.4、§6.6、Table 7)

- 每次运行**先**从 [../templates/experiment-log-template.md](../templates/experiment-log-template.md) 建记录,存入 `experiments/`,再启动。
- 最小集合:1 个 baseline + 2 个 ablation,遵守 fair comparison 约束。候选 ablation:去掉 shortcut connection(指南自己的 ResNet 切入点)、深度 18 → 34、更换 LR schedule —— 每次只改一个因素。
- 退出标准:每份记录以条件化判断收尾,写清结果支持什么、不支持什么。

## Phase 4 —— 报告(指南 §4.4–4.5)

- 在 `report/` 写 LaTeX 报告,按 §4.4 的叙事线:问题为什么重要 → 已有方法差在哪 → 做了什么 → 实验是否支持。附 baseline / ablation 对照表。
- 退出标准:报告能编译;报告里每个数字都能追溯到一份实验记录。

## 进度

- 2026-07-07 —— 第二阶段脚手架完成(计划 + 模板);仓库语言切换为中文。任务选择待确认。
- 2026-07-07 —— 三分结构落位(#2):`stage2/` 拆分为 `templates/`(任务无关模板)与 `example/`(本文件,作者验证跑);任务无关内容暂存于本文件顶部注释区,待 #3 上收进 README。
- 2026-07-07 —— Phase 1 验证跑(#7):完成 ResNet 种子论文三轮笔记([papers/he2016-deep-residual-learning.md](papers/he2016-deep-residual-learning.md))与引文检索/分级简记([papers/citation-triage.md](papers/citation-triage.md))。笔记中"相关性 / 贡献 / 局限"三处判断按 issue #7 的作者参与点约定留空待作者本人填写,未替作者代笔。
- 2026-07-07 —— Phase 0 验证跑完成(#6):conda 环境 `cvpractice`(Python 3.11.15 + torch 2.12.1 + torchvision 0.27.1)在 Apple M5 / macOS 27.0 上装成,MPS 可用;MNIST 冒烟(极小 CNN,2 epoch)跑通,test_acc 0.9807 → 0.9873。记录见 [experiments/EXP-00-phase0-env-mnist-smoke.md](experiments/EXP-00-phase0-env-mnist-smoke.md)。
- 2026-07-08 —— Phase 2 复现爬梯进行中(#8):选定 [kuangliu/pytorch-cifar](https://github.com/kuangliu/pytorch-cifar)(commit `49b7aa9`)为参考实现,复用 `cvpractice` 环境。第 0–2 级(锚点定位、环境、前向 demo)完成,记录见 [experiments/EXP-01-phase2-repro-env-setup.md](experiments/EXP-01-phase2-repro-env-setup.md);对上游 `main.py`/`utils.py` 做了最小改动(补 MPS 设备分支、加 `--epochs`/`--tag` 参数、修后台无 tty 时的 `stty` 探测崩溃)。第 3 级起需要真实 CIFAR-10 数据集,`torchvision` 自动下载源(`www.cs.toronto.edu`)带宽极低(实测约 1MB/min,~170MB 全量预计数小时),已转后台下载,不阻塞其他工作;checklist 见 [repro/repro-checklist.md](repro/repro-checklist.md)。
- 2026-07-09 —— Phase 2 复现爬梯第 3–5 级(#8):数据集下载完成(MD5 校验通过)。第 3 级实跑时发现并修复一处 macOS 专属 bug(`DataLoader num_workers=2` 在 spawn 起动方式下递归死锁,改为非 CUDA 设备 `num_workers=0`);修复后 5 epoch 短训练评测(EXP-02)、20 epoch 小规模训练(EXP-03)均通过,test acc 分别到 65.08%/84.92%,loss 单调下降、checkpoint/日志落位正常。第 5 级完整 200 epoch 训练已建好实验记录([EXP-04](experiments/EXP-04-phase2-repro-full-training.md))并在后台启动,预计耗时 7–10 小时(单 epoch 实测约 2–3 分钟);完成后回填最终指标与参考 93.02% 的差距分析,再收尾 checklist、提交、关闭 issue #8。
