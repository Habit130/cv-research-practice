# IEEEtran 验证跑报告

[main.tex](main.tex) 是作者验证跑的报告源码,[report.pdf](report.pdf) 是同一源码的编译产物。报告的问题定位、观点与叙事已获作者终审批准(#10 Completed,2026-10-16;PR #15 已合并至 `79c864a919024508560ea5a28b84211a5665c915`);该批准不替代仓库发布终审,发布状态见 [PLAN.md](../PLAN.md) 的进度记录。

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

PR #15 交付时已执行数字抽查:95.45% → EXP-05、95.11% → EXP-06、86.88% → EXP-07,三处均与原记录及真实 epoch 200 日志一致。表中差值、loss、耗时和累计值也与 EXP-07 对照表核对一致。报告没有新增训练或评测结果。

## 本机验证与作者终审

本机编译与视觉检查(PR #15 交付时执行,属 PR #15 的历史验证记录;文档收尾 #22 时只同步状态说明、未重跑编译,本次 #24 重编译见下节):TeX Live 2026、XeLaTeX、latexmk 4.88 编译;PDF 两页,逐页渲染检查中文、公式、双栏、表格和参考链接。源码引用均已解析,未出现 missing character 或 overfull box。文档未进行 MPS/CUDA 新评测,沿用实验记录中的真实硬件证据。

作者终审(已完成):问题定位符合验证跑目的、参考差距解释准确、结果与限制的表述充分。报告终审不替代仓库发布终审;发布收尾与待办见 [PLAN.md](../PLAN.md) 的进度记录与 issue #12。

## 2026-10-16 终审状态同步与重编译(#24)

- 状态同步:[main.tex](main.tex) 作者栏、摘要与结语中残留的"作者终审草稿 / 仍待作者终审"句已同步为 #9/#10 已批准,区分报告形成日期 2026-10-10 与批准日期 2026-10-16,不把原报告写成 2026-10-10 当时已获批准;科学叙事、实验方法、全部指标/config、条件化结论与限制、五个固定实验引用 commit 未改。
- 实际重编译(2026-10-16,本机 TeX Live 2026;latexmk 4.88、XeTeX 3.141592653):命令与上文编译节一致,exit 0;日志无未解析引用、无 missing character、无正文 overfull/underfull box;仅剩已知的初始 ptm 字形替换(4 处,回退 lmr)与 FandolSong small-caps 替换(1 处)字体警告,非阻塞。
- 产物:[report.pdf](report.pdf) 与本次构建产物 `build/main.pdf` 逐字节一致(SHA-256 `5f6927cf041d50bf2798ef1df63079e644d5117f0cb490b7793a0dd839f4a64a`),PDF 两页。
- 提取与链接核对(Poppler 26.06.0 的 pdftotext/pdftoppm/pdftohtml):95.45%、95.11%、86.88%、-0.34pp、-8.57pp、0.1730995893/0.2076407678/0.3807913750、5.2115/5.1725/5.0133 h、累计 55430.08841 s 均与 EXP-05/06/07 一致;五个实验参考链接仍指向 `4fd32e0c87a76b9ecf975837b243b8dd3199daf0`;旧状态句在提取文本中不再出现。
- 逐页视觉检查:两页均以 150 dpi 实际渲染并逐页查看,中文、公式、双栏与表格可读,作者栏/摘要/结语显示新批准状态,无裁切或重叠。
- 边界:本次只做文档状态同步、重编译与阅读检查,未重跑训练/评测,不是新的 MPS/CUDA 验证,不改变任何实验证据;渲染图与提取文本只在本地 `build/`(已 Git 排除),不入库。
