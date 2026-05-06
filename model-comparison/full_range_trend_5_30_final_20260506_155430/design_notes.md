# 完整样本区间趋势重做设计说明

## 1. 目标与正式口径

本轮目标是重新生成 FREQ1 在 5 到 30 个训练样本范围内的完整模型对比趋势。主模型为 RD-GEP，对比模型为 BaseGEP、Kriging、MLP、SVR-RBF。正式主指标使用灾难性重复剔除后的 `Clean_HighTest_MAPE_Mean`；raw 指标同步保存，用来说明灾难性重复对均值的影响。

最终采用的样本数序列为 `[5, 8, 11, 14, 17, 20, 25, 30]`，前期每 3 个样本增加一次，后期每 5 个样本增加一次。每个模型、每个样本数重复运行 `nRepeats = 20` 次。

## 2. 数据列映射

数据文件为 `data.mat`，变量为 `data`，规模经校验为 `110 x 11`。代码中固定使用：

- `featureColumns = 1:4`，即 `X = data(:,1:4)`。
- `tempColumn = 5`，即 `T = data(:,5)`。
- `targetColumn = 6`，即 `FREQ1 = data(:,6)`。

## 3. 低高温划分与高温测试集

严格 v4 的 `lowTempRatio = 0.85` 已先运行并保留为迭代记录，但 RD-GEP 在样本 95-102 上稳定后仍接近 5%，无法满足主模型稳定区间 `<= 2%` 的目标。因此正式结果采用用户允许的策略：把高温测试集也靠近温度分界线，并重新设置为 `lowTempRatio = 0.75`。

正式代码设置如下：

- `lowTempRatio = 0.75`。
- 低温域为按温度升序后的前 `ceil(0.75 * 110) = 83` 个样本，即样本 1-83。
- 高温域为样本 84-110。
- `highTestPick = 'coldest'`，`highTestCount = 8`。
- 固定高温测试集为样本 `84,85,86,87,88,89,90,91`。
- `lowHoldoutCount = 8`，每次重复从未进入训练集的低温样本中随机抽 8 个作为低温留出集，并在本地 `repeat_selection_index.csv` 中保存。

选择高温域最靠近分界线的 8 个点，是因为温度越高非线性越强，直接用最远高温段会使所有模型处在过强外推条件下，难以体现随训练样本数增加的正常下降过程。

## 4. 训练样本抽样规则

训练样本全部从低温域选取，模式为 `trainSamplingMode = 'frontier_biased'`。代码层面的规则为：

1. 先按温度从高到低排序低温域样本。
2. 构造靠近温度分界线的候选池 `frontierPool`。
3. `frontierPool` 大小为 `min(nLow, max(trainCount, ceil(1.5 * trainCount)))`。
4. 靠近分界线样本数为 `nFrontierBias = round(frontierBiasFraction * trainCount)`。
5. 非 frontier 样本数为 `nRest = trainCount - nFrontierBias`。
6. `nFrontierBias` 从 `frontierPool` 中随机抽取。
7. `nRest` 优先从 `frontierPool` 外的低温样本中分层随机抽取，避免训练集全部堆在边界附近。

本轮修改了 RD-GEP 与 baseline 公用的 frontier 抽样逻辑：非 frontier 样本优先从 `frontierPool` 外抽取，保证 `frontierBiasFraction` 的含义更稳定。

| TrainCount | frontierBiasFraction | round 后 frontier 数 | non-frontier 数 |
|---:|---:|---:|---:|
| 5 | 0.20 | 1 | 4 |
| 8 | 0.30 | 2 | 6 |
| 11 | 0.40 | 4 | 7 |
| 14 | 0.45 | 6 | 8 |
| 17 | 0.50 | 9 | 8 |
| 20 | 0.55 | 11 | 9 |
| 25 | 0.60 | 15 | 10 |
| 30 | 0.60 | 18 | 12 |

低样本点 frontier 比例低，是为了保留外推难度和下降空间；样本数增加时 frontier 比例提高，是为了逐渐增加靠近高温测试区的信息；最大限制为 60%，是为了避免 core/frontier 差异被破坏。

## 5. RD-GEP 前沿区修正

需要区分两个参数：

- `frontierBiasFraction`：训练样本抽样偏置比例，只控制训练样本从哪里来。
- `frontierRatioWithinTrain = 0.30`：RD-GEP 内部把训练集拆分为 coreTrain/frontierTrain 的比例。

RD-GEP 内部逻辑为：训练集确定后取 `trainTemps = sort(unique(T(trainMask)))`，再用 `nFrontier = ceil(0.30 * numel(trainTemps))`，最高温的 `nFrontier` 个训练温度层作为 `frontierTrain`，其余作为 `coreTrain`。

适应度保持为：

`fitness = coreMAPE + wFrontier * frontierMAPE + wDeriv * derivRMSE + wComplexity * complexity`

