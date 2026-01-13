---
layout: about
title: about
permalink: /
subtitle: Robotics | Control | Systems
profile:
  align: right
  image: QingxinWang.jpg
  image_circular: false # crops the image to a circle
  address: >
    <p>Undergraduate Student</p>
    <p>Tianjin University</p>

news: false # includes a list of news items
selected_papers: false # disable selected publications on homepage
social: false # includes social icons at the bottom of the page
---

I am a senior undergraduate at **Tianjin University** and a research intern at the **MARS Lab** (Tsinghua University), advised by **Prof. Hang Zhao**.

My research pursues **agile aerial manipulation**. I focus on bridging **optimization-based control** with rigorous system design to achieve robust interaction in complex environments.

Previously, as a core member of the **PeiYang Robotics Team** (RoboMaster), I architected full-stack autonomy systems—from real-time firmware to state estimation. This experience grounded my philosophy: theoretical optimality must be validated on rugged, real-world hardware.

### Research Interests

- **Whole-Body MPC:** Formulating optimization problems to explicitly handle coupled dynamics, contact constraints, and actuation limits.
- **Agile Aerial Manipulation:** Controlling floating-base systems under aerodynamic disturbances and physical interaction.
- **State Estimation for Control:** Ensuring low-latency, drift-free localization (e.g., via LIO) to support high-bandwidth feedback in aggressive maneuvers.

### Technical Stack

- C/C++, Python, ROS/ROS2, PyTorch, PX4, STM32/Embedded

### Featured projects

<ol class="project-list project-list--compact">
  {% assign featured_projects = site.projects | sort: 'importance' | slice: 0, 3 %}
  {% for project in featured_projects %}
    {% include project_entry.liquid project=project %}
  {% endfor %}
</ol>
