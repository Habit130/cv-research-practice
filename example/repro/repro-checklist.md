# 复现清单 —— kuangliu/pytorch-cifar

> 阶梯依据指南 §6.3;环境排查依据 Table 6;代码验证依据 §3.5。
> 不许跳级。每一级的结果先写进实验记录,再进入下一级。

- 上游仓库 / commit:[kuangliu/pytorch-cifar](https://github.com/kuangliu/pytorch-cifar) @ `49b7aa97b0c12fe0d4054e670403a16b6b834ddd`(默认分支 `master` 最新提交,2021-02-25;克隆到仓库外 `~/repro/pytorch-cifar`,ADR 0002)
- 论文及其宣称的指标:选型对象是 He et al. 2016(ResNet)的一个社区复现仓库,不是论文官方代码。论文本身在 CIFAR-10 上报告的是 ResNet-20/32/44/56/110(§4.2 命名法,plain/residual 对照),并未单独给出"ResNet-18"在 CIFAR-10 上的数字;本仓库的 `models/resnet.py` 是社区常见的 ImageNet 风格 ResNet-18(3×3 stem、无 maxpool,适配 32×32 输入)。故第 3–5 级的参考基准取**该复现仓库 README 自报数字**(ResNet18 93.02%),而非论文原始数字——两者不是同一件事,后续对比只针对该仓库自身声明。

## 选型理由

候选即 issue 建议的 `kuangliu/pytorch-cifar`:6.4k+ stars,MIT 许可,`models/` 覆盖 ResNet18 等十余种架构且各自报告 CIFAR-10 accuracy(README 表格)。活跃度核实:默认分支最后一次代码提交在 2021-02-25(已停止维护,但未 archive,106 个 open issues 多为讨论而非阻断性 bug);仍是 GitHub 上被广泛引用的 CIFAR-10 baseline 复现,accuracy 表格数字与社区认知一致。判断:虽不活跃,但代码本身简单(单文件 `main.py` + `models/`)、无需运行不通的依赖,足以支撑本次复现爬梯,未更换候选。

## 第 0 级 —— 先读后跑

- [x] README 从头到尾读完;记下支持的任务、需要的数据和 checkpoint、期望的数据目录结构 —— 支持任务:CIFAR-10 图像分类,`models/` 内 15 种架构任选;数据:`torchvision.datasets.CIFAR10` 自动下载到 `./data`;无预训练 checkpoint 分发,`main.py --resume` 只用于恢复本机训练产生的 `./checkpoint/ckpt.pth`
- [x] 定位四个锚点:训练入口 `main.py`(单文件,写死 200 epoch 训练循环 + cosine annealing);config 无独立文件,超参数为脚本内硬编码 + 少量 CLI 参数(`--lr`、`--resume`);数据目录 `./data`;模型目录 `models/`(`models/resnet.py` 含 `ResNet18`)

## 第 1 级 —— 环境

- [x] 复用 EXP-00(issue #6)已装好的 conda 环境 `cvpractice`(Python 3.11.15、torch 2.12.1、torchvision 0.27.1);`from models import ResNet18` 可正常 import,参数量 11,173,962;`torch.backends.mps.is_available() == True`
- 出问题按 Table 6 顺序排查:本级一次通过,未触发排错分支

## 第 2 级 —— demo / 少量样例

- [x] 上游未附带独立 demo 脚本;用一个 batch=4 的随机 tensor(`(4,3,32,32)`,匹配 CIFAR-10 输入)在 MPS 设备上跑通 `ResNet18` 前向,输出 shape `(4,10)`,与 10 类分类任务的期望一致

## 第 3 级 —— 用官方 checkpoint 评测

- [x] 上游不提供任何预训练权重下载(README 的 accuracy 表格是作者自报的训练结果,不附 checkpoint 文件;`main.py --resume` 只读本机训练产生的本地文件)——**无官方 checkpoint 可评测**,按 issue #8 规格("官方 checkpoint 或短训练评测")改走**短训练评测**替代:5 epoch,test acc 39.79%→65.08%(epoch 3 最佳),loss 单调下降,checkpoint 落盘机制验证通过。过程中发现并修复一处 macOS 专属 bug(`DataLoader num_workers=2` 触发 spawn 递归死锁),见 [EXP-02](../experiments/EXP-02-phase2-repro-short-eval.md)

## 第 4 级 —— 小规模训练

- [x] 20 epoch;train loss 单调降(1.887→0.389),test acc 40.27%→84.92%(best @ epoch 18);checkpoint/日志落在预期位置。test acc 存在 ±5–10pp 波动(固定 LR 0.1、cosine T_max=200 早期尚未显著衰减所致,非 bug),见 [EXP-03](../experiments/EXP-03-phase2-repro-small-scale.md)

## 第 5 级 —— 完整训练

- [ ] 待补(EXP-04):最终指标 ___ vs 参考(该仓库自报)93.02%(差距:___;已解释:数据、schedule、硬件还是随机种子)

## §3.5 验证笔记 —— 针对我改过的任何代码

对上游 `main.py` / `utils.py` 做了三处最小改动:新增 MPS 设备分支(原代码只判断 `cuda`/`cpu`)、新增 `--epochs`/`--tag` CLI 参数(避免每级复现覆盖同一个 `checkpoint/ckpt.pth`)、`utils.py` 的 `stty size` 探测加了 try/except 兜底(后台无 tty 运行时原代码会抛异常)——diff 见 [EXP-01](../experiments/EXP-01-phase2-repro-env-setup.md);第 3 级实跑时另发现并修复 `num_workers=2` 在 macOS 上触发 multiprocessing spawn 递归死锁,改为非 CUDA 设备 `num_workers=0`——diff 见 [EXP-02](../experiments/EXP-02-phase2-repro-short-eval.md)。

- [x] 模块边界处 tensor shape 已检查:第 2 级 forward 输出确认 `(4,10)`;device 正确路由到 `mps`(EXP-01)
- [x] config 仍与 README 和论文描述一致:模型选择改的是文件内注释切换(`net = ResNet18()`),训练超参数(lr 0.1、SGD momentum 0.9、weight_decay 5e-4、cosine annealing T_max=200)未改动
- [x] 修改保持在预期的小范围内:`git diff` 共 4 处小改动(设备分支、CLI 参数、checkpoint 文件名变量化),未动训练逻辑本身
