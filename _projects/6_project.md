---
layout: page
title: <span style="text-transform:uppercase;">T</span>hermal <span style="text-transform:uppercase;">P</span>rotection <span style="text-transform:uppercase;">S</span>ystem modeling
description: Multi-scale thermal response modeling of AVCOAT-like thermal protection material
img: assets/img/projects/avcoat/heatflux_3d.png
importance: 2
category: Ph.D.
related_publications: true
---

<div align="justify">
During the hypersonic reentry of spacecraft into Earth's atmosphere, immense heat is generated. To safeguard astronauts onboard, it's crucial to prevent the spacecraft's internal body from overheating. This is where ablative thermal protection systems (TPS) come into play.
</div>

<div align="justify">
<br>
One significant advantage of these TPS materials is their porous microstructure, as shown in figure (1a), making them lightweight and capable of reducing heat loads on the space capsule through thermal depolymerization of composite materials.  This process, known as pyrolysis, involves the generation of gaseous species within the heat shield's virgin microstructure. The resulting pressure buildup propels the pyrolysis gases outward towards the boundary layer.
</div>

<div align="justify">
<br>
As these gases move upstream, they extract heat from the already pyrolyzed, relatively hotter composite, known as char. However, the porous nature of ablative TPS materials may also permit an inward flow of hot boundary layer gases into the microstructure. Therefore, understanding the interaction between the boundary layer and pyrolysis species is essential for accurately predicting the performance of ablative TPS.
</div>

<div align="justify">
<br>
At the kinetic level, collisions between boundary layer and pyrolysis gases, along with the internal material microstructure, directly influence the material's thermal response by altering the thermal and bulk velocity components of the gases. This affects convective heat fluxes on both the spacecraft surface and within the material's microstructure, potentially impacting conductive and radiative fluxes. Even when the external hypersonic flow is entirely continuum, the microscale Knudsen number within the material varies from transitional to continuum due to its small length scale.
</div>

<div align="justify">
<br>
Traditionally, thermal responses of TPS have been explored using volume-averaged material response (MR) solvers, categorized as type-1, 2, or 3, based on modeling intricacy. However, these approaches do not model TPS material micro-morphology.
We investigated a class of TPS materials similar to <a href="https://scitechconnect.elsevier.com/the-space-between-voids-in-heat-shield-protecting-orion-spacecraft/">AVCOAT</a> using a multi-scale kinetic approach, combining Direct Simulation Monte Carlo (DSMC) for convective heat transfer modeling and a stochastic random walk method for conduction and radiation modeling. 
Our aim was to understand the role of material microstructure with its thermal response.
</div>
<br>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/avcoat/overview.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
<div align="justify">
Fig. 1. (a) An SEM image of <a href="https://scitechconnect.elsevier.com/the-space-between-voids-in-heat-shield-protecting-orion-spacecraft/">AVCOAT sample</a>. (b) A CAD model of microstructure approximating the SEM image with the one dimensional plates used in the walker simulations and finite volume cells for the MR solver. The DSMC simulation computational particles (filled circles) are designated by solid and hollow headed arrows for the boundary layer and pyrolysis species, respectively. (c) Schematic of the walker model showing walkers (unfilled circles) that are introduced to simulate a convective heat flux boundary (dashed arrows) and the evaluation of walker representation as radiative or conductive heat carriers (solid vs. open arrow heads) performed at the interface between two plates.
</div></div>

<div align="justify">
Toward this goal, we developed a CAD model of the microstructure, illustrated in figure (1b), to mimic AVCOAT's characteristics, such as composition, void fraction, and average balloon radius. Initially, silica fibers were omitted from the model, aligning with findings from molecular dynamic (MD) studies {% cite harpale_tps_2018 %} indicating that they do not pyrolyze at peak temperatures endured by the Orion heatshield. 
Subsequently, we established a comprehensive stochastic model for conduction-radiation and conducted extensive validation studies, as outlined in {% cite sawant_avcoat_2019 %}.
The DSMC and material thermal response solutions were loosely coupled because the time taken for the flow to reach steady state is at least an order of magnitude smaller than that required for the material temperature profile to change. 
</div>
<br>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/avcoat/approach.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
<div align="justify">
Fig. 2. Flow chart of the solution strategy. One iteration refers to a DSMC simulation followed by a thermal response. t<sub>i</sub>; &Delta;t<sub>D</sub>, and &Delta;t<sub>TR</sub>, are the time at which the DSMC simulation is performed, time elapsed during the DSMC simulation, and the elapsed time during the thermal analysis, respectively. &Delta;t<sub>D</sub> << &Delta;t<sub>TR</sub>. See {% cite sawant_avcoat_2019 %} for explanation of symbols.
</div></div>

<div align="justify">
Figure (2) presents an overview of the multi-scale computational strategy. A one-dimensional material response (MR) solver was developed to provide subsonic gas pressures at the inlet and outlet boundaries, P<sub>in</sub> and P<sub>out</sub>, as well as initial profiles of material temperature, T<sub>s</sub>, and porosity, &phi;, along the material depth. The DSMC simulations yield convective heat fluxes Q<sub>conv</sub> of boundary layer and pyrolysis gases as output, which is subsequently utilized as input to the stochastic thermal response model, known as the Walker method.
We also conducted studies without the DSMC step, where the MR solver was used to setup only the Walker method, allowing us to isolate the role of detailed interactions of boundary-layer and pyrolysis gases on thermal response. 
The solutions were compared with standalone simulations of a type-2 MR solver.
</div>

<div align="justify">
<br>
One of the important findings of our work was: the codes that do not include the detailed interactions of boundary layer and pyrolysis gases, such as traditional type-2 MR solvers, significantly underpredict the TPS material temperature.
For details, please refer to {% cite sawant_avcoat_2019 %}.
</div>
<br>
