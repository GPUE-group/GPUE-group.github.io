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
\displaystyle\sum\limits_{i=1}^{4} \phi_i = 3.14 + 3.14 -2.76 -2.76 = 2\pi.
$$
With this formalism, we can identify the vortex core to the region of a $2\times 2$ grid plaquette. However, we may also determine a better sub-grid resolution position of the core, by realising that a vortex core corresponds with a zero-crossing in the complex plane for both the real and imaginary components. Following [O'Riordan, 2017](https://oist.repo.nii.ac.jp/?action=pages_view_main&active_action=repository_view_main_item_detail&item_id=182&item_no=1&page_id=15&block_id=79)

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