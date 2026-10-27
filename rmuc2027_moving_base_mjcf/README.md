# RMUC2027 battlefield MJCF

Load `model.xml` with this directory as the component root. All assets and XML
includes use relative paths; no converter or parent workspace is required.
Units are metres, Z is up, and the supplied centered field frame is preserved.

## 隊友使用入口

本目錄是完整的新場地，已整合學長 `CosmosMount/rmuc2027_mjarena`
commit `ffd1eb7d1d1a2d1654125d8f316fdb0fdac6d001` 的地形與可動基地。
保留整個目錄即可載入 `model.xml`；無需原工作區或生成器，不附 `.mjb` 快取。
與其他完整場地二選一，不要重複 include。已驗證 MuJoCo 3.17.0。

鍵盤／隨機移動與 mjviser 展示在 `shiuhou/rm27_sim` 的
`feat/moving-base-target` 分支。單檔 `base_rail_control.py` 只依賴 NumPy，
可接入鏢組自己的 MuJoCo 迴圈；本場地不包含鏢組的視覺或尾翼控制算法。
關節 `blue_base_slide` 與致動器 `blue_base_drive` 請依名稱查找。
行程約 ±0.3005 m；質量、慣量與致動參數為展示估計，未以實物辨識。

整合回歸覆蓋端點／隨機到位保持、整組平移與下裝甲固定、綠燈／裝甲射線、
外部球接觸與原位置無幽靈碰撞，以及來源地形雜湊保留。
這不構成高速飛鏢碰撞或實際視覺／飛控算法驗證。

## Moving-base integration

The current entry adds the blue-base upper carriage to the `ffd1eb7` arena.
The green lens, upper armor and connected brackets move together along world
-Y; the lower sloped armor, guide and red base stay fixed. The limited slide
`blue_base_slide` spans approximately ±0.3005 m, and the position actuator
`blue_base_drive` takes metres. The viewer CLI and Python `RailMotion` interface are provided by
`shiuhou/rm27_sim` on branch `feat/moving-base-target`; see its README. Inertia, 1 kg mass and drive gains are
engineering estimates, not hardware identification.

All closure and ripple assets are byte-identical to the upstream arena.
Of the 18,303 upstream repair patches, 73 now follow the carriage and 293 are
assigned to fixed rail support. The 107 patches on the two green lenses are
superseded by two audited closed convex lens colliders; their mesh definitions
are removed to avoid duplicate contact. The other 18,196 repair patches remain.
All 33 retained front armor shell positions are corrected inward by 2.5 mm to
match the visible front surface. There are now **56,793 colliding geoms**.
Only carriage/support internal contact is excluded; external objects still
collide with both. Static fusion remains enabled, although bodies referenced
by the target contact exclusion can remain present. Do not rely on body IDs.

`assembly_manifest.json` records the upstream file hashes, the validated
carriage source hashes, every reparented patch and every superseded lens patch.
`surface_repair_sources.json` retains the original patch index mapping, including
superseded patches for provenance. `provenance.json` and the verification tables
below describe the upstream static arena, not fresh validation of this integration.

The reproducible merge tool is `rm27_sim.integration.arena_base_rail` in the
separate simulator repository. It consumes an unchanged upstream arena and the
validated `rmuc2027_moving_base_mjcf` directory at rm_mjcf commit `d86578e`, rejects ambiguous repair ownership,
and exports a new directory. Loading or using this model does not require that tool.

## Upstream terrain and requested closures

The 2027 V1.0.0 rulebook (2026-10-15), chapter 4, was used to distinguish
highlands, platforms, ripple roads, traversable tunnels and recovery openings.
The user explicitly requested closing the black side openings while keeping
robot tunnels open. The closures are therefore a simulation modification,
not an assertion that the official field has solid walls at these openings.

| Feature | Visual and contact representation | Preserved behavior |
|---|---|---|
| Sloping ground, highlands, platforms and steps | Original visuals, retained surface collision and bounded convex repair patches | Original elevations, inclines and discrete steps |
| Side ventilation/grilles | 14 closed convex covers derived from connected source assemblies, expanded radially by 1 mm | Sealed openings, matching visible/contact geometry |
| Side apertures | Four 5 mm plates from source wall outlines, with 0.3% outline overlap | Closed mouths without broad bounding boxes |
| Low wall recovery gaps | 100 thin skirt segments from source lower wall edges down to the local sloping ground | Closed wall feet; tunnel passage remains open |
| Mirrored ripple roads | Original visuals plus two sampled height fields | Continuous support following the visible corrugations |
| Robot tunnels | Original geometry | Original traversable openings |

`closure_assets.xml` stores the cover meshes inline and references the two
binary height fields; `closure_geoms.xml` instantiates their roles. Every cover
has a non-colliding group-2 visual geom and a group-3 collision geom referencing
the **same convex mesh and transform**. The 118 cover/plate/skirt pieces have
exact geometry correspondence. Skirts fill source gaps 12–75 mm above the
local ground; they are not extrapolated across elevated tunnel openings.

