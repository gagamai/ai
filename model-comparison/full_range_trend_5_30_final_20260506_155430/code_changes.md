# Code changes

- Added `run_full_range_trend_v4.m` for trend-oriented full-range experiments.
- Added `run_full_range_trend_v4_batch.m` and `run_full_range_trend_v4_boundary75_batch.m` batch wrappers.
- Added `scan_rd_gep_v4_refine.m` for RD-GEP refinement checks under the strict 0.85 split.
- Added `scan_basegep_conservative_boundary75.m` for the conservative BaseGEP candidate pool used in the final accepted comparison.
- Added `assemble_final_boundary75_results.m` to assemble the final accepted output directory.
- Updated `v6_traincount_testcount_vis_rankpoint.m` so `frontier_biased` sampling draws non-frontier samples outside the frontier pool with stratified selection when possible.
- Updated `baseline_benchmark_utils.m` with the same frontier-biased sampling rule and the helper `stratified_pick_indices_local`.
- Updated `train_kriging_model.m` to support configurable `cfg.kriging.standardize` in `fitrgp`.
- Updated `train_mlp_model.m` to support configurable `cfg.mlp.lambda` in `fitrnet`.
