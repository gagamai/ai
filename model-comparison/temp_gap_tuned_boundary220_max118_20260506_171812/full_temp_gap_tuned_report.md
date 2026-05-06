# Boundary220 Tuned Temperature-Gap Report

## Purpose

This experiment moves the low/high temperature boundary to `220 deg` and tests how far each surrogate model can extrapolate from the low-temperature training domain. It compares the previous locked parameters (`baseline_locked`) with tuned candidate pools for each model and training count.

## Data and Split

- Target: `FREQ1`
- Mapping: `X=data(:,1:4)`, `T=data(:,5)`, `FREQ1=data(:,6)`
- Low training domain: `T <= 220`, samples `1-39`
- Forced training sample: sample `39`, `T=219.33484`, so `max(T_train)` is fixed in every repeat
- Train counts: `N=20` and `N=30`
- Repeats: `20` per candidate
- Low holdout: `8` samples per repeat from unused low-domain samples
- High test sets: fixed 8-sample bands, not randomly sampled
- Clean metric: `Clean_HighTest_MAPE_Mean`, removing repeats with `HighTest_MAPE >= 100%`

## Gap Bands

| Band | High Test Samples | Actual Gap |
|---|---|---:|
| G20 | 50-57 | 20.55190 |
| G40 | 62-69 | 41.16199 |
| G60 | 75-82 | 60.68398 |
| G80 | 90-97 | 80.05913 |
| G100 | 97-104 | 100.87925 |
| G118 | 103-110 | 118.02772 |

## Tuned Clean MAPE Mean, N=20

| Model | G20 | G40 | G60 | G80 | G100 | G118 |
|---|---:|---:|---:|---:|---:|---:|
| RD-GEP | 0.5845 | 1.4231 | 1.7515 | 7.1351 | 17.2976 | 41.4629 |
| BaseGEP | 1.9289 | 4.0339 | 7.3645 | 16.3192 | 30.1987 | 52.8134 |
| Kriging | 3.6077 | 6.1214 | 9.1002 | 19.2714 | 36.6888 | 70.8362 |
| MLP | 9.8702 | 20.8892 | 24.9765 | 40.0135 | 47.0329 | 46.1630 |
| SVR-RBF | 4.5512 | 7.5660 | 11.3688 | 22.6564 | 41.8367 | 78.8611 |

## Tuned Clean MAPE Mean, N=30

| Model | G20 | G40 | G60 | G80 | G100 | G118 |
|---|---:|---:|---:|---:|---:|---:|
| RD-GEP | 0.6266 | 1.2548 | 2.1156 | 6.8850 | 16.6417 | 38.5802 |
| BaseGEP | 1.7814 | 3.7452 | 6.7406 | 16.6982 | 32.5296 | 61.8009 |
| Kriging | 2.9336 | 5.1653 | 8.0356 | 17.6233 | 34.4095 | 67.6409 |
| MLP | 0.9177 | 2.3587 | 6.0572 | 12.6698 | 20.9826 | 30.1330 |
| SVR-RBF | 3.4796 | 6.2182 | 9.8412 | 20.7513 | 39.5085 | 75.8613 |

## Max Usable Gap After Tuning

At `N=20`, RD-GEP reaches the largest usable gap at strict thresholds: up to `60.68398 deg` under 2% and 5%, and `80.05913 deg` under 10% and 15%. BaseGEP reaches `20.55190 deg` under 2%, `41.16199 deg` under 5%, and `60.68398 deg` under 10%-15%. Kriging and SVR-RBF become useful mainly at 5%-15% thresholds. MLP remains unstable at N=20.

At `N=30`, RD-GEP reaches `41.16199 deg` under 2%, `60.68398 deg` under 5%, and `80.05913 deg` under 10%-15%. MLP benefits strongly from tuning and reaches `20.55190 deg` under 2%, `41.16199 deg` under 5%, `60.68398 deg` under 10%, and `80.05913 deg` under 15%.

## Tuning Effect

Tuning improved BaseGEP, Kriging, SVR-RBF, and especially MLP. RD-GEP baseline stayed best among its candidate pool for every gap, so the tuned lock selected `baseline_locked` for RD-GEP. This means the previous RD-GEP locked parameters were already robust for this extrapolation design.

Largest observed tuning gains:

- MLP, N=30, G100: MAPE reduced from `78.4866` to `20.9826`, a `73.27%` relative reduction.
- MLP, N=30, G80: MAPE reduced from `64.8042` to `12.6698`, an `80.45%` relative reduction.
- SVR-RBF, N=30, G118: MAPE reduced from `89.7395` to `75.8613`.
- BaseGEP, N=20, G118: MAPE reduced from `68.8035` to `52.8134`.

## Notes

The full local result directory contains the complete candidate-level and repeat-level records, all `.mat` files, and the two plots. The GitHub folder stores compact core text summaries for easier review.