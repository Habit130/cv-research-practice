# Agent 模拟静态走查:零基础读者路径

> **性质声明(先读)**:这是维护者测试记录,不是读者教程,也不是第二份学习导航。本次是 **DeepSeek Agent 模拟的零基础视角静态文档走查**,不是真实读者试读,不是安装/实操验证,不是视频观看记录,不是硬件或训练验证。样本为 1 个模型、1 次尝试;所有"读者会遇到"均为静态推断,未由真人验证。未安装 Python、Git、conda 或任何依赖,未执行任何学习练习命令,未创建练习仓库/PR,未登录第三方,未播放视频,未训练/评测/编译。

| 项目 | 值 |
|---|---|
| 契约 | Issue #32 `#32-v1` / attempt 1 / Execution |
| 被测 commit | `dd27de016684aeffa270c2bbb3b4469c485ea021` |
| Base | `origin/main` @ 同一 commit(测试时 `git fetch origin --prune` 后核对,main 未前进) |
| Branch | `docs/newcomer-agent-walkthrough` |
| Worktree | 仓库根目录下 `.local/worktrees/newcomer-agent-walkthrough/`(本机绝对路径见 Issue #32 交回) |
| 测试日期 | 2026-10-25(本地 EDT) |
| 客户端 / 模型 | OpenCode harness;显示名 `DeepSeek V4.1 Flash`;provider `opencode-go`;model ID `deepseek-v4.1-flash`;精确服务端 revision 未知 |
| Tracker 身份 | `gh` 认证为 habit 的 GitHub 账号 `Habit098`;不代表模型自身是 GitHub 用户 |
| 记录类型 | 维护者测试记录(非读者教材) |
| 独立验收 | required(由 habit 另启;本记录不自评通过) |

## 摘要

- 读者入口链完整:根 `README.md` 把零基础读者分流到 `learning/README.md`,`learning/README.md` 的 Python → Git 与 GitHub → 环境与依赖三节顺序、锚点、下一步衔接均存在且可达;三节的主资料入口、练习、自检、下一步四类内容都有明确落点。
- 指定外部主资料与备选均可读(正文完整、无需登录;用 `curl` 核对的页面返回 HTTP 200);视频只核公开页面与 view API,**未播放**,因此视频内章节/时间戳一律记为 `not checked`。
- 静态走查记录了 6 条候选文档缺口(PY-1、GIT-2、ENV-1、PY-2、GIT-1、ENV-3),外加 1 条前置缺口(GIT-4)、1 条已声明限制(GIT-3)、1 条外部资料时效(PY-3)与 3 条非缺陷观察。候选缺口不等于真实读者失败;本次不设"必须发现问题"的门槛,也不据此声称读者可用性已验证。
- 三节点的练习与自检**均未实操**:Python 未安装/未写脚本,Git 未登录/未克隆/未推送,环境未安装 conda/未建环境。后两节点是在"前置未实测、条件性继续"下检查的,不能写成自检通过。
- `access blocked` 计数为 0;视频未播放属于协议要求,不属于访问失败。

最值得先看的三条:

1. **PY-1(中)**:Python 练习要求读取"整数文本文件"并打印合计,但选读范围没有给出文本→整数的转换(`int(...)`)示例;按指定资料读完,读者只能靠先验补齐。
2. **GIT-2(中)**:Git 练习要求"建一个 branch,把另一次提交推上去",但指定文字备选没有 `git push` 新分支的步骤;GitHub 的 PR 文档以"分支已在远端"为起点,视频又未核验。
3. **ENV-1(中低)**:环境练习要求用 `conda list` 或 `pip show` 记录包版本,但 conda 指定四小节没有包版本查看的示范;页面别处的 `conda --version` 查的是 conda 自身版本,不是已安装包。

## 测试身份、上下文与污染声明

本会话由 habit 在本轮新启动,启动提示声明为 Execution,并要求先读冻结 handoff、不预读历史测试结论。本会话未复用旧会话输出,未委派其他模型代测;实际可见的模型/客户端如上表。精确服务端模型 revision、量化与上下文长度在本地不可见,记为未知,不猜测。

实际注入或预读的上下文:

| 来源 | 类型 | 是否构成读者路径证据 |
|---|---|---|
| `~/.config/opencode/AGENTS.md` | 全局协作与权限边界 | 否 |
| 仓库 `AGENTS.md` | 仓库角色/Git/审查规则 | 否 |
| 环境信息块(目录、平台、日期)与 skill 清单 | harness 注入 | 否 |
| `docs/orchestration/handoffs/AC-32-v1-a1-execution.md` | 冻结 handoff | 否 |
| `docs/agents/delivery.md`、`issue-tracker.md`、`research-practice.md` | 治理规则 | 否 |
| Issue #32 正文与 1 条 Orchestration 冻结评论 | 契约/控制面 | 否 |
| `.local/newcomer-agent-test-contract-v1.md` | 完整契约快照 | 否 |

未读取(按契约禁止):`.local/agent-context.md`、旧交回/验收/测试报告、Issue #7/#11/#28/#30 的讨论、任何候选缺陷清单。未发现自动注入的历史交付/验收内容。

**非纯读者条件(必须保留)**:治理与契约预读使本会话知道仓库结构、审查规则和本轮测试范围;模型已有编程知识也无法消除。因此报告中的每条判断都尽量指向被测文档或其链接资料,并用 `documented` / `inferred` / `not checked` 区分来源;不声称"模型已经忘记编程",也不把先验知识算作文档覆盖。

### 测试前记录

在访问被测 `README.md` 与任何学习页之前,先在 worktree 的忽略路径建立测试记录:

- 路径(worktree 内,已被 Git 忽略):`.local/newcomer-walkthrough/raw-observations.md`
- 建立时间:2026-10-25 22:14:59(本地);预置协议/模型/上下文/被测 commit 的时间戳为 22:14:37
- 忽略核验:`git check-ignore -v` 命中 `.gitignore:2:.local/*`
- 原始记录 SHA-256:`3e87d0c154b60af9149a87709458638dae9c0a4fe2c2e7b07ad22ac7ac645626`(39,411 bytes,316 行)

该 digest 只是记录完整性指针,不是绝对时间戳或不可篡改证明;报告中的摘要可在该文件中按 O1–O13 编号复核。原始记录不提交,也不是公开可访问链接。

## 协议与实际执行

1. **读者路径**:只从被测 `README.md` 正文链接进入 `learning/README.md`,依次检查 Python → Git 与 GitHub → 环境与依赖。没有用仓库搜索、维护者文档或先验经验补学习步骤。
2. **节点覆盖**:每节点检查主资料入口/范围、练习、自检、下一步;练习按子步骤拆分,逐项标注。
3. **证据分类**:
   - `documented`:被测文档或其指定链接资料明确给出,有定位;
   - `inferred`:模型自行推断/既有知识,不计为文档覆盖;
   - `not checked`:未读到/未验证;
   - `access blocked`:访问失败,单列(本次为 0)。
4. **条件性继续**:前节点未实操,后续节点只做条件性检查;没有把"继续检查"写成"前节点通过"。
5. **有界阅读**:只读学习页指定章节/备选;不扩展全网搜索,不读整本书。Git 视频不播放,只用公开页面与 view API;文字备选不冒充视频章节。
6. **只观察不修复**:未改 README、learning、模板、example、runs、CONTEXT、AGENTS/CLAUDE、ADR 或其他 docs;未安装、未运行教程命令、未创建练习仓库/PR、未登录第三方。
7. **记录顺序**:原始观察按访问顺序写入上述文件;外部页面抓取副本另存 `.local/newcomer-walkthrough/pages/`。页面抓取时间以文件 mtime 为准;未落盘的 webfetch 与本地阅读在原始记录中用 `≈` 标注,不虚构秒数。环境节点的两份页面在 Git 节点观察写入前已抓取,原始记录已注明,阅读与判定在之后完成,未据此改动先前记录。

## 三节点覆盖矩阵

状态含义:`documented` 有文档定位;`inferred` 需先验/外部补充;`not checked` 未覆盖;`access blocked` 访问失败。所有"练习/自检"均未实操,状态只表示文档覆盖,不表示读者已会。

### 入口:`README.md`

| 检查项 | 状态 | 主要定位 | 说明 |
|---|---|---|---|
| 零基础分流 | documented | `README.md` L7、L25、L29–L30 | 明确"不要从五个 Phase 直接进入",先到学习准备;不假定会 Git/conda |
| 三节点顺序 | documented | L38–L48 | 表格第 1–3 行分别指 `#python`、`#git-github`、`#env`;锚点存在 |
| 资料/练习/自检在本页 | not checked | L32、L38 | 本页明确"细节、资料和自检在 learning/README.md";不在入口页展开 |
| 下一步 | documented | L32 | "读者的下一步在学习页和准备实践页,不在维护者文档里" |
| 硬件前提 | documented | L62–L80 | 学习准备只需普通电脑;准备实践默认 CPU;Phase 0 才涉及 MNIST/固定版本 |

### 节点 1:Python

| 检查项 | 状态 | 主要定位 | 说明 |
|---|---|---|---|
| 主资料入口 | documented | `learning/README.md` L13 | 廖雪峰《Python 教程》;外部简介确为"中文,免费,零起点" |
| 选读范围 | documented | L15–L24 | 列出 18 个章节/小节 URL;本次按最低练习读了其中 11 页,其余未读项见"未覆盖" |
| 练习:仓库外建文件夹 | documented | L26 | "在本仓库目录之外建一个文件夹" |
| 练习:写 `.py` 并保存 | documented | 外部 4.1 文本编辑器 | 推荐 VS Code;保存为 `.py`;提醒不要用 Word/记事本 |
| 练习:用函数读取整数文本文件 | **部分**:读取 `documented`,整数转换 `inferred` | 外部 6.2、13.1、5.1 | `def`/`open`/`readlines`/`strip` 有示例;`int(...)` 在指定范围内没有示例 |
| 练习:用 list 保存 | documented | 外部 5.3 | list 定义、索引、`append`、`len` |
| 练习:打印个数与合计 | **部分**:个数 `documented`,合计依赖整数列表 | 外部 5.3、5.6 | `len` 与 for 累加有示例,但示例直接对整数 list 求和 |
| 练习:终端 `cd` 后运行 | documented | 外部 4、4.1 | `cd work`、`python hello.py`;找不到文件时切目录 |
| 练习:`pip` 安装第三方包 | documented | 外部 9.2 | `pip install Pillow`;macOS/Linux 通常自带 pip |
| 练习:`import` 成功 | **部分**:import 概念 `documented`,安装名/导入名对应 `inferred` | 外部 9.2 | 页面只写"就可以使用Pillow了";未写 `import PIL` |
| 自检:缩进 / if / for / 函数 | documented | 外部 5.1、5.6、6.2 | 有语法与示例;未实操验证 |
| 自检:终端进入目录运行 | documented | 外部 4、4.1 | 有命令与报错解释 |
| 自检:读文本文件 | documented | 外部 13.1 | `open`/`read`/`readlines`/`with` |
| 自检:安装并导入第三方包 | **部分**:依赖 PY-2 | 外部 9.2 | 概念有,名称对应无 |
| 下一步 | documented | L34 | `#git-github` 锚点存在 |
| 备选 | not checked | L30 | 官方中文教程明确"只给已会另一种语言的人",不属于零基础第一站;本次未读正文 |

### 节点 2:Git 与 GitHub

| 检查项 | 状态 | 主要定位 | 说明 |
|---|---|---|---|
| 主资料入口 | documented | `learning/README.md` L42 | B 站视频;页面/API 可读,标题、作者、单 P、2570 秒、发布日与学习页一致 |
| 主资料内容/章节 | not checked | L42–L44 | **未播放**;无公开字幕,API 无分 P 章节;学习页明确不把时间戳当已核对内容 |
| 选读范围 | documented | L44 | "看这一集公开视频";最低能力由练习定义;不要求 rebase/贡献 |
| 练习:免费 GitHub 账号 | **前置缺口** | L46、L62 | 只说明"需要免费 GitHub 账号",未给注册入口/步骤;记为 GIT-4 |
| 练习:新建空仓库 | documented | 外部《从远程库克隆》 | "登陆GitHub,创建一个新的仓库";可勾选 README(与"空仓库"措辞略异) |
| 练习:`git clone` | documented | 外部《从远程库克隆》 | SSH 与 HTTPS 地址都提到 |
| 练习:改文本文件 | documented | Python 节点 4.1 | 编辑器保存纯文本;本节点未重复,但在读者路径上可达 |
| 练习:`git diff` | **部分** | 外部《管理修改》 | 正文只有 `git diff HEAD -- readme.txt`;没有单独的 `git diff` 示例;记为 GIT-1 |
| 练习:`git add` / `git commit` | documented | 外部《创建版本库》《工作区和暂存区》 | 暂存区、status、commit |
| 练习:`git push` | **部分** | 外部《添加远程库》 | `git push -u origin master` 有;HTTPS 认证无步骤(仓库已声明不覆盖);记为 GIT-3 |
| 练习:建 branch | documented | 外部《创建与合并分支》 | `git checkout -b dev` / `git switch -c dev` |
| 练习:推另一个提交到新 branch | **缺口** | L51;外部两页均无 | 没有 `git push origin <branch>` 示例;记为 GIT-2 |
| 练习:向默认分支开 PR | documented(前提分支已在远端) | 外部 GitHub《创建拉取请求》 | base/compare、Compare & pull request、Create;PR 只能在两分支间开 |
| 自检:commit/branch 概念 | documented | 外部 Git 页 | 提交、分支、HEAD、合并 |
| 自检:PR 对比哪两个分支 | documented | GitHub 文档第 4 步 | base / compare 下拉 |
| 下一步 | documented | L64 | `#env` 锚点存在 |
| 备选 | documented | L57–L60 | 7 个廖雪峰 Git 章节 + GitHub PR 文档;均读到正文;rebase 明确不在最低要求 |

### 节点 3:环境与依赖

| 检查项 | 状态 | 主要定位 | 说明 |
|---|---|---|---|
| 主资料入口 | documented | `learning/README.md` L72 | conda 官方 Getting started,英文免费网页 |
| 选读范围 | documented | L74 | 只读 Before you start / Starting conda / Creating environments / Installing packages |
| 主资料正文 | documented | 外部 conda 四节 | 四节均可读;Before you start 点名 Miniconda/Miniforge,并说明 Anaconda 仓库 ToS |
| 练习:选最小安装器 | documented | 外部 Before you start;L76 | "use a minimal installer such as Miniconda or Miniforge";学习页要求优先 Miniforge |
| 练习:安装步骤 | documented | 外部 Miniforge `#install` | Unix `curl`/`wget` + `bash`;Windows exe;macOS PKG;交互安装提示 `conda init` |
| 练习:新建练习环境 | documented | 外部 Creating environments | `conda create --name <env-name>` |
| 练习:激活环境 | documented | 外部 Installing packages | `conda activate myenvironment` |
| 练习:安装一个小包 | documented | 外部 Installing packages | `conda install matplotlib`;也可 `pip`,页面未示范 pip in env |
| 练习:记录包版本 | **部分** | L74/L76/L78;外部四节 | 命令名 `conda list` / `pip show` 在仓库页有;四节没有包版本查看示范,四节外的 `conda --version` 查 conda 自身;记为 ENV-1 |
| 约束:不复用 `cvpractice` | documented | L76 | 明确"不要复用别人机器上的 cvpractice 环境" |
| 约束:不装 Phase 0 钉死版本 | documented | L76 | 明确留到正式闭环之后 |
| 自检:当前目录/Python 来源环境 | documented(条件性) | 外部 Starting conda、activate | 能说环境名;需要实际 shell,未实测 |
| 自检:包名与版本 | **部分** | L78;ENV-1 | 依赖未示范的版本查看 |
| 下一步 | documented | L84 | `#math-dl` 锚点存在 |
| 备选 | documented | L80;外部 Miniforge README | Install 一节存在;默认 conda-forge 通道;英文;未运行安装器 |

### 三节点衔接

- 锚点:`#python`、`#git-github`、`#env`、`#math-dl` 均在 `learning/README.md` 实际存在;根 README 的对应链接可定位。
- 顺序衔接:Python 下一步 → Git 与 GitHub;Git 与 GitHub 下一步 → 环境与依赖;环境与依赖下一步 → 数学与深度学习(未进入,超出本次范围)。
- 条件性:Python 未实操,因此 Git 节点的"起点:能在终端运行自己的 Python 脚本"未被本人验证;Git 未实操,因此环境节点的"起点:能在终端进入目录并运行 Python"同样未被验证。三节点均只能记为"文档覆盖检查",不是"读者已通过"。
- 入口页对"准备实践"的下一步(`learning/preparation-practice.md`)存在,但不在本次三节点范围,未读。

## 外部资料来源核对

访问日期均为 2026-10-25(本地 EDT);仓库学习页记录的核查日为 2026-10-23,两者不混用。所有页面均为公开可读,未绕过登录/付费/TLS;未下载安装器/PDF/数据。

| # | 资料 | URL | 实际读到的正文 | 限制 |
|---|---|---|---|---|
| 1 | 廖雪峰 Python 简介 | <https://liaoxuefeng.com/books/python/introduction/index.html> | "小白的Python新手教程";"中文,免费,零起点";面向"会使用电脑,但从来没写过程序" | 页面未显示更新日期;未读全书 |
| 2 | 安装 Python | <https://liaoxuefeng.com/books/python/install/index.html> | Windows 官网安装器 + PATH;macOS 官网/Homebrew;`python3` vs `python`;`>>>`/`exit()` | macOS 段"系统自带 Python 2.x"已过时(见 PY-3) |
| 3 | 第一个 Python 程序 | <https://liaoxuefeng.com/books/python/first-program/index.html> | 命令行模式 vs 交互模式;`cd` 后 `python hello.py`;No such file 解释 | Windows 示例为主;macOS 需自行换 `python3` |
| 4 | 使用文本编辑器 | <https://liaoxuefeng.com/books/python/first-program/text-editor/index.html> | 推荐 VS Code;保存 `.py`;`cd` 到目录运行;不用 Word/记事本 | 未安装/未实操 |
| 5 | 输入和输出 | <https://liaoxuefeng.com/books/python/first-program/input-output/index.html> | `print()` 多参数、`input()` 提示;变量 | 练习为 `1024 * 768`,未做 |
| 6 | 数据类型和变量 | <https://liaoxuefeng.com/books/python/basic/data-types/index.html> | 整数/浮点/字符串/布尔/空值;变量;`/`、`//`、`%` | **无 `int()`/`float()` 转换示例**(PY-1 依据) |
| 7 | list 和 tuple | <https://liaoxuefeng.com/books/python/basic/list-tuple/index.html> | list 定义、`len`、索引、`append`/`insert`/`pop` | 示例以字符串 list 为主 |
| 8 | 循环 | <https://liaoxuefeng.com/books/python/basic/loop/index.html> | for 遍历 list/range;while;break/continue;整数 list 求和 | 未读 5.4/5.7 |
| 9 | 定义函数 | <https://liaoxuefeng.com/books/python/function/define-function/index.html> | `def`、缩进、`return`、`None`、`from ... import`、`isinstance` | 无"函数内读文件"组合示例 |
| 10 | 文件读写 | <https://liaoxuefeng.com/books/python/io/file/index.html> | `open`/`read`/`close`/`with`;`readlines()` + `strip()`;编码/写文件 | 无文本行→整数的示范 |
| 11 | 安装第三方模块 | <https://liaoxuefeng.com/books/python/module/install/index.html> | `pip install Pillow`;"就可以使用Pillow了";Anaconda 段 `import numpy`;`sys.path` | 未写 `import PIL`;推荐 Anaconda 与下一节点取向不同 |
| 12 | Git 视频公开页 | <https://www.bilibili.com/video/BV1ySLc6QEcB/> | `<title>` 与学习页标题一致;页面 HTTP 200 | **未播放**;播放器/登录限制未核验 |
| 13 | Git 视频 view API | <https://api.bilibili.com/x/web-interface/view?bvid=BV1ySLc6QEcB> | title、owner `技术爬爬虾`、duration 2570、单 P、pubdate 2026-05-18、简介 | 仅元数据;`subtitle.list=[]`;无章节 |
| 14 | Git 安装 | <https://liaoxuefeng.com/books/git/install-git/index.html> | Homebrew / Xcode / 官网;`git -v`;`git config --global user.name/email` | macOS Xcode 段是旧界面;Homebrew 可用 |
| 15 | 创建版本库 | <https://liaoxuefeng.com/books/git/create-repo/index.html> | `git init`、`git add`、`git commit -m`;FAQ 仓库目录/路径错误 | 默认分支 `master` |
| 16 | 工作区和暂存区 | <https://liaoxuefeng.com/books/git/time-travel/working-stage/index.html> | stage/index、`git add`、`git commit`、`git status`;自动 master | 示例 `master` |
| 17 | 管理修改 | <https://liaoxuefeng.com/books/git/time-travel/add/index.html> | 修改→add→再修改→commit;`git status`;`git diff HEAD -- readme.txt` | **无单独 `git diff` 示例**(GIT-1) |
| 18 | 添加远程库 | <https://liaoxuefeng.com/books/git/remote/add-remote/index.html> | `git remote add origin git@...`;`git push -u origin master`;SSH fingerprint | SSH 视角;HTTPS 认证/新分支 push 无 |
| 19 | 从远程库克隆 | <https://liaoxuefeng.com/books/git/remote/clone/index.html> | GitHub 建仓(可勾 README);`git clone`;HTTPS 地址也可;HTTPS 每次需"口令" | "口令"说法过时;未教 PAT/credential manager |
| 20 | 创建与合并分支 | <https://liaoxuefeng.com/books/git/branch/create/index.html> | `git checkout -b dev`、`git branch`、`git switch -c`、`git branch -d` | **无 `git push` 新分支**(GIT-2) |
| 21 | GitHub 创建拉取请求 | <https://docs.github.com/zh/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request> | PR 只能在两分支间;需头分支写权限;Branch 菜单→Compare & pull request→base/compare→Create;CLI/Desktop/Codespaces | 以分支已在 GitHub 为前提;页面无更新日期 |
| 22 | conda Getting started | <https://docs.conda.io/projects/conda/en/stable/user-guide/getting-started.html> | 四节:Miniconda/Miniforge、Terminal、`conda create`、`conda activate`、`conda install`;Anaconda ToS 提示 | 英文;无 `conda list`/`pip show`;未安装 |
| 23 | Miniforge README | <https://github.com/conda-forge/miniforge#install> | Install 一节:Unix `curl`/`wget` + `bash`;Windows exe;macOS PKG;`conda init` 提示;默认 conda-forge 通道 | 英文;`#install` 只核 raw README 标题,未在渲染页确认跳转;未运行安装器 |

访问限制汇总:`access blocked = 0`。评论/发帖需要登录(未使用),视频播放未核验(协议不要求),GitHub 写操作未执行(禁止实操),第三方登录未使用。

## 候选卡点

候选卡点按"来源位置 → 触发 → 可能影响 → 替代路径 → 分类"记录。强度是静态文档判断,不是真实读者反应;允许零卡点,以下不是"必须修复"清单。

| ID | 位置 | 触发 | 可能影响 | 可用替代路径 | 分类 / 强度 |
|---|---|---|---|---|---|
| **PY-1** | `learning/README.md` L26 练习 vs L20 选读范围;外部 5.1、13.1、5.6 | 按练习读取"整数文本文件",用 list 保存并打印合计 | 读出的行是字符串,直接求和会拼接或 `TypeError`;读者不知道要补 `int(...)`,也不知道该回哪一节 | 模型先验 `int(line)`;或读未列入范围的 5.2/官方文档 | 候选文档缺口 / 中 |
| **GIT-2** | `learning/README.md` L51;外部《创建与合并分支》《创建拉取请求》 | 本地 `checkout -b` + commit 后,要把分支推上去并开 PR | 不知道 `git push origin <branch>`;GitHub 上看不到分支,PR 页也无从 compare;视频未核验 | 从 `git push -u origin master` 迁移(`inferred`);或查 GitHub 文档 | 候选文档缺口 / 中 |
| **ENV-1** | `learning/README.md` L74/L76/L78;conda 四节;Miniforge README | 按练习用 `conda list` 或 `pip show` 记录包版本 | 四节没有包版本查看示范;读者可能把页面别处的 `conda --version` 当成包版本,或读不懂输出;自检"写出版本"可能被误判 | 练习已给出命令名,可直接运行;输出表通常自解释(`inferred`) | 候选文档缺口 / 中低 |
| **PY-2** | `learning/README.md` L26;外部 9.2 | 按 9.2 唯一示例 `pip install Pillow` 后写 `import Pillow` | `ModuleNotFoundError`;读者可能以为安装失败 | 选安装名=导入名的包(`requests`/`numpy`,`inferred`);或查 PyPI 的 `PIL` | 候选文档缺口 / 中低 |
| **GIT-4** | `learning/README.md` L46、L62 | 零基础读者尚无 GitHub 账号,练习要求"用自己的免费 GitHub 账号" | 在注册一步停住;文档只说"需要",未给入口 | github.com 自助注册(`inferred`);或先看 GitHub 帮助 | 前置缺口 / 低 |
| **GIT-1** | `learning/README.md` L49;外部《管理修改》 | 按练习写 `git diff` 看改动 | 正文只示范 `git diff HEAD -- file`(提交后),提交前示范 `git status`;读者可能混淆暂存区差异 | `git status` 已文档化;`git diff HEAD` 可迁移 | 候选文档缺口 / 低 |
| **GIT-3** | `learning/README.md` L62;《从远程库克隆》 | 用 HTTPS 地址执行 `git push` | 按模型既有知识,GitHub 当前 HTTPS 推送通常需要 PAT/credential manager 而不是账户密码;读者可能遇到凭据提示或失败;SSH 公钥生成也未教 | GitHub 官方文档 / credential manager / PAT | **已声明限制**:仓库明确"不编造口令或 token 步骤";记录为边界,不是隐瞒 |
| **ENV-3** | 外部 Miniforge Install;conda Starting conda | `bash Miniforge3-...sh` 后直接 `conda activate`;非交互 `-b` 不初始化 shell | `conda: command not found`;需要重开终端或 `conda init` | README 有 `conda init`;conda 页 Note 建议重开终端(后者不在四节内) | 候选文档缺口 / 低 |
| **PY-3** | 外部安装页 macOS 段 | macOS 读者按"系统自带 Python 2.x"理解当前系统 | 对系统状态有错误预期;同页官网/Homebrew 两条路径仍可用 | 用官网安装器或 Homebrew | 外部资料时效 / 低,非仓库断链;按模型既有知识判断,未运行 `python2` 验证 |
| OBS-ENV-2 | L72–L76 vs Miniforge README | 安装器选择在 conda 页、安装在 Miniforge 页 | 需在两份英文资料间跳转;不影响正确性 | 学习页已明确分工 | 非缺陷观察 |
| OBS-GIT-5 | L46 与《从远程库克隆》 | 练习说"空仓库",示例勾选 README | 按 Git 常识两者都可 clone(未实测);不影响完成 | — | 非缺陷观察 |
| OBS-NODE-1 | Python 9.2 vs 环境 L76 | 9.2 推荐 Anaconda,环境页要求优先 Miniforge | 取向不同,但环境页已明确纠正;不构成矛盾 | 以环境页为准 | 非缺陷观察 |

补充说明:

- PY-1 的"指定范围没有 `int()` 示例"是本次实际检索结果:5.1 全文无 `int(`/`float(` 转换示例,13.1 只示范读取/`strip`,5.6 直接对整数 list 求和。模型知道 `int(line)` 可用,但这是 `inferred`,不计为文档覆盖。
- PY-2 的 `Pillow` 导入名 `PIL` 属于模型既有知识;指定页面未写。报告不把它写成"文档错误",只写"练习要求与唯一安装示例之间缺少名称对应"。
- GIT-2/GIT-3 都可能被视频覆盖,但视频未播放、无公开字幕,不能写成"视频里讲过";这正是把它们列为候选而非定论的原因。
- ENV-1 与 PY-2 的共同点是"命令/名称给了,但示范或对应关系没给";影响取决于读者先验,故强度低于 PY-1/GIT-2。

## 未覆盖与限制

- **未安装/未实操**:没有安装 Python、Git、conda、PyTorch 或任何包;没有运行 `python`、`pip`、`git clone`、`git push`、`conda create`;没有创建练习仓库/PR;没有登录 GitHub。所有练习与自检均为静态覆盖判断。
- **未播放视频**:只读公开页面与 view API;视频章节、时间戳、画面内容、播放器登录/地区限制均未核验。文字备选章节没有归到视频名下。
- **未读全书**:Python 节点只读了支持最低练习的 11 页;未读 5.4、5.7、6.1、6.3、9、10.1、13.3;未读官方中文备选正文;未读 `learning/preparation-practice.md`、五个 Phase、理论指南 PDF、数学/PyTorch 节点。
- **外部资料会变化**:页面内容、安装器、GitHub UI 与默认分支都可能更新;本记录只对 2026-10-25 读到的内容负责。仓库页记录的 2026-10-23 核查日未被本次重新认证。
- **模型先验不可消除**:本会话知道编程、Git、conda 的一般用法;报告用 `inferred` 标注需要先验补齐的地方,但不能证明"零基础真人也会同样卡住"。
- **样本限制**:1 个模型、1 次尝试、1 个静态路径;没有真实读者、没有学习效果、没有可用性认证。记录到卡点不等于交付失败,零卡点也不等于读者可用。
- **记录时间**:原始文件中的精确时间以本机 mtime 为准;未落盘页面的阅读时间用 `≈` 标注;报告不把近似值写成精确时间戳。

## 记录完整性与可复核指针

| 项目 | 值 |
|---|---|
| 原始观察(忽略,未提交) | worktree `.local/newcomer-walkthrough/raw-observations.md` |
| 原始观察 SHA-256 | `3e87d0c154b60af9149a87709458638dae9c0a4fe2c2e7b07ad22ac7ac645626` |
| 大小/行数 | 39,411 bytes / 316 行 |
| 页面抓取副本 | worktree `.local/newcomer-walkthrough/pages/`(22 个文件,HTML/JSON/纯文本) |
| 预测试记录时间 | 2026-10-25 22:14:37–22:14:59(本地) |
| 被测 commit | `dd27de016684aeffa270c2bbb3b4469c485ea021` |

原始观察中的 O1–O13 对应本报告的入口、Python、Git、环境、汇总五段;候选 ID 与原始记录中的 CAND-*/OBS-* 对应。原始记录与页面副本都在 Git 忽略目录,不是公开链接;需要复核时由维护者从 worktree 读取。

## 验证与交付检查

以下命令在 worktree 内执行;`git diff --check` 与范围检查在提交前后各做一次。具体输出、退出码与 PR/exact head 见 Issue #32 的交回评论;本文件不预写尚未发生的 PR 编号。

| 检查 | 命令 | 结果 |
|---|---|---|
| 工作区忽略 | `git check-ignore -v .local/newcomer-walkthrough/raw-observations.md` | 命中 `.gitignore:2:.local/*` |
| 提交前空白检查 | `git diff --cached --check` | exit 0(暂存后) |
| 提交后空白检查 | `git diff --check origin/main...HEAD` | exit 0 |
| 单文件范围 | `git diff --name-only origin/main...HEAD` | 仅 `docs/validation/newcomer-agent-static-walkthrough.md` |
| 报告 diff | `git diff origin/main...HEAD -- docs/validation/newcomer-agent-static-walkthrough.md` | 单文件新增;命令与完整输出见 Issue #32 交回 |
| 本地链接/锚点 | 检查报告中的相对链接与 `#python`/`#git-github`/`#env`/`#math-dl` | 报告无相对 markdown 链接;4 个锚点均存在;引用的 `README.md`、`learning/README.md`、`learning/preparation-practice.md` 均存在 |
| 禁收产物 | 检查 diff 无数据/权重/checkpoint/缓存/凭据/任务代码/嵌套仓库 | 1 个 UTF-8 文本文件;无二进制/子模块/凭据模式 |
| 审查 | exact head 请求 `@cursor review`,读取收据并处置 | 见交回;不可用则如实记录 |

## 准则证据矩阵

`Criterion ID -> primary evidence -> Pass/Fail`。本报告是 Execution 自检;独立 Acceptance 仍需提供自己的完整映射。

| Criterion | 主要证据 | 结果 |
|---|---|---|
| **BASE-EVIDENCE** | 本文件的 BASE×6 + SIM×6 矩阵;每行指向报告章节、原始观察 O1–O13 或命令 | Pass |
| **BASE-VERIFY** | 测试前记录(22:14)先于 README;三节点外部资料实际读取;原始观察先保存并计算 SHA-256;`git diff --check`/范围/单文件检查;`@cursor review` 请求与收据见 Issue #32 | Pass(审查结果见交回) |
| **BASE-SEVERITY** | 仅新增一份维护者记录;无数据/权重/checkpoint/缓存/凭据/任务代码;未编造运行结果;未把 Agent 模拟写成真人/硬件验证;Codex 发现按交付评论处置 | Pass(待审查收据) |
| **BASE-SCOPE** | 唯一提交 `docs/validation/newcomer-agent-static-walkthrough.md`;README/learning/templates/example/runs/CONTEXT/AGENTS/ADR 未改;未安装/未实操/metadata 未动;#7 未恢复;不合并 | Pass |
| **BASE-DOCS** | 报告写明被测 commit、协议、`documented`/`inferred`/`not checked`、访问日期与限制;不替代 README/learning,不改变学习要求或 Phase 标准,不是第二份教材 | Pass |
| **BASE-PR** | 单一 feature branch/PR;英文 Conventional Commit 标题;PR 含中文 Motivation/Changes/Verification 与 `Closes #32`;issue/PR 保持打开,释放写所有权 | Pass(交付面) |
| **SIM-01** | 启动声明为 habit 新启 Execution;报告列出实际模型/客户端/未知 revision、预读治理/契约、禁止读取项、预测试记录时间与路径;未声称先验已消除 | Pass |
| **SIM-02** | 访问链 README → `#python` → `#git-github` → `#env`;每节点覆盖主资料/范围/练习/自检/下一步;逐项 `documented`/`inferred`/`not checked`;前节点未实操,后节点条件性继续 | Pass |
| **SIM-03** | 外部来源限于学习页指定主资料/备选;URL、访问日期、实际正文摘要、限制表;视频未播放且未把文字备选冒充视频;无登录/付费/TLS 绕过;无全网补资料 | Pass |
| **SIM-04** | 原始观察先于本报告保存;路径、SHA-256、39,411 bytes/316 行;候选卡点有位置/触发/影响/替代/分类;区分缺口、访问限制、模型限制;允许零卡点 | Pass |
| **SIM-05** | 顶部显著标注 Agent 模拟静态测试;未声称真实读者/视频观看/教程实跑/安装成功/硬件验证;仅一份报告入库;本地链接检查与单文件 diff 见交回;无敏感信息 | Pass |
| **SIM-06** | 契约/digest、base/branch/worktree/PR/exact head、矩阵、命令输出、上下文/访问限制、原始观察路径/digest、审查收据/发现处置、后续候选项写入 Issue #32 交回;Independent acceptance 仍 required | Pass(交付面) |

结论:文档骨架与入口链完整,三节点均可静态走通;发现的 6 条候选缺口集中在"练习需要但指定资料未示范"的环节,不构成对仓库整体结构的否定。测试路径的卡点可作为有效观察交付,不自动触发修复;本次未改读者文档。等待 habit 另启独立 Acceptance。
