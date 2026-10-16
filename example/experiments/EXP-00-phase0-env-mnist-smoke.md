# EXP-00 —— Phase 0 环境搭建 + MNIST 冒烟测试

> 五类必填信息依据指南 §6.4;对比纪律依据 §6.6 与 Table 7。
> 在启动运行**之前**创建本记录。

**类型**(Table 7):环境冒烟测试(不参与 baseline / ablation / fair comparison 对比,仅验证"环境是否可用",对应 issue #6、PRD 的 Phase 0)
**日期**:2026-07-07

## 1. 这次实验想验证什么

验证本次验证跑(ResNet-18 + CIFAR-10)所需的实验环境已经可用:conda 环境隔离、固定版本的 PyTorch/torchvision 能装上、PyTorch 能看到加速设备(MPS 或 CUDA),并用一个训练时间在分钟级的极小 CNN 在 MNIST 上跑通一次完整的前向 + 反向 + 评测,作为 Phase 0 的退出标准判据。

## 2. 代码版本与数据划分

- 仓库 / commit:本条目不对应克隆的上游仓库(ADR 0002:仓库只含流程文本);冒烟脚本是下方"环境搭建步骤"里内联的 ~60 行自写代码,不作为文件提交进本仓库
- 数据集与划分:torchvision 内置 `datasets.MNIST`,标准 60000 张 train / 10000 张 test 划分,下载到仓库外 `~/mnist_data`(未入库,ADR 0002)

### 环境搭建步骤

> 以下命令是 2026-07-07 本次运行实际执行的历史命令,当时并未精确钉选版本:Python 只固定到 3.11,torch / torchvision 未钉版本、装的是当日最新,从而解得下方运行环境表的组合(Python 3.11.15 / torch 2.12.1 / torchvision 0.27.1)。最新版本随时间漂移,今天照抄同一命令不保证同一组合(2026-10-16 冷启动实测照抄得到 Python 3.11.17 / torch 2.14.1 / torchvision 0.29.1,见 issue #19);要精确固定版本,用下方"复执行示例"。

```bash
# 1. 新建 conda 环境,固定 Python 版本
conda create -y -n cvpractice python=3.11
conda activate cvpractice

# 2. 安装固定版本的 PyTorch / torchvision(pip 官方源;macOS 的 wheel 天然带 MPS 支持,无需区分 cpu/cuda 变体)
pip install torch torchvision

# 3. 自检:PyTorch 能否看到加速设备
python -c "
import torch
print('torch', torch.__version__, '| torchvision 需另行 import 确认版本')
print('mps available:', torch.backends.mps.is_available())
print('cuda available:', torch.cuda.is_available())
"
```

若自检失败,按 Table 6 顺序排查(包缺失 → 版本冲突 → CUDA/MPS 不可用 → 算子/扩展不匹配 → 路径混乱)。本次运行一次通过,未触发排错分支。

### 复执行示例(2026-10-16 事后补充,非本次运行的实跑记录)

以下命令是为精确固定版本而事后补充的示例:2026-07-07 本次运行没有执行它们,本记录各节的结果也不由它们产生。它把 Python 与 torch / torchvision 都钉到运行环境表的组合。

- 版本配对依据 [PyTorch 官方历史版本页](https://pytorch.org/get-started/previous-versions/) 的 v2.12.1 小节:该页对 OSX 给出的命令即 `torch==2.12.1 torchvision==0.27.1`,Linux / Windows 的 CUDA 变体带对应 `--index-url`。
- 该组合只验证于 Apple Silicon / MPS;其他平台按 [PyTorch 官方安装入口](https://pytorch.org/get-started/locally/) 选平台与计算后端,再到历史版本页复制匹配的固定命令。
- 若你的包源解析不到某个精确版本,改选最近的兼容版本,并把实际装到的版本记入实验记录。

```bash
conda create -y -n cvpractice python=3.11.15
conda activate cvpractice
# macOS / MPS
pip install torch==2.12.1 torchvision==0.27.1
```

### MNIST 下载失败时(2026-10-16 事后补充,非本次运行的实跑记录)

torchvision 下载时会做完整性校验,损坏或不完整的文件会在重跑同一脚本时被重新下载,一般无需手工清理数据;但重跑不保证必然恢复,持续失败时保留完整报错、核查网络/代理与数据目录可写性,并参见 [torchvision 的 MNIST 数据集文档](https://pytorch.org/vision/stable/generated/torchvision.datasets.MNIST.html)与 README Phase 0 的下载提示。

作为参照:2026-10-16 的冷启动检查(issue #11,提交 `79c864a`,macOS 27.2 / torch 2.14.1 / torchvision 0.29.1)中,首次下载在 `t10k-images-idx3-ubyte.gz` 的完整性校验处失败、脚本以非零状态退出;不改任何配置、直接重跑同一脚本即成功。该观测只对应当次日期与版本组合,不代表相关下载源的长期状态。

### MNIST 冒烟脚本(内联,非仓库文件)

```python
import time
import torch
import torch.nn as nn
import torch.nn.functional as F
from torch.utils.data import DataLoader
from torchvision import datasets, transforms

DATA_DIR = "~/mnist_data"
EPOCHS = 2
BATCH_SIZE = 128


class SmallCNN(nn.Module):
    def __init__(self):
        super().__init__()
        self.conv1 = nn.Conv2d(1, 16, 3, padding=1)
        self.conv2 = nn.Conv2d(16, 32, 3, padding=1)
        self.fc1 = nn.Linear(32 * 7 * 7, 128)
        self.fc2 = nn.Linear(128, 10)

    def forward(self, x):
        x = F.max_pool2d(F.relu(self.conv1(x)), 2)
        x = F.max_pool2d(F.relu(self.conv2(x)), 2)
        x = x.flatten(1)
        x = F.relu(self.fc1(x))
        return self.fc2(x)


def main():
    device = torch.device("mps" if torch.backends.mps.is_available() else "cpu")
    print(f"device: {device}")

    tfm = transforms.Compose([transforms.ToTensor(), transforms.Normalize((0.1307,), (0.3081,))])
    train_set = datasets.MNIST(root=DATA_DIR, train=True, download=True, transform=tfm)
    test_set = datasets.MNIST(root=DATA_DIR, train=False, download=True, transform=tfm)
    train_loader = DataLoader(train_set, batch_size=BATCH_SIZE, shuffle=True)
    test_loader = DataLoader(test_set, batch_size=BATCH_SIZE, shuffle=False)

    model = SmallCNN().to(device)
    opt = torch.optim.Adam(model.parameters(), lr=1e-3)

    for epoch in range(1, EPOCHS + 1):
        model.train()
        t0 = time.time()
        for x, y in train_loader:
            x, y = x.to(device), y.to(device)
            opt.zero_grad()
            loss = F.cross_entropy(model(x), y)
            loss.backward()
            opt.step()
        epoch_time = time.time() - t0

        model.eval()
        correct, total = 0, 0
        with torch.no_grad():
            for x, y in test_loader:
                x, y = x.to(device), y.to(device)
                pred = model(x).argmax(dim=1)
                correct += (pred == y).sum().item()
                total += y.size(0)
        acc = correct / total
        print(f"epoch {epoch}: {epoch_time:.1f}s, test_acc={acc:.4f}")


if __name__ == "__main__":
    main()
```

## 3. 关键 config 与训练参数

- Config 文件:无独立 config,参数写死在脚本内(冒烟测试,非正式实验)
- 覆盖项 —— batch size 128、LR 1e-3(Adam,无 schedule)、epoch 2、seed 未固定(冒烟测试不要求可复现到位小数点)、pretrained weights 无(从随机初始化训练)
- 相对 baseline,本次只改的那一个因素:不适用(本条目无 baseline,是环境自检)

## 4. 结果放在哪

- 日志:本文件第 5 节的终端输出
- Checkpoint:无(冒烟测试不保存 checkpoint)
- 指标输出 / 曲线:无曲线,仅每 epoch 打印一行 `epoch_time` 与 `test_acc`

### 运行环境(硬件声明,供 README #3 直接引用)

| 项目 | 值 |
|---|---|
| 机型 | MacBook Air (Apple M5) |
| 芯片 | Apple M5 |
| 内存 | 24 GB |
| 操作系统 | macOS 27.0 (Build 26A5378j) |
| Python | 3.11.15(conda 环境 `cvpractice`) |
| PyTorch | 2.12.1 |
| torchvision | 0.27.1 |
| 加速设备 | MPS(`torch.backends.mps.is_available() == True`);无 CUDA |

### 终端输出

```
device: mps
epoch 1: 6.1s, test_acc=0.9807
epoch 2: 3.9s, test_acc=0.9873
```

## 5. 结论(支持 / 不支持什么判断)

- 结果:conda 环境隔离 + 固定版本 PyTorch/torchvision 在 Apple M5(macOS 27.0)上一次装成;`torch.backends.mps.is_available()` 为 `True`,MPS 后端可用,无需 CUDA 排错分支。极小 CNN 在 MNIST 上 2 个 epoch 内(单 epoch 约 4–6 秒,含首个 epoch 的 MPS 图编译开销)前向 + 反向 + 评测全部跑通,test accuracy 从 0.9807(epoch 1)升到 0.9873(epoch 2)。
- 条件化判断:在 Apple M5 + macOS 27.0 + Python 3.11.15 + torch 2.12.1 这一组合下,Phase 0 的退出标准(PyTorch 能看到 GPU 或 MPS;冒烟测试的前向传播跑通)成立;此结论**只对 Apple Silicon + MPS 路径**成立,未验证 CUDA 路径(读者在 NVIDIA GPU 机器上复现时,自检脚本中 `cuda available` 应为 `True`,`device` 会退化到 cpu 分支需自行确认)。代价:该环境仅约 200MB(torch + torchvision wheel),搭建耗时约 2–3 分钟(不含下载 MNIST 的网络等待)。尚未验证:更大 batch size 或更长 epoch 下 MPS 是否稳定;下一个实验:Phase 1 论文选读完成后进入 Phase 2 复现阶梯,复用本环境(`cvpractice`)克隆 ResNet-18 参考实现到 `example/repro/`。
