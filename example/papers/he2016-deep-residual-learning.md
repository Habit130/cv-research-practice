# 论文笔记 —— He et al., 2016, Deep Residual Learning for Image Recognition (ResNet)

> 字段依据指南 §4.2;阅读轮次依据 §4.3;五要素依据 Table 2。
> 每篇必读论文复制一份。第一轮读完再决定是否继续往下读。

**引用信息**:He, K., Zhang, X., Ren, S., & Sun, J. (2016). Deep Residual Learning for Image Recognition. *CVPR 2016*, pp. 770–778. (arXiv:1512.03385, 2015-12-10 提交)

**分级**(Table 4):**必读**(本次验证跑的种子论文,Loop A 模型能力入口)
**已完成轮次**:☑ 第一轮·略读 ☑ 第二轮·结构 ☑ 第三轮·细节

## 第一轮 —— 是否相关?(标题 / 摘要 / 引言 / 核心图表 / 结论)

- 研究的任务、所用数据集与指标:图像分类(ImageNet-1k,附带 CIFAR-10 补充实验)、目标检测/定位(PASCAL VOC、COCO,作为骨干网络的下游验证)。指标:ImageNet top-1 / top-5 error;CIFAR-10 test error;检测用 mAP。
- 与当前项目的关系、为什么现在值得读:

  > **TODO(作者填写)**——这是"相关性"判断,按 issue #7 的作者参与点约定,由作者本人结合 [PLAN.md](../PLAN.md) 里 Loop A(ResNet-18 + CIFAR-10)的复现目标来写,不由 session 代笔。

- **结论:是否继续** —— 是,继续三轮阅读(本文是种子论文,必读,无需相关性甄别)。

## 五要素(Table 2)

| 要素 | 本文 |
|---|---|
| 数据 | ImageNet-1k(~128 万训练图,1000 类);CIFAR-10(6 万张 32×32,10 类)作为深度效应的补充验证;检测实验用 PASCAL VOC 2007/2012 与 COCO |
| 任务 | 图像分类为主;附带以分类骨干做目标检测/定位的迁移实验 |
| 模型 | 带 identity shortcut 的残差网络:多个 residual block 堆叠,浅层用 2 层 3×3 basic block,深层(50/101/152 层)用 1×1-3×3-1×1 的 bottleneck block 控制计算量;最深至 152 层(仍比 VGG-19 计算量低) |
| Loss function | Softmax + 交叉熵(分类);检测部分沿用 Faster R-CNN 自身的多任务 loss(分类 + 边框回归),ResNet 只是替换骨干 |
| 指标 | ImageNet top-1/top-5 error;CIFAR-10 test error;检测用 mAP(VOC/COCO) |

## 第二轮 —— 结构

- 研究问题与动机:更深的网络是否总是更容易训练、效果更好?论文观察到"退化问题"(degradation problem)——单纯堆叠更多层后,训练误差反而上升,且不是梯度消失/爆炸或过拟合能解释的(训练误差本身变差),说明深层 plain 网络更难优化。
- 核心贡献:

  > **TODO(作者填写)**——这是"贡献"判断,由作者本人写清楚"这篇论文真正解决了什么、和 Highway Networks 等同期工作比新意在哪",不由 session 代笔。

