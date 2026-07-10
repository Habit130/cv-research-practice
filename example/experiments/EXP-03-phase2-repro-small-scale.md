# EXP-03 —— Phase 2 复现第 4 级:小规模训练(20 epoch)

> 五类必填信息依据指南 §6.4;对比纪律依据 §6.6 与 Table 7。
> 在启动运行**之前**创建本记录。

**类型**(Table 7):环境/代码验证(不参与 baseline / ablation / fair comparison 对比,对应 issue #8 复现爬梯第 4 级)
**日期**:2026-07-09

## 1. 这次实验想验证什么

复现清单第 4 级:比 EXP-02 的 5 epoch 更长的训练(20 epoch)下,loss 是否持续下降、checkpoint 和日志是否稳定落在预期位置;同时观察 EXP-02 中出现的 test acc 波动是否随训练拉长而收敛。

## 2. 代码版本与数据划分

- 仓库 / commit:[kuangliu/pytorch-cifar](https://github.com/kuangliu/pytorch-cifar) @ `49b7aa97b0c12fe0d4054e670403a16b6b834ddd`(含 EXP-01/EXP-02 的最小改动:mps 设备分支、`--epochs`/`--tag`、`stty` 兜底、`num_workers` 修复)
- 数据集与划分:同 EXP-02,`torchvision.datasets.CIFAR10` 标准划分,50000 train / 10000 test

## 3. 关键 config 与训练参数

- Config 文件:无独立 config,沿用脚本内硬编码超参数
- 覆盖项:batch size 128/100、LR 0.1(SGD,momentum 0.9,weight_decay 5e-4,CosineAnnealingLR T_max=200——20/200 epoch 内仅小幅衰减)、epoch 20(`--epochs 20 --tag L4small`)、seed 未固定、pretrained weights 无
- 相对 baseline,本次只改的那一个因素:不适用(复现爬梯验证节点,非对比实验)

## 4. 结果放在哪

- 日志:`~/repro/level4_small_scale.log`(仓库外,未提交)
- Checkpoint:`~/repro/pytorch-cifar/checkpoint/ckpt_L4small.pth`(44MB,best test acc 84.92% @ epoch 18)
- 指标输出 / 曲线:逐 epoch train/test 打印,摘录如下

### 逐 epoch 结果

| epoch | train loss | train acc | test loss | test acc |
|---|---|---|---|---|
| 0 | 1.887 | 31.99% | 1.570 | 40.27% |
| 3 | 0.974 | 65.35% | 0.927 | 67.71% |
| 6 | 0.619 | 78.40% | 0.729 | 75.93% |
| 9 | 0.516 | 82.26% | 0.713 | 76.48% |
| 12 | 0.462 | 84.15% | 0.772 | 75.42% |
| 15 | 0.419 | 85.55% | 0.588 | 80.81% |
| 18 | 0.400 | 86.25% | **0.450** | **84.92%(best)** |
| 19 | 0.389 | 86.70% | 0.612 | 80.25% |

(完整 20 行见 `~/repro/level4_small_scale.log`)

## 5. 结论(支持 / 不支持什么判断)

- 结果:20 个 epoch 内 train loss 从 1.887 单调降到 0.389,train acc 从 31.99% 升到 86.70%,全程未出现异常(无 NaN/loss 爆炸);test acc 从 40.27% 升到最高 84.92%(epoch 18),但逐 epoch 存在 ±5–10pp 的波动(如 epoch 12 从 80.86% 掉到 75.42%,epoch 17 从 82.55% 掉到 73.71%)。checkpoint 只在刷新 best_acc 时落盘(共 8 次:epoch 0/1/2/3/5/6/9/10/11/13/16/18,按 acc 是否超过历史最佳判断),日志落在预期路径。
- 条件化判断:在 Apple M5 + MPS + 当前 config(固定 LR 0.1、cosine T_max=200)下,20 epoch 内 test acc 的波动**未收敛**,与 EXP-02 的 5 epoch 观察一致——原因是 T_max=200 的 cosine schedule 在前 20 epoch 只衰减了 `cos(20/200 * π)` 对应的一小段(LR 仍接近 0.1 量级),配合无 warmup 是这类"固定较高 LR"训练在早期常见的行为,不是代码 bug(EXP-02 已排除的 `num_workers` 死锁问题在本次未复现)。此结论只对本仓库这套 config(无 warmup、LR 0.1 起步)成立,未验证降低起始 LR 或加 warmup 后波动是否会减小。尚未验证:200 epoch 全量训练下,cosine schedule 后段(LR 显著衰减)能否让 test acc 收敛到 EXP-04 的参考区间(该仓库自报 93.02%)附近。下一个实验:第 5 级——完整 200 epoch 训练(EXP-04),预计耗时 7–10 小时,后台运行。