Each ripple height field covers `x=[6.30,8.75], y=[5.56,7.49]` m or its centrally
mirrored counterpart. Its 491 columns and 40 rows sample the supplied visual
surface; support is continuous between samples. Retained source shells cover
the boundaries outside those rectangles. The height fields are collision-only.

The repair removes 2,912 fully superseded collision geoms (2,269 inside covers
and 643 in the ripple regions) and 1,441 unused collision mesh definitions.
The upstream static model has **56,898 colliding geoms**: 53,951 meshes, 2,945 boxes and two
height fields. There are 251 visual-only geoms, including the original two dock
fixtures and the 118 new closure pieces.

`surface_repair_assets.xml` and `surface_repair_geoms.xml` add 18,303 convex
patches derived from 51,109 source triangles across 69 original meshes. They
repair omitted small and curved surfaces, including central mechanisms and
base details. Source triangle indices are retained in
`surface_repair_sources.json`.
Each patch extrudes 2 mm inward. Adjacent faces merge only when their projected
footprint is convex, their normal differs by at most 30 degrees from the seed,
and their displacement from its plane is at most 1.5 mm. These are construction
limits; independent hull-to-source checks below measure the resulting error.

The compiler uses `fusestatic="true"`. All geom names, site names and their
world transforms are preserved (57,149 geoms and 10 sites checked). Static body
IDs and names are removed from the compiled model; consumers should address
named geoms/sites rather than the former static grouping bodies.

## Verification and limits

Validated on MuJoCo 3.15.0 at the existing 2 ms timestep, loading a standalone
copy with all relative references resolved. Visual-only, collision-only and
translucent overlay views were inspected around the highlands and central
mechanisms. Visual appearance and physical contact are tested separately:

- All 131 original visual mesh hashes are unchanged. The 118 closure pairs use
  identical compiled meshes and transforms and passed individual contact probes.
- The dense source audit tests every original triangle larger than 0.00001 m²:
  546,343 triangle centroids, of which 364,044 are outward-exposed. After excluding
  source faces buried inside the requested closures, no checked point lacks a
  collision surface within 6 mm.
- Independent vertical and four-side rays produce 10,500 and 4,510 surface hits.
  No checked hit has a false void or false solid over 6 mm. Outlier nearest-surface
  distances are at most 5.68 mm (visual to collision) and 2.95 mm (reverse).
- On the new convex hulls, 1,251,628 vertex, edge-midpoint and facet-centroid
  samples are at most **3.79 mm** from their source triangles.
- All **18,303 repair patches** generate sphere contact individually with a
  15 mm-radius probe. This tests contact availability, not robot traversal.
- **224 bounded tunnel rays** retain exactly the same visual/collision clearance
  as the previous approved closure/ripple version, within a 3 m passage segment.
- The earlier 2,000 ripple samples have maximum height error **3.44 mm**, with
  zero over 6 mm; four moving support probes passed. Ripple assets are unchanged.

These are sampled acceptance results, not an everywhere guarantee. Triangles
at or below the stated area threshold are not exhaustively audited. Thin-shell
silhouettes can shift the first hit to a distant surface: 58 top rays and 420
side rays still differ by over 20 mm along the ray, although their hit points
are within the accepted 3D surface distance. Thus raw ray travel distance must
not be presented as either a local penetration depth or exact alignment.

The same ten-sphere workload used 2,000 physics steps, including 500 warm-up
steps, without rendering. The baseline is the previous closure/ripple model;
the final candidate adds the repair patches and enables static fusion.

| Metric | Previous model | Final model |
|---|---:|---:|
| Median step time | 0.977 ms | 0.691 ms |
| P95 step time | 1.012 ms | 0.716 ms |
| Real-time factor | 2.05 | 2.89 |
| Median / maximum contacts | 10 / 12 | 10 / 12 |

Static fusion supplies the speed gain; patches themselves add work. A separate
comparison with fusion enabled in both versions measured 0.461 ms before and
0.637 ms after. Timings describe this probe workload, not a robot benchmark.
No controller, wheel traversal, flight or high-speed impact certification is
claimed. The legacy docks remain approximations.

The retained shells and repair patches use priority 1 and `solref=".004 1"`.
These settings are numerical choices for the tested timestep, not official
material measurements; changes to timestep or contact priorities need retesting.

## Retained fixtures

Two legacy four-slot docks remain at `(+/-1.03237595, +/-1.03237595, 1.6)` m,
with 45-degree yaw. Each has one visual mesh, eight collision boxes and four
slot sites at z=1.76 m. They are simulation fixtures, not official CAD structures;
the normalized mesh is local to this component.
