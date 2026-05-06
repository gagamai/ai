# Code changes for boundary220 tuned temp-gap experiment

- Added `run_temp_gap_tuned_boundary220_max118.m` as a non-destructive new experiment entry point.
- Added `run_temp_gap_tuned_boundary220_max118_batch.m` for MATLAB batch execution and status capture.
- Reused existing RD-GEP and baseline training functions without changing their model internals.
- Reused prior support for fixed/precomputed splits, forced train indices, and fixed high-test indices.
- Added candidate pools, candidate-level summaries, tuned locking, tuning-gain summaries, plots, and an iteration archive writer.
