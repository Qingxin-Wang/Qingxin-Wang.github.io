---
layout: page
title: Teleoperation Manipulation Framework
description: A real-time teleoperation interface for robotic arm for RoboMaster 2024/2025, Peiyang Robot
img: assets/img/engineer.jpg
importance: 3
category: work
---

# 🎮 Teleoperation Manipulation Framework

> **Project Overview:** High-frequency compliant teleoperation stack for RoboMaster assembly tasks, combining impedance control with analytical IK on resource-limited hardware.

- **Role:** Control and kinematics engineer
- **Tech Stack:** C, STM32, embedded control, custom IK solver
- **Hardware:** RoboMaster arm (7 DOF), DC motor, custom controller

## Features

### Impedance Control

- Kinematic-only control was brittle under contact; added Cartesian impedance using motor currents for proprioceptive torque estimation.
- Compliant dynamics enabled robust contact adaptation and a 90% success rate in complex assemblies within 10 seconds.

### High-Frequency IK Solver

- Mechanically decoupled wrist axes to satisfy Pieper’s Criterion and allow a closed-form IK solution.
- Achieved stable 150 Hz control on constrained hardware without slow iterative solvers (derivation below).

### Timeline

- Built for RoboMaster 2024; reused in 2025 with minimal changes due to proven reliability.

## Videos

<div class="row justify-content-center">
  <div class="col-sm-10 col-md-8 col-lg-7 mt-3 mt-md-0">
    <video class="w-100 rounded z-depth-1" controls preload="metadata">
      <source src="/assets/video/teleoperation.mp4" type="video/mp4">
      Your browser does not support the video tag.
    </video>
    <div class="caption mt-2">
      2024 Teleoperation First Prototype.
    </div>
  </div>
</div>
<div class="row justify-content-center">
  <div class="col-sm-10 col-md-8 col-lg-7 mt-3 mt-md-0">
    <video class="w-100 rounded z-depth-1" controls preload="metadata">
      <source src="/assets/video/engineer.mp4" type="video/mp4">
      Your browser does not support the video tag.
    </video>
    <div class="caption mt-2">
      2025 Engineer Teleoperation Demo.
    </div>
  </div>
</div>

<details markdown="1">
<summary style="font-weight: 700; font-size: 1.6rem; cursor: pointer;">Robotic arm kinematics derivation (click to expand)</summary>

### robotic arm

The DH frame set (MOD_DH):

<table class="table table-sm">
  <thead>
    <tr>
      <th>Joint</th>
      <th>\( \alpha_{i-1} \)</th>
      <th>\( a_{i-1} \)</th>
      <th>\( d_i \)</th>
      <th>\( \theta_i \)</th>
    </tr>
  </thead>
  <tbody>
    <tr><td>1</td><td>\(0\)</td><td>\(0\)</td><td>\(0\)</td><td>\(\theta_1\)</td></tr>
    <tr><td>2</td><td>\(0\)</td><td>\(a_1\)</td><td>\(0\)</td><td>\(\theta_2\)</td></tr>
    <tr><td>3</td><td>\(-\dfrac{\pi}{2}\)</td><td>\(0\)</td><td>\(d_2\)</td><td>\(\theta_3\)</td></tr>
    <tr><td>4</td><td>\(\dfrac{\pi}{2}\)</td><td>\(0\)</td><td>\(d_3\)</td><td>\(\theta_4\)</td></tr>
    <tr><td>5</td><td>\(-\dfrac{\pi}{2}\)</td><td>\(0\)</td><td>\(d_4\)</td><td>\(\theta_5\)</td></tr>
  </tbody>
</table>

The MCU in the controller estimates its orientation (Kalman Filter) and Cartesian position (3 motors with encoders). Given the target pose, we solve IK for all joints.

#### definition

Initial state of joint x: $P_{x0}$; current state: $P_x$.

#### position part

To decouple end joints for position vs. orientation, consider joints 3/4 overlapped. Let the distance from joint 3/4 to end effector be $l$:

$$
{}_5p^3_{e} = {\begin{bmatrix}0&0&l\end{bmatrix}}^\top
$$

Controller gives $R^{50}_5$, and from fig 1:

$$
R^0_{50} = \begin{bmatrix}0&0&1\\-1&0&0\\0&-1&0\end{bmatrix}
$$

Transform to frame 0:

$$
{}_0p^3_e=R^0_{50}R^{50}_5{}_5p^3_{e}
$$

Relative position joint 0 to 3 in frame 0:

$$
{}_0p^0_3 = {}_0p^0_e - {}_0p^3_e
$$

with ${}_0p^0_e$ from the controller. Z is decoupled: prismatic joint reaches height, XY solved for $\theta_1,\theta_2$.

#### orientation part

End three axes intersect. Controller gives $R^{50}_5$; joints 1–2 define $R^{30}_3=R^{50}_3$:

$$
R^3_5 = {R^{50}_3}^\top R^{50}_5
$$

DH transform:

$$
T^{i-1}_i = \begin{bmatrix}
C\theta_i & -S\theta_i & 0 & a_{i-1} \\
S\theta_iC\alpha_{i-1} & C\theta_iC\alpha_{i-1} & -S\alpha_{i-1} & -S\alpha_{i-1}d_i \\
S\theta_iS\alpha_{i-1} & C\theta_iS\alpha_{i-1} & C\alpha_{i-1} & C\alpha_{i-1}d_i \\
0 & 0 & 0 & 1
\end{bmatrix},
\quad C \triangleq \cos,\; S \triangleq \sin
$$

Substituting DH table:

$$
R^3_5 =\begin{bmatrix}
C\theta_3 C\theta_4 C\theta_5-S\theta_3 S\theta_5 & -C\theta_3 C\theta_4 C\theta_5-S\theta_3 C\theta_5 & -C\theta_3 C\theta_4  \\
S\theta_4C\theta_5 & -S\theta_4S\theta_5 & C\theta_4 \\
-S\theta_3 C\theta_4 C\theta_5-C\theta_3 S\theta_5 & S\theta_3 C\theta_4 C\theta_5-C\theta_3 C\theta_5  & S\theta_3S\theta_4 \\
\end{bmatrix}
$$

Solve $\theta_3,\theta_4,\theta_5$ from above.

</details>
