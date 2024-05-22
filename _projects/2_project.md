---
layout: page
title: <span style="text-transform:uppercase;">ELEQTRON</span>e<span style="text-transform:uppercase;">X</span>
description: A GPU-Accelerated Self-Consistent Quantum Transport Framework for Modeling Nanomaterials
img: assets/img/projects/quantum_transport/CNTFET_mod.gif
importance: 1
category: Postdoc
related_publications: false
---

<div align="justify">
<a href='https://github.com/AMReX-Microelectronics/ELEQTRONeX'>ELEQTRONeX</a> is a framework for <strong>ele</strong>ctrostatic-<strong>q</strong>uantum <strong>tr</strong>ansport modeling <strong>o</strong>f <strong>n</strong>anomaterials at <strong>ex</strong>ascale, built using the <a href='https://amrex-codes.github.io/amrex/'>AMReX</a> library, currently supporting the modeling carbon nanotube field-effect transistors (CNTFETs) with multiple nanotubes. It is developed as part of a DOE-funded project called 'Codesign and Integration of Nanosensors on CMOS'. See this <a href='https://www.youtube.com/watch?v=snAeWpFTvrs'>overview video</a> for full scale of this project.
</div>
<div align="justify">
<br>
One of the applications of CNTFETs is their use as sensors in advanced photon detectors for applications such as rare event detection (e.g. dark matter, neutrinos, electron-proton decay), biological imaging, single  photon LiDAR for mapping 3D terrains, etc. Carbon nanotubes are particularly attractive due to their high surface-to-volume ratio, making them highly sensitive to their environment. Such sensing applications require modeling arrays of nanotubes on the order of hundreds of nanometers in length, which may be functionalized with photosensing materials such as quantum dots. The goal of ELEQTRONeX is to model such systems, making efficient use of CPU/GPU heterogeneous architectures. 
</div>
<div align="justify">
<br>
The framework comprises three major components: the electrostatic module, the quantum transport module, and the part that self-consistently couples the two modules. The electrostatic module computes the electrostatic potential induced by charges on the surface of carbon nanotubes, as well as by source, drain, and gate terminals, which can be modeled as embedded boundaries with intricate shapes. The quantum transport module uses the nonequilibrium Green's function (<a href='https://courses.cit.cornell.edu/ece5390/4_datta_negf_LNE.pdf'>NEGF</a>) method to model induced charge. Currently, it supports coherent (ballistic) transport, contacts modeled as semi-infinite leads, and Hamiltonian representation using the tight-binding approximation. The self-consistency between the two modules is achieved using <a href='https://journals.aps.org/prb/abstract/10.1103/PhysRevB.34.8391'>Broyden's modified second algorithm</a>, which is parallelized on both CPUs and GPUs. Preliminary studies have demonstrated that the electrostatic and quantum transport modules can compute the potential on billions of grid cells and compute the Green's function for a material with millions of site locations within a couple of seconds, respectively. 
</div>
<br>
(Special thanks to <a href='https://scholar.google.com/citations?user=_ng6y8wAAAAJ&hl=en'>François Léonard</a> for his help with the theory and numerical details of modeling CNTs.)

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/quantum_transport/Summary_ELEQTRONeX.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
<div align="justify">
A scalable approach to modeling 3D nonperiodic arrangement of CNTFETs. The upper left figure shows the self-consistent electrostatic potential at gate-source voltage of -2V. The lower right figure shows differences in the zero-bias conductance in three arrangements: periodic (typically simulated), non-periodic but parallel, and non-parallel (experimentally relevant). The first two arrangements do not show any difference since the spacing between the tubes is larger than the gate-oxide thickness and there is negligible influence from neighboring nanotubes. However, nearly 20% lower conductance is observed in the non-parallel arrangement. The lower left figure shows the weak-scaling analysis of the solver up to 512 GPUs.
</div></div>
<!--
The lower left figure shows the electrostatic potential for a planar CNTFET configuration with gate-source and drain-source biases set at -2 V and 0 V, respectively. In the lower right figure, we observe excellent agreement in the zero-bias conductance with <a href='https://iopscience.iop.org/article/10.1088/0957-4484/17/9/051'>Léonard (2006)</a> for a periodic simulation emulating an infinite array of nanotubes. The figure also highlights the impact of neglecting periodicity and modeling only five nanotubes, with conductance in the middle and first adjacent tubes closely matching the periodic simulation, while that in the farthermost tubes exhibits significantly higher values.

A scalable approach to modeling CNTFETs using the 3D exascale electrostatic-quantum transport framework. The lower left figure shows the variation in the self-consistently computed electrostatic field in a gate-all-around CNTFET due to variation in the user-defined gate-source voltage for a fixed drain-source bias of -0.1 V. The lower right figure shows the magnitude of the drain-source current as a function of gate-source voltage for the same CNTFET and subthreshold swing of 69 mV/decade, along with a comparison with the results of <a href='https://iopscience.iop.org/article/10.1088/0957-4484/17/18/029'>Léonard & Stewart (2006)</a>. 
-->
