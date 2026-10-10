# EXP-06 —— Phase 3 no-shortcut

> 五类信息依据理论指南 §6.4;对比纪律依据 §6.6、Table 7。本记录在启动前建立。

**类型**:ablation
**日期**:2026-10-09
**状态**:完整 200 epoch 已完成,最终指标与权重已核验。

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
- 本次唯一因素:关闭 BasicBlock shortcut 相加;其他条件与 EXP-05 相同。
- 要求显式使用 MPS,不可用则停止。实测环境见 EXP-08。

## 4. 结果放在哪

- 日志:`/Users/habit/AgentWorkspace/Codex/cv-research-practice-experiments/pytorch-cifar/artifacts/no-shortcut/run.log`。
- 权重:`/Users/habit/AgentWorkspace/Codex/cv-research-practice-experiments/pytorch-cifar/artifacts/no-shortcut/last.pth`,每个完整 epoch 更新,第 200 epoch 完成后作为最终权重。
- 指标:`/Users/habit/AgentWorkspace/Codex/cv-research-practice-experiments/pytorch-cifar/artifacts/no-shortcut/metrics.jsonl`,逐 epoch loss/accuracy、LR、耗时。
- 完整代码改动:`/Users/habit/AgentWorkspace/Codex/cv-research-practice-experiments/pytorch-cifar/artifacts/phase3.patch`;环境:`/Users/habit/AgentWorkspace/Codex/cv-research-practice-experiments/pytorch-cifar/artifacts/environment.json`。

## 5. 结论(支持 / 不支持什么判断)

在与 [EXP-05](EXP-05-phase3-baseline.md) 相同的 Apple M5 / MPS、官方 CIFAR-10 划分、seed=20261009、SGD 与 200 epoch cosine 预算下,只关闭 BasicBlock shortcut 相加,最终 test accuracy 从 95.45% 变为 **95.11%**(-0.34 percentage points),test loss 从 0.1730995893 变为 **0.2076407678**。本组最终 train accuracy 为 99.998%,train loss 为 0.0016996589。

训练及逐 epoch 评测累计耗时 **18621.03799 秒**(约 **5.1725 小时**,不含下载和 preflight),baseline 为 18761.22599 秒;本次耗时减少约 140.188 秒。投影分支保留参数但不执行,注册参数量相同,参与计算的参数与算子减少;两组按顺序运行,未控制温度及后台负载,不能将小幅耗时差直接归因于结构变化。

结果只支持本设备、划分、单 seed 和固定预算下的观察:关闭 shortcut 后,本次最终 test accuracy 略低、test loss 较高。它不支持该差异有统计显著性、所有任务都会退化或 shortcut 是本次差异唯一确定原因的判断;尚未验证多 seed、其他深度及设备。下一组为 EXP-07 固定 LR,主指标仍采用最终 epoch。

核验:metrics.jsonl 含连续 epoch 1–200,所有 loss/accuracy 有限,每轮样本数 50000 train / 10000 test;completed.json 与 last.pth 的 epoch=200、variant=no-shortcut、seed=20261009、final metrics 全部一致。最终 checkpoint SHA256:`20b8af9216e1b04302796e66666655cf176d6a216eaa742048de054159604bcf`;metrics.jsonl SHA256:`d512723452ecb086601f8227de766220252cb0fe48d3ea5dc82ba080f250319e`。仅在 CPU 加载权重元数据,未执行 CPU 模型评测;训练实际使用 MPS。

## 运行与交接

环境、完整 patch/runner hash、数据及队列路径见 [EXP-05](EXP-05-phase3-baseline.md)、[EXP-08](EXP-08-phase3-preflight.md)。命令:`/opt/homebrew/Caskroom/miniforge/base/envs/cvpractice/bin/python -u phase3.py --variant no-shortcut`。初始化与 baseline 相同,随后关闭所有 BasicBlock 的 shortcut 相加;投影分支保留但不执行。加载权重必须根据 variant 恢复开关。
