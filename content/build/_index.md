+++
title = "Building GPUE"
description = ""
weight = 2
alwaysopen = true
+++

{{%children style="h2" description="true"%}}

GPUE is designed with both a traditional Makefile and CMake, both of which may be used for different purposes, depending on the hardware available.

## Dependencies and Hardware

We have attempted to maintain a small dependency list for GPUE, and so only require the following dependencies:

1. CUDA (version $\geq$7.5.18)
2. CUFFT (bundled with CUDA)
3. GCC (version $\geq$4.9) or clang (version $\geq$3.9)
4. CMake (version $\geq$3.8; optional)

GPUE performs computation almost entirely on GPU hardware, and the `sm_2.0` (Fermi) to `sm_7.0` (Volta) architectures are all supported.
A list of all cards within these ranges can be found [on wikipedia](https://en.wikipedia.org/wiki/CUDA#GPUs_supported).

## CMake

CMake is the preferred building system for GPUE; however, a CMake version $>=$ 3.8 must be used.
In this case, run 

```
cmake .
```

in the primary GPUE directory and then run `make` as in the Makefile example.
`make clean` will clean the directory for rebuilding.

## Makefile
If you wish to build without CMake, a sample makefile is provided that may 
be modified to suit the CUDA paths on your test system. Here a slightly 
modified GPUE Makefile:

```
CUDA_HOME = /path/to/cuda/
GPU_ARCH        = sm_XX
OS:=    $(shell uname)
ifeq ($(OS),Darwin)
CUDA_LIB        = $(CUDA_HOME)/lib
CUDA_HEADER     = $(CUDA_HOME)/include
CC              = $(CUDA_HOME)/bin/nvcc -ccbin /usr/bin/clang --ptxas-options=-v
CFLAGS          = -g -std=c++11 -Wno-deprecated-gpu-targets
else
CUDA_LIB        = $(CUDA_HOME)/lib64
CUDA_HEADER     = $(CUDA_HOME)/include
CC              = $(CUDA_HOME)/bin/nvcc --ptxas-options=-v --compiler-options -Wall
CHOSTFLAGS      = #-fopenmp
CFLAGS          = -g -O3 -std=c++11 -Xcompiler '-std=c++11' -Xcompiler '-fopenmp'
endif

CUDA_FLAGS      = -lcufft

CLINKER         = $(CC)
RM              = /bin/rm
INCFLAGS        = -I$(CUDA_HEADER)
LDFLAGS         = -L$(CUDA_LIB)
EXECS           = gpue # BINARY NAME HERE

DEPS = ./include/constants.h ./include/ds.h ./include/edge.h ./include/evolution.h 
./include/fileIO.h ./include/init.h ./include/kernels.h ./include/lattice.h ./include/manip.h
./include/minions.h ./include/node.h ./include/operators.h ./include/parser.h ./include/split_op.h
./include/tracker.h ./include/unit_test.h ./include/vort.h ./include/vortex_3d.h ./include/dynamic.h

OBJ = fileIO.o kernels.o split_op.o tracker.o minions.o ds.o edge.o node.o lattice.o manip.o
vort.o parser.o evolution.o init.o unit_test.o operators.o vortex_3d.o dynamic.o

%.o: ./src/%.cc $(DEPS)
        $(CC) -c -o $@ $(INCFLAGS) $(CFLAGS) $(LDFLAGS) -Xcompiler "-fopenmp" -arch=$(GPU_ARCH) $<

%.o: ./src/%.cu $(DEPS)
        $(CC) -c -o $@ $(INCFLAGS) $(CFLAGS) $(LDFLAGS) $(CUDA_FLAGS) -Xcompiler "-fopenmp" -arch=$(GPU_ARCH) $< -dc

gpue: $(OBJ)
        $(CC) -o $@ $(INCFLAGS) $(CFLAGS) $(LDFLAGS) $(CUDA_FLAGS) -Xcompiler "-fopenmp" -arch=$(GPU_ARCH) $^

clean:
        @-$(RM) -f r_0 Phi_0 E* px_* py_0* xPy* xpy* ypx* x_* y_* yPx* p0* p1* p2*
EKp* EVr* gpot wfc* Tpot 0* V_* K_* Vi_* Ki_* 0i* k s_* si_* *.o *~ PI* $(EXECS) $(OTHER_EXECS)
*.dat *.eps *.ii *.i *cudafe* *fatbin* *hash* *module* *ptx test* vort* v_opt*;
```

To use the Makefile, the first 2 lines must be modified to reflect your desired `cuda/` path and the architecture of your GPU device.
After this, simply `make` to build the code.
If rebuilding is necessary, run `make clean` then `make`.

If you are developing GPUE, this file will need to be modified as new code is developed.

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

