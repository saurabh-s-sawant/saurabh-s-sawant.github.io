---
layout: page
title: <span style="text-transform:uppercase;">P</span>article<span style="text-transform:uppercase;">-G</span>as <span style="text-transform:uppercase;">I</span>nteractions using <span style="text-transform:uppercase;">FLASH</span>
description: Validation of particle module in the FLASH solver.
img: assets/img/projects/particleLifting/schlieren.gif
importance: 3
category: small side projects
related_publications: true
---

<div align="justify">
For a small side project during my Ph.D., I got an opportunity to work on the <a href="https://flash.rochester.edu/site/flashcode/">FLASH</a> solver, which is an open-source, scalable simulation tool for modeling compressible flow problems in astrophysical environments. 
The goal was to modify the particle module in FLASH for modeling particle lifting mechanisms due to gas-particle interactions, as well as plasma-particle interactions.
I made a small contribution to the first part, while working with <a href="https://www.akhilmarayikkottu.com/">Akhil Marayikkottu</a>, who led the project to completion and published a paper {% cite marayikkottu_2021 %}.
As a validation, I modeled particle lifting when a normal shock passes over a particle. 
As shown in 1(b), the result compares well with experiments.
</div>
<br>

<div class="row">
    <div class="col-sm-7 mt-2 mt-md-0"> <!-- Assign col-sm-8 to make the first figure larger -->
        {% include figure.liquid path="assets/img/projects/particleLifting/ShockTube.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-5 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/particleLifting/validation.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
<div align="justify">
Fig. 1.(a) An instance of shock tube simulation showing important flow features and the trajectory of a lifted particle. (b) Comparison of particle trajectory with numerical results of <a href="https://www.scopus.com/record/display.uri?eid=2-s2.0-0036273404&origin=inward">Gosteev & Fedorov</a> and experiments of <a href="https://doi.org/10.1016/0301-9322(78)90028-9">Merzkirch & Bracht</a>.
</div></div>

