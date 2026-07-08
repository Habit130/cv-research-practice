# Phase 1 检索与分级过程简记 —— 种子论文 ResNet(He et al., 2016)

> 依据指南 §4.1(如何找到论文)、§4.3(backward/forward citation)、Table 4(三层分级)。
> 本文件是 issue #7 交付物之二:"检索与分级过程简记"。按 issue 里的分工,引文网络检索和分级整理由 session 完成;论文笔记里"相关性 / 贡献 / 局限"三处判断留给作者本人(见 [he2016-deep-residual-learning.md](he2016-deep-residual-learning.md) 内的 TODO 标记)。

## 种子论文

- **He, Zhang, Ren, Sun (2016). Deep Residual Learning for Image Recognition. CVPR 2016.**(arXiv:1512.03385)—— **必读**,不参与分级判断,是本次验证跑的入口论文。

## Backward citation(种子论文引用、构成其方法脉络的前置工作)

| 论文 | 分级 | 处理方式 / 暂存原因 |
|---|---|---|
| Krizhevsky, Sutskever, Hinton (2012). ImageNet Classification with Deep CNNs (AlexNet). NIPS 2012. | 背景 | ResNet 论文的训练细节(颜色增强、10-crop 测试)直接沿用 AlexNet 的做法;帮助理解"CNN 在 ImageNet 上起势"的背景,但不含 ResNet 核心方法,不必读全文,只需摘要+方法细节。issue 里把 AlexNet 列为选做的 backward citation 笔记候选之一,本轮未做完整笔记,只记录摘要与引用关系。 |
| Simonyan & Zisserman (2014). Very Deep Convolutional Networks for Large-Scale Image Recognition (VGG). ICLR 2015. | 背景(选做笔记的另一候选) | ResNet 的 34 层 plain 网络就是仿照 VGG 的"全 3×3 卷积、逐层加深"哲学搭建的直接对照组;是 Table 5 参数量/计算量对比的基准对象。价值高但本轮时间只做 1 份必读笔记,暂存为背景,留原因:非残差机制本身,读懂 ResNet 不依赖精读全文。 |
| Szegedy et al. (2015). Going Deeper with Convolutions (GoogLeNet/Inception). CVPR 2015. | 暂存 | 仅作为 ILSVRC 2014 同期最优结果的对比基准出现,ResNet 未借用其多分支结构。暂存原因:与残差学习的方法脉络关系弱,当前 Loop A(分类)用不到,先记线索。 |
| Srivastava, Greff, Schmidhuber (2015). Highway Networks. | 背景 | ResNet 论文 related work 中明确对比的最相关同期工作——两者都用"跳过路径"训练超深网络,但 Highway Networks 用带参数的门控(gate)决定跳过比例,ResNet 用零参数的 identity shortcut。这一对比是理解"ResNet 到底新在哪"的关键背景,优先级高于 AlexNet/GoogLeNet,但仍不是本次必读(必读只留种子论文本身)。 |
| Ioffe & Szegedy (2015). Batch Normalization. ICML 2015. | 背景 | ResNet 每个卷积后接 BN,是复现训练 config 必须理解的组件,但 BN 本身不是残差学习的方法脉络,列为背景技术参考。 |
| Girshick et al. 系列(R-CNN / Fast R-CNN / Faster R-CNN) | 暂存 | ResNet 论文用 Faster R-CNN + ResNet-101 骨干做检测实验,但这部分是"骨干网络的下游验证",与 Loop A(CIFAR-10 分类)复现无关。暂存原因:留给 Loop B(小型目标检测)阶段再读。 |

## Forward citation(引用/建立在 ResNet 之上的后续工作,用于理解其影响面)

| 论文 | 分级 | 处理方式 / 暂存原因 |
|---|---|---|
| He, Zhang, Ren, Sun (2016). Identity Mappings in Deep Residual Networks. ECCV 2016. | 背景 | 同作者对 ResNet 的直接改进(pre-activation 设计,即 BN→ReLU→conv 顺序调整),如果 Phase 3 的 ablation 想试"更换 block 内部顺序",这篇是直接参考。暂存原因:超出当前必读范围,留作 ablation 设计的候选参考。 |
| Zagoruyko & Komodakis (2016). Wide Residual Networks. | 背景 | 讨论深度 vs 宽度的取舍,与 PLAN.md 里"深度 18→34"这个 ablation 的设计思路相关,可能在写 Phase 3 实验记录时用作对比视角。 |
| Xie et al. (2017). Aggregated Residual Transformations for Deep Neural Networks (ResNeXt). | 暂存 | 引入 cardinality 维度,超出本次 Loop A 的最小复现范围。 |
| Huang et al. (2017). Densely Connected Convolutional Networks (DenseNet). | 暂存 | 替代性的连接方式(dense connection vs residual connection),作为对比视角有价值但非本次必读。 |
| Hu et al. (2018). Squeeze-and-Excitation Networks (SENet). | 暂存 | 在骨干上加通道注意力,超出本次复现范围。 |
| He et al. (2017). Mask R-CNN. | 暂存 | 用 ResNet+FPN 做检测/分割骨干,和 backward citation 里的 Faster R-CNN 一样,留给 Loop B。 |

## 分级小结

- **必读(1 篇)**:ResNet 本身——本次验证跑只做种子论文的完整三轮笔记,符合 issue #7"1–2 份"的下限。
- **背景(选做笔记候选)**:VGG、AlexNet 二选一可作为 issue 里"选做"的 backward citation 笔记;Highway Networks、Batch Normalization、Identity Mappings、Wide ResNet 列为背景参考,不单独立笔记,按需在 Phase 2/3 复现时回查。
- **暂存**:GoogLeNet、R-CNN 系列、ResNeXt、DenseNet、SENet、Mask R-CNN——与当前 Loop A(CIFAR-10 分类)关系弱或超出复现范围,原因已逐条注明,留待 Loop B 或后续扩展时再捞回。