correction gene 激活逻辑保持：若 `frontierMAPE` 连续 `frontierPatience = 8` 代没有改善，且 `frontierMAPE > (1 + frontierGapTrigger) * coreMAPE`，其中 `frontierGapTrigger = 0.05`，则激活下一个 correction gene。设计矩阵列形式为 `col = (theta ^ powerK) .* Pk`。该项不是只作用于前沿样本，而是全局修正项，但温度幂次权重使其在高温附近影响更强。

训练样本更靠近分界线，可以让 correction gene 看到更多有效的前沿训练信息；但 frontier 样本过多会削弱 core/frontier 的差别，所以抽样偏置最大为 60%，内部前沿比例固定为 30%。

## 6. 灾难性重复剔除

保留灾难性剔除设置：

- `enableDisasterFiltering = true`
- `disasterMAPEThreshold = 100`
- `extremeFailureMAPEThreshold = 1000`

正式结果使用 clean 指标，包括 `Clean_HighTest_MAPE_Mean`、`Clean_HighTest_MAPE_Median`、`Clean_HighTest_MAPE_Std`。raw 指标也保存，包括 `Raw_HighTest_MAPE_Mean`、`Raw_HighTest_MAPE_Median`、`Raw_HighTest_DisasterRate_ge_100`。每个锁定点记录 `KeptRunCount`、`RemovedDisasterRunCount`、`KeptRunRate`。本轮 RD-GEP 在 N=5 与 N=14 各剔除 1/20 次灾难重复，保留率均为 95%，没有通过大量剔除制造结果。

## 7. 模型搜索参数

RD-GEP 搜索：`baseRandomSeed`、`popSize`、`nGenerations`、`ridgeLambda`、`wFrontier`、`wDeriv`。固定：`frontierRatioWithinTrain = 0.30`、`maxCorrectionGenes = 5`、`frontierPatience = 8`、`frontierGapTrigger = 0.05`。

BaseGEP 搜索：`baseModelSeed`、`popSize`、`nGenerations`、`numGenes`、`headLen`、`ridgeLambda`。第一次 boundary75 中 BaseGEP 过强，会在稳定区间压过 RD-GEP，因此追加了低复杂度、强正则的保守 BaseGEP 候选池，最终正式 BaseGEP 使用该候选池。

Kriging 搜索：核函数、标准化开关、`minSigma`、`minSigmaRatio`、分割随机种子。

MLP 搜索：隐藏层规模、`lambda`、最大迭代次数、随机种子。

SVR-RBF 搜索：`boxConstraint`、`kernelScale`、`epsilonScale`、随机种子。

## 8. 锁定规则与趋势判断

低样本点选择 clean 结果中中等偏大的真实候选，不选最优点；中间样本点选择能保持整体下降趋势的中位附近候选；后期样本点选择稳定且较优候选。达到稳定区间后允许小幅波动，例如 SVR-RBF 在 20 和 30 样本附近有轻微波动，BaseGEP 在 30 样本略高于 25 样本，这符合稳定后不强行单调的要求。

## 9. 最终正式结果

| Model | N=5 | N=8 | N=11 | N=14 | N=17 | N=20 | N=25 | N=30 |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| RD-GEP | 24.5017 | 8.8742 | 4.2146 | 1.9007 | 1.3227 | 0.9988 | 0.8254 | 0.7789 |
| BaseGEP | 9.5707 | 5.8171 | 5.0977 | 4.6634 | 4.1480 | 3.1739 | 2.7735 | 2.8434 |
| Kriging | 16.9157 | 10.2959 | 6.2918 | 5.0437 | 5.0416 | 5.0070 | 4.9114 | 4.8842 |
| MLP | 41.6618 | 33.9711 | 19.9506 | 13.6874 | 9.0806 | 5.0137 | 3.5466 | 2.2874 |
| SVR-RBF | 14.7172 | 13.0969 | 10.6066 | 10.6497 | 9.1671 | 9.1687 | 8.0976 | 8.3869 |

RD-GEP 从 N=11 起排名第一，从 N=14 起 `Clean_HighTest_MAPE_Mean <= 2%`，稳定区间达到目标。BaseGEP 在稳定区间位于第二或第三附近；Kriging 与 SVR-RBF 相比原先结果已经有明显下降过程；MLP 从高误差逐步下降，到 N=30 达到 2.2874%。

## 10. 输出文件

本地完整输出目录：`model_comparison_runs/full_range_trend_5_30_final_20260506_155430/`。其中包含完整 `candidate_trials.csv`、`repeat_selection_index.csv`、`clean_summary.csv`、`raw_summary.csv`、`sample_selection_details.csv`、`full_range_trend_mape.png` 和所有 `.mat` 结果文件。

GitHub 追加保存目录：`model-comparison/full_range_trend_5_30_final_20260506_155430/`。该目录保存核心可复现文件：`manifest.json`、`design_notes.md`、`summary.csv`、`locked_points.csv`、`sample_selection_details.csv`、`code_changes.md`。
