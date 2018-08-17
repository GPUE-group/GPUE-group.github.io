+++
title = "Dynamic Fields"
description = ""
weight = 5
alwaysopen = true
+++

GPUE allows for users to input their own custom, dynamic fields that can vary with time, space, and auxiliary parameters.
These fields are parsed during run-time and allow users to run GPUE with multiple different distributions without recompiling.
This was done for the following reasons:

1. GPUE hardware is inherently limited in memory.
After some development, we found that there simply was not enough room on older GPU hardware for the wavefunction, gauge fields, and auxiliary operators for evolution.
2. If we wanted to change these fields during evolution, that is very difficult to do on the GPU because transferring between the CPU host and GPU device is a slow process -- even with recent progress in NVLink technology!
3. We cannot assume that all users are also developers. Adding new operators to GPUE was once a very difficult process that would take a few hours on the part of the developer.
This obviously meant that users could only use GPUE for a few small-scale simulations and could not use it for general use.
By allowing users to input their own fields during run-time, GPUE can be used for a much larger number of cases.

{{%children style="h2" description="true"%}}

## Using dynamic fields
All dynamic fields must be specified in some configuration file to be read by the `./gpue -I file.cfg` flag.

An example is provided in the `src/example.cfg` file:

```
rad = 10
omegaR = 7071
V = rad*omegaR*x*y*z
```

Here, we specify whatever variables we like.
This also allows us to redefine parameters for the simulation.
The terms `x`, `y`, `z`, `t`, `Ax`, `Ay`, `Az`, `V`, and `K` are all reserved and recognized by the parsing scheme.
All standard mathematical operations are also supported.

As a note: using the dynamic parser in this way saves on memory usage; however, it also slows down the simulation slightly.
Dynamic fields should only be used if your simulation requires time-dependent potentials or gauge fields or if you need to save on memory.
