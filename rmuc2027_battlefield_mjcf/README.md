# RMUC2027 battlefield MJCF

Entry point: `model.xml`.

This is a MuJoCo runtime scene converted from the official RMUC2027 CAD
release at [`shiuhou/scene`](https://github.com/shiuhou/scene), commit
`a4f31da5e724a0dc7bd6ecef59221c838833975c`. The source release contains only
`RMUC2027_V1.step` and `RMUC2027_V1.SLDASM`; the source CAD is intentionally not
copied into this runtime asset repository.

The visual mesh preserves the imported CAD coordinates and converts millimetres
to metres. It was tessellated with a 5 mm linear deflection and reduced to 20%
of the tessellated triangle count for a portable visual mesh. The resulting
mesh has 706,123 triangles. The scene also contains 629 static axis-aligned
bounding-box collision proxies, one for each placed STEP solid.

The visual geom has `contype="0"` and `conaffinity="0"`. Collision is kept in a
separate static body with `contype="1"` and `conaffinity="1"`. These boxes are
coarse engineering proxies derived from CAD bounds: they provide MuJoCo contact
surfaces but are not certified field collision, target semantics, gameplay
labels, or a measured contact model. Review or replace them for high-fidelity
flight/contact work.

A 0.1 m dynamic probe dropped at `(13, 0, 0)` contacts the CAD floor proxy
after 1,000 steps at the model's 2 ms timestep. This verifies that the proxy
layer participates in MuJoCo contact; it does not validate every field surface.

The model is self-contained and must be loaded with this directory as the XML
root so `assets/rmuc2027_scene_visual.msh` resolves through its relative path.
Source URLs, commit and input/output hashes are recorded in `provenance.json`.
