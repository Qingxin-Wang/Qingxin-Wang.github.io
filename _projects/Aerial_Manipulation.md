---
layout: page
title: Efficient Whole-Body MPC for Aerial Manipulation
description: Unified whole-body control with sim-to-real deployment validation
img: "assets/img/Aerial Manipulation.jpg"
importance: 1
category: work
related_publications: false
---

# 🚁 Efficient Whole-Body MPC for Aerial Manipulation

> **Project Overview:** A unified, end-effector-centric Whole-Body MPC framework for aerial manipulators. By modeling the UAV and arm as a single dynamic entity, this system achieves precise trajectory tracking and force interaction. The project spans from mathematical formulation (MJPC) to a decoupled simulation architecture and sim-to-real deployment on PX4 hardware.

- **Role:** Lead Researcher & System Architect
- **Tech Stack:** MuJoCo MJPC, Crocoddyl, Pinocchio, PX4 Autopilot, ROS, C++, Python
- **Hardware:** Quadcopter + 2-DOF Arm, PX4 flight controller, Mid-360 LiDAR
- **Keywords:** Whole-Body Model Predictive Control (MPC), Impedance Control, System Identification, Sim-to-Real,

## 1. Motivation: Breaking the Decoupling Barrier

Traditional aerial manipulation treats the drone and arm as separate subsystems, often viewing interaction forces merely as external disturbances. This decoupled view simplifies control but severely caps performance and precision.

**My approach:** Build a unified, whole-body control framework that explicitly models the coupling between the floating base and the manipulator. This allows the optimizer to _exploit_ coupling dynamics rather than rejecting them, enabling precise interaction without heavy, fully actuated hardware.

## 2. Methodology: End-Effector Centric Optimization

We adopted **MJPC (MuJoCo MPC)** with an iLQG solver to handle the non-linear dynamics of the underactuated system.

### A. Unified Dynamics & Cost Formulation

We modeled the 8-DOF system (UAV + Arm) as a single entity in the MJCF format, exported and refined from SolidWorks. The core innovation lies in the cost function design $J(x, U)$:

$$J_0(x,U) = \sum_{i=0}^{N-1} \ell(x_i, u_i) + \ell_f(x_N)$$

- **Residuals Design:** We defined precise residuals for End-Effector position ($e_p$) and orientation ($e_R$), alongside body pose ($e_{BR}$) and control inputs to penalize jerky motions.
- **Safety via Smoothed Penalties:** Since vanilla MJPC lacks hard constraint handling, I implemented **smoothed penalty functions** (using cubic splines, $\mathcal{L}_\epsilon[x]$) to strictly enforce actuator limits and safety boundaries (e.g., $p_B^{max}$) while preserving a differentiable gradient landscape for the solver.

## 3. System Architecture & Engineering Challenges

To ensure robustness and scalability, I engineered a **decoupled simulation framework** that separates the control algorithm from the environment.

### A. Decoupled Architecture

- **StateManager & Controller:** Designed a stateless `mjpc_ctrl_headless` module that communicates via a thread-safe `StateManager`.
- **Flexibility:** This architecture allows seamless switching between **MuJoCo** (fast physics), **Gazebo + PX4 SITL** (realistic flight stack), and **Physical Hardware** without changing the core control code.

### B. Bridging the Reality Gap (Dynamics Alignment)

A major challenge was the dynamic mismatch between MuJoCo's ideal actuators and the real-world PX4 flight controller.

- **Actuator Mapping:** I derived a linear mapping to align MuJoCo's PID parameters with PX4's normalized rate loop. This ensured that a specific control signal produced identical physical responses in both simulators.
- **Validation:** The pitch and roll rate responses in Gazebo (green) and MuJoCo (orange) achieved a near-perfect match after alignment.

