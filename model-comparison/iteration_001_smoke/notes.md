# Model comparison iteration

- Mode: smoke
- Target: FREQ1
- Primary metric: HighTest_MAPE_Mean
- Monotonic handling: real locked points only; no envelope post-processing.
- Locked rows: 7
- Common-count summary rows: 5

## Summary

- BaseGEP N=3 HighTest_MAPE_Mean=39.0673% reason=middle_quantile_selected
- SVR_RBF N=3 HighTest_MAPE_Mean=39.501% reason=middle_quantile_selected
- RD_GEP N=3 HighTest_MAPE_Mean=42.419% reason=middle_quantile_selected
- Kriging N=3 HighTest_MAPE_Mean=44.275% reason=middle_quantile_selected
- MLP N=3 HighTest_MAPE_Mean=53.0949% reason=middle_quantile_selected

## Code changes in this iteration

- Corrected FREQ1 column mapping in RD-GEP and baseline configs to X=data(:,1:4), T=data(:,5), f=data(:,6).
- Changed default temperature-domain split to lowTempRatio-based splitting so the FREQ1 data has enough low-temperature samples.
- Renamed the RD-GEP callable entrypoint to v6_traincount_testcount_vis_rankpoint and added cfg override merging.
- Added run_model_comparison_goal.m for reproducible candidate search, point locking, regular sample-count handling, and artifact export.
- Adjusted Kriging sigma initialization so Sigma is above SigmaLowerBound.

## Smoke caveat

This smoke run intentionally uses nRepeats=2 and tiny GEP populations/generations. It validates the workflow only; it is not the final performance result.
