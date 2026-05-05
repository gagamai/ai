# Current accepted simulation

Recommended version: `iterations/iter13_final_paper_uniform6_four_edge_support`.

## Why this version

The current-structure attempt was physically reasonable but did not make modes 1-4 all exceed the required 35% six-point frequency variation. Therefore the workflow switched to the paper-like fallback, while retaining the required non-uniform thermal field, thermal stress, and virtual boundary springs.

## Physical background

This model represents a frame-supported high-speed/hypersonic skin panel under non-uniform aerodynamic heating. The hot zone is fitted as a smooth local surface heating region; the edges and back face stay cooler through frame conduction and back-side convection. Short-edge virtual boundary springs represent temperature-sensitive boundary/contact stiffness. Four-edge transverse support represents the surrounding frame preventing free out-of-plane edge motion.

## Accepted settings

- Geometry: `0.8 x 0.3 x 0.005 m`
- Mesh size: `0.005 m`, not larger than thickness
- Temperature points: `20, 96, 172, 248, 324, 400 degC`
- Non-uniform temperature field: `TEMP_FIELD_MODE=2`
- Boundary springs: `BOUNDARY_MODE=2`, `EBC0=4.55e8`, `EBC1=9.10e7`, `MIN_EBC=1e8`, `MAX_EBC=1e9`
- Thermal stress retained: `RELEASE_INPLANE_THERMAL=0`
- Material degradation: `Ex` decreases from `217 GPa` to `189.2 GPa`, about `12.81%`

## Result

| Mode | Range/base over six temperatures |
|---:|---:|
| 1 | 49.05% |
| 2 | 60.35% |
| 3 | 54.55% |
| 4 | 45.79% |

No zero frequency, no negative-frequency warning, and no MAPDL error were observed in the accepted run. The source code archive is stored in the iteration folder as `final_source_archive.zip.b64`.
