---
layout: about
title: about
permalink: /
subtitle: Robotics Researcher | Embedded Systems Engineer

profile:
  align: right
  image: prof_pic.jpg
  image_circular: false # crops the image to a circle
  address: >
    <p>Undergraduate Student</p>
    <p>Tianjin University</p>

news: false  # includes a list of news items
selected_papers: false # disable selected publications on homepage
social: false  # includes social icons at the bottom of the page
---

Welcome to my academic portfolio. I am an undergraduate at **Tianjin University**, working on **Hybrid Control**, **Aerial Manipulation**, and **Robot Learning**. I enjoy building real-world systems where control, perception, and embedded hardware meet.

### Research interests

- Robust and adaptive control for agile aerial manipulation
- Whole-body model predictive control and contact-rich planning
- Embedded sensing pipelines that close the loop at high bandwidth

### Recent highlights

- **Whole-body MPC for aerial manipulation** — unified control of coupled quadrotor-arm dynamics with hardware validation.
- **Portable data acquisition (Galaxea AI)** — robot-free, high-throughput dataset collection for manipulation and perception.
- **RoboMaster embedded stack** — led design of high-performance control and sensing boards for competition robots.

### Featured projects

<ol class="project-list project-list--compact">
  {% assign featured_projects = site.projects | sort: 'importance' | slice: 0, 3 %}
  {% for project in featured_projects %}
    {% include project_entry.liquid project=project %}
  {% endfor %}
</ol>

For more details, see the full list on the <a href="{{ '/projects/' | relative_url }}">projects page</a>.
