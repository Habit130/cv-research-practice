# EXP-04 —— Phase 2 复现第 5 级:完整训练(200 epoch)

> 五类必填信息依据指南 §6.4;对比纪律依据 §6.6 与 Table 7。
> 在启动运行**之前**创建本记录。

**类型**(Table 7):环境/代码验证(不参与 baseline / ablation / fair comparison 对比,对应 issue #8 复现爬梯第 5 级——最终交付于参考实现自报指标的对比)
**日期**:2026-07-09(启动)

## 1. 这次实验想验证什么

复现清单第 5 级:完整跑满上游脚本默认的 200 epoch(cosine annealing 全程衰减到底),ResNet18 在 CIFAR-10 上的最终 test accuracy 是否接近参考实现自报的 93.02%;若有差距,解释来源(数据、schedule、硬件还是随机种子)。

## 2. 代码版本与数据划分

- 仓库 / commit:[kuangliu/pytorch-cifar](https://github.com/kuangliu/pytorch-cifar) @ `49b7aa97b0c12fe0d4054e670403a16b6b834ddd`(含 EXP-01/EXP-02 的最小改动:mps 设备分支、`--epochs`/`--tag`、`stty` 兜底、`num_workers=0` 修复)
- 数据集与划分:`torchvision.datasets.CIFAR10` 标准划分,50000 train / 10000 test,与 EXP-02/EXP-03 同一份本地缓存(MD5 `c58f30108f718f92721af3b95e74349a`)

## 3. 关键 config 与训练参数

- Config 文件:无独立 config,沿用上游脚本默认值,与 README 训练命令一致(`python main.py`),未做任何超参数覆盖
- 覆盖项:batch size 128(train)/ 100(test,均为上游默认)、LR 0.1 起步(SGD,momentum 0.9,weight_decay 5e-4,CosineAnnealingLR T_max=200,本次 epoch 数与 T_max 对齐,衰减到底)、epoch 200(`--epochs 200 --tag L5full`,即上游默认 epoch 数)、seed 未固定(上游脚本本身不设 seed,与参考实现声明的条件一致)、pretrained weights 无(随机初始化,与参考实现一致)
- 相对 baseline,本次只改的那一个因素:不适用(本条目本身即复现的"完整训练"节点,是后续 EXP-05+ baseline/ablation 实验的基础版本,而非其对比对象)

## 4. 结果放在哪

- 日志:`~/repro/level5_full_training.log`(仓库外,未提交,后台运行中)
- Checkpoint:`~/repro/pytorch-cifar/checkpoint/ckpt_L5full.pth`(训练中持续更新为 best test acc 对应权重)
- 指标输出 / 曲线:待补(训练完成后回填逐 epoch 摘要)

**训练已提交后台运行,预计耗时 7–10 小时(依据 EXP-02/EXP-03 实测单 epoch 约 2–3 分钟外推)。以下第 5 节留待训练完成后填写。**

## 5. 结论(支持 / 不支持什么判断)

- 待补:最终 test acc ___ vs 参考(该仓库自报)93.02%,差距 ___,解释 ___
