# 学习准备

这页是给本科新生的外部资料导航,不是第二份教材,也不是本仓库的方法论。理论权威仍是根目录 [《计算机视觉科研入门指南》](../计算机视觉科研入门指南.pdf)。[Research-Pathways-for-Newcomers](https://github.com/Event-AHU/Research-Pathways-for-Newcomers) 只作拓展入口:它面向已经准备读专业书单的新生,不代替本页的选读范围,也不转移本仓库的方法论权威。本页不抄它的书单。

按下面的顺序走。每一节点只指定一份主资料和有限范围;自检通过就可以进入下一步,不必读完整本书或整门课。已经会的节点直接做自检,通过则跳过。练习代码、数据和权重放在本仓库之外,不要提交进本仓库,也不要写进 [example/](../example/PLAN.md)。

<a id="python"></a>

## Python

**起点**:会使用电脑,但从来没写过程序。不从 PyTorch 教程或《动手学深度学习》起步。

**主资料**:廖雪峰《Python 教程》,中文、免费、网页文字。[简介](https://liaoxuefeng.com/books/python/introduction/index.html) 写明面向零起点,受众包括会用电脑但没写过程序的人。旧地址 <https://www.liaoxuefeng.com/wiki/1016959663602400> 在 2026-10-23 打开后落到同一套教程;请以新地址为准。

**选读范围**:只读这些章节,不要把目录里的 Web、异步、数据库当成前置。

- [1. 简介](https://liaoxuefeng.com/books/python/introduction/index.html)
- [3. 安装 Python](https://liaoxuefeng.com/books/python/install/index.html)
- [4. 第一个 Python 程序](https://liaoxuefeng.com/books/python/first-program/index.html),含 [4.1 使用文本编辑器](https://liaoxuefeng.com/books/python/first-program/text-editor/index.html) 和 [4.2 输入和输出](https://liaoxuefeng.com/books/python/first-program/input-output/index.html)。这里的命令行模式就是后面要用的终端最低知识
- [5.1 数据类型和变量](https://liaoxuefeng.com/books/python/basic/data-types/index.html)、[5.3 list 和 tuple](https://liaoxuefeng.com/books/python/basic/list-tuple/index.html)、[5.4 条件判断](https://liaoxuefeng.com/books/python/basic/if/index.html)、[5.6 循环](https://liaoxuefeng.com/books/python/basic/loop/index.html)、[5.7 dict 和 set](https://liaoxuefeng.com/books/python/basic/dict-set/index.html)
- [6.1 调用函数](https://liaoxuefeng.com/books/python/function/call-function/index.html)、[6.2 定义函数](https://liaoxuefeng.com/books/python/function/define-function/index.html)、[6.3 函数的参数](https://liaoxuefeng.com/books/python/function/parameter/index.html)
- [9. 模块](https://liaoxuefeng.com/books/python/module/index.html) 和 [9.2 安装第三方模块](https://liaoxuefeng.com/books/python/module/install/index.html)
- [10.1 类和实例](https://liaoxuefeng.com/books/python/oop/class/index.html)。后面的 PyTorch 模型会写成类;高级面向对象编程先不读
- [13.1 文件读写](https://liaoxuefeng.com/books/python/io/file/index.html) 和 [13.3 操作文件和目录](https://liaoxuefeng.com/books/python/io/dir/index.html)。路径和目录的最低知识停在这里,不另写工具教程

**练习**:在本仓库目录之外建一个文件夹。写一个 `.py` 文件,用函数读取一个你自己做的整数文本文件,用 list 保存,打印个数与合计。在终端里 `cd` 到该文件夹后运行。再用 `pip` 安装一个第三方包并成功 `import`。不要把这个练习放进本仓库。

**自检**:不看教程也能解释缩进;能独立写出 `if` / `for` / 函数;能在终端进入脚本所在目录并运行;能读一个文本文件;能安装并导入一个第三方包。做不到的那一项就回到对应章节,不要整本重读。

**备选**:[Python 官方教程中文版](https://docs.python.org/zh-cn/3/tutorial/index.html) 只给已经会另一种编程语言的人。该页写明教程针对新入门 Python 的程序员,而不是新入门编程的初学者,所以不是零基础第一站。2026-10-23 读到的是 3.15.1 文档。

**限制**:免费网页,不需要账号即可阅读。安装包从该教程给出的来源下载;本仓库没有代为安装,也不核验每个操作系统的安装器。

**下一步**:[Git 与 GitHub](#git-github)。

<a id="git-github"></a>

## Git 与 GitHub

**起点**:能在终端运行自己的 Python 脚本。还不会 Git 也可以从这里开始。

**主资料**:本仓库指定的视频《Git+Github核心概念大串讲,从零到一全攻略,详细实战教程》,<https://www.bilibili.com/video/BV1ySLc6QEcB/>。2026-10-23 公开页面与 view API 可读:作者「技术爬爬虾」,单分 P,API 记录时长 2570 秒,发布时间为 2026-05-18。简介写的是先讲本地仓库的提交、分支、分区,再讲 GitHub 的基础用法、多人协作和向开源项目贡献代码。

**选读范围**:看这一集公开视频。视频内章节和时间戳没有核验——没有播放,公开字幕列表为空,API 也没有分 P 章节——所以不要把任何时间戳当成已核对内容。本节点要达到的最低能力不来自编造的视频目录,而来自下面的练习。不要求 rebase,也不要求向别人的仓库贡献代码。

**练习**:用自己的免费 GitHub 账号新建一个空仓库,不要使用本仓库。在那个独立仓库里完成:

1. `git clone`
2. 改一个文本文件,用 `git diff` 看改动,再 `git add` 与 `git commit`
3. `git push`
4. 建一个 branch,把另一次提交推上去,并向该仓库自己的默认分支开一个 pull request

练习文件留在那个仓库。不要把练习提交进本流程仓库。

**自检**:能在自己的仓库里指出哪一次是 commit、哪一个是 branch、pull request 对比的是哪两个分支。说不出就回到备选文字页对应小节,而不是假设视频某一分钟讲过。

**备选**:视频打不开、播放器要求登录,或某一步在视频里对不上时,用下面两份已核对的中文文字,不要把它们的章节号说成视频章节。

- 命令对照:廖雪峰《Git 教程》[简介](https://liaoxuefeng.com/books/git/introduction/index.html) 写明面向没接触过版本控制的初学者。只读 [3. 安装 Git](https://liaoxuefeng.com/books/git/install-git/index.html)、[4. 创建版本库](https://liaoxuefeng.com/books/git/create-repo/index.html)、[5.2 工作区和暂存区](https://liaoxuefeng.com/books/git/time-travel/working-stage/index.html)、[5.3 管理修改](https://liaoxuefeng.com/books/git/time-travel/add/index.html)(正文有 `git diff`)、[6.1 添加远程库](https://liaoxuefeng.com/books/git/remote/add-remote/index.html)、[6.2 从远程库克隆](https://liaoxuefeng.com/books/git/remote/clone/index.html)、[7.1 创建与合并分支](https://liaoxuefeng.com/books/git/branch/create/index.html)。[7.7 Rebase](https://liaoxuefeng.com/books/git/branch/rebase/index.html) 不在最低要求里
- 在自己的仓库开 pull request: [GitHub 文档《创建拉取请求》](https://docs.github.com/zh/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request)。廖雪峰 [9. 使用 GitHub](https://liaoxuefeng.com/books/git/github/index.html) 讲的是 Fork 别人的项目再发 pull request,那是额外练习,不是本节点的最低要求

**限制**:需要免费 GitHub 账号才能完成 clone / push / pull request。视频播放本身没有核验;若播放器拦截,保留链接并改用备选文字,不要绕过登录。廖雪峰克隆页的示例是 SSH 地址,同一页也给出 HTTPS;推送时的登录方式以 GitHub 当时页面为准,这里不编造口令或 token 步骤。该教程示例里的默认分支常写作 `master`,你的新仓库可能是 `main`,以你实际看到的默认分支名为准。

**下一步**:[环境与依赖](#env)。

<a id="env"></a>

## 环境与依赖

**起点**:能在终端进入一个目录并运行 Python。路径、目录和终端的最低练习已经在 Python 节点完成,这里不重写一份终端教程。

**主资料**:conda 官方 [Getting started with conda](https://docs.conda.io/projects/conda/en/stable/user-guide/getting-started.html),英文、免费网页。只读四小节:Before you start、Starting conda、Creating environments、Installing packages。

**选读范围**:知道怎样打开终端、怎样 `conda create`、怎样 `conda activate`、怎样往环境里安装一个包,以及怎样记下实际版本。不读通道配置、整站更新或图形界面 Navigator。

**练习**:按主资料 Before you start 选择一个最小安装器。优先看它点名的 Miniforge,而不是默认走进 Anaconda 仓库。安装步骤用备选里的 Miniforge 说明。然后新建一个只属于这次练习的环境,激活它,用 `pip` 或 `conda` 安装一个小包,把 `conda list` 或 `pip show` 里的实际版本记下来。不要复用别人机器上的 `cvpractice` 环境,也不要在这一步安装本仓库 Phase 0 的那套钉死版本——那是进入正式闭环之后的事。

**自检**:能说出当前终端在哪个目录、Python 来自哪个环境;能把刚装上的包名和版本写下来。写不出版本,就还不能进入下一步。

**备选**:[Miniforge README 的 Install 一节](https://github.com/conda-forge/miniforge#install)。用途只有一个:完成主资料要求的最小安装器,并使用它默认的 conda-forge 通道。主资料写明,从 Anaconda 仓库安装包可能受其服务条款约束,个人和高校等情形有例外;本仓库不解释该条款是否适用于你,只要求你读那一节原文再选择安装器。

**限制**:两份资料都是英文。本交付没有安装 conda,也没有核验每个操作系统的安装器。国内镜像页本次没有读到可用正文,所以不指定镜像。

**下一步**:[数学与深度学习,按需补齐](#math-dl)。

<a id="math-dl"></a>

## 数学与深度学习,按需补齐

**起点**:能读简单的中文技术读物。不要求先修完线性代数、微积分或概率课程,也不把这节点当成第一次动手的闸门。

**主资料**:邱锡鹏《神经网络与深度学习(通识版)》,中文、免费的出版前电子稿。入口页 <https://nndl.github.io/nndl-ge/>,PDF <https://github.com/nndl/nndl/releases/download/book-pdf/nndl-ge.pdf>。2026-10-23 核对到 PDF 文件头为 PDF-1.7,封面日期为 2026 年 9 月 7 日。前言写明它面向不想被公式拦住的非专业读者,数学从高中知识起,第一次阅读可以先跳过公式。

**选读范围**:读前言里作者自己的读法,再读第 1–4 章。前言把第 1 章当作全貌,把第 2–4 章当作学习、网络和训练三块地基。目录标题是:

- 第 1 章 智能的第三次浪潮
- 第 2 章 机器如何自己学习
- 第 3 章 从神经元到深度网络
- 第 4 章 把网络训好的艺术

第 2 章里优先看 2.2、2.4、2.5 的标题所在小节。第 5 章以后留到以后真的碰到注意力、大模型或强化学习时再读,不是准备实践的前置。这不是本仓库的理论指南,纸质版页面写着即将出版。

**练习**:合上 PDF,用自己的话各写一句:数据或例子、模型、训练、评估分别在你读到的章节里指什么。写不出的那一句就回到对应章,不要靠猜,也不要先去背公式。

**自检**:能不看 PDF 说出第 2–4 章各自在回答什么问题。做不到就只补那一章。做到了就可以去 PyTorch,即使线性代数还没系统学过。后面哪一个符号挡住了你,再回到这一章或备选,不要把整条路线停住。

**备选**:《动手学深度学习》中文版 [2.3 线性代数](https://zh.d2l.ai/chapter_preliminaries/linear-algebra.html)、[2.4 微积分](https://zh.d2l.ai/chapter_preliminaries/calculus.html)、[2.6 概率](https://zh.d2l.ai/chapter_preliminaries/probability.html)。用途只有一个:准备实践或 PyTorch 里某个符号确实看不懂,而通识版不够用时再查对应一节。它的[前言](https://zh.d2l.ai/chapter_preface/index.html)假定读者已有线性代数、微积分、概率和很基础的 Python,所以不是零基础第一站,也不是本节点的主资料。站点同时有 MXNet、PyTorch、TensorFlow、Paddle 标签,查的时候选 PyTorch。

**限制**:通识版是出版前电子稿,不是稳定教材版本;本次没有逐页核验第 5 章以后的正文。不要把 Event-AHU 仓库里的其他书单当成必读。

**下一步**:[PyTorch](#pytorch)。

<a id="pytorch"></a>

## PyTorch

**起点**:Python 自检已通过,并且能用自己的话说明模型、训练和评估。还不会这些就回到上一节点,不要用本节点代替零基础编程。

**主资料**:PyTorch 官方 [Learn the Basics](https://docs.pytorch.org/tutorials/beginner/basics/intro.html),英文、免费网页。该页写明假定读者已有基本的 Python 和深度学习概念,并使用 FashionMNIST,不是 MNIST。2026-10-23 页面标注 Last Updated 为 2026-01-20。

**选读范围**:该页写明,不熟悉其他深度学习框架的人从 Tensors 开始,而不是从 Quickstart 开始。只读这六节:

1. [Tensors](https://docs.pytorch.org/tutorials/beginner/basics/tensorqs_tutorial.html)
2. [Datasets and DataLoaders](https://docs.pytorch.org/tutorials/beginner/basics/data_tutorial.html)
3. [Transforms](https://docs.pytorch.org/tutorials/beginner/basics/transforms_tutorial.html)
4. [Build Model](https://docs.pytorch.org/tutorials/beginner/basics/buildmodel_tutorial.html)
5. [Automatic Differentiation](https://docs.pytorch.org/tutorials/beginner/basics/autogradqs_tutorial.html)
6. [Optimization Loop](https://docs.pytorch.org/tutorials/beginner/basics/optimization_tutorial.html)

Save & Load 可以以后再读。Quickstart 留到准备实践,避免在两个节点各跑一遍却分不清目的。

本地安装走 [PyTorch 官方本地安装页](https://pytorch.org/get-started/locally/)。2026-10-23 抓取该页时得到的是页面壳,没有核验到可选命令,所以这里不抄安装命令。在浏览器里打开该页,有加速设备也不必为准备实践选择 CUDA;没有加速设备就选 CPU。把页面当时给出的命令和你实际装上的版本记下来。文档站角标里的 `v2.14.0+cu130` 是文档版本标签,不是要求你安装 CUDA 13。Colab 需要 Google 账号,不是本路径的必经步骤。

**练习**:不要求在这一节点跑完整训练。对着六节页面,各用一句话写出 tensor、Dataset、`nn.Module`、loss、optimizer 在教程里做什么。句子必须对应你打开的页面,不要抄本页没有核验过的数字。

**自检**:五句话都能指回具体小节。指不回就重读那一节。能指回就可以进入准备实践,即使还没跑出准确率。

**备选**:读不懂英文时,用《动手学深度学习》的 PyTorch 标签对照同一类概念:[2.1 数据操作](https://zh.d2l.ai/chapter_preliminaries/ndarray.html)、[3.5 图像分类数据集](https://zh.d2l.ai/chapter_linear-networks/image-classification-dataset.html)、[3.7 softmax 回归的简洁实现](https://zh.d2l.ai/chapter_linear-networks/softmax-regression-concise.html)。3.5 明确写的是 Fashion-MNIST,不是 MNIST,并区分训练集与测试集。不要跟着 [安装](https://zh.d2l.ai/chapter_installation/index.html) 里的旧版本钉死命令走——该页示例仍是 Python 3.9 与 `torch==1.12.0`,那不是本仓库 Phase 0 的版本,也没有在本次交付里实跑。站点默认还展示 MXNet 代码,必须切到 PyTorch 标签。

**限制**:主资料是英文。安装命令未从官方安装页核验到正文。本交付没有安装 PyTorch,也没有跑教程。

**下一步**:[准备实践](preparation-practice.md)。

<a id="prep"></a>

## 准备实践

按 [CPU 准备实践](preparation-practice.md) 做。运行前先复制 [记录模板](../templates/preparation-log-template.md)。这一步仍然不是五个 Phase,也不是 Phase 0。

<a id="formal"></a>

## 进入正式闭环

只有下面四项都成立,才回到 [README 的五个 Phase](../README.md#phases):

1. 需要走的准备节点都已按自检通过,或已有基础并在自己的笔记里写下跳过理由
2. 读完 [理论指南](../计算机视觉科研入门指南.pdf)。本 README 的方法论只引用它的章节号
3. 会在终端里进入目录、用 Git 做 clone / diff / commit / push / branch / pull request、用 conda 激活环境并说出版本
4. 知道 Phase 0 的退出标准仍是原文:PyTorch 能看到 GPU 或 MPS,并且冒烟测试的前向传播跑通。CPU 准备实践不满足这一条

然后按 README 建立 `runs/<任务>/`,复制计划模板,从 Phase 0 开始。正式实验仍先建 [实验记录](../templates/experiment-log-template.md),不要用准备实践的短记录代替。

<a id="sources"></a>

## 资料核查记录

核查日期:2026-10-23(美东)。部分响应的 HTTP `Date` 为 2026-10-24 UTC。网页可读不等于视频已观看或教程已实跑。

| 资料 | URL | 本次读到的正文 | 限制 |
|---|---|---|---|
| 廖雪峰 Python 简介与目录 | <https://liaoxuefeng.com/books/python/introduction/index.html> | 零起点说明;目录第 1–25 部分。安装、第一个程序、安装第三方模块、类和实例、文件与目录等页返回了正文,不是空壳 | 未逐页核验未列入选读范围的后半本 |
| Python 官方教程 | <https://docs.python.org/zh-cn/3/tutorial/index.html> | 明确不是编程初学者教程;3.15.1 | 只作已会编程者的备选 |
| 指定 Git 视频 | <https://www.bilibili.com/video/BV1ySLc6QEcB/> | 页面与 view API:标题、作者、单分 P、2570 秒、简介 | 未播放;无公开字幕;无章节时间戳。`pay` / `ugc_pay` / `arc_pay` 为 0,不能据此声称已确认免登录可播 |
| 廖雪峰 Git | <https://liaoxuefeng.com/books/git/introduction/index.html> | 初学者定位与目录。克隆、管理修改、创建分支、使用 GitHub 四页读到了命令正文 | 示例默认分支常为 `master`;不把这些章节当成视频章节 |
| GitHub 创建拉取请求 | <https://docs.github.com/zh/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request> | 中文步骤:从自己的分支创建 pull request | 需要 GitHub 账号 |
| conda Getting started | <https://docs.conda.io/projects/conda/en/stable/user-guide/getting-started.html> | create / activate / install,以及 Anaconda 服务条款提示 | 英文;未安装 |
| Miniforge | <https://github.com/conda-forge/miniforge> | README 的安装说明与 conda-forge 默认通道 | 英文;未运行安装器。`conda-forge.org/download` 本次读取不完整,故不引用 |
| 通识版 | <https://nndl.github.io/nndl-ge/> 与 PDF | 网页章节目录;PDF 前言和目录第 1–4 章标题 | 出版前电子稿。第 5 章以后未逐页核验 |
| 《动手学深度学习》 | <https://zh.d2l.ai/chapter_preface/index.html> | 前言受众;安装页的旧版本钉死命令;3.5 写明 Fashion-MNIST 与训练/测试划分 | 有数学和 Python 前提;多框架标签;安装命令过时。不是零基础第一站 |
| PyTorch Learn the Basics | <https://docs.pytorch.org/tutorials/beginner/basics/intro.html> | 受众、FashionMNIST、建议从 Tensors 而不是 Quickstart 开始 | 英文;未实跑 |
| PyTorch Quickstart | <https://docs.pytorch.org/tutorials/beginner/basics/quickstart_tutorial.html> | FashionMNIST、`CrossEntropyLoss`、train / test、CPU 回退句、可改的 `batch_size` / `lr` / `epochs` | 页面示例输出写着 CUDA 和一次运行的准确率,不是读者结果。详见准备实践 |
| PyTorch 本地安装页 | <https://pytorch.org/get-started/locally/> | 抓到页面壳,没有可选命令正文 | 不抄安装命令 |
| Event-AHU 导航仓 | <https://github.com/Event-AHU/Research-Pathways-for-Newcomers> | README 是书单和课程导航,受众强于本页 | 只作拓展,不抄书单,不转移理论权威 |
