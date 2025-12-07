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

<div class="single-column">
Welcome to my academic portfolio. I am currently an undergraduate at **Tianjin University**, focusing on **Hybrid Control**, **Aerial Manipulation**, and **Robot Learning**.

### Featured Projects

* **Whole-Body MPC for Aerial Manipulation:** Developed a unified control framework addressing coupled dynamics.
* **Portable Data Acquisition System (Galaxea AI):** Designed a robot-free, high-throughput data collection device.
* **RoboMaster Engineering:** Led the embedded system design for high-performance combat robots.

<br>
_Detailed project videos, code repositories, and technical reports are being updated. Please check back shortly._

<div class="projects mt-4">
  <div class="row">
    {% assign sorted_projects = site.projects | sort: 'importance' %}
    {% for project in sorted_projects %}
      {% include projects_horizontal.liquid %}
    {% endfor %}
  </div>
</div>
</div>
