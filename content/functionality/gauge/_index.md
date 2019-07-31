+++
title = "Gauge Fields"
description = ""
weight = 3
alwaysopen = true
+++

One notable feature of GPUE is its ability to use gauge fields to create rotational effects in BEC simulations.
Gauge fields are somewhat tricky to understand and this guide is not meant to provide a full physical understanding of what the fields are or how they work.
If you with to learn more about the physical interpretation of these fields, [this review by Jean Dalibard](https://arxiv.org/abs/1504.05520) is relatively in-depth.

This section will highlight how gauge fields are implemented in GPUE and how to use them on your own as a user of the codebase.
If you wish to provide a new default field in GPUE, itself, please go to the developer guide for this later in the documentation and provide an appropriate PR to GPUE on [GitHub](https://github.com/GPUE-group/GPUE)

{{%children style="h2" description="true"%}}

## Introduction to gauge fields

For the purposes of the GPUE simulations, we will be solving the non-linear Schr&ouml;dinger equation:

$$
\frac{\partial \Psi(\mathbf{r},t)}{\partial t} = \left( \frac{(p-m\mathbf{A})^2}{2m} + V_0 + g|\Psi(\mathbf{r},t)|^2 \right)\Psi(\mathbf{r},t),
$$

where $\Psi(\mathbf{r},t)$ is a complex-valued wavefunction, $r$ is a real-space grid, $p$ is a momentum-space grid, $m$ is the mass of the atomic system, $V_0$ is a real-valued trapping potential, $g$ is a coupling constant, and $A$ is a real-valued gauge field.
As mentioned, the gauge field is hard to physically interpret; however, it can be more easily understood through the artificial magnetic field, $B = \nabla \times A$.
Here, it appears that rotation occurs around the magnetic field lines, so vortices in a BEC will follow the direction of the magnetic field at any moment in time.

Gauge fields are implemented in GPUE by splitting the operator up in position and momentum space.
Because $p$ is completely in momentum space, while $A$ is in real space, the $\frac{(p-mA)^2}{2m}$ term will have three different types of values after expanding:

$$
\frac{(p-mA)^2}{2m} = \frac{p^2 + (mA)^2 + 2mpA}{2m}
$$

Here, $\frac{p^2}{2m}$ will be in momentum-space, $\frac{mA^2}{2}$ will be completely in position-space, and $pA$ will be in momentum-space for one dimension and position space for all other dimensions.
For example, one possible gauge field can be created with the angular momentum operator as $xp_y - yp_x$.

Because of this, we need to perform a 1 dimensional FFT on our wavefunction in order to apply our gauge fields to the system.
This means that when we run the simulation with gauge fields, we need to perform an additional FFT every timestep for each dimension of our system.
In addition, we need to read in a field for each dimension.
For example, with $A = xp_y - yp_x$, we will use the following field:

$$
\begin{align}
A_x &= -y \newline
A_y &= x \newline
A_z &= 0
\end{align}
$$

To apply this field, we will need to FFT our wavefunction in first the x-direction, then multiply by $A_x$, iFFT back and do the same for $A_y$ and $A_z$.
This is a costly process, which is why you should only use gauge fields if you need them.

As mentioned, this documentation does not intend to provide a full in-depth description of gauge fields and artificial magnetic fields.
It instead attepts to provide the basics for users that may need more information about what gauge fields are and how they influence the simulation.
Basically, if you want to do vortex simulations, you will need gauge fields.

## Using gauge fields in GPUE

GPUE comes packaged with a few in-built gauge fields:

1. *Rotation*: This is used with the `./gpue -A rotation` flag.
Here, we apply the rotational operation to the wavefunction as $\Omega(xp_y - yp_x)$, where $\Omega$ is some rotation frequency.
If a rotation above some critical rotation frequency is used, vortices will appear in the BEC, eventually creating a triangular vortex lattice.
2. *Constant:* This is used with the `./gpue -A constant` flag.
Here, we simply read in $A_x = A_y = A_z = 0$.
This is useful for debugging in certain cases
3. *Test:* This is used with the `./gpue -A test` flag.
Here, we apply a constant field in $A_y$ and $A_z$ and provide a sinusoudal field in $A_x$.
This is another testing field that does not use standard rotation.

To run the simulation with gauge fields, the `-l` flag is necessary and will turn on the part of the simulation with the additional FFT's for applying the necessary gauge field.
*No gauge field will be applied without the `-l` flag!*

Gauge fields can also be used with the dynamic fields to be covered in the next section!

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
