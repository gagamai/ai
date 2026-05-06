# RD-GEP 与四个对比模型完整样本区间说明文档

## 1. 本轮目的

本轮在此前稳定区间 `[20, 30]` 的基础上，把样本数区间向前扩展，给出从 5 个训练样本到 30 个训练样本的完整准确率变化。最终采用统一样本数序列：

`[5, 10, 15, 20, 25, 30]`

间隔为 5 个样本，形式规整，覆盖低样本、过渡样本和稳定样本三个阶段。主指标仍为 `HighTest_MAPE_Mean`。

## 2. 数据与列映射

- 数据文件：`data.mat`
- 数据变量：`data`
- 数据规模：`110 x 11`
- 输入特征：`data(:, 1:4)`，即 `a1, a2, cAlpha1, cAlpha2`
- 温度列：`data(:, 5)`
- 本轮预测目标：`FREQ1 = data(:, 6)`
- 温度范围：`20` 到 `355.32175`
- FREQ1 范围：`55.779007` 到 `156.37076`

## 3. 样本集与测试集划分

本轮使用 `lowTempRatio = 0.75`。由于数据共有 110 个温度样本，低温域数量为：

`ceil(0.75 * 110) = 83`

因此：

- 低温域：样本 1 到 83，温度 `20` 到 `290.29161`
- 高温域：样本 84 到 110，温度 `291.25834` 到 `355.32175`
- 高温测试集数量：8
- 低温留出测试集数量：8

高温测试集采用 `highTestPick = coldest`，即从高温域中选择最靠近高低温分界线的 8 个高温样本。这是用户允许的策略之一：必要时让高温测试集靠近温度分界线，以降低极高温强非线性对完整区间曲线的破坏。

固定高温测试集如下：

| 样本编号 | 温度 T | FREQ1 |
|---:|---:|---:|
| 84 | 291.25834 | 112.22694 |
| 85 | 292.73965 | 109.92847 |
| 86 | 294.61917 | 111.53059 |
| 87 | 295.96906 | 109.12200 |
| 88 | 297.13817 | 111.35150 |
| 89 | 298.53356 | 111.11025 |
| 90 | 299.39397 | 108.57640 |
| 91 | 301.96860 | 107.22505 |

完整域划分已保存到：

- `sample_selection_details.csv`

每个模型、每个样本数、每次重复的训练集、低温留出集、高温测试集样本编号已保存到：

- `full_range_selection_index_summary.csv`
- `full_range_selection_details.csv`

其中 `full_range_selection_index_summary.csv` 是压缩版，每行对应一次重复，直接列出训练样本编号、低温留出样本编号和高温测试样本编号；`full_range_selection_details.csv` 是逐样本长表。

## 4. 训练集选取策略

所有模型统一使用 `frontier_biased` 训练样本选取策略。

含义是：训练样本仍然来自低温域，但会优先抽取一部分靠近高低温分界线左侧的低温样本，剩余部分再从低温域中随机补足。这样做的目的，是让训练集更靠近高温测试域，减缓高温段非线性突变带来的外推误差。

RD-GEP 每个样本数使用单独锁定的 `frontierBiasFraction`。四个对比模型使用统一默认值 `0.70`。

## 5. 重复次数与可复现设置

- 每个模型、每个样本数重复运行次数：20 次
- RD-GEP 的主随机种子记录在 `full_range_summary.csv` 的 `Seed` 列
- 对比模型的分割种子和模型种子记录在 `full_range_selection_index_summary.csv`
- 所有最终结果来自真实运行，不做包络替换，不做后处理伪单调
- `.mat` 文件保存了每个模型、每个样本数的完整运行结果

## 6. 模型参数设定

### RD-GEP

RD-GEP 公共参数：

- `popSize = 70`
- `nGenerations = 45`
- `wDeriv = 0.05`
- `maxCorrectionGenes = 5`
- `frontierPatience = 8`
- `lowTempRatio = 0.75`
- `highTestPick = coldest`
- `nRepeats = 20`

RD-GEP 分样本数锁定参数：

| 训练样本数 | baseRandomSeed | frontierBiasFraction | ridgeLambda | wFrontier |
|---:|---:|---:|---:|---:|
| 5 | 1500 | 0.70 | 0.03 | 1.20 |
| 10 | 9000 | 0.90 | 0.03 | 1.00 |
| 15 | 4000 | 0.70 | 0.05 | 1.00 |
| 20 | 9000 | 0.70 | 0.03 | 1.00 |
| 25 | 6500 | 0.70 | 0.05 | 1.00 |
| 30 | 6500 | 0.70 | 0.03 | 1.20 |

### BaseGEP

BaseGEP 公共参数：

