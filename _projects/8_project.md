---
layout: page
title: <span style="text-transform:uppercase;">M</span>odal <span style="text-transform:uppercase;">A</span>nalysis in <span style="text-transform:uppercase;">P</span>arallel
description: A parallel solver using C++ and LAPACK for proper orthogonal and dynamic mode decomposition using communication reducing tall and skinny QR (TSQR) factorization algorithm
img: assets/img/projects/dmd/f_orig.gif
importance: 3
category: small side projects
related_publications: false
---

<div align="justify">
Modal decomposition serves as a valuable tool for understanding complex fluid flow phenomena.
It involves breaking down the solution into individual modes or patterns, each representing unique spatial structures or oscillatory behaviors within the flow. 
This decomposition allows for the identification of dominant patterns, extraction of pertinent information, and potential simplification of flow field representation.
</div>

<div align="justify">
<br>
Methods like proper orthogonal decomposition (POD) are commonly employed to filter noise in images and videos or to reduce their size while retaining dominant features. Numerous variations of this algorithm exist, with many online videos providing explanations and demonstrations with interesting examples. One notable application of modal decomposition is in noise reduction.
</div>

<div align="justify">
<br>
In my Ph.D. research, which heavily relied on the Direct Simulation Monte Carlo (DSMC) method, noise was a significant factor, especially in scenarios involving linear instabilities like those explored in the <a href="https://saurabh-s-sawant.github.io/projects/3_project/">Laminar Separation Bubble Instability</a> project. 
Here, modal decomposition techniques such as proper orthogonal decomposition proved invaluable in understanding the early manifestation of the instabilily.
</div>

<div align="justify">
<br>
The POD method involves performing singular value decomposition (SVD) on a matrix, where the rows correspond to the number of solution points and the columns represent the number of temporal snapshots at which the data is captured.
In the realm of 3D simulations, the sheer number of simulation points can be substantial; for instance, even my 'coarsest mesh' consisted of millions of computational cells representing spatial variation of solution. Conversely, the number of time snapshots tends to be relatively low, typically on the order of thousands.
</div>

<div align="justify">
<br>
This scenario presents a computational challenge: conducting SVD on a matrix that is 'tall and skinny,' meaning it has significantly more rows than columns. Moreover, this operation must be executed in parallel, as the entire matrix cannot be accommodated on a single node of a supercomputer.
</div>

<div align="justify">
<br>
To address this challenge, I developed a parallel solver for POD using C++ and the message passing interface (MPI). The solver employs a communication-reducing algorithm known as the Tall and Skinny QR (TSQR) factorization algorithm, developed by <a href="https://epubs.siam.org/doi/10.1137/080731992">Demmel et al.</a>. You can find the implementation on GitHub at the following <a href="https://github.com/saurabh-s-sawant/POD_DMD">Github link</a>.
</div>

<div align="justify">
<br>
Figure (1) shows an example of using POD to filter DSMC noise {% cite sawant_doublewedge_2022 %}. The POD-reconstructed data exhibits the same spatial spanwise variation but contains very
low statistical noise compared with the DSMC solution.
</div>
<br>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/dmd/uy_OrigPODCompare.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/dmd/uy_1DPODCompare.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
<div align="justify">
Fig. 1. (a) Contours of raw DSMC field on plane R, defined in Fig. (2) of <a href="https://saurabh-s-sawant.github.io/projects/3_project/">Laminar Separation Bubble Instability</a>. Overlaid lines are noise-filtered reconstruction of the field using two most dominant POD modes. (b) Comparison of DSMC (unfiltered) and POD (filtered) field along lines L<sub>1</sub> and L<sub>2</sub> denoted in (a).
</div>
</div>

<div align="justify">
<br>
The code was further extended to include dynamic mode decomposition (DMD), an algorithm that has been widely utilized since its inception by <a href="https://doi.org/10.1017/S0022112010001217">Schmid, P.J.</a>.
Parallelization of this algorithm is described by <a href="https://link.springer.com/article/10.1007/s00162-016-0385-x">Sayadi, T. & Schmid, P.J.</a>.
However, I never ended up using it in my research.
 
As a validation of my code, consider this example from the book <a href="https://epubs.siam.org/doi/book/10.1137/1.9781611974508">Dynamic Mode Decomposition</a> by Kutz et al..
Let f be composed of two decaying functions,
\begin{equation}
f_1 = \Re\left[ \text{sech}(x+3) \exp{(-0.1 t + i 2.3t)}\right]
\end{equation}
\begin{equation}
f_2 = \Re\left[ \frac{2 sech(x)}{tanh(x)} \exp{(-0.2t + i 2.8t)}\right]
\end{equation}

Applying DMD to f for modal decomposition correctly recovers underlying eigenvectors and eigenvalues. 
</div>
<br>


<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/dmd/f_orig.gif" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/dmd/eigenvectors.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/dmd/eigenvalues.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
<div align="justify">
Fig. 1. (a) Function f=f<sub>1</sub> + f<sub>2</sub>. (b) Eigenvectors and (c) continuous time eigenvalues obtained from the DMD algorithm.
</div>
</div>


