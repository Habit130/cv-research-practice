# 计算机视觉科研实践闭环模板

> 🚧 **建设中**:本仓库正在搭建,结构与内容可能调整。

## 这是什么

本仓库与《计算机视觉科研入门指南》([计算机视觉科研入门指南.pdf](计算机视觉科研入门指南.pdf),下称"理论指南")配对使用:**理论指南回答"为什么"——科研训练要求什么、如何建立能力地图;本仓库提供"怎么做"的脚手架**——克隆之后,围绕你自己选定的一个小型 CV 任务,真实跑一遍"论文选读 → 代码复现 → 实验记录 → 对比分析 → 报告写作"的实践闭环。

仓库分三部分:

```
templates/   # 任务无关模板(PLAN / 论文笔记 / 复现清单 / 实验记录),复制到 runs/ 下使用
example/     # 作者本人的验证跑(ResNet-18 + CIFAR-10,全深度),仅供参考体例
runs/        # 你的工作区 —— 你的实践闭环在这里进行
```

[example/](example/PLAN.md) 是一份"活例子":它的每一步都按本 README 走过一遍。任何 Phase 拿不准体例时,去 `example/` 对应目录看同款文件即可;但不要把你自己的内容写进去。

## 本仓库不做什么

- **不重复理论指南的内容**:方法论一律只引用章节号,不复述(否则会出现两个必然漂移的真相源)。
- **不教深度学习知识本身**:网络、优化、损失函数等请回到课程与理论指南的引文。
- **不自写通用工具教程**:[git](https://git-scm.com/doc)、[conda](https://docs.conda.io)、[LaTeX](https://www.latex-project.org/help/) 请直接看官方文档。
- **不含任务代码与数据集**:本仓库永远是纯文本流程仓库,代码克隆自上游、数据留在仓库外(决定记录见 [docs/adr/0002](docs/adr/0002-仓库只含流程文本.md))。
- **不承诺"跑完 = 会科研"**:这里只是你第一次完整闭环的脚手架(指南 §7)。

## 前置条件与硬件声明

开始前你需要:

1. 读完理论指南——本 README 的所有方法论都以章节号指回它,不另作解释;
2. 会基本的命令行操作、git 与 conda(见上节官方文档链接);
3. 一台能跑 PyTorch 的机器,三条路径任选其一:
   - **本机 NVIDIA GPU(CUDA)**:最常规的路径;
   - **Apple Silicon(MPS)**:`example/` 的验证跑即走此路径,可行性已验证;
   - **租用 GPU 服务器**:适合本机没有加速设备的读者。作者推荐 [优云智算 Compshare](https://passport.compshare.cn/register?referral_code=32azz6jfCsUEdtSr0CwjsW)(UCloud 优刻得旗下):按小时计费、关机不收费,单张 RTX 4090 约 1–3 元/小时(以[官方价格页](https://www.compshare.cn/price-list)为准),足够覆盖本仓库任务梯度的全部三档。

最低档任务 MNIST 纯 CPU 即可跑通,因此没有加速设备也能完成 Phase 0 并开始闭环。

`example/` 的全部运行发生在以下设备上(引自 [EXP-00 实验记录](example/experiments/EXP-00-phase0-env-mnist-smoke.md)):

| 项目 | 值 |
|---|---|
| 机型 | MacBook Air (Apple M5) |
| 内存 | 24 GB |
| 操作系统 | macOS 27.0 |
| Python | 3.11.15(conda 环境 `cvpractice`) |
| PyTorch / torchvision | 2.12.1 / 0.27.1 |
| 加速设备 | MPS(无 CUDA) |

## 任务梯度:如何选你的任务

三档参考,按需就近选一档:

| 档位 | 任务 | 定位 |
|---|---|---|
| 最低档 | **MNIST 手写数字分类** | 纯 CPU 可跑,兼任 Phase 0 的冒烟测试,不单独走闭环 |
| 推荐档 | **CIFAR-10 图像分类** | `example/` 验证跑即此档(ResNet-18 + CIFAR-10),全程有活例子可对照 |
| 进阶自选 | **小型目标检测** | 理论指南贯穿全文的例子,建议完成一轮分类闭环后再做 |

也可以选三档之外你自己的任务,判断标准只有一条:任务的五要素(数据 / 任务 / 模型 / loss / 指标,指南 Table 2)每一项都足够小,**可被单张 GPU(或 MPS)承载**,让闭环的每个环节都能在业余时间里做完。拿不准就选 CIFAR-10 分类。

## 实践闭环:五个 Phase

开始前,在 `runs/` 下建立你的任务目录(如 `runs/my-task/`),复制 [templates/PLAN-template.md](templates/PLAN-template.md) 为其中的 `PLAN.md`,写下任务决定;再按 `example/` 同构建立 `papers/`、`repro/`、`experiments/`、`report/` 四个子目录。此后每完成一个 Phase,回 `PLAN.md` 记一行进度。

贯穿全程的两条纪律:

- **先建记录,再跑实验**:任何一次训练/评测启动之前,先用实验记录模板建好本次记录(冒烟测试也不例外);
- **结论必须条件化**:每份记录的结论写成"指标在什么条件下从 X 变到 Y、代价是什么",不写无条件的"效果变好"。

### Phase 0 —— 环境

- **目标**:搭好一个版本固定、可复用整个闭环的 PyTorch 环境,并用分钟级的 MNIST 冒烟测试证明它可用。
- **具体动作**:新建 conda 环境并固定 Python 版本 → 安装固定版本的 PyTorch / torchvision → 自检加速设备(CUDA 或 MPS)→ 用一个极小 CNN 在 MNIST 上跑通一次完整的前向 + 反向 + 评测。可照抄的完整命令与冒烟脚本见 [example 的 EXP-00 记录](example/experiments/EXP-00-phase0-env-mnist-smoke.md);数据集下载到仓库外,不要提交。遇到环境问题,按指南 Table 6 的顺序排查。
- **退出标准**:PyTorch 能看到 GPU 或 MPS;冒烟测试的前向传播跑通。
- **模板**:[templates/experiment-log-template.md](templates/experiment-log-template.md)(冒烟测试同样先建记录再跑)。
- **指南**:§6.2、Table 6。

### Phase 1 —— 论文选读

- **目标**:围绕你的任务读透 1–2 篇必读论文,建立对方法本身的把握。
- **具体动作**:确定你任务的种子论文(该方向的经典方法)→ 向后追它引用的奠基工作、向前检索 forward citation → 检索结果按指南 Table 4 三层分级并记下分级理由 → 每篇必读论文复制论文笔记模板,按 §4.3 完成三轮阅读,笔记存入 `papers/`。体例参考 [example 的 ResNet 笔记](example/papers/he2016-deep-residual-learning.md)与[引文分级简记](example/papers/citation-triage.md)。
- **退出标准**:完成 1–2 份必读笔记;不翻 PDF 也能说出种子论文的五要素(数据 / 任务 / 模型 / loss / 指标)。
- **模板**:[templates/paper-notes-template.md](templates/paper-notes-template.md)。
- **指南**:§4.1–4.3、Table 4。

### Phase 2 —— 代码复现

- **目标**:把种子论文的一个参考实现在你的环境里完整复现,指标接近参考数字。
- **具体动作**:选定一个参考实现,克隆到 `runs/<你的任务>/repro/<仓库名>/` 并在其中修改 → 复制复现清单,按指南 §6.3 的阶梯逐级爬(环境 → demo → checkpoint 评测 → 小规模训练 → 完整训练),**不许跳级** → 每一级的结果先写进实验记录,再进入下一级。数据集与 checkpoint 留在克隆仓库自己的数据目录,不进本仓库。
- **退出标准**:完整训练的指标接近参考数字,剩余差距能解释(数据、schedule、硬件还是随机种子)。
- **模板**:[templates/repro-checklist.md](templates/repro-checklist.md)。
- **指南**:§6.3、§3.5。

### Phase 3 —— 实验与记录

- **目标**:在复现基础上完成一组最小但纪律完整的对比实验。
- **具体动作**:最小集合为 1 个 baseline + 2 个 ablation,每次只改一个因素,对比纪律依据指南 §6.6 与 Table 7 → 每次运行之前先从模板建立实验记录(必填信息见 §6.4),存入 `experiments/` → 跑完把结果与结论写回同一份记录。
- **退出标准**:每份记录以条件化判断收尾,写清结果支持什么、不支持什么。
- **模板**:[templates/experiment-log-template.md](templates/experiment-log-template.md)。
- **指南**:§6.4、§6.6、Table 7。

### Phase 4 —— 报告写作

- **目标**:把整个闭环写成一份数字可追溯的报告。
- **具体动作**:在 `report/` 下按指南 §4.4 的叙事线组织报告,附 baseline / ablation 对照表;每写下一个数字,确认它能指回 `experiments/` 里的某份实验记录。载体选择见下节。
- **退出标准**:报告成稿(用 LaTeX 则必须能编译);报告里每个数字都能追溯到一份实验记录。
- **模板**:无单独模板,体例参考 [example/report/](example/report/)。
- **指南**:§4.4–4.5。

## 报告载体政策

`example/` 的报告采用 IEEEtran LaTeX,作为满配示范;你的第一轮闭环**允许用 markdown 写报告**。Phase 4 的考核点是叙事线(§4.4)与每个数字可追溯到实验记录,不是排版。

## 维护者层

仓库里的 [CLAUDE.md](CLAUDE.md)、[CONTEXT.md](CONTEXT.md) 与 [docs/](docs/) 是维护者与 AI 协作层,走闭环时可以无视;附带的好处是——用 Claude Code 的读者克隆本仓库,打开即得一个已经懂这套流程、会守上述纪律的助手。
