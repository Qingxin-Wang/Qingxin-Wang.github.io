---
layout: page
title: LIO based Navigation Framework
description: RoboMaster Sentry 2025 navigation stack based on Point-LIO + Hybrid A*
img: assets/img/sentry.jpg
importance: 2
category: work
giscus_comments: true
links:
  github: https://github.com/Qingxin-Wang/Sentry25NAVI/tree/on_robot
---

# 🤖 RoboMaster Sentry 2025: Robust Navigation & Localization System Design

> **Project Overview**: Designed a comprehensive navigation solution based on **Point-LIO** and **Hybrid A\*** to address localization drift in high-speed, dynamic environments. Achieved centimeter-level accuracy and resolved robustness issues present in traditional schemes for the Sentry robot.

- **Role:** Algorithm Design & Implementation  
- **Tech Stack:** ROS, C++, LiDAR SLAM, Control Theory  
- **Hardware:** Livox Mid-360, Omnidirectional Chassis  

## 1. Background & The Challenge

- **2023 (2D LiDAR + Cartographer):** Insufficient accuracy and stability during aggressive motion.  
- **2024 Early (FAST-LIO2 + AMCL):** Significant drift in regional matches; AMCL over-dependent on initial pose.  
- **Navigation pain points:** Weak long-term map maintenance and false slope obstacles in CMU exploration.  
- **Goal:** Improve odometry during aggressive maneuvers and optimize global planning/trajectory tracking.

## 2. Simulation & Preprocessing

- Built a custom Gazebo Livox Mid-360 plugin.  
- Fixed intrinsic point cloud distortion and added multiple ROS message formats for driver-free usage.

<div class="row my-4">
  <div class="col-sm d-flex justify-content-center">
    {% include figure.liquid path="assets/img/pointcloud_before.png" title="Simulated point cloud (distorted)" class="img-fluid rounded z-depth-1" max-width="520px" %}
  </div>
  <div class="col-sm d-flex justify-content-center">
    {% include figure.liquid path="assets/img/pointcloud_after.png" title="Point cloud after distortion repair" class="img-fluid rounded z-depth-1" max-width="520px" %}
  </div>
</div>
<div class="caption mt-2 mb-4">
  Distortion repair in the custom Livox Mid-360 Gazebo plugin.
</div>

## 3. Localization Algorithm Design

### 3.1 Coordinate System Definition

- Split transform publication for stability:  
  - $T^{odom}_{Map}$: set once at relocalization startup and held static.  
  - $T^{body}_{odom}$: updated in real time by high-frequency odometry.

### 3.2 Odometry: Point-LIO (vs. FAST-LIO2)

1) **Point-wise LIO:** Per-point processing removes intra-frame distortion; tracks extreme dynamics.  
2) **Stochastic IMU model:** Treats IMU outputs as system outputs; augments kinematics to smooth saturated sensors.  
3) **Tightly coupled & lightweight:** Real-time on low-power ARM.

### 3.3 Relocalization: SAC_IA + Small_GICP

- Pipeline: FPFH feature extraction (ground filtered) → **SAC-IA** coarse alignment → **Small_GICP** fine registration.  

| Feature | AMCL (Legacy) | Small_GICP (New) |
| :--- | :--- | :--- |
| Initial Pose Dependency | High | **Low** (X: 4–5m, Yaw: 30–45°) |
| Computational Efficiency | Degrades with particles | **Very fast** (faster than Fast_GICP) |
| Robustness | Sensitive to dynamics | High, via feature-based matching |

<div class="row my-4">
  <div class="col-sm d-flex justify-content-center">
    {% include figure.liquid path="assets/img/GICP_result.png" title="Relocalization result (Small_GICP)" class="img-fluid rounded z-depth-1" max-width="820px" %}
  </div>
</div>
<div class="caption mt-2 mb-4">
  SAC-IA coarse alignment followed by Small_GICP fine registration for fast, robust relocalization.
</div>

## 4. Navigation & Path Planning

### 4.1 Global Planning: Hybrid A\*

- Continuous state search $(x, y, \theta)$; kinematic-feasible paths.  
- Dual heuristics:  
  - $h_1$: Holonomic (obstacle-free) via Dubins/Reeds-Shepp.  
  - $h_2$: Non-holonomic (with obstacles) via Dijkstra.  
  - Cost: $\max\{h_1, h_2\}$.

<div class="row my-4">
  <div class="col-sm d-flex justify-content-center">
    {% include figure.liquid path="assets/img/Astar.png" title="Legacy A* path" class="img-fluid rounded z-depth-1" max-width="520px" %}
  </div>
  <div class="col-sm d-flex justify-content-center">
    {% include figure.liquid path="assets/img/Hybrid_Astar.png" title="Hybrid A* kinematic-feasible path" class="img-fluid rounded z-depth-1" max-width="520px" %}
  </div>
</div>
<div class="caption mt-2 mb-4">
  Hybrid A* generates smoother, vehicle-feasible paths compared to grid-based A*.
</div>

### 4.2 Dynamic Obstacle Segmentation

1) De-skew with odometry and align using prior $T^{Map}_{body}$.  
2) Transform scan to Map; far points from prior map → dynamic obstacles.  
3) Health monitor: excessive dynamics trigger relocalization.

## 5. Motion Control

### 5.1 Local Smoothing & Curvature Estimation

- Take the 20 poses before the goal and spline-interpolate:  
  $$
  S(x) = a_i(x-x_i)^3 + b_i(x-x_i)^2 + c_i(x-x_i) + d_i
  $$
- Curvature (Three-Point Method):  
  $$
  \kappa = \frac{2\| (P_1 - P_0) \times (P_2 - P_0) \|}{\| P_1 - P_0 \| \,\| P_2 - P_1 \| \,\| P_2 - P_0 \|}
  $$

### 5.2 Adaptive Lookahead Index

$$
\Delta \text{index} =
\begin{cases}
\lfloor |v| \cdot k_v + N_{base} \rfloor, & \kappa \leq \kappa_{th} \\
N_{curve}, & \text{otherwise}
\end{cases}
$$

### 5.3 Velocity Generation & Yaw Compensation

$$
\begin{bmatrix}
v_x^{cmd} \\
v_y^{cmd}
\end{bmatrix}
= \left(K_p \cdot dist + K_d \cdot \frac{d}{dt}dist\right)
\begin{bmatrix}
\cos\left(\theta - \tfrac{1}{2}\omega_z \Delta t\right) \\
\sin\left(\theta - \tfrac{1}{2}\omega_z \Delta t\right)
\end{bmatrix}
$$

- Compensates gimbal-induced yaw differences between base_link and motion vector, eliminating overshoot.

<div class="row my-4">
  <div class="col-sm d-flex justify-content-center">
    {% include figure.liquid path="assets/img/rate_compensate.PNG" title="Yaw-rate compensation effect" class="img-fluid rounded z-depth-1" max-width="720px" %}
  </div>
</div>
<div class="caption mt-2 mb-4">
  Angular velocity compensation removes overshoot during high-speed rotation.
</div>

## 6. Conclusion

- Point-LIO delivers high-frequency robustness; Small_GICP relocalizes without fragile initial poses.  
- Hybrid A* plus curvature-adaptive PD control yields smooth obstacle avoidance and stable tracking in complex terrain.

[🔗 View on GitHub]({{ page.links.github }})
