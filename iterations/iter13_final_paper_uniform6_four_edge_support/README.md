# Iteration 13 - accepted paper-like uniform6 simulation

This is the accepted fallback after the current `0.6 x 0.3 x 0.006 m` aero-skin case could not satisfy both physical reasonableness and the front-four-mode frequency variation target.

## Simulation story

Frame-supported high-speed/hypersonic skin panel under a fitted non-uniform aerodynamic heating field. The surface hot zone is centered on the panel and covers most of the stream-heated area, while the panel edges and back face remain cooler because of frame conduction and back-side convection. Short-edge virtual boundary springs model contact/slip stiffness degradation and recovery with temperature. All four edges retain transverse frame support, which avoids unphysical rigid/free transverse boundary behavior while preserving virtual boundary stiffness along the short edges.

## Main settings

- Profile: `paper_virtual_boundary_nonuniform_v1`
- Sweep mode: `PARAM_SWEEP_MODE=uniform6`
- Temperature points: `20, 96, 172, 248, 324, 400 degC`
- Geometry: `0.8 x 0.3 x 0.005 m`
- Mesh size: `0.005 m`, equal to thickness and not larger than thickness
- Temperature field: `TEMP_FIELD_MODE=2`, non-uniform analytic fitted hot-zone field
- Boundary mode: `BOUNDARY_MODE=2`, virtual boundary springs
- Four-edge transverse support: `SUPPORT_UZ_ALL_EDGES=1`
- Thermal stress retained: `RELEASE_INPLANE_THERMAL=0`
- Material degradation over 20-400 degC: `Ex` drops from `217 GPa` to `189.2 GPa`, about `12.81%`

## Acceptance check

Frequency range is computed as `(max(freq over six temperatures) - min(freq over six temperatures)) / freq_at_20C`.

| Mode | Six-point range/base |
|---:|---:|
| 1 | 49.05% |
| 2 | 60.35% |
| 3 | 54.55% |
| 4 | 45.79% |
| 5 | 22.74% |
| 6 | 10.10% |

Modes 1-4 all exceed the 35% target. The final accepted run has no zero frequency, no negative-frequency warning, and no MAPDL error. The remaining warning in `tmcase.err` is the benign thermal-solve message caused by prescribed nodal temperatures.

## Reproduction command

```powershell
$env:PARAM_PROFILE='paper_virtual_boundary_nonuniform_v1'
$env:PARAM_SWEEP_MODE='uniform6'
$env:PARAM_EBC0='4.55e8'
$env:PARAM_EBC1='9.10e7'
$env:PARAM_MIN_EBC='1.0e8'
$env:PARAM_MAX_EBC='1.0e9'
$env:PARAM_UNIFORM_ALPHA0='1.01e-5'
$env:PARAM_UNIFORM_CA1='1.90e-6'
$env:PARAM_UNIFORM_CA2='0'
$env:PARAM_SUPPORT_UZ_ALL_EDGES='1'
matlab -batch "cd('C:\Users\Lenovo\Desktop\参数化'); paths = run_parametric_ansys_from_matlab('', true, 6); disp(paths.resultsCsv);"
```

## Source archive

`final_source_archive.zip.b64` contains:

- `run_parametric_ansys_from_matlab.m`
- `thermo_modal_temperature_param.inp`
- `generate_second_order_nonlinear_coeff_design.m`

Decode on Windows PowerShell:

```powershell
$b64 = Get-Content .\final_source_archive.zip.b64 -Raw
[IO.File]::WriteAllBytes('final_source_archive.zip', [Convert]::FromBase64String($b64))
```
