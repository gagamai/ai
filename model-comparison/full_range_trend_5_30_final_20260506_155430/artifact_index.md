# Artifact index

Core GitHub artifacts in this directory:

- `manifest.json`: final experiment configuration.
- `design_notes.md`: detailed setting-level design explanation.
- `full_range_trend_report.md`: final result report.
- `summary.csv`: final official comparison table with clean and raw means.
- `locked_points.csv`: final locked model/sample configurations, seeds, and disaster filtering stats.
- `clean_summary.csv`: clean metric table.
- `raw_summary.csv`: raw metric table.
- `sample_selection_details.csv`: low/high domain and fixed high-test sample roles.
- `code_changes.md`: code changes made for this iteration.

Full local artifacts are stored at `model_comparison_runs/full_range_trend_5_30_final_20260506_155430/` and include:

- `candidate_trials.csv`: all candidate trial results.
- `repeat_selection_index.csv`: all 800 repeat-level train/frontier/non-frontier/holdout sample selections.
- `full_range_trend_mape.png`: final trend figure.
- `candidate_*.mat`: MATLAB result files for the locked conservative BaseGEP candidates and assembled run outputs.

The local files are the complete run archive. The GitHub directory stores the reproducible text summary and final locked configuration in append-only form.
