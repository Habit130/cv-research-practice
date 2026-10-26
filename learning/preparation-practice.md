# CPU 准备实践

这是正式五个 Phase 之前的一次外部教程练习,用来确认你能在普通电脑上跑一个小分类模型,并说清自己看见了什么。它不是论文阅读,不要求复现论文指标,也不是正式 ablation。跑通不等于理解模型,更不等于科研闭环已经完成。

先读 [学习准备](README.md)。Python 和 PyTorch 自检没通过时,不要从这页开始。

<a id="not-this"></a>

## 这不是什么

- 不是本仓库 Phase 0。区别不在有没有加速设备:本练习使用 FashionMNIST,不要求钉死 Python 与 torch / torchvision 版本,也不建立正式实验记录;Phase 0 要求先建正式记录、固定这些版本,并在仓库外用 MNIST 完成前向、反向与评测。当前标准见 [README 的 Phase 0](../README.md#phase-0)。[EXP-00](../example/experiments/EXP-00-phase0-env-mnist-smoke.md) 是作者当次 MPS 历史记录,不是本练习的通过证明。不要把本练习写成 MNIST
- 不是 Phase 0 已经通过,也不是仓库里既有的论文选读或验证跑已经通过。Issue #7 仍按原决定挂起,本练习不能拿来声称它已通过
- 不是付费云或加速设备的门槛,也不以必须看到 GPU 或 MPS 来和 Phase 0 区分。教程若自动用上 CUDA 或 MPS,只把打印出的设备记下来;那次打印不是 Phase 0 的正式记录
- 页面上的示例输出不是你的结果。Quickstart 页面有一次写着 `Using cuda device`,并打印了一组准确率。不要把那些数字抄进记录

<a id="before-run"></a>

## 运行之前

1. 若还没有 `runs/<任务>/preparation/`,先建这个目录,再复制 [记录模板](../templates/preparation-log-template.md)。运行前就建好记录。没有真实运行时,状态保持 `未运行`,观察栏不要填数字
2. 代码和数据放在本仓库之外,例如你自己的家目录下单独的文件夹。不要在本仓库里运行教程,不要把教程代码、FashionMNIST 数据、权重或 checkpoint 提交进本仓库,也不要写进 `example/`
3. `runs/<任务>/preparation/` 只放这份短记录。本仓库只忽略 `runs/*/repro/`,不忽略准备记录目录;因此更不要把代码和数据放进该目录再提交

<a id="tutorial"></a>

## 用哪一份教程

主资料是 PyTorch 官方 [Quickstart](https://docs.pytorch.org/tutorials/beginner/basics/quickstart_tutorial.html),英文、免费网页。2026-10-23 读到的正文使用 `datasets.FashionMNIST`,不是 MNIST;loss 是 `nn.CrossEntropyLoss()`;训练函数叫 `train`,测试函数叫 `test`;测试打印的指标是 accuracy 和 average loss。设备选择写的是:当前加速设备可用就用它,否则用 CPU。

从该页的 “Download Python source code” 或 “Download Jupyter notebook” 把文件下到仓库外的目录,并在记录里写下你实际打开的 URL 和访问日期。不要把页面上的下载哈希地址抄成永久版本;以你下载当天页面给出的链接为准。若你另外克隆了某个仓库,再记下实际 commit;只下载单个脚本时,commit 写 `不适用`。

本地安装仍按 [学习准备的 PyTorch 节点](README.md#pytorch):官方安装页本次没有核验到命令正文,所以本清单不代填安装命令。没有加速设备就在该页选择 CPU,把实际命令和版本写入记录。不要求 Colab,也不要求付费 GPU。

读不懂英文时,只用《动手学深度学习》PyTorch 标签的 [3.5 图像分类数据集](https://zh.d2l.ai/chapter_linear-networks/image-classification-dataset.html) 和 [3.7 softmax 回归的简洁实现](https://zh.d2l.ai/chapter_linear-networks/softmax-regression-concise.html) 对照概念。3.5 写明 Fashion-MNIST 由训练集和测试集组成,不要改称为 MNIST。不要使用该站的 MXNet 标签,也不要使用它安装页里的 `torch==1.12.0`。对照阅读不能代替你自己的运行记录。

<a id="run"></a>

## 第一次运行

在仓库外的目录运行你下载的 Quickstart 脚本或笔记本。运行前在记录里写下计划:预计改哪一个参数、预期只是“看看 loss 和 accuracy 怎么变”,不要预写数字。

运行后只记录你的终端真实打印:

- 设备那一行实际写的是 `cpu`、`mps`、`cuda` 还是别的
- 你实际执行的命令
- 训练过程打印的 loss,以及测试打印的 accuracy 和 average loss

下载失败、报错或没跑完,就保留完整报错。已经执行并得到报错时,状态写 `已记录结果`,观察栏只填报错;还没执行就保持 `未运行`。不要补一组看起来合理的准确率。

<a id="identify"></a>

## 辨认组成

对着你正在用的那份代码,在记录里指出下面五项。指的是代码位置,不是复述本页:

| 要辨认的东西 | Quickstart 正文里对应什么 |
|---|---|
| 数据 | `datasets.FashionMNIST`,且 `train=True` 与 `train=False` 各一份 |
| 模型 | `class NeuralNetwork` |
| loss | `nn.CrossEntropyLoss()` |
| 指标 | `test` 里由预测与标签比较得到的 accuracy,以及 average loss |
| 训练和测试 | `train(...)` 使用训练数据并更新参数;`test(...)` 使用测试数据且放在 `torch.no_grad()` 中 |

若你改用中文备选,就改指 3.5 的 Fashion-MNIST 读取和 3.7 里对应的模型、损失、训练循环与评价。不要混用两套代码却只记一套名字。

<a id="change-one"></a>

## 只改一个参数

第一次运行有了真实输出之后,再改下面三个量中的恰好一个,其他保持不动:

- `batch_size = 64`
- `lr=1e-3`
- `epochs = 5`

改之前先在记录里写计划。改之后用同一条命令再跑,把新的设备、命令和打印结果记在“改动后观察”。这只是为了练习比较两次真实观察,不是 Phase 3 的 baseline / ablation,也不要写成“效果变好”这种无条件结论。

<a id="train-test"></a>

## 训练和测试

用自己的话写清:训练时模型看见的是哪一份数据,测试时看见的是哪一份,测试指标为什么不能拿去继续更新参数。写不出就回到 Quickstart 的 `train` 与 `test`,或中文备选的对应段,不要靠本页代替阅读。

<a id="done"></a>

## 做完之后

准备实践结束的信号是:记录里有运行前计划、实际命令、改动前后的真实观察或明确的未运行/失败状态、你自己的解释,以及仍然不懂的一项。然后回到 [进入正式闭环](README.md#formal)。

没有这些记录,即使脚本跑完了,这一步也还没完成。有了这些记录,也仍然要读理论指南,并按 [README 的 Phase 0](../README.md#phase-0) 继续。本练习不自动通过 Phase 0,也不缩短那个标准。
