# RMUC2027 battlefield MJCF

Entry point: `model.xml`.

This is a MuJoCo runtime scene converted from the official RMUC2027 CAD release at [`shiuhou/scene`](https://github.com/shiuhou/scene), commit `a4f31da5e724a0dc7bd6ecef59221c838833975c`. The source CAD is intentionally not copied into this runtime asset repository.

The visual layer now uses the supplied RMUC2027 MuJoCo V1 export: 131 separate colored mesh assets and 131 visual geoms. It preserves the field coordinate frame and carries the dark floor, raised platforms, red/blue boundaries, white markings, logos and central assembly through per-geom MuJoCo RGBA materials. The visual layer is `contype="0"` and `conaffinity="0"`.

Collision is generated from the supplied visual export's actual world-space
surfaces, following the older field's surface-partition approach. The former
629 whole-solid AABBs have been removed. `collision_assets.xml` and
`collision_geoms.xml` contain 41,371 static convex surface shells and two base
lamp colliders, using 38,116 mesh geoms backed by 27,135
convex mesh assets and 3,257 tightly fitted oriented boxes. Concave
outlines and usable openings are partitioned rather than filled by object hulls.
The original visual geoms remain non-colliding.

Generation uses a maximum source-vertex plane displacement of 1 mm, centered
5 mm shells, 2 mm outline simplification, and a 0.001 m² minimum planar patch
area. Holes unable to contain a 10 mm disk are filled as fastener detail. Tight
box fits allow 0.1 mm outline displacement; shared mesh coordinates use a 10 µm
key. Small patches and some curved details are omitted; this remains an
engineering approximation, not exact CAD contact. Ray errors can be large at
thin edges or silhouettes even when the nearby surfaces agree. The original
grid still has 245 samples with height differences over 2 cm (including 13
lower collision hits), with a largest absolute ray-height difference of 0.868 m.
Do not interpret a percentile as a maximum geometry error bound.

Each field shell explicitly uses `priority="1"` and `solref=".004 1"` at the
scene's 2 ms timestep. Equal-priority mixing with a dynamic geom's default soft
contact allowed a falling probe to cross the thin floor; shell priority preserves
the tested stiffness. These are numerical engineering settings. Faster impacts
and other contact priorities require separate checks.

The blue and red base guidance lamps are explicitly retained as separate convex
lens meshes. The source triangle-to-hull-plane deviation must stay below 0.2 mm
(measured maximum 0.156 mm); closing the rear of each lens is intentional.
Their small curved patches were previously all removed by the planar area cutoff.
The field-facing armor skin and front rim use 5 mm shells extending inward from
the visible surface, removing the previous 2.5 mm outward offset. This moves
17 blue-base and 16 red-base surface pieces; other existing collision pieces
and all visual meshes remain unchanged.

`collision_details.json` records these source-specific policies, mesh hashes,
and measured surface selectors. The converter reads it automatically next to
`model.xml`; a changed source hash or unmatched selector fails the rebuild.
The correction covers the two lamps and the field-facing armor skin/rim, not
every small curved or side-facing base detail. Discrete soft contact at 2 ms
can still overlap surfaces briefly at high speed; geometry alignment is not
a zero-penetration or continuous-collision guarantee.

Headless base checks on the complete standalone field sample 441 armor and 313
lamp rays per team (1,508 total). Maximum ray error is below 0.001 mm on both
armor patches and 0.058 mm on both lenses. All 32 normal impacts at 1 and 5 m/s
contact the geometry; lamp impacts specifically contact the new lens colliders.
At 5 m/s the 15 mm-radius probe can briefly overlap the visible surface by
about 5 mm at the unchanged 2 ms timestep, but its center never crosses the
tested plane. These are local sampling results, not maximum whole-model bounds.

Two legacy four-slot docks are mounted on the central CAD H platforms at
`(+/-1.03237595, +/-1.03237595, 1.6)` m, with 45-degree yaw. Their
0.30 x 0.30 x 0.20 m geometry is reused from `rm27_drone_mjcf/dock`, with
its old baked scene pose removed. Each dock has one non-colliding visual mesh,
eight primitive collision boxes, and four named slot sites at z=1.76 m.
These additions are simulation fixtures, not structures from the official CAD.
The normalized dock mesh is included locally, so this directory remains portable.

Coordinates use the centered visual export (metres, Z up). Collision surfaces
are generated directly in that frame, without the former CAD-frame AABB offset.
The field plus retained docks have 41,389 colliding geoms.

Headless validation on MuJoCo 3.12.0, using a 0.2 m XY grid and 11,200
vertical rays: samples differing by over 2 cm fell from 93.125% with the former
AABBs to 2.1875%; the 95th percentile absolute height error fell from
533.513 mm to 2.882 mm. The older field's corresponding rate is 4.491% and
95th percentile is 5.221 mm (each compared against its own visuals).
Four horizontal views (15,400 rays, 3,726 visual hits) reduced missing hits
from 270 to 8, extra hits from 732 to 174, and common-hit differences over
2 cm from 2,854 to 375; side-ray 95th percentile distance error is 95.504 mm.
These residuals are retained explicitly; this is not a full-field certification.

The two reproduced false-obstacle points have no sphere contacts. Two ground
probes and eight dock-slot probes dropped from 1 m settle within 1 cm of the
expected height; six 5 m/s impacts along actual surface normals produce contact
without crossing the tested planes. These checks use 15 mm-radius spheres,
not a flight controller. A clean standalone directory copy loads and passes these checks.
The preceding collision conversion's EGL comparison differed by at most one
8-bit channel level; the base correction changes no visual input files.
Ten probes run 1,000
steps in about 1 second on the
validation machine. The corrected model's standalone XML compilation took
about 173 seconds; the detailed
collision representation increases startup time substantially.

The converter is `rm27_sim.simulation.field_collision` in the separate
`rm_uav_mujoco` simulator component. Its README documents the rebuild command;
source identities, generation parameters, output hashes and validation results
are in `provenance.json`. Conversion code is not required to load this asset.

The model is self-contained and must be loaded with this directory as the XML root. `model.xml` includes both visual and collision assets/geoms XML; those files resolve the 131 meshes under `assets/` through relative paths. Source URLs, commits, input hashes and the supplied visual asset manifest are recorded in `provenance.json`.
