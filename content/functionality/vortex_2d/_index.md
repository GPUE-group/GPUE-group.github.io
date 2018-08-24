+++
title = "2D Vortex Tracking"
description = ""
weight = 4
alwaysopen = true
+++

One of the major components of GPUE is the ability to track and manipulate vortices in Bose-Einstein condensates. While we can work with creating vortices in 3D, the majority of the vortex creation and manipulation framework exists solely in 2D. This is due to the 2D vortex code being developed for the project on vortex dynamics, published in PRA [here](https://journals.aps.org/pra/abstract/10.1103/PhysRevA.94.053603) ([arXiV version here](https://arxiv.org/abs/1608.07756)).

To allow us to track and manipulate vortices in a (2D) condensates, we require some numerical techniques and tools:

1. Vortex detection.
2. Vortex position refinement
3. Vortex unique identification and tracking.
4. Arbitrary phase control of the condensate.
5. Lattice model creation.

## 1. Vortex detection
To find a vortex in the condensate, we can rely on several methods, such as tracking the condensate density minima. However, given the nature of the wavefunction (a complex valued scalar field), and a quantum vortex (topological defect of the wavefunction), we can examine the phase of the condensate, $\phi$, such that $\Psi=|\Psi|\exp\left(i \phi\right)$. Every vortex in the condensate will have integer winding in the complex plane, wherein the phase has a singular point around which it winds through $2\pi$. By examining the condensate for these phase windings, we can identify the presence of a vortex to a location on our numerical grid.

![Phase plaquette](/img/phi_grid.png)

The above image show the phase of a condensate containing four vortices. The zoomed region shows the numerical values of the sampled grid near this vortex core. By following a closed path around the dotted green line the the vortex core can be determined when the value is $\pm 2\pi$, positive for vortices, negative for anti-vortices (ie, vortices rotating in the opposite direction). In this instance,
$$
\displaystyle\sum\limits_{i=1}^{4} \phi_i = 3.14 + 3.14 +2.76 -2.76 = 2\pi.
$$
From the above plaquette, we can say that a vortex core has been identified, and as a result, we can keep note of the planar indices corresponding to this.

## 2. Vortex position refinement

With the above formalism, we can identify the vortex core to the region of a $2\times 2$ grid plaquette. However, we may also determine a better sub-grid resolution position of the core, by realising that a vortex core corresponds with a zero-crossing in the wavefunction for both the real and imaginary components. Following the formalism discussed in [O'Riordan, 2017](https://oist.repo.nii.ac.jp/?action=pages_view_main&active_action=repository_view_main_item_detail&item_id=182&item_no=1&page_id=15&block_id=79), we can calculate the 2D deviation from 0 in for the phase within this region, $\Delta \mathbf{r} = (\Delta x, \Delta y)$, and use this value to update the position as $\mathbf{r} \rightarrow \mathbf{r} - \Delta\mathbf{r}$.

For this we use the least-squares formalism, and attempt to minimise the function
$$
S(\mathbf{r}) = \displaystyle\sum |b\_i - \displaystyle\sum A\_{ij} r\_j |^2
$$
with our observations as
$$
     \mathbf{b} = \left(
     \begin{array}{cccc}
         \Psi(x_0,y_0) & \Psi(x_0,y_1) & \Psi(x_1,y_0) & \Psi(x_1,y_1)
     \end{array} \right)^{T},
$$. The integers are the index $(x,y)$ indices in our plaquette (asuming clockwise or counter-clockwise is fine, provided we are consistent). Our $\mathbf{A}$ matrix represents the $(x,y,c)$ data points, such that $x+y=-c$,  on which we sample our wavefunction, and can be defined as
$$
     \mathbf{A} = \left(
     \begin{array}{ccc}
         0 & 0 & 1 \\\\\
         0 & 1 & 1 \\\\\
         1 & 0 & 1 \\\\\
         1 & 1 & 1
     \end{array}\right).
$$

The minimisation problem can be solved by setting $\partial\_\mathbf{r}S(\mathbf{r})=0$. For a given $\mathbf{A}$ and $\mathbf{b}$, with an unknown position $\mathbf{r}$, and an approximate vortex plaquette position, $\mathbf{r}\_0$, we can determine the unknown $\mathbf{r}$ as
\begin{align}
    \mathbf{A}^{T}\mathbf{A} \mathbf{r} &= \mathbf{A}^{T}\mathbf{b}, \\\\\
    \mathbf{r} &= (\mathbf{A}^{T}\mathbf{A})^{-1}\mathbf{A}^{T}\mathbf{b}.
\end{align}

The resulting $\mathbf{r}$ then becomes
\begin{align}
    \mathbf{r} &= \left(\begin{array}{c}
         x \\\\\
         y \\\\\
         c
     \end{array}\right) = \left(
     \begin{array}{c}
         {\left( -\Psi(x\_0,y\_0) + \Psi(x\_0,y\_1) - \Psi(x\_1,y\_0) + \Psi(x\_1,y\_1) \right)}{/2} \\\\\
         {\left( -\Psi(x\_0,y\_0) - \Psi(x\_0,y\_1) + \Psi(x\_1,y\_0) + \Psi(x\_1,y\_1) \right)}{/2} \\\\\
         3\Psi(x\_0,y\_0) + \Psi(x\_0,y\_1) - \Psi(x\_1,y\_0) - \Psi(x\_1,y\_1)
     \end{array}\right).
\end{align}

From this, we can determine the correction to the vortex plaquette position using $\mathbf{r} = \mathbf{r}\_0 - \Delta\mathbf{r}$, and by solving the linear system
$$
     \left(\begin{array}{cc}
         \Re(x) & \Re(y) \\\\\
         \Im(x) & \Im(y) \\\\\
     \end{array}\right)
     \left(
     \begin{array}{c}
         \Delta x \\\\\
         \Delta y
     \end{array}\right)
     = -
     \left(
     \begin{array}{c}
         \Re(\mathrm{c}) \\\\\
         \Im(\mathrm{c})
     \end{array}\right),
$$
which allows us to examine the zero crossings in both the real and imaginary planes for both $x$ and $y$ dimensions. Using this approach our vortex positions are now siginificantly more accurate, and can be used to allow trajectory calculations, and statistical properties. When tracking vortices, the condensate outputs their discovered positions into a CSV file `vort\_arr\_<timestep>`, one for every printed wavefunction time-point. A sample output of the CSV file format is 
```csv
#  X,   X_refined,  Y,   Y_refined, Winding
 485,4.858494e+02,485,4.858132e+02,1
 485,4.858717e+02,538,5.381655e+02,1
 538,5.381728e+02,485,4.858345e+02,1
 538,5.381506e+02,538,5.381441e+02,1
```
for a given four vortex condensate. The values for positions are given in terms of the numerical grid coordinates. To determine actual positions, for a given simulation, it is necessary to examine `Params.dat`, and check the grid-size ($\textrm{xDim}$), grid-increments ($\textrm{dx}$), and normalise the values from 0. As an example, $X\_\textrm{pos} = (X\_\textrm{refined}-\textrm{xDim}/2)*\textrm{dx}$. For the above simulation, $\textrm{xDim}=1024$, $\textrm{dx}=6.84732\times 10^{-7}~\textrm{(m)}$, given a position of $X\_\textrm{pos} = -1.7906\times 10^{-5}$ for a condensate centered on $(0,0)$ at grid point $[511,511]$ (or $[512, 512]$ if you are a 1-based indexing person).
## 3. Vortex unique identification and tracking.
If we are interested in statistical quantities such as vortex count, average separation, or distribution of windings (rotation directions), we can use the above methods. However, sometimes we wish to indentify and track unqiue vortices over the course of a simulation. As we are employing a field-based simulation in GPUE, namely solving the Gross-Pitaevskii equation for the wavefunction at points in time, we cannot maintain knowledge of our vortices between timesteps --- each newly simulated wavefunction follows no particle objects which may be easily tracked. Instead, we must re-run the above tracking methods for each wavefunction at an in time to determine the vortex positions.

Given these newly determined vortex positions, it is important to identify which vortices at previous timepoints correspond to vortices in the current timepoints. For this, we rely on the unordered vortex positions file



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