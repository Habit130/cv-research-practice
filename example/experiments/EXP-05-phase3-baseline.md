# EXP-05 —— Phase 3 baseline

> 五类信息依据理论指南 §6.4;对比纪律依据 §6.6、Table 7。本记录在启动前建立。

**类型**:baseline
**日期**:2026-10-09
**状态**:MPS 训练已启动,尚无完整 200 epoch 指标。

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

运行中,尚无完整结果。本组是 EXP-06/07 的对照;只支持本设备、划分、seed 和完整预算下的判断。不得用历史或参考数字代填本次指标。

## 运行与交接

- 环境:Apple M5 / 24 GiB / macOS 27.2;Python 3.11.15、PyTorch 2.12.1、torchvision 0.27.1、NumPy 2.4.4;实际 MPS 检查通过。
- 完整 patch SHA256:`3014e72138c30d6c1a2b278ccf955449eeb1d9f1e8d3ee96cfbfb59967a05f0d`;runner SHA256:`229f308399479d2171c987f630791400aecef54468c8e019e0d3670f885390b9`。patch 含完整新增 runner 和模型改动,留在仓库外。
- 在第 2 节目录运行:`/opt/homebrew/Caskroom/miniforge/base/envs/cvpractice/bin/python -u phase3.py --variant baseline`。
- 队列入口 `run_phase3_queue.py`,顺序 baseline → no-shortcut → constant-lr,每组 200 epoch,任一失败停止。状态 `artifacts/queue-state.json`,PID `artifacts/queue-pid.txt`。
- 数据恢复与检查见 [EXP-08](EXP-08-phase3-preflight.md)。每组启动前保存 `artifacts/<variant>/preflight.log`;完成须核对 200 条 epoch 指标和 `completed.json`。
- no-shortcut 保留投影参数但不执行相加,注册参数量不等于参与计算参数量。加载权重须根据 checkpoint variant 恢复 BasicBlock.use_shortcut=False。
