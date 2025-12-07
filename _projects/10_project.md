---
layout: page
title: Hybrid Manipulation Platform
description: Unified control stack for aerial manipulation and embedded sensing.
img: assets/img/robotics-project.jpg
importance: 1
category: research
links:
  demo: https://example.com/demo
  github: https://github.com/example/hybrid-manipulation
---

Designed and implemented a hybrid control platform that fuses whole-body MPC with high-bandwidth embedded sensing. The system streams telemetry to a ROS2 backend, enabling real-time optimization and rapid iteration.

- **Multi-body dynamics:** Coupled quadrotor-arm dynamics modeled in `casadi` and exported to C++ for onboard execution.
- **Embedded stack:** STM32-based sensor fusion board with redundant IMUs and synchronized force-torque readings.
- **Field validation:** Demonstrated stable grasping on moving targets and teleoperated manipulation with sub-5 mm precision.

{% include figure.liquid path="assets/img/6.jpg" title="manipulation setup" class="img-fluid rounded z-depth-1" %}
