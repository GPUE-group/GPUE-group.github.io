+++
title = "Python Scripts"
description = ""
weight = 2
alwaysopen = true
+++

GPUE provides a number of python scripts in the `py/` directory.
This page explains what each script is and its intended use.

As a note: GPUE is primarily a simulation program.
Data analysis of this nature must be done on a case-by-case basis, and as such, these scripts only intend to provide basic functionality for plotting the output wavefunction and additional variables.
If more advanced functions are required, please create these on your own or discuss them with us on [GitHub](https://github.com/GPUE-group/GPUE)

## plot.py

This file is a stand-alone plotting script for 2 dimensional data and can be run like so:

```
python plot.py i wfc g 256 256 r 0 1000 100 d data
```

This will plot the wavefunction from imaginary-time evolution using a grid of 256x256 from values 0 to 1000 in steps of 100.
Obviously, the code requires these data files to exist in the `data` directory before plotting.
The script will default to plotting only a single 512x512 data file from the `data` directory if no gridsize, range, or directory is provided.

All 2 dimensional variables can be plotted in this way.
If real-time dynamics are desired, simply use the `wfc_ev` flag instead of `wfc`.

## gen_data.py

This file is a list of functions necessary to analyze 3 dimensional data, and an example of how it might be used can be found in the `vis_scripts.py` file.

Here are the primary functions and their intended usage:
```
wfc_density(xDim, yDim, zDim, data_dir, pltval, i)
```

This function outputs an `item` that corresponds to the wavefunction density.
This `item` can be turned into a `.bvox` or `.vtk` file with the `to_bvox()` and `to_vtk()` functions described later.
The `pltval` argument can read either `wfc` for imaginary-time evolution or `wfc_ev` for real-time evolution.

```
wfc_phase(xDim, yDim, zDim, data_dir, pltval, i)
```

This function outputs an `item` that corresponds to the wavefunction phase.
This `item` can be turned into a `.bvox` or `.vtk` file with the `to_bvox()` and `to_vtk()` functions described later.
The `pltval` argument can read either `wfc` for imaginary-time evolution or `wfc_ev` for real-time evolution.


```
proj_phase_2d(xDim, yDim, zDim, data_dir, pltval, i)
```

This slices the wavefunction along the z-axis and outputs the phase as a `wfc_ph_i` file, where `i` corresponds to the value read into this function.
The `pltval` argument can read either `wfc` for imaginary-time evolution or `wfc_ev` for real-time evolution.
This file can be plotted with `plot.py`, for example.

```
proj_2d(xDim, yDim, zDim, data_dir, pltval, i)
```

This slices the wavefunction along the z-axis and outputs the density as a `wfc_i` file, where `i` corresponds to the value read into this function.
The `pltval` argument can read either `wfc` for imaginary-time evolution or `wfc_ev` for real-time evolution.
This file can be plotted with `plot.py`, for example.

```
var(xDim, yDim, zDim, data_dir, pltval)
```

This function outputs an `item` that corresponds to the variable to be plotted.
This `item` can be turned into a `.bvox` or `.vtk` file with the `to_bvox()` and `to_vtk()` functions described later.
The `pltval` argument can be any existing file in the data_directory that is formatted for 3 dimensional output.

```
proj_var2d(xdim, yDim, zDim, data_dir, pltval, file_string)
```

This slices the 3 dimensional variable along the z-axis and outputs the phase as a file with the name `file_string`
This file can be plotted with `plot.py`, for example.


```
proj_var1d(xdim, yDim, zDim, data_dir, pltval, file_string)
```

This slices the 3 dimensional variable along the z and y axes and outputs the phase as a file with the name `file_string`
This file can be plotted with any standard plotter, like gnuplot.


```
to_bvox(item, xDim, yDim, zDim, nframes, filename)
```

This takes an `item` output from the `wfc_phase()`, `wfc_density()`, or `var()` functions and turns it into a `.bvox` file with name `filename` to be read by blender.
The `nframes` function corresponds to the number of frames blender will plot.
For almost all cases, this should be 1.
The `.bvox` file can be used further with the `visualize_3d.py` file.

```
to_vtk(item, xDim, yDim, zDim, data_dir, filename)
```

This takes an `item` output from the `wfc_phase()`, `wfc_density()`, or `var()` functions and turns it into a `.vtk` file with name `filename` to be read by blender.
This can be directly read into 3 dimensional plotters like Paraview and is the preferred method to visualize data in 3 dimensions.


## visualize_3d.py

This file works with the blender bpy library to create a 3 dimensional image from a `.bvox` file and the `voxelfile` and `outfile` must be modified depending on the file input and output.

It is run with the following command:

```
blender -b -P visualize_3d.py
```

To open up the blender GUI with the density plotted, run:

```
blender -P visualize_3d.py
```

## en.py

This is an example script of how to find the energy of a 2 dimensional simulation from GPUE.
As a note, this functionality is already completely implemented in GPUE by outputting the energy every timestep with the `-E` flag; however, this script provides an example of how this can be done with python.

## vort.py
This file enables ordering and trajectory calculation of the vortices that are output from GPUE. All available `vort_arr_XYZ` files are loaded from the GPUE simulation output, and a linked-list data-structure is create, wherein each vortex is assigned a unique ID (`uid`). These `uid` values are then ordered and swapped between subsequent timesteps to the most-likely candidate after each time-step of evolution. The resulting data, once ordered, is then output into a new file format `vort_ord_XYZ.csv`, with each vortex `uid` constant over the time-series. This allows for the creation of vortex trajectories, as is perfomed by `matlab/vtxTrajectory.m`. Some additional details are also given on the [2D GPUE functionality page](/functionality/vortex_2d/).


A typical workflow of involving this file is as follows:

```bash
gpue <simulation parameters>
cd <data_directory>
python py/vort.py
```

## mpi_vis.py
This script enables MPI-distributed wavefunction density generation for 2D data only. $|\Psi|^2$ is plotted over all available timesteps, and is distributed using mpi4py. To ensure this works, mpi4py MUST be built against the MPI library running on the local machine (be it a laptop, or a supercomputer). Some useful instructions on this are provided by [NERSC](http://www.nersc.gov/users/data-analytics/data-analytics-2/python/anaconda-python/) under the "Building MPI4PY" subtitle.

Once set-up, this script can be run to generate images both with and without the colour-bar, and enumerating the specific vortices as analysed by `vort.py`, as follows:

Note: to ensure this script runs correctly, `vort.py` should be run first to generate ordered vortex lists as `vort_ord_XYZ.csv`.

```bash
mpirun -n 32 python py/mpi_vis.py true true #first true is for colorur-bar, second true is for vortex enumeration in plot
```

The result will be `n` PNG images, one for each wavefunction dataset. 

## stats.py [deprecated]
This file was originally developed for calculating statistical quantities over the vrortex data. However, much of the functionality has been added to other segments of the code-base, and all that remains here is a sample script for the vortex least-squares position estimation, as discussed [here](/functionality/vortex_2d/). The example can be run as:

```bash
python py/stats.py 0 1000 #where 0 and 1000 represent the start and end values for which to calculate the least-sqyuares refinement.
```

## observables.py [deprecated]
This file offers a re-write of the MATLAB code for calculation of the kinetic energy spectra, as well as the ability to calculate values for some known observables, such as the quadrupole mode, breathing mode, and angular momentum as key examples. Running this file expects the wavefunctions and Params.dat to be available in the running directory. Values are calculated and plotted over the available timesteps, and output as PDF files.