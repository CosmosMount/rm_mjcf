# RM27 drone MJCF assets

Current vehicle: **26droneVer2, 150 g including battery**, updated 2026-10-13.
The reviewed CAD retains `jiangbao5.0` / `jiangbao5-2`, removes the two 3-series
frames and adds the user's 30 × 65 × 5 mm core-board envelope above the top plate.

```text
visual/model.xml   # current Ver2 CAD appearance, static
dynamic/model.xml  # current Ver2, 150 g, free body + four rotor actuators
dynamic/scene.xml  # current vehicle with a ground plane for interactive inspection
assets/           # eight shared CAD visual meshes
model_info.json   # geometry, frame, parameters, assumptions and per-part inventory
legacy_v1/        # previous visual and 180 g dynamic entries, unchanged
dock/model.xml     # standalone static dock
hanger_base_mjcf/model.xml  # isolated hanger/base scene
```

Load each entry point independently with MuJoCo. Keep the whole aircraft package
when moving it: both current entries reference `../assets/` using relative paths.
The visual model has no collision, joints, actuators or inertial parameters.
The dynamic model contains the same CAD appearance, one free body, 246 rigid
collision proxies, ideal gyro/accelerometer sensors, and four filtered thrust actuators.
The dock is a static visual mesh
with primitive collision boxes and slot sites. The hanger/base scene is a
separate composed scene and does not change the standalone dock asset.

Body name is `rm27_drone`. Body axes are +X toward the camera, +Y left, +Z up;
CAD millimetres map through `(Z,X,Y)/1000` after removing the recorded origin.
The assumed COM is at the body origin, 22.5 mm above the lowest foot. The full
CAD envelope is approximately 114 × 114 × 45 mm.

Actuator controls are **individual rotor thrusts in newtons**, not PWM or normalized
throttle. Sites `rm27_drone_rotor_0..3` use XY positions `(+a,+a)`, `(+a,-a)`,
`(-a,-a)`, `(-a,+a)`, with `a = 0.0275 m`. Positive thrust is body +Z; assumed
yaw signs are `+,-,+,-`. Each actuator accepts 0–1.103625 N, has a 40 ms
first-order filter and 0.018 m yaw-torque/thrust coefficient. Level equilibrium
is 0.367875 N per rotor (initialize both `data.ctrl` and `data.act` for a hover reset).
Hardware motor numbering and spin directions have not been calibrated.

The 150 g total is user-reported. COM and diagonal inertia
`[0.0001877625, 0.0001877625, 0.0003249] kg m²` use a uniform-envelope-box
assumption. Thrust limits, motor lag, yaw torque and contact parameters remain
engineering estimates. Visual rotor disks do not collide; segmented frame rings,
supports, motor cylinders and component boxes provide an explicit rigid proxy.
This is not a measured structural, aerodynamic or propeller-strike model.
Collision proxies are in visualization group 3; CAD appearance is group 2.

The exporter is `rm27/integration/drone_v2.py`, configured by
`configs/drone_v2.json` in `rm27_drones`. The headless validator
`rm27.integration.validate_drone_v2` checks portable loading, hover equilibrium,
motor wrench signs, open rotor regions, passive contact and a 13 s truth-feedback
takeoff/hover/translate/return/landing. Its passing result does not establish
ArduPilot/PX4 SITL integration or real-aircraft performance. Root report:
`docs/DRONE_V2_SIMULATION.md`.

Flight controllers, converters, demonstrations and generated evidence stay in
`rm27_drones`; this repository contains runtime assets and provenance only.