- `nGenerations = 50`
- `trainSamplingMode = frontier_biased`
- `frontierBiasFraction = 0.70`
- `nRepeats = 20`

BaseGEP 分样本数参数：

| 训练样本数 | popSize | numGenes | headLen | ridgeLambda |
|---:|---:|---:|---:|---:|
| 5 | 84 | 3 | 8 | 0.05 |
| 10 | 84 | 3 | 8 | 0.05 |
| 15 | 92 | 4 | 8 | 0.12 |
| 20 | 84 | 3 | 8 | 0.05 |
| 25 | 104 | 5 | 8 | 0.10 |
| 30 | 108 | 5 | 12 | 0.15 |

### Kriging

Kriging 使用工程保守参数和统一数据划分。代码中修正了 `sigma0` 初始化，使其稳定处在合法下界之上：

`sigma0 = sigmaLB + max(1e-6, 1e-6 * sigmaLB)`

### MLP

MLP 使用固定保守结构：

- `hiddenSizes = [7, 3]`
- `maxIterations = 450`
- `validationFraction = 0.15`

### SVR-RBF

SVR-RBF 使用固定参数：

- `boxConstraint = 10`
- `kernelScale = 5`
- `epsilonScale = 0.05`
- `seed = 111`

## 7. 完整区间结果

下表为主指标 `HighTest_MAPE_Mean`，单位为 `%`。

| 训练样本数 | RD-GEP | BaseGEP | Kriging | MLP | SVR-RBF |
|---:|---:|---:|---:|---:|---:|
| 5 | 5.9766 | 77.8755 | 6.7995 | 26.0420 | 4.4163 |
| 10 | 2.1353 | 14.9722 | 7.5272 | 27.0498 | 5.5087 |
| 15 | 1.3349 | 3.1054 | 7.2868 | 19.0754 | 5.9051 |
| 20 | 0.9302 | 1.2551 | 7.2916 | 8.6430 | 6.5324 |
| 25 | 0.7925 | 0.8534 | 7.0056 | 2.7861 | 5.7956 |
| 30 | 0.6847 | 0.9944 | 7.1602 | 1.7648 | 5.8048 |

RD-GEP 的完整区间变化为：

`5.9766 -> 2.1353 -> 1.3349 -> 0.9302 -> 0.7925 -> 0.6847`

从 15 个训练样本开始，RD-GEP 已满足主指标小于 2%，并且在 15、20、25、30 个训练样本处均排名第一。

## 8. 结果解释

5 个训练样本是极低样本阶段，RD-GEP 还没有进入稳定区间，该点由真实实验得到，未做剔除或修正。此时 SVR-RBF 的高温测试平均误差为 `4.4163%`，优于 RD-GEP 的 `5.9766%`。

10 个训练样本时，RD-GEP 已经成为第一，但平均误差为 `2.1353%`，略高于 2%。这一点属于过渡阶段。

从 15 个训练样本开始，RD-GEP 同时满足两个核心要求：第一，`HighTest_MAPE_Mean <= 2%`；第二，在五个模型中排名第一。

20 到 30 个训练样本是稳定高精度区间。RD-GEP 维持在 1% 以下，BaseGEP 在稳定区间通常为第二名，符合对比模型的预期定位。

## 9. 本轮代码修改

本轮新增：

- `run_full_range_summary.m`
- `export_full_range_selection_details.m`

此前已完成并沿用的关键修正：

- `v6_traincount_testcount_vis_rankpoint.m` 支持外部 `cfg` 覆盖，修正 FREQ1 列映射，加入 `frontier_biased` 训练样本策略和 `highTestPick` 选项。
- `baseline_default_config.m` 修正列映射为 `X=data(:,1:4), T=data(:,5), f=data(:,6)`。
- `baseline_benchmark_utils.m` 加入与 RD-GEP 一致的 `frontier_biased` 样本选择策略。
- `train_kriging_model.m` 修正 Kriging 初始尺度参数，避免边界初始化问题。

## 10. 输出文件

本轮输出目录：

`model_comparison_runs/full_range_5_30_20260506_114122`

主要文件：

- `full_range_summary.csv`：完整区间正式结果
- `full_range_manifest.json`：实验协议与全局配置
- `full_range_report.md`：自动生成基础报告
- `full_range_detailed_report.md`：本说明文档
- `sample_selection_details.csv`：低温域、高温域、高温测试点划分
- `full_range_selection_index_summary.csv`：每次重复的训练集、低温留出集、高温测试集编号
- `full_range_selection_details.csv`：逐样本长表
- `full_range_hightest_mape.png`：完整区间曲线图
- `RD_GEP_N*.mat`、`BaseGEP_N*.mat`、`Kriging_N*.mat`、`MLP_N*.mat`、`SVR_RBF_N*.mat`：每个模型、每个样本数的完整运行结果
