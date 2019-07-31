+++
title = "Building GPUE"
description = ""
weight = 2
alwaysopen = true
+++

{{%children style="h2" description="true"%}}

GPUE uses CMake to build.

## Dependencies and Hardware

We have attempted to maintain a small dependency list for GPUE, and so only require the following dependencies:

1. CUDA (version $\geq$7.5.18)
2. CUFFT (bundled with CUDA)
3. GCC (version $\geq$4.9) or clang (version $\geq$3.9)
4. CMake (version $\geq$3.8)

GPUE performs computation almost entirely on GPU hardware, and the `sm_2.0` (Fermi) to `sm_7.0` (Volta) architectures are all supported.
A list of all cards within these ranges can be found [on wikipedia](https://en.wikipedia.org/wiki/CUDA#GPUs_supported).

## CMake

CMake is the building system for GPUE, and requires CMake version $\geq$ 3.8.
To build, run:

```
cmake .
```

in the primary GPUE directory and then run `make`.
`make clean` will clean the directory for rebuilding.

## Testing GPUE

Once GPUE is built, please run unit tests with `./gpue -u` and make sure everything passes.
If there is a failure in the build, please create an issue on [GitHub](https://github.com/GPUE-group/GPUE).


## Experimental Docker support
Given the recent interest in containerised HPC software, we have provided some support for using GPUE within
a Docker environment. To take advantage of this requires the installation of the Nvidia CUDA runtime for Docker
(see [here](https://github.com/NVIDIA/nvidia-docker) for details). For a successfully installed (and working)
Docker environment, a local GPUE Docker image can be built using:
```bash
    cd gpue/
    docker build .
```

After the build, the container may be run as
```bash
docker run --runtime=nvidia <IMAGE TAG> /gpue/gpue -u
```
The provided GPUE path assumes it has been installed within the root (`/`) directory on the container.
Additionally, an automated build is available for the latest changes of the GPUE source code using the Dockerhub
`mlxd/gpue:latest`, wherein a build is triggered by a commit hook against the GPUE master branch.

Please note that tests with native builds are expected to offer greater performance.

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

