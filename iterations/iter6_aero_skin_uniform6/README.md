# iter6_aero_skin_uniform6

Profile: `aero_skin_nonuniform_v1`

Purpose: six-temperature check on the current 0.6 x 0.3 x 0.006 m structure with a non-uniform fitted temperature field, finite virtual-boundary springs, and moderate material degradation.

Temperatures: 20, 120, 220, 320, 420, 520 degC.

Verdict: failed. Mode 2 and mode 4 do not reach the 35% six-point range criterion, and MAPDL reported negative-frequency warnings, indicating thermal-stress / modal-order contamination.

Six-point range over 20 degC baseline:

| Mode | Range/base |
| --- | ---: |
| 1 | 218.55% |
| 2 | 16.24% |
| 3 | 86.70% |
| 4 | 14.60% |
| 5 | 24.25% |
| 6 | 8.02% |

Conclusion: stop tuning the current structure and switch to the paper-like fallback structure.
