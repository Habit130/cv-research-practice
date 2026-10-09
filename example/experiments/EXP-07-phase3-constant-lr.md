# EXP-07 —— Phase 3 constant-lr

> 五类信息依据理论指南 §6.4;对比纪律依据 §6.6、Table 7。本记录在启动前建立。

**类型**:ablation
**日期**:2026-10-09
**状态**:完整 200 epoch 已完成,最终指标与权重已核验;三组队列已结束。

## 1. 这次实验想验证什么

作者在当前编排聊天中决定恢复完整 200 epoch 预算。检验 shortcut 与 LR schedule 因素。EXP-04 提前终止且未固定 seed,不作为本组 baseline。

## 2. 代码版本与数据划分

- 上游:[kuangliu/pytorch-cifar](https://github.com/kuangliu/pytorch-cifar) @ `49b7aa97b0c12fe0d4054e670403a16b6b834ddd`。
- 工作目录:`/Users/habit/AgentWorkspace/Codex/cv-research-practice-experiments/pytorch-cifar`(仓库外);完整 patch 与 SHA256 见运行与交接。
- CIFAR-10 官方 50000 train / 10000 test,不另拆 validation。主指标为预先固定的第 200 epoch checkpoint 的 test accuracy,不按 best test accuracy 选权重。
- train:RandomCrop(32,padding=4)、RandomHorizontalFlip、ToTensor、Normalize(mean=(0.4914,0.4822,0.4465),std=(0.2023,0.1994,0.2010));test:ToTensor、同一 Normalize。

## 3. 关键 config 与训练参数

- ResNet18,无 pretrained weights;SGD LR 0.1,momentum 0.9,weight_decay 5e-4;batch 128/100;200 epoch;本组固定 LR 0.1,不调用 scheduler.step。EXP-05 使用 cosine T_max=200、epoch 后 step。
- seed=20261009,固定 Python / NumPy / torch 及 DataLoader generator;num_workers=0。固定 seed 不代表 MPS 位级确定性;单 seed 不支持统计显著性判断。
- 本次唯一因素:cosine 改为固定 LR 0.1;其他条件与 EXP-05 相同。
- 要求显式使用 MPS,不可用则停止。实测环境见 EXP-08。

## 4. 结果放在哪

- 日志:`/Users/habit/AgentWorkspace/Codex/cv-research-practice-experiments/pytorch-cifar/artifacts/constant-lr/run.log`。
- 权重:`/Users/habit/AgentWorkspace/Codex/cv-research-practice-experiments/pytorch-cifar/artifacts/constant-lr/last.pth`,每个完整 epoch 更新,第 200 epoch 完成后作为最终权重。
- 指标:`/Users/habit/AgentWorkspace/Codex/cv-research-practice-experiments/pytorch-cifar/artifacts/constant-lr/metrics.jsonl`,逐 epoch loss/accuracy、LR、耗时。
- 完整代码改动:`/Users/habit/AgentWorkspace/Codex/cv-research-practice-experiments/pytorch-cifar/artifacts/phase3.patch`;环境:`/Users/habit/AgentWorkspace/Codex/cv-research-practice-experiments/pytorch-cifar/artifacts/environment.json`。

## 5. 结论(支持 / 不支持什么判断)

在与 [EXP-05](EXP-05-phase3-baseline.md) 相同的 Apple M5 / MPS、官方 CIFAR-10 划分、seed=20261009、ResNet18、SGD 和 200 epoch 预算下,只将 cosine schedule 改为固定 LR 0.1,最终 test accuracy 从 95.45% 变为 **86.88%**(-8.57 percentage points),test loss 从 0.1730995893 变为 **0.3807913750**。最终 train accuracy 为 89.674%,train loss 为 0.3008817615。

训练及逐 epoch 评测累计耗时 **18047.82443 秒**(约 **5.0133 小时**,不含下载和 preflight),比 baseline 少约 713.402 秒。三组顺序执行,未控制温度及后台负载,耗时差不能作为 schedule 的因果效率证据。结果只支持本设备、数据划分、单 seed、固定 200 epoch 下,固定 LR 0.1 的最终 accuracy 较低、loss 较高;不支持统计显著性、所有固定 LR 都较差或其他预算/设备下相同的判断。尚未验证多 seed、其他固定 LR、延长训练预算和 CUDA。

核验:metrics.jsonl 含连续 epoch 1–200,各轮 LR 均为 0.1,所有 loss/accuracy 有限,每轮样本数 50000 train / 10000 test;completed.json 与 last.pth 的 epoch=200、variant=constant-lr、seed=20261009、final metrics 全部一致。最终 checkpoint SHA256:`ef6ef94807e9441ddf21d0b4dcad014a0184d28e609bea9208dd7002275c340f`;metrics.jsonl SHA256:`3c4eb0102648261a87ac663860fc926d344680e39d44f48ded9e56023a76fc24`。仅在 CPU 加载权重元数据,未执行 CPU 模型评测;训练实际使用 MPS。数据归档与 train/test/meta MD5、runner 与 patch SHA256 在三组结束后重新核对通过。

### 完整 200 epoch 对照表

每组只与 baseline 作单因素比较;两组 ablation 之间同时存在 shortcut 与 schedule 两项差异,不作单因素归因。表中均为固定第 200 epoch 的真实指标,不使用 best test accuracy。

| 记录 | 唯一改动(相对 baseline) | final test accuracy | 差值(pp) | final test loss | 训练及评测耗时(h) |
| --- | --- | --- | --- | --- | --- |
| [EXP-05](EXP-05-phase3-baseline.md) | 无 | 95.45% | 0 | 0.1730995893 | 5.2115 |
| [EXP-06](EXP-06-phase3-no-shortcut.md) | 关闭 shortcut 相加 | 95.11% | -0.34 | 0.2076407678 | 5.1725 |
| 本记录 | cosine → 固定 LR 0.1 | 86.88% | -8.57 | 0.3807913750 | 5.0133 |

三组训练及逐 epoch 评测累计 **55430.08841 秒(约 15.3972 小时)**,不含数据准备、preflight、门禁等待和审查。结论为据日志整理的条件化草稿,仍须作者本人终审;不能据单 seed 断言统计显著性。

## 运行与交接

环境、完整 patch/runner hash、数据及队列路径见 [EXP-05](EXP-05-phase3-baseline.md)、[EXP-08](EXP-08-phase3-preflight.md)。命令:`/opt/homebrew/Caskroom/miniforge/base/envs/cvpractice/bin/python -u phase3.py --variant constant-lr`。
