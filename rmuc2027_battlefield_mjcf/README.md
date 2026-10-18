# RMUC2027 battlefield MJCF

Entry point: `model.xml`.

This is a MuJoCo runtime scene converted from the official RMUC2027 CAD release at [`shiuhou/scene`](https://github.com/shiuhou/scene), commit `a4f31da5e724a0dc7bd6ecef59221c838833975c`. The source CAD is intentionally not copied into this runtime asset repository.

The visual layer now uses the supplied RMUC2027 MuJoCo V1 export: 131 separate colored mesh assets and 131 visual geoms. It preserves the field coordinate frame and carries the dark floor, raised platforms, red/blue boundaries, white markings, logos and central assembly through per-geom MuJoCo RGBA materials. The visual layer is `contype="0"` and `conaffinity="0"`.

Collision remains separate from appearance. The scene keeps the original 629 static axis-aligned bounding-box proxies, with `contype="1"` and `conaffinity="1"`. They are coarse engineering proxies derived from CAD bounds, not certified field collision or target semantics. Do not make the visual meshes collidable: their detailed geometry is for rendering only.

The model is self-contained and must be loaded with this directory as the XML root. `model.xml` includes `visual_assets.xml` and `visual_geoms.xml`; those files resolve the 131 meshes under `assets/` through relative paths. Source URLs, commits, input hashes and the supplied visual asset manifest are recorded in `provenance.json`.
