---
layout: page
title: Research
permalink: /projects/
description: <p> Select research projects conducted during Ph.D. and postdoctoral studies <br> highlighting experience in the following&#58;</p> <ul>   <li>Scientific software development & High-performance computing <ul><li>using message passing interface <u>(MPI), GPU, C++, Python</u> for analysis</li> <li><u>scaling, profiling, and performance optimization</u> strategies</li> </ul></li>    <li>Quantum and classical charge transport in the next-generation transistors<ul><li>nonequilibrium Green's function (<u>NEGF</u>) method for electrons and phonons</li> <li>electrostatics with multiple embedded boundaries</li> <li>parallel <u>matrix-based algorithms</u> using MPI, GPU, and the AMReX library</li></ul></li> <li> Kinetic transport in high temperature gas dynamics <ul> <li> direct simulation Monte Carlo (<u>DSMC</u>) method</li> <li>modeling of shock-wave/boundary-layer interactions (<u>SWBLI</u>)</li> <li>adaptive mesh refinement (<u>AMR</u>) of octree grids</li> <li> parallel <u>statistical algorithms</u></li></ul></li> <li> Data-driven methods and stability theory for modal analysis in fluid dynamics <ul><li><u>generalized eigenvalue problems</u> arising from linear stability analysis</li> <li>parallel <u>communication-reducing algorithms</u> for proper-orthogonal and dynamic-mode decomposition</li> </ul> </li> </ul>
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