<div class="row">
  <div class="col-lg-10 col-md-11 mx-auto mt-3">
    {% include figure.liquid path="assets/img/arm_alignment.png" title="Arm sim-to-sim alignment (orange: MuJoCo, blue: Gazebo)" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<p class="text-center text-muted mb-4">Arm sim-to-sim alignment (orange: MuJoCo, blue: Gazebo).</p>

<div class="row">
  <div class="col-lg-10 col-md-11 mx-auto mt-3">
    {% include figure.liquid path="assets/img/px4_alignment.png" title="PX4 attitude alignment (orange: MuJoCo response, green: PX4 SITL)" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<p class="text-center text-muted mb-4">PX4 attitude alignment (orange: MuJoCo response, green: PX4 SITL).</p>

### C. Solving Tracking Latency

Standard MJPC uses a static target per iteration, causing tracking lag. I implemented a time-based trajectory interpolation module that feeds the solver with future reference states, significantly reducing dynamic tracking error.

## 4. Quantitative Results

The framework underwent rigorous validation in high-fidelity PX4 SITL (Software-in-the-Loop) environments tracking complex figure-8 trajectories.

- **Tracking Precision:** Achieved sub-5cm accuracy in complex 3D maneuvers.
  - **RMSE (3D Position):** **0.038 m** (X: 0.030m, Y: 0.021m, Z: 0.011m)
  - **Attitude Stability:** Maintained pitch RMSE **0.45** and yaw RMSE **0.64**.

<div class="row justify-content-center mt-3">
  <div class="col-lg-10 col-md-11 mx-auto">
    <div class="embed-responsive embed-responsive-16by9 rounded overflow-hidden mb-3">
      <video class="embed-responsive-item" controls preload="metadata">
        <source src="/assets/video/mjpc.mp4" type="video/mp4">
        Your browser does not support the video tag.
      </video>
    </div>
    <p class="text-center text-muted mb-4">MJPC whole-body tracking demo.</p>
  </div>
</div>

<div class="row justify-content-center mt-4">
  <div class="col-lg-6 col-md-10 mb-4">
    {% include figure.liquid path="assets/img/EE_tracking.png" title="End-effector trajectory (orange: actual, blue: reference)" class="img-fluid rounded z-depth-1" %}
    <p class="text-center text-muted mb-0">End-effector trajectory (orange: actual ee path, blue: reference, green: actual body path).</p>
  </div>
  <div class="col-lg-6 col-md-10 mb-4">
    {% include figure.liquid path="assets/img/EE_error.png" title="End-effector tracking error (RMSE shown, colors per axis)" class="img-fluid rounded z-depth-1" %}
    <p class="text-center text-muted mb-0">End-effector error (per-axis colors per legend, RMSE annotated in the title).</p>
  </div>
</div>

<div class="row justify-content-center mt-2">
  <div class="col-lg-6 col-md-10 mb-4">
    {% include figure.liquid path="assets/img/pitch_tracking.png" title="Pitch tracking overlay" class="img-fluid rounded z-depth-1" %}
    <p class="text-center text-muted mb-0">Pitch tracking (orange: actual Pitch, blue: reference, green: error).</p>
  </div>
  <div class="col-lg-6 col-md-10 mb-4">
    {% include figure.liquid path="assets/img/yaw_tracking.png" title="Yaw tracking overlay" class="img-fluid rounded z-depth-1" %}
    <p class="text-center text-muted mb-0">Yaw tracking (orange: actual Yaw, blue: reference, green: error).</p>
  </div>
</div>

## 5. Hardware Deployment: Perception & State Estimation

MPC control here runs **without the arm attached**, paired with LiDAR-based state estimation for stable flight.

<div class="row align-items-start mt-3">
  <div class="col-lg-6 col-md-7">
    <h4 class="h5 mb-2">A. Hardware Stack</h4>
    <ul class="mb-3">
      <li>Airframe: Custom quadrotor with a bottom-mounted 2-DOF servo arm.</li>
      <li>Compute: Pixhawk 6C handling attitude; onboard NUC/Jetson runs MPC and SLAM.</li>
      <li>Sensing: Livox Mid-360 LiDAR (omnidirectional) with integrated IMU for tightly coupled feedback.</li>
    </ul>
  </div>
  <div class="col-lg-6 col-md-5">
    <div class="embed-responsive embed-responsive-16by9 rounded overflow-hidden mb-3">
      <video class="embed-responsive-item" controls preload="metadata">
        <source src="/assets/video/LIO_on_UAV.mp4" type="video/mp4">
        Your browser does not support the video tag.
      </video>
    </div>
  </div>
</div>

### B. High-Bandwidth LIO Integration

- Challenge: Standard GPS/VIO pipelines drift or update too slowly, destabilizing the MPC during contact-rich tasks.
- Algorithm: Point-LIO fuses raw Mid-360 point clouds with IMU to emit odometry at **100 Hz**.
- Closed loop: The estimated state $\hat{x}$ is transformed to the body frame and streamed via MAVROS directly into the MPC solver, enabling drift-free hovering and precise indoor maneuvers.

## 6. Evolution: From Numerical to Analytical

While finite-difference derivatives in MJPC validated the concept, they limited the convergence speed. The framework is currently pivoting to **Analytic Differential Dynamic Programming (DDP)** using **Crocoddyl** and **Pinocchio**. By computing analytical derivatives, we target full convergence within a 100 Hz control loop, paving the way for highly dynamic aerial interaction tasks.

<div class="row align-items-start mt-3">
  <div class="col-lg-6 col-md-7">
    <p class="mb-2"><strong>Latest DDP result:</strong> analytic derivatives running at 100 Hz with tight tracking.</p>
    <ul class="mb-0">
      <li>RMSE x: <strong>0.0141m</strong></li>
      <li>RMSE y: <strong>0.0001m</strong></li>
      <li>RMSE z: <strong>0.0089m</strong></li>
      <li>RMSE 3D: <strong>0.0167m</strong></li>
    </ul>
  </div>
  <div class="col-lg-6 col-md-5">
    <div class="embed-responsive embed-responsive-16by9 rounded overflow-hidden mb-3">
      <video class="embed-responsive-item" controls preload="metadata">
        <source src="/assets/video/ddp2.mp4" type="video/mp4">
        Your browser does not support the video tag.
      </video>
    </div>
  </div>
</div>

---
