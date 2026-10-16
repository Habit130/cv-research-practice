# IEEEtran 验证跑报告

[main.tex](main.tex) 是作者验证跑的报告草稿,[report.pdf](report.pdf) 是同一源码的编译产物。实验条件下的结论已获作者批准;报告的问题定位、观点与叙事仍待作者终审,不作为发布定稿。

正文按理论指南 §4.4–4.5 组织为“问题为什么重要 → 已有方法与证据缺口 → 做了什么 → 实验是否支持结论”。本报告记录 ResNet18 + CIFAR-10 的实践闭环,不将参考实现训练称为原论文实验的严格复现。表格采用固定第 200 epoch 指标,单 seed 不支持统计显著性;参考 best accuracy 只作诊断核对。

## 编译

需要 TeX Live / MacTeX,含 `xelatex`、`latexmk`、`IEEEtran`、`ctex`、`fontspec`、`booktabs`、`balance`、`hyperref` 及发行版内的 Fandol、TeX Gyre Termes 字体。源码按字体文件名加载,无需依赖 macOS 安装字体。

在仓库根目录执行:

```bash
mkdir -p example/report/build
latexmk -norc -xelatex -interaction=nonstopmode -halt-on-error \
  -synctex=1 -outdir=example/report/build example/report/main.tex
cp example/report/build/main.pdf example/report/report.pdf
```

`build/` 已局部 Git 排除;只提交源码、本文与报告 PDF,不提交 aux/log/xdv 等缓存。IEEEtran 类在加载时可能提示初始 `ptm` 字形替换;正文明确使用 TeX Gyre Termes 与 Fandol。字体警告不能代替 PDF 阅读检查。

## 数字追溯

正文引用和 PDF 中的实验参考链接固定指向已合并的 `4fd32e0c87a76b9ecf975837b243b8dd3199daf0`,不随后续分支移动。所有结果及超参数指向以下记录;论文背景指向原论文,方法论指回仓库理论指南。

| 报告内容 | 对应记录 |
| --- | --- |
| baseline 的配置、95.45%、test loss、5.2115 h;best 95.60% @189、93.02% 参考及 +2.58pp 限制 | [EXP-05](../experiments/EXP-05-phase3-baseline.md) |
| no-shortcut 的 95.11%、-0.34pp、loss、5.1725 h、权重开关恢复要求 | [EXP-06](../experiments/EXP-06-phase3-no-shortcut.md) |
| constant-lr 的 86.88%、-8.57pp、loss、5.0133 h;三组累计 55430.08841 s / 15.3972 h | [EXP-07](../experiments/EXP-07-phase3-constant-lr.md) |
| 数据划分、完整性、设备与依赖版本、MPS preflight | [EXP-08](../experiments/EXP-08-phase3-preflight.md) |
| 早期训练的 epoch 37 范围收窄及后续说明 | [EXP-04](../experiments/EXP-04-phase2-repro-full-training.md) |

已执行数字抽查:95.45% → EXP-05、95.11% → EXP-06、86.88% → EXP-07,三处均与原记录及真实 epoch 200 日志一致。表中差值、loss、耗时和累计值也与 EXP-07 对照表核对一致。报告没有新增训练或评测结果。

## 本机验证与作者终审

本机使用 TeX Live 2026、XeLaTeX、latexmk 4.88 编译;PDF 两页,逐页渲染检查中文、公式、双栏、表格和参考链接。源码引用均已解析,未出现 missing character 或 overfull box。文档未进行 MPS/CUDA 新评测,沿用实验记录中的真实硬件证据。

作者终审需确认:问题定位是否符合验证跑目的、参考差距解释是否准确、结果与限制的表述是否充分。报告终审不替代后续陌生人冷启动测试或发布终审。
