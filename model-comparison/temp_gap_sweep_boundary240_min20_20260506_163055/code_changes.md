# Code changes

- Added `run_temp_gap_sweep_boundary240_min20.m` to run the boundary-240 temperature-gap extrapolation sweep.
- Added `run_temp_gap_sweep_boundary240_min20_batch.m` for batch execution and status recording.
- Extended `v6_traincount_testcount_vis_rankpoint.m` with optional `precomputedSplits`, `fixedHighTestIndices`, and `forceTrainIndices` support so RD-GEP can use exactly the same splits as the baseline models.
- Extended `baseline_benchmark_utils.m` with optional `fixedHighTestIndices` and `forceTrainIndices` support.
- The new optional fields are inactive unless explicitly set, so previous experiments keep their original behavior.