- 方法框架 —— 输入 → 主要模块 → 输出:输入图像 → stem(7×7 conv + maxpool)→ 多个 stage,每个 stage 由若干 residual block 堆叠(每个 block 内部路径的输出与输入直接相加,即 `y = F(x) + x`,恒等映射不引入额外参数)→ 全局平均池化 → 全连接层 → softmax 输出类别。降维/跨维度对齐时用 zero-padding(方案 A)或 1×1 conv 投影(方案 B/C)处理 shortcut。
- 实验设置 —— 数据集、baseline、指标:ImageNet 上以同等层数的 plain 网络(去掉 shortcut)为主要对照,报告 18/34/50/101/152 层;CIFAR-10 上以 20/32/44/56/110/1202 层的 plain vs 残差网络对照,验证"深度—误差"曲线;检测任务以 VGG-16 骨干的 Faster R-CNN 为对照,替换成 ResNet-101 骨干看 mAP 提升。
- 主要结论,以及各由哪个实验支撑:
  - 深层 plain 网络存在退化问题,残差网络能让深度网络的训练误差随深度增加而单调不升——由 34 层 plain vs 34 层残差网络的训练/验证曲线对比支撑。
  - 100+ 层的残差网络在 ImageNet 上可以稳定训练并持续降低 error——由 18/34/50/101/152 层残差网络的逐层对比表支撑,152 层残差网络计算量仍低于 VGG-19。
  - identity shortcut(不引入参数)已经足以解决退化问题,投影 shortcut 只带来很小的额外增益——由 Ablation A/B/C 三种 shortcut 方案的对比支撑(见第三轮)。
  - 极深网络(1202 层)在 CIFAR-10 这种小数据集上会出现过拟合(训练误差相近但测试误差比 110 层差),而非优化困难——由 1202 层 vs 110 层残差网络的训练/测试误差对比支撑。
  - ResNet 作为骨干网络能直接提升下游检测/定位任务效果——由替换 Faster R-CNN 骨干后 VOC/COCO mAP 的提升数据支撑,并助力论文拿下 ILSVRC & COCO 2015 多项第一。

## 第三轮 —— 细节(仅在需要复现或借鉴时)

- 值得复用的训练细节 / config(ImageNet):SGD,momentum 0.9,weight decay 0.0001;batch size 256;初始 lr 0.1,验证误差平台期时除以 10;He 初始化;每个 conv 后接 BN、再接 ReLU(conv→BN→ReLU 顺序);不用 dropout;数据增强为尺度扰动(短边随机 resize 到 [256, 480])后随机裁剪 224×224、随机水平翻转、AlexNet 风格的颜色扰动,输入做逐像素均值归一化。CIFAR-10 上:同样 SGD + momentum 0.9 + weight decay 0.0001,batch size 128,初始 lr 0.1,在 32k/48k 迭代处除以 10,共训 64k 迭代;数据增强仅用 4px padding 后随机裁剪 32×32 + 随机水平翻转。这些数字是 Loop A 复现(ResNet-18 + CIFAR-10)最直接可参照的 baseline config。
- 各 ablation 及其分别证明了什么:
  - **plain vs 残差**(相同层数逐一对照):证明退化问题是可以被残差连接解决的优化问题,而非模型容量问题。
  - **shortcut 方案 A(zero-padding,全 identity)vs B(仅升维处投影)vs C(全部投影)**:C 略优于 B 略优于 A,但差距很小;论文据此论证"投影 shortcut 不是必需的",默认用零参数的 identity shortcut,只在需要控制深层模型复杂度时才用投影,为 bottleneck 设计的取舍提供依据。
  - **basic block vs bottleneck block**(50 层起改用 1×1-3×3-1×1 结构):证明 bottleneck 能在层数大幅增加时把计算量控制在与更浅的 basic-block 网络相近的水平,是让 101/152 层可行的关键设计。
- 局限与疑问:

  > **TODO(作者填写)**——这是"局限"判断,由作者本人写(例如:极深网络在小数据集上的过拟合边界在哪、退化问题的根因论文并未完全解释清楚等),不由 session 代笔。

## 对我的项目(启发)

- 打算复用什么 / 和什么对比:训练 config(SGD + momentum 0.9 + weight decay 0.0001 + step decay)可直接作为 [PLAN.md](../PLAN.md) Phase 3 baseline 的参照起点;PLAN.md 已定的两个 ablation ——"去掉 shortcut connection"对应本文 plain-vs-residual 实验,"深度 18→34"对应本文的层数对照表——可以直接复用本文的对照设计逻辑,是否照搬还是调整留待 Phase 3 实验记录里定。
- 待追的 backward citation / 已发现的 forward citation:见同目录 [citation-triage.md](citation-triage.md)(session 代查引文网络的完整记录)。
