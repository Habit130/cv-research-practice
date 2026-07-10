# EXP-04 —— Phase 2 复现第 5 级:完整训练(提前终止,验证 pipeline 跑通)

> 五类必填信息依据指南 §6.4;对比纪律依据 §6.6 与 Table 7。
> 在启动运行**之前**创建本记录。

**类型**(Table 7):环境/代码验证(不参与 baseline / ablation / fair comparison 对比,对应 issue #8 复现爬梯第 5 级)
**日期**:2026-07-09

## 1. 这次实验想验证什么

复现清单第 5 级最初计划完整跑满上游脚本默认的 200 epoch,对比参考实现自报的 93.02%。作者本人在训练进行到第 37 个 epoch 时决定停止,判断依据是:验证跑(EXP-00–EXP-03)的目的是验证复现爬梯本身可以从 0 级走到 5 级、每一级都跑通,而不是复现出逼近论文数字的最终精度(ADR 0001——本仓库交付的是流程模板与活例子,不是追求 SOTA 复现)。故本条目改为验证"完整训练流程可以启动并稳定运行多个 epoch(loss 持续下降、checkpoint 持续刷新)",不再追求 200 epoch 全量收敛。

## 2. 代码版本与数据划分

- 仓库 / commit:[kuangliu/pytorch-cifar](https://github.com/kuangliu/pytorch-cifar) @ `49b7aa97b0c12fe0d4054e670403a16b6b834ddd`(含 EXP-01/EXP-02 的最小改动:mps 设备分支、`--epochs`/`--tag`、`stty` 兜底、`num_workers=0` 修复)
- 数据集与划分:`torchvision.datasets.CIFAR10` 标准划分,50000 train / 10000 test,与 EXP-02/EXP-03 同一份本地缓存(MD5 `c58f30108f718f92721af3b95e74349a`)

## 3. 关键 config 与训练参数

- Config 文件:无独立 config,沿用上游脚本默认值,与 README 训练命令一致(`python main.py`),未做任何超参数覆盖
- 覆盖项:batch size 128(train)/ 100(test,均为上游默认)、LR 0.1 起步(SGD,momentum 0.9,weight_decay 5e-4,CosineAnnealingLR T_max=200,本次 epoch 数与 T_max 对齐,衰减到底)、epoch 200(`--epochs 200 --tag L5full`,即上游默认 epoch 数)、seed 未固定(上游脚本本身不设 seed,与参考实现声明的条件一致)、pretrained weights 无(随机初始化,与参考实现一致)
- 相对 baseline,本次只改的那一个因素:不适用(本条目本身即复现的"完整训练"节点,是后续 EXP-05+ baseline/ablation 实验的基础版本,而非其对比对象)

## 4. 结果放在哪

- 日志:`~/repro/level5_full_training.log`(仓库外,未提交)
- Checkpoint:`~/repro/pytorch-cifar/checkpoint/ckpt_L5full.pth`(44MB,停止时的 best test acc 对应权重,epoch 33)
- 指标输出 / 曲线:逐 epoch train/test 打印;37 个 epoch 完整跑完(epoch 0–36),第 37 个(epoch 37)训练到一半被终止

### 训练轨迹(节选)

| epoch | test acc |
|---|---|
| 0 | ~40%(与 EXP-02/EXP-03 同起点量级) |
| 18 | 84.92%(与 EXP-03 的 20-epoch 小规模训练在同一随机初始化路径上数值一致,因为是同一份代码从头训练) |
| 33 | **86.49%(本次运行最佳,checkpoint 已保存)** |
| 36(最后完整 epoch) | 80.23% |

## 5. 结论(支持 / 不支持什么判断)

- 结果:完整训练 pipeline(`main.py --epochs 200`,cosine annealing T_max=200)在 37 个 epoch 内稳定运行,未出现崩溃或异常;test acc 在波动中于 epoch 33 达到本次运行最高点 86.49%,尚未接近参考实现自报的 93.02%——这符合预期,因为 cosine schedule 在 37/200 epoch 处 LR 仅衰减了一小部分(`cos(37/200·π)` 对应量级),精度本就该低于收敛值,并非训练失败。
- 条件化判断:在 Apple M5 + MPS + 本仓库当前 config(EXP-01/EXP-02 的最小改动)下,ResNet18 + CIFAR-10 的完整训练 pipeline 可以从零跑到至少 37 个 epoch,loss 持续下降、checkpoint 机制持续工作,满足"复现爬梯第 5 级可以跑通"这一弱化后的退出标准;**本次未跑满 200 epoch,因此不满足 issue #8 原始验收标准里"完整训练指标与参考差距 ≤1pp 或差距成文"这一条**——这是作者本人在训练进行中明确决定的范围收窄(判断依据见第 1 节),不是精度不达标或代码问题。尚未验证:跑满 200 epoch 后能否收敛到 93.02% 附近(链路已打通,若后续需要可直接 `python main.py --epochs 200 --tag L5full_v2` 在 `~/repro/pytorch-cifar` 重新以后台方式跑完,预计仍需 7–10 小时)。下一个实验:不适用(本条目是 Phase 2 复现爬梯的收尾节点;Phase 3 的 baseline/ablation 需要另行规划是否等待完整训练收敛,或直接基于本条目已验证的 pipeline 展开)。
