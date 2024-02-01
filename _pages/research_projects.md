---
layout: page
title: Research
permalink: /projects/
description: <p> Select research projects conducted during Ph.D. and postdoctoral studies <br> highlighting experience in the following&#58;</p> <ul>   <li>Scientific software development & High-performance computing <ul><li>using MPI, GPU, C++, Python for analysis</li> <li>scaling strategies, profiling & optimization</li> </ul></li>    <li>Quantum and classical charge transport in the next-generation transistors<ul><li>nonequilibrium Green's function (NEGF) method for electrons and phonons, electrostatics</li><li>parallel matrix-based algorithms using MPI, GPU, and the AMReX library</li></ul></li> <li> Kinetic transport in high temperature gas dynamics <ul> <li> direct simulation Monte Carlo (DSMC) method</li> <li>modeling of shock-wave/boundary-layer interactions</li> <li>adaptive mesh refinement (AMR) of octree grids</li> <li> parallel particle-based algorithms</li></ul></li> <li> Data-driven methods and stability theory for modal analysis in fluid dynamics <ul><li>Linear stability analysis (generalized eigenvalue problems)</li> <li>parallel communication reducing algorithms for proper-orthogonal and dynamic-mode decomposition</li> </ul> </li> </ul>
nav: true
nav_order: 1
display_categories: [Postdoc, Ph.D.]
horizontal: false
---

<!-- pages/projects.md -->
<div class="projects">
{% if site.enable_project_categories and page.display_categories %}
  <!-- Display categorized projects -->
  {% for category in page.display_categories %}
  <a id="{{ category }}" href=".#{{ category }}">
    <h2 class="category">{{ category }}</h2>
  </a>
  {% assign categorized_projects = site.projects | where: "category", category %}
  {% assign sorted_projects = categorized_projects | sort: "importance" %}
  <!-- Generate cards for each project -->
  {% if page.horizontal %}
  <div class="container">
    <div class="row row-cols-2">
    {% for project in sorted_projects %}
      {% include projects_horizontal.liquid %}
    {% endfor %}
    </div>
  </div>
  {% else %}
  <div class="grid">
    {% for project in sorted_projects %}
      {% include projects.liquid %}
    {% endfor %}
  </div>
  {% endif %}
  {% endfor %}

{% else %}

<!-- Display projects without categories -->

{% assign sorted_projects = site.projects | sort: "importance" %}

  <!-- Generate cards for each project -->

{% if page.horizontal %}

  <div class="container">
    <div class="row row-cols-2">
    {% for project in sorted_projects %}
      {% include projects_horizontal.liquid %}
    {% endfor %}
    </div>
  </div>
  {% else %}
  <div class="grid">
    {% for project in sorted_projects %}
      {% include projects.liquid %}
    {% endfor %}
  </div>
  {% endif %}
{% endif %}
</div>
