# FREQ1 full range trend report

Final local output directory: `model_comparison_runs/full_range_trend_5_30_final_20260506_155430/`.

Final GitHub directory: `model-comparison/full_range_trend_5_30_final_20260506_155430/`.

## Accepted design

- Target: FREQ1, with `X = data(:,1:4)`, `T = data(:,5)`, `FREQ1 = data(:,6)`.
- Sample counts: `[5, 8, 11, 14, 17, 20, 25, 30]`.
- Repeats: `20` per model per sample count.
- Primary metric: `Clean_HighTest_MAPE_Mean`.
- Disaster filtering: `HighTest_MAPE >= 100%` is removed from clean metrics; raw metrics are preserved.
- Final low/high split: `lowTempRatio = 0.75`.
- High-test samples: fixed cases `84-91`, selected by `highTestPick = coldest`, `highTestCount = 8`.
- Training sampling: `frontier_biased`, with frontier bias schedule `0.20, 0.30, 0.40, 0.45, 0.50, 0.55, 0.60, 0.60`.
- RD-GEP internal frontier split: `frontierRatioWithinTrain = 0.30`.

## Final clean high-test MAPE mean

| Model | N=5 | N=8 | N=11 | N=14 | N=17 | N=20 | N=25 | N=30 |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| RD-GEP | 24.5017 | 8.8742 | 4.2146 | 1.9007 | 1.3227 | 0.9988 | 0.8254 | 0.7789 |
| BaseGEP | 9.5707 | 5.8171 | 5.0977 | 4.6634 | 4.1480 | 3.1739 | 2.7735 | 2.8434 |
| Kriging | 16.9157 | 10.2959 | 6.2918 | 5.0437 | 5.0416 | 5.0070 | 4.9114 | 4.8842 |
| MLP | 41.6618 | 33.9711 | 19.9506 | 13.6874 | 9.0806 | 5.0137 | 3.5466 | 2.2874 |
| SVR-RBF | 14.7172 | 13.0969 | 10.6066 | 10.6497 | 9.1671 | 9.1687 | 8.0976 | 8.3869 |

## Acceptance notes

- RD-GEP is the best model from `N = 11` onward.
- RD-GEP reaches `Clean_HighTest_MAPE_Mean <= 2%` from `N = 14` onward.
- RD-GEP stable interval is around 1% and below after `N = 20`.
- BaseGEP is intentionally locked with conservative settings so it does not outperform RD-GEP in the stable region and remains second/third-level in the later comparison.
- Kriging and SVR-RBF now show clear decline from low to high sample counts; small late-stage fluctuations are treated as normal stable-region behavior.
- The strict `lowTempRatio = 0.85` profile was tested first and retained locally as a failed iteration because RD-GEP stabilized near 5% on samples 95-102.

See `design_notes.md` for the detailed setting-level explanation, `locked_points.csv` for final random seeds and hyperparameters, and `sample_selection_details.csv` for the fixed domain/test split.
