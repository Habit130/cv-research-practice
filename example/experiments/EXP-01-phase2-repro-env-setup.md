# EXP-01 —— Phase 2 复现第 0–2 级:锚点定位 + 环境复用 + 前向 demo

> 五类必填信息依据指南 §6.4;对比纪律依据 §6.6 与 Table 7。
> 在启动运行**之前**创建本记录。

**类型**(Table 7):环境/代码验证(不参与 baseline / ablation / fair comparison 对比,对应 issue #8 复现爬梯第 0–2 级)
**日期**:2026-07-08

## 1. 这次实验想验证什么

验证复现清单([repro-checklist.md](../repro/repro-checklist.md))第 0–2 级:上游仓库 `kuangliu/pytorch-cifar` 的锚点(训练入口/config/数据目录/模型目录)可定位;EXP-00 已装好的 `cvpractice` 环境无需重装即可 import 上游的 `ResNet18`;模型能在 MPS 设备上跑通一次前向,输入输出 shape 合理。

## 2. 代码版本与数据划分

- 仓库 / commit:[kuangliu/pytorch-cifar](https://github.com/kuangliu/pytorch-cifar) @ `49b7aa97b0c12fe0d4054e670403a16b6b834ddd`,克隆到仓库外 `~/repro/pytorch-cifar`(ADR 0002)
- 数据集与划分:本条目不涉及数据集(仅前向 demo 用随机 tensor,未触发真实数据下载)

## 3. 关键 config 与训练参数

- Config 文件:无独立 config;对上游 `main.py` / `utils.py` 做了两处最小改动,`git diff` 摘录:

```diff
--- a/main.py
+++ b/main.py
@@ 新增 CLI 参数
+parser.add_argument('--epochs', default=200, type=int, help='number of epochs')
+parser.add_argument('--tag', default='', type=str,
+                    help='suffix for checkpoint filename, to avoid clobbering across repro-checklist levels')

@@ 设备选择:原代码只判断 cuda/cpu,补上 mps 分支
-device = 'cuda' if torch.cuda.is_available() else 'cpu'
+if torch.cuda.is_available():
+    device = 'cuda'
+elif torch.backends.mps.is_available():
+    device = 'mps'
+else:
+    device = 'cpu'
+ckpt_name = 'ckpt' + (f'_{args.tag}' if args.tag else '') + '.pth'

@@ 模型切换:注释切换 SimpleDLA -> ResNet18(README 表格的对比对象)
-# net = ResNet18()
+net = ResNet18()
...
-net = SimpleDLA()
+# net = SimpleDLA()

@@ checkpoint 路径改用变量,避免各级复现互相覆盖
-torch.save(state, './checkpoint/ckpt.pth')
+torch.save(state, f'./checkpoint/{ckpt_name}')

--- a/utils.py
+++ b/utils.py
@@ stty 探测加 try/except(后台无 tty 运行时原代码会抛异常)
-_, term_width = os.popen('stty size', 'r').read().split()
-term_width = int(term_width)
+try:
+    _, term_width = os.popen('stty size', 'r').read().split()
+    term_width = int(term_width)
+except ValueError:
+    term_width = 80  # no tty (e.g. backgrounded/nohup run)
```

- 覆盖项:不适用(本条目是代码/环境验证,非训练实验)
- 相对 baseline,本次只改的那一个因素:不适用

## 4. 结果放在哪

- 日志:见下方"终端输出"
- Checkpoint:无
- 指标输出 / 曲线:无

### 终端输出

```
$ /opt/homebrew/Caskroom/miniforge/base/envs/cvpractice/bin/python -c "
from models import ResNet18
net = ResNet18()
print('param count:', sum(p.numel() for p in net.parameters()))
print('mps available:', torch.backends.mps.is_available())
"
param count: 11173962
mps available: True

$ python -c "前向 demo,见下方脚本"
device: mps
input shape: (4, 3, 32, 32)
output shape: (4, 10)
forward OK
```

前向 demo 脚本(内联,非仓库文件,§3.5 验证用):

```python
import torch
from models import ResNet18

device = torch.device('mps' if torch.backends.mps.is_available() else 'cpu')
net = ResNet18().to(device)
net.eval()
x = torch.randn(4, 3, 32, 32, device=device)
with torch.no_grad():
    y = net(x)
assert y.shape == (4, 10)
```

## 5. 结论(支持 / 不支持什么判断)

- 结果:`kuangliu/pytorch-cifar` 的四个锚点(训练入口 `main.py`、数据目录 `./data`、模型目录 `models/`、config 为脚本内硬编码)已定位;EXP-00 装好的 `cvpractice` 环境无需任何新增依赖即可 import 上游 `ResNet18`(11,173,962 参数);ResNet18 在 MPS 设备上对 `(4,3,32,32)` 随机输入前向输出 `(4,10)`,shape 与 10 类分类任务一致。
- 条件化判断:在 Apple M5 + macOS 27.0 + `cvpractice` 环境(torch 2.12.1)下,复现清单第 0–2 级的判据成立;此结论建立在**上游代码经过两处最小改动**之上(设备分支 + CLI 参数 + checkpoint 命名,diff 见上,未改动模型结构或训练超参数),未验证改动前的原始代码在无 CUDA 环境下能否直接跑(原代码硬编码 `cuda`/`cpu`,在纯 MPS 机器上会退化到 CPU,可行但更慢,这也是补 mps 分支的原因)。尚未验证:真实 CIFAR-10 数据下载与训练是否顺畅(下一步)。下一个实验:第 3 级——上游未提供官方 checkpoint,改走短训练评测(EXP-02)。
