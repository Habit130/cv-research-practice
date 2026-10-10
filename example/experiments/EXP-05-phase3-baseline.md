# EXP-05 —— Phase 3 baseline

> 五类信息依据理论指南 §6.4;对比纪律依据 §6.6、Table 7。本记录在启动前建立。

**类型**:baseline
**日期**:2026-10-09
**状态**:完整 200 epoch 已完成,最终权重和指标已核验。Phase 2 结果于提交 `3e68c0b` 发布后已解除队列闸门;EXP-06/07 均已完成 200 epoch。

## 1. 这次实验想验证什么

作者在当前编排聊天中决定恢复完整 200 epoch 预算。检验 shortcut 与 LR schedule 因素。EXP-04 提前终止且未固定 seed,不作为本组 baseline。

## 2. 代码版本与数据划分

- 上游:[kuangliu/pytorch-cifar](https://github.com/kuangliu/pytorch-cifar) @ `49b7aa97b0c12fe0d4054e670403a16b6b834ddd`。
- 工作目录:`/Users/habit/AgentWorkspace/Codex/cv-research-practice-experiments/pytorch-cifar`(仓库外);完整 patch 与 SHA256 见运行与交接。
- CIFAR-10 官方 50000 train / 10000 test,不另拆 validation。主指标为预先固定的第 200 epoch checkpoint 的 test accuracy,不按 best test accuracy 选权重。
- train:RandomCrop(32,padding=4)、RandomHorizontalFlip、ToTensor、Normalize(mean=(0.4914,0.4822,0.4465),std=(0.2023,0.1994,0.2010));test:ToTensor、同一 Normalize。

## 3. 关键 config 与训练参数

- ResNet18,无 pretrained weights;SGD LR 0.1,momentum 0.9,weight_decay 5e-4;batch 128/100;200 epoch;CosineAnnealingLR T_max=200,epoch 后 step。
- seed=20261009,固定 Python / NumPy / torch 及 DataLoader generator;num_workers=0。固定 seed 不代表 MPS 位级确定性;单 seed 不支持统计显著性判断。
- 本次唯一因素:无。
- 要求显式使用 MPS,不可用则停止。实测环境见 EXP-08。

## 4. 结果放在哪

- 日志:`/Users/habit/AgentWorkspace/Codex/cv-research-practice-experiments/pytorch-cifar/artifacts/baseline/run.log`。
- 权重:`/Users/habit/AgentWorkspace/Codex/cv-research-practice-experiments/pytorch-cifar/artifacts/baseline/last.pth`,每个完整 epoch 更新,第 200 epoch 完成后作为最终权重。
- 指标:`/Users/habit/AgentWorkspace/Codex/cv-research-practice-experiments/pytorch-cifar/artifacts/baseline/metrics.jsonl`,逐 epoch loss/accuracy、LR、耗时。
- 完整代码改动:`/Users/habit/AgentWorkspace/Codex/cv-research-practice-experiments/pytorch-cifar/artifacts/phase3.patch`;环境:`/Users/habit/AgentWorkspace/Codex/cv-research-practice-experiments/pytorch-cifar/artifacts/environment.json`。

## 5. 结论(支持 / 不支持什么判断)

在本记录的 Apple M5 / MPS、CIFAR-10 官方划分、seed=20261009 和完整 200 epoch 条件下,最终 epoch 的 test accuracy 为 **95.45%**,test loss 为 **0.1730995893**;train accuracy 为 99.998%,train loss 为 0.0016433455。训练和逐 epoch 评测累计耗时 18761.22599 秒(约 5.2115 小时,不含下载和 preflight)。本组作为 EXP-06/07 的固定 final-epoch 对照;已完成的 shortcut 对比见 [EXP-06](EXP-06-phase3-no-shortcut.md),LR schedule 对比与三组汇总表见 [EXP-07](EXP-07-phase3-constant-lr.md)。

Phase 2 复现参考核对另用整段训练的 **best test accuracy 95.60% @ epoch 189**,与上游自报 93.02% 相差 +2.58 percentage points。best 是从同一 test 集逐 epoch 观察得到的诊断指标,不是无偏模型选择评估;本次没有保留 epoch 189 权重,只保留最终权重。该值不得替代预登记的 final accuracy 作为三组主比较。

本次完成训练且达到参考实现自报准确率,但不能将数值差异归因于某一改动:上游参考运行的 seed、设备、精确环境未由自报表固定,当前运行增加固定 seed、MPS、日志及 final checkpoint 规则。best-to-best 对比不是全部条件严格匹配的 fair comparison,不支持优于参考实现或统计显著性的判断。绝对差超过原始 ≤1pp 门槛;这里只完成完整预算和指标/比较限制成文,不宣称满足该数值门槛。

核验:metrics.jsonl 恰有 epoch 1–200 共 200 条,completed.json 和 last.pth 的 epoch/variant/seed/final metrics 全部匹配。最终 checkpoint SHA256:`e253ddfa282227a0f424c773f71d516688fdd583adcd0bcf5ae2845c00aa73d2`;metrics.jsonl SHA256:`13c81a716d53d3f155b66a51e8cc2749092f0403756188ad6fe8bae8b3a4594d`。代码 runner hash 与运行前冻结值一致。权重仅加载到 CPU 进行元数据核验,没有执行 CPU 模型评测;训练设备为 MPS。

## 运行与交接

- 环境:Apple M5 / 24 GiB / macOS 27.2;Python 3.11.15、PyTorch 2.12.1、torchvision 0.27.1、NumPy 2.4.4;实际 MPS 检查通过。
- 完整 patch SHA256:`3014e72138c30d6c1a2b278ccf955449eeb1d9f1e8d3ee96cfbfb59967a05f0d`;runner SHA256:`229f308399479d2171c987f630791400aecef54468c8e019e0d3670f885390b9`。patch 含完整新增 runner 和模型改动,留在仓库外。
- 在第 2 节目录运行:`/opt/homebrew/Caskroom/miniforge/base/envs/cvpractice/bin/python -u phase3.py --variant baseline`。
- 队列入口 `run_phase3_queue.py`,顺序 baseline → no-shortcut → constant-lr,每组 200 epoch,任一失败停止。状态 `artifacts/queue-state.json`,PID `artifacts/queue-pid.txt`。
- **Phase 2 验收闸门历史**:首次 Codex review 后向队列父进程 PID 74276 发送 SIGSTOP,baseline 子进程继续完成全部训练;暂停期间两个 ablation 未被调度。随后核验 200 条指标、completed.json、最终 checkpoint hash,将最终结果、best-to-best 参考差距及限制写入本记录与 Phase 2 复现清单,于 2026-10-09 发布提交 `3e68c0b`。发布后向父进程发送 SIGCONT,`artifacts/phase2-gate.json` 标记为 `released_after_published_record`,并记录该提交。队列随后依次调度 no-shortcut 和 constant-lr;截至提交 `b26adbd`,前者已完成 200 epoch,后者已启动。以上放行未宣称满足原始 ≤1pp 数值门槛,不以未完成曲线值验收,不把 final accuracy 与 best 参考指标直接作差。
- 实际执行代码已发布到持久 fork:[Habit130/pytorch-cifar @ 2f737a307973c2ef3cb8deb4be22657f093dd4be](https://github.com/Habit130/pytorch-cifar/commit/2f737a307973c2ef3cb8deb4be22657f093dd4be)。该 commit 基于上游 `49b7aa9`,只增加 `phase3.py` 和 BasicBlock shortcut 开关,与本次运行文件 SHA256 一致;不含数据、权重或缓存。其任务分支为 `codex/cv-phase3-full-ablation`,通过 commit URL 可在其他环境获取训练代码。
- 数据恢复与检查见 [EXP-08](EXP-08-phase3-preflight.md)。每组启动前保存 `artifacts/<variant>/preflight.log`;完成须核对 200 条 epoch 指标和 `completed.json`。
- no-shortcut 保留投影参数但不执行相加,注册参数量不等于参与计算参数量。加载权重须根据 checkpoint variant 恢复 BasicBlock.use_shortcut=False。
