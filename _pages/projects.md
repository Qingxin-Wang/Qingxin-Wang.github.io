---
layout: page
title: projects
permalink: /projects/
description: A growing collection of your cool projects.
nav: false
nav_order: 3
---

<div class="projects projects-list">
  {% assign sorted_projects = site.projects | sort: "importance" %}
  <ol class="project-list">
    {% for project in sorted_projects %}
      {% include project_entry.liquid project=project %}
    {% endfor %}
  </ol>
</div>
