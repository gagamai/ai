# Artifact index

Core GitHub artifacts in this directory:

- `manifest.json`: experiment configuration.
- `gap_band_definition.csv`: fixed high-test samples and actual temperature gaps.
- `gap_sweep_summary.csv`: official clean/raw summary by model, train count, and gap band.
- `max_usable_gap_summary.csv`: maximum usable gap at 2%, 5%, 10%, and 15% error thresholds.
- `full_temp_gap_report.md`: readable final report and conclusions.
- `code_changes.md`: code changes made for this experiment.

Full local archive:

`model_comparison_runs/temp_gap_sweep_boundary240_min20_20260506_163055/`

The local archive also contains `candidate_trials.csv` with all 1200 repeat-level model/gap results, `repeat_selection_index.csv` with all 240 split records, ten `.mat` benchmark files, and `temp_gap_sweep_mape.png`.
