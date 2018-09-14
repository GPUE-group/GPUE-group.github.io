+++
title = "Introduction to GPUE"
description = ""
weight = 1
alwaysopen = true
+++

{{%children style="h2" description="true"%}}

GPUE is a Gross&mdash;Pitaevskii equation solver that is accelerated on GPU hardware with CUDA.
Though the project began as a general method to study vortex dynamics in 2 dimensional Bose&mdash;Einstein Condensates (BECs), it has since grown into a general-purpose BEC simulator using the Split-Operator method for 1, 2, and 3 dimensions and allows for dynamic gauge fields and potentials.
The purpose of this documentation is to provide a general introduction to running GPUE for various purposes and provide a detailed guide for those wishing to develop GPUE in the future.

GPUE supports linux and MacOS environments and has been developed primarily for HPC computing Tesla-series Nvidia compute devices.
Though other platforms may run GPUE, they are not officially supported.

## Purpose of GPUE

GPUE is GPU accelerated software to solve the Gross-Pitaevskii equation for superfluid simulations of Bose--Einstein Condensates (BECs) with a particular emphasis on vortex simulations generated via rotation or gauge fields.
The Gross-Pitaevskii equation is a nonlinear Schr&ouml;dinger equation:

$$
\frac{\partial\Psi(\mathbf{r},t)}{\partial t} = \left( -\frac{(p-mA)^2}{2m} + V(\mathbf{r}) + g|\Psi(\mathbf{r},t)|^2\right)\Psi(\mathbf{r},t)
$$

Where $\Psi(\mathbf{r},t)$ is the many-body wavefunction of the quantum system, $m$ is the atomic mass, $p = -i\hbar\nabla$ is the standard momentum space operator, $A$ is a gauge field to induce rotational effects, $V(\mathbf{r})$ is a potential to trap the atomic system, $g = \frac{4\pi\hbar^2a_s}{m}$ is a coupling factor, and $a_s$ is the scattering length of the atomic species.
Here, the GPE is shown in 1 dimension, but it can easily be extended to 2 or 3 dimensions, if necessary.

From this equation, we can describe how superfluid BEC systems will behave in an experimental setting.
As such, the Gross-Pitaevskii equation is a powerful tool that allows theoretical quantum physicists to better understand superfluid dynamics and explore areas like quantum turbulence in a straightforward computational system.

Currently, there are standard software packages like [GPELab](http://gpelab.math.cnrs.fr/) in Matlab or [TrotterSuzuki](https://trotter-suzuki-mpi.readthedocs.io/en/stable/index.html) which uses GPU acceleration.
These software packages are either too slow or too narrowly focused on other areas of superfluid simulations for simulating vortex dynamics quickly and efficiently.
As such, we have designed GPUE to provide an experimentally realistic model of how a BEC could behave in superfluid turbulence simulations.

In addition, GPUE is able to simulate a wide variety of other BEC and standard Schr&ouml;dinger equations experiments and also allows for the modification of potentials and gauge fields during both imaginary and real-time dynamics.

# The simulation method

GPUE uses the [split-step (also called the split-operator) method](https://www.algorithm-archive.org/contents/split-operator_method/split-operator_method.html) to perform it's simulations.
Because of this, the simulation is split into two distinct parts:
1. Imaginary time evolution to find the lowest energy (ground) state of the quantum system
2. Real time evolution to determine how the BEC would behave in an actual experimental set-up

In most cases, GPUE simulations will consist of creating a standard wavefunction "guess" that is close to the ground state, followed by a small number of steps in imaginary time to find the ground state, and then subsequent simulation in real time.


<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.4/MathJax.js?config=TeX-MML-AM_CHTML">
</script>
<script type="text/x-mathjax-config">
MathJax.Hub.Config({
  tex2jax: {
    inlineMath: [['$','$'], ['\\(','\\)']],
    displayMath: [['$$','$$'], ['\[','\]']],
    processEscapes: true,
    processEnvironments: true,
    skipTags: ['script', 'noscript', 'style', 'textarea', 'pre','code'],
    TeX: { equationNumbers: { autoNumber: "AMS" },
         extensions: ["AMSmath.js", "AMSsymbols.js"] }
  }
});
</script>

