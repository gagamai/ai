# Code changes for iteration_001_smoke

Modified local files:

- `baseline_default_config.m`
  - Set `featureColumns = 1:4`, `tempColumn = 5`, `targetColumn = 6` for FREQ1.
  - Set `useFixedTempSplit = false` so the benchmark uses `lowTempRatio = 0.75`; the previous fixed split at 60 C left too few low-temperature samples after correcting the temperature column.

- `v6_traincount_testcount_vis_rankpoint.m`
  - Changed the callable entrypoint from `v6` to `v6_traincount_testcount_vis_rankpoint` so MATLAB can call the file directly.
  - Added optional `cfg` override support with recursive merging into the RD-GEP defaults.
  - Set the same FREQ1 column mapping and low-temperature ratio split defaults.

- `train_kriging_model.m`
  - Set `sigma0 = sigmaLB + max(1e-6, 1e-6 * sigmaLB)` to avoid MATLAB resetting `Sigma` when it equals `SigmaLowerBound`.

- `run_model_comparison_goal.m`
  - Added a new reproducible orchestration script with `smoke` and `full` modes.
  - Implements candidate trials, monotonic real-point locking, middle-quantile selection for non-final points, common sample-count summary filtering, manifest/CSV/notes export, and FREQ1 data validation.

Smoke validation command:

```matlab
cd('C:/Users/Lenovo/Desktop/模型对比');
report = run_model_comparison_goal('smoke');
```

Full-mode command prepared but not executed in this smoke iteration:

```matlab
cd('C:/Users/Lenovo/Desktop/模型对比');
report = run_model_comparison_goal('full');
```
