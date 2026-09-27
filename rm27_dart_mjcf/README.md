# RM27 dart MJCF

Standalone reusable dart asset for MuJoCo and visual-algorithm tests.

## Entry point

Load `model.xml` with MuJoCo. The scene contains one `mocap` body named
`darts_projectile`, a visual-only mesh, the `darts_front` site, and the
`darts_fpv` camera. Set `data.mocap_pos` and `data.mocap_quat` before
`mj_forward` or `mj_step` to place the dart.

The body origin is the CAD front-face center. Local `+X` is the dart forward
axis. The FPV camera is at that origin and looks along local `+X`; its default
vertical field of view is 60 degrees. Camera metadata and capture conventions
are in `fpv/camera.json`.

The mesh is visual-only (`contype=0`, `conaffinity=0`); this package does not
certify dart impact or aerodynamic behavior.

The sibling `dart_base_mjcf/model.xml` is the static RM27 dart-area/base
scene. It is kept separate from this moving dart asset.

The analytic flight runner, target geometry, generated videos and image
captures remain in `rm27_drones/research/Darts/`.
