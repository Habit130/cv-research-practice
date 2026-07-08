# EXP-<编号> —— <一句话名称>

> 五类必填信息依据指南 §6.4;对比纪律依据 §6.6 与 Table 7。
> 在启动运行**之前**创建本记录。

**类型**(Table 7):baseline / ablation / fair comparison
**日期**:

## 1. 这次实验想验证什么

## 2. 代码版本与数据划分

- 仓库 / commit:
- 数据集与划分:

## 3. 关键 config 与训练参数

- Config 文件:
- 覆盖项 —— batch size、LR 及 schedule、epoch、seed、pretrained weights:
- 相对 baseline,本次只改的那一个因素:

## 4. 结果放在哪

- 日志:
- Checkpoint:
- 指标输出 / 曲线:

## 5. 结论(支持 / 不支持什么判断)

- 结果:
- 条件化判断 —— 禁止只写"效果变好":与 <baseline> 相比,<指标> 在 <条件> 下从 <X> 变为 <Y>;在 <哪些划分> 上成立;代价是 <算力/参数量>;尚未验证:<什么>;下一个实验:
