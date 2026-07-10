# EXP-02 —— Phase 2 复现第 3 级:短训练评测(替代官方 checkpoint)

> 五类必填信息依据指南 §6.4;对比纪律依据 §6.6 与 Table 7。
> 在启动运行**之前**创建本记录。

**类型**(Table 7):环境/代码验证(不参与 baseline / ablation / fair comparison 对比,对应 issue #8 复现爬梯第 3 级)
**日期**:2026-07-09

## 1. 这次实验想验证什么

上游 `kuangliu/pytorch-cifar` 不分发任何预训练 checkpoint(README 的 accuracy 表格是作者自报的训练结果,无下载链接;`--resume` 只读本机训练产生的本地文件),故按 issue #8 规格("官方 checkpoint 或短训练评测")改走**短训练评测**:用 5 个 epoch 验证——真实 CIFAR-10 数据能正常加载、ResNet18 在 MPS 上训练时 loss 下降、checkpoint 落盘机制工作正常,为第 4/5 级探明单 epoch 耗时。

## 2. 代码版本与数据划分

- 仓库 / commit:[kuangliu/pytorch-cifar](https://github.com/kuangliu/pytorch-cifar) @ `49b7aa97b0c12fe0d4054e670403a16b6b834ddd`,克隆到仓库外 `~/repro/pytorch-cifar`
- 数据集与划分:`torchvision.datasets.CIFAR10` 标准划分,50000 train / 10000 test,下载到 `~/repro/pytorch-cifar/data`(仓库外,ADR 0002)。MD5 校验:`c58f30108f718f92721af3b95e74349a`(与 torchvision 内置 `tgz_md5` 一致)

### 本级发现并修复的一处代码 bug(§3.5)

首次运行(5 epoch)在 `Epoch: 0` 处挂起,进程 15 小时几乎不耗 CPU。排查发现:上游 `main.py` 的 `DataLoader(num_workers=2)` 在 macOS 上会用 `multiprocessing` 的 `spawn` 起动方式(Linux 默认 `fork`),而 `main.py` 全部代码在模块顶层、没有 `if __name__ == '__main__':` 保护;`spawn` 起 worker 时会重新 import/执行整个 `main.py`,导致子进程里递归再次触发 DataLoader 起 worker,被 Python 检测为"在 bootstrapping 阶段前又起新进程"而报 `RuntimeError`,外层被这个异常连环卡死。修复:非 CUDA 设备把 `num_workers` 降到 0(不起子进程,规避这条路径),CUDA 保持 2 不变:

```diff
-trainloader = torch.utils.data.DataLoader(
-    trainset, batch_size=128, shuffle=True, num_workers=2)
+_num_workers = 2 if device == 'cuda' else 0
+trainloader = torch.utils.data.DataLoader(
+    trainset, batch_size=128, shuffle=True, num_workers=_num_workers)
 ...
-testloader = torch.utils.data.DataLoader(
-    testset, batch_size=100, shuffle=False, num_workers=2)
+testloader = torch.utils.data.DataLoader(
+    testset, batch_size=100, shuffle=False, num_workers=_num_workers)
```

修复后重跑,训练正常推进(loss 单调下降,见下)。

## 3. 关键 config 与训练参数

- Config 文件:无独立 config;沿用上游脚本内硬编码超参数
- 覆盖项:batch size 128(train)/ 100(test)、LR 0.1(SGD,momentum 0.9,weight_decay 5e-4,CosineAnnealingLR T_max=200,5 个 epoch 内几乎不衰减)、epoch 5(`--epochs 5`)、seed 未固定(短评测不要求逐位复现)、pretrained weights 无(随机初始化)
- 相对 baseline,本次只改的那一个因素:不适用(本条目非 baseline/ablation 对比,是复现爬梯的验证节点)

## 4. 结果放在哪

- 日志:`~/repro/level3_short_eval.log`(仓库外,未提交;关键行摘录见下)
- Checkpoint:`~/repro/pytorch-cifar/checkpoint/ckpt_L3short.pth`(44MB,best test acc 65.08% @ epoch 3)
- 指标输出 / 曲线:无曲线,逐 epoch train/test 打印

### 单 epoch 耗时(用于规划第 4/5 级)

train 阶段每 epoch 约 1m52s–2m53s(391 steps/epoch,batch 128),test 阶段每 epoch 约 5–7s(100 steps,batch 100);全 epoch 合计约 2–3 分钟。据此估算第 5 级完整 200 epoch 训练约需 7–10 小时。

### 逐 epoch 结果

| epoch | train loss | train acc | test loss | test acc | checkpoint |
|---|---|---|---|---|---|
| 0 | 1.907 | 30.37% | 1.603 | 39.79% | 保存(best) |
| 1 | 1.448 | 46.52% | 1.341 | 51.97% | 保存(best) |
| 2 | 1.174 | 57.67% | 1.076 | 61.43% | 保存(best) |
| 3 | 0.987 | 64.81% | 1.020 | 65.08% | 保存(best) |
| 4 | 0.831 | 70.67% | 1.314 | 58.70% | 未保存(低于 best) |

## 5. 结论(支持 / 不支持什么判断)

- 结果:修复 `num_workers` 的 macOS spawn 问题后,ResNet18 在真实 CIFAR-10 数据上 5 个 epoch 内 train loss 从 1.907 单调降到 0.831,test accuracy 从 39.79% 升到最高 65.08%(epoch 3);checkpoint 落盘机制(`best_acc` 触发保存)按预期工作。epoch 4 test acc 回落到 58.70%——固定 LR 0.1(cosine schedule 在前 5/200 epoch 内几乎不衰减)下的正常训练波动,不是 bug。
- 条件化判断:在 Apple M5 + MPS + `cvpractice` 环境、`num_workers=0` 的这一组合下,复现清单第 3 级判据(上游无官方 checkpoint,短训练评测替代)成立——loss 下降、checkpoint 机制工作、真实数据管线打通;此结论**依赖上一步的 `num_workers` 修复**,未验证修复前的原始上游代码在其他 macOS + PyTorch 版本组合下是否同样会卡死(该 bug 与 Python multiprocessing 的 spawn/fork 差异有关,理论上所有 macOS + 默认 CPython 组合都会触发,但未逐一验证)。尚未验证:更长训练下 5-epoch 内观察到的 test acc 波动是否会随 epoch 增多而收敛。下一个实验:第 4 级——小规模训练(EXP-03),验证更长训练下 loss/acc 趋势更稳定,同时进一步确认 checkpoint/日志落位。
