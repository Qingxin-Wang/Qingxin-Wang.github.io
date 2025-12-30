---
layout: page
title: Hybrid Manipulation Platform
description: Unified control stack for aerial manipulation and embedded sensing.
img: 
importance: 4
category: research
links:
  demo: https://example.com/demo
  github: https://github.com/example/hybrid-manipulation
---

# 🛠️ Hybrid Manipulation Platform
> **Project Overview:** Unified control and sensing stack that blends whole-body MPC with high-bandwidth embedded telemetry for agile aerial manipulation experiments.

- **Role:** Platform architect and firmware/control developer
- **Tech Stack:** CasADi, C++, ROS2, STM32, CAN bus
- **Hardware:** Quadrotor + arm testbed, STM32 fusion board, force-torque sensors

## System Design
- Coupled quadrotor-arm dynamics modeled in CasADi and exported to C++ for onboard execution to retain real-time performance.
- Telemetry streamed to a ROS2 backend for online optimization, fast tuning, and logging.
- STM32-based sensor fusion board provides redundant IMUs and synchronized force-torque readings over CAN.

## Results
- Stable grasping on moving targets and teleoperated manipulation with sub-5 mm precision in field tests.
- Control and sensing stack reused across rapid prototyping cycles without re-flashing flight firmware.

{% include figure.liquid path="assets/img/6.jpg" title="manipulation setup" class="img-fluid rounded z-depth-1" %}
