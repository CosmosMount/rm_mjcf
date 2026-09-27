# RM27 drone MJCF assets

This directory contains separate runtime models for the RM27 vehicle:

```text
visual/model.xml   # CAD appearance only
dynamic/model.xml  # 180 g provisional dynamic proxy
dock/model.xml     # standalone static dock
hanger_base_mjcf/model.xml  # isolated hanger/base scene
```

Load each entry point independently with MuJoCo. The visual model has no
collision, joints, actuators or inertial parameters. The dynamic model has one
free body and four filtered rotor actuators; its mass, inertia and thrust
values are provisional simulation values. The dock is a static visual mesh
with primitive collision boxes and slot sites. The hanger/base scene is a
separate composed scene and does not change the standalone dock asset.

The flight controller, mission code, CAD converters and experiment scenes stay
in `rm27_drones`.
