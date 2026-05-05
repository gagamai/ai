# Final selected code and protocol changes

Local code changes used for the final run:

- `baseline_default_config.m`
  - FREQ1 mapping: `featureColumns=1:4`, `tempColumn=5`, `targetColumn=6`.
  - `useFixedTempSplit=false` and `lowTempRatio=0.75` by default.
  - `highTestPick='random'` by default, while final fallback protocol overrides it to `coldest`.
  - Added `frontierBiasFraction=0.70`.

- `v6_traincount_testcount_vis_rankpoint.m`
  - Callable entrypoint renamed to match the file name.
  - Optional `cfg` override support added.
  - FREQ1 mapping fixed.
  - Added `highTestPick` support: `random`, `coldest`, `hottest`.
  - Added `frontier_biased` training sampling, drawing 70% of training samples from near the low/high boundary and the rest from the low-temperature domain.

- `baseline_benchmark_utils.m`
  - Added the same `frontier_biased` split construction for baseline models.

- `train_kriging_model.m`
  - Set initial `sigma0` slightly above `SigmaLowerBound` to avoid MATLAB resetting it.

- `run_model_comparison_goal.m`
  - Added goal, diagnostic, boundary, compressed-random, and final-core orchestration modes.
  - Added real locked-point selection, candidate-trial logging, model-specific stable-plateau tolerance, and final artifact export.
  - Added tuned RD-GEP and SVR candidate pools for the final fallback protocol.

Final selected command:

```matlab
cd('C:/Users/Lenovo/Desktop/模型对比');
report = run_model_comparison_goal('final_core');
```

Final selected display counts are `[20 30]`; the full `final_core` run also tested `N=40`, but it was excluded because MLP slightly outperformed RD-GEP there after the models had reached a sub-1% stable region.
