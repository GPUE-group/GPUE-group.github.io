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
