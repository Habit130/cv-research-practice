# EXP-08 Phase 3 preflight

> 理论指南 §6.4, Table 6. 本记录在运行前建立。

**类型**: 环境/代码验证;不参与 baseline/ablation 对比。
**日期**: 2026-10-09
**状态**: 检查完成。

## 1. 这次实验想验证什么

恢复上游代码与 CIFAR-10 数据,确认实际 MPS 前向/反向/eval 可用。

## 2. 代码版本与数据划分

- [kuangliu/pytorch-cifar](https://github.com/kuangliu/pytorch-cifar) @ `49b7aa97b0c12fe0d4054e670403a16b6b834ddd`.
- 完整 patch/runner SHA256 与仓库外目录见 [EXP-05](EXP-05-phase3-baseline.md)。
- 官方源 TLS EOF/HTTP empty reply;使用 [原始归档镜像](https://huggingface.co/datasets/MIT-OL-AI-D/cifar-10-python/tree/a48007227f9e2cd0af96175f4afb5ac4965e261b)。
- 归档 MD5 `c58f30108f718f92721af3b95e74349a`,与 torchvision 期望相同;train/test/meta 文件全部校验通过,50000 train / 10000 test。

## 3. 关键 config 与训练参数

- `python phase3.py --variant <baseline|no-shortcut|constant-lr> --preflight`:seed=20261009,随机输入 (4,3,32,32),目标 [0,1,2,3],SGD 一步,eval 有限输出检查。此步骤不涉及真实数据。
- 数据另用 CIFAR10(train=True/False,download=True) 校验。
- 沙箱内 MPS 不可见;本机权限下 MPS 可用,未回退 CPU。

## 4. 结果放在哪

以下路径相对仓库外代码目录:

- artifacts/environment.json: 真实环境与数据/代码 hash。
- artifacts/data-prepare.log: 官方源失败;artifacts/mirror-download.log: 镜像下载。
- artifacts/<variant>/preflight.log: 队列每组启动前保存;未启动组尚无队列日志。启动前已另在终端检查三组。
- preflight 不产训练 checkpoint 或 accuracy。

## 5. 结论(支持 / 不支持什么判断)

三组随机张量输出均为 (4,10),loss 有限,backward/SGD 和 eval 通过;registered_parameters 均为 11173962。独立静态审查与 JSONL 换行复核通过,无已证实 P0/P1 启动阻塞。支持启动训练,不支持精度或收敛结论。

数据加载发出 NumPy 2.4 VisibleDeprecationWarning,未阻止校验或训练。后续真实指标见 EXP-05~07。

### 实测环境

```json
{
  "python": "3.11.15",
  "torch": "2.12.1",
  "torchvision": "0.27.1",
  "numpy": "2.4.4",
  "os": "macOS-27.2-arm64-arm-64bit",
  "cpu": "Apple M5",
  "memory_bytes": 25769803776,
  "mps_available": true,
  "archive_md5": "c58f30108f718f92721af3b95e74349a",
  "train": 50000,
  "test": 10000,
  "mirror_revision": "a48007227f9e2cd0af96175f4afb5ac4965e261b",
  "runner_sha256": "229f308399479d2171c987f630791400aecef54468c8e019e0d3670f885390b9",
  "patch_sha256": "3014e72138c30d6c1a2b278ccf955449eeb1d9f1e8d3ee96cfbfb59967a05f0d"
}
```
