# RMUC2027 battlefield visual MJCF

Entry point: `model.xml`.

This is a MuJoCo runtime visual asset converted from the official RMUC2027 CAD
release at [`shiuhou/scene`](https://github.com/shiuhou/scene), commit
`a4f31da5e724a0dc7bd6ecef59221c838833975c`. The source release contains only
`RMUC2027_V1.step` and `RMUC2027_V1.SLDASM`; the source CAD is intentionally not
copied into this runtime asset repository.

The mesh preserves the imported CAD coordinates and converts millimetres to
metres. It was tessellated with a 5 mm linear deflection and reduced to 20% of
the tessellated triangle count for a portable visual mesh. The resulting mesh
has 706,123 triangles and is loaded by the single static geom in `model.xml`.

This entry is **visual-only**. Its geom has `contype="0"` and
`conaffinity="0"`; the official CAD release does not define certified collision
surfaces, target semantics, field frames, or gameplay labels. Use a separately
reviewed collision/target asset when a physics experiment requires one. Visual
loading does not establish contact behavior or field-rule fidelity.

The model is self-contained and must be loaded with this directory as the XML
root so `assets/rmuc2027_scene_visual.msh` resolves through its relative path.
Source URLs, commit and input/output hashes are recorded in `provenance.json`.
