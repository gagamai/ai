# Iteration 14 - Chinese comments and temperature-field figures

This iteration does not change the accepted physical result from `iter13_final_paper_uniform6_four_edge_support`. It cleans the code comments into Chinese, adds a standalone temperature-field visualization script, and updates the parameter generator so it can explicitly generate deterministic uniform temperature samples.

## Main code changes

- `run_parametric_ansys_from_matlab.m`
  - Replaced English comments with Chinese comments.
  - Kept `PARAM_SWEEP_MODE=uniform6` behavior for six-temperature deterministic scans.

- `thermo_modal_temperature_param.inp`
  - Replaced APDL explanatory comments with Chinese comments.
  - Kept the accepted `TEMP_FIELD_MODE=2` non-uniform temperature-field formula.

- `generate_second_order_nonlinear_coeff_design.m`
  - Replaced remaining English comments/plot labels with Chinese wording.
  - Added `cfg.temperatureSamplingMode = "lhs" | "uniform"`.
  - Added `cfg.uniformTemperatureList`, so the generator can directly create deterministic uniform temperature points when needed.

- `plot_current_temperature_field.m`
  - New script that reproduces the APDL `TEMP_FIELD_MODE=2` formula without launching ANSYS.
  - Saves clean explanatory PNG figures and a temperature-field summary CSV.

## Local figure outputs

Saved under:

`C:\Users\Lenovo\Desktop\参数化\结果\图片文件夹\当前版本温度场`

Files:

- `温度场_六温度点_上表面.png`
- `温度场_最高温_上表面与背面.png`
- `温度场_最高温_中心线和厚向.png`
- `temperature_field_summary.csv`

## Temperature field formula

For each point `(x, y, z)`, the accepted version uses:

```text
T_edge = T_inf + TEMP_EDGE_FRAC * (T_case - T_inf)
R2 = ((x - PATCH_CX*Lx)/(Lx*PATCH_FX/2))^2 + ((y - PATCH_CY*Ly)/(Ly*PATCH_FY/2))^2
T_surface = T_edge + (T_case - T_edge) * exp(-TEMP_SHAPE_EXP * R2)
T_back = T_inf + TEMP_BACK_FRAC * (T_surface - T_inf)
T(x,y,z) = T_surface*(1 - z/thk) + T_back*(z/thk)
```

Accepted values:

- `T_inf = 20 degC`
- `TEMP_EDGE_FRAC = 0.55`
- `TEMP_BACK_FRAC = 0.85`
- `TEMP_SHAPE_EXP = 1.20`
- `PATCH_CX = 0.5`, `PATCH_CY = 0.5`
- `PATCH_FX = 0.75`, `PATCH_FY = 0.70`

## Verification

Executed without ANSYS:

```powershell
$env:PARAM_PROFILE='paper_virtual_boundary_nonuniform_v1'
$env:PARAM_SWEEP_MODE='uniform6'
matlab -batch "cd('C:\Users\Lenovo\Desktop\参数化'); paths = run_parametric_ansys_from_matlab('', false, 6); disp(paths.configFile); disp(paths.designCsv);"
```

The generated design table still gives the six accepted temperature points: `20, 96, 172, 248, 324, 400 degC`.

`source_archive.zip.b64` contains the updated source files.
