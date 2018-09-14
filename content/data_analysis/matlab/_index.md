+++
title = "Matlab Scripts"
description = ""
weight = 3
alwaysopen = true
+++

GPUE provides a number of MATLAB scripts in the `matlab/` directory.
This page explains what each script is and its intended use. Please note that these scripts were verified to work under MATLAB r2016b, but have not been tested with newer releases. Some in-built function changes may require updating, but it is expected that most scripts work as standard. MATLAB development has ceased on this project, and any newer funcionality required should be performed in Julia, Python, or C++/CUDA directly.

As with the Python scripts, data analysis of this nature must be done on a case-by-case basis, and as such, these scripts only intend to provide basic functionality for data examination of the simulation results. If more advanced functions are required, please create these on your own or discuss them with us on [GitHub](https://github.com/GPUE-group/GPUE)

## binData.m
Bin the data returned from g6_struct into equi-separated partitions
```matlab
function [g6B,bin] = binData(g6C,binMax,bins)
% g6C:       Matrix containing the g6 values output from g6_struct
% binMax:    The maximum binning value
% bins:      The number of bins to take
%Returns
% g6B:       The binned g6 data
% bin:       The bin values of g6B
```

## findNN.m
Returns values and indices of nearest neighbours to location `pos` in positions `X,Y` within `radius`.
```matlab
function [neighbours,locations] = findNN(pos,X,Y,radius)
```

## getAngle.m
Returns angle between 2 points on a plane
```matlab
function [ang] = getAngle(P1,P2)
```


## kinSpec.m
This function is used to process a large number of wavefunctions to examine
the kinetic energy spectra. The function first transforms the position-space wavefunction
into momentum space, and decomposes the kinetic energy into compressible (phonons) and incompressible (vortices)
components (quantum pressure term assumed to be neglible for these routines).

```matlab
function [EKc,EKi] = kinSpec(wfc_str,dimSize,dx,mass,qSpec,init,incr,fin)
%   wfc_str is the string of the wavefunction name. For time-ev this is
%       usually wfc_ev; wfc_0_const or wfc_0_ramp for ground-states
%   dimSize is a vector with the dimenion size, so that the wavefunctions
%       can be appropriately reshaped when loaded
%   dx is the increment along x. dx==dy
%   qSpec enables classical (0) or quantum (1) variant of energy spectra
%   init,incr,fin are the loop values for specifiying the range of the data
```





## loadVtx.m
Loads the processed vorts from the output file produced from `vort.py`, and structures them
into Matlab structs for Vortex position and index. Forms the basis of analysing all vortex data
output from GPUE.
```matlab
function [vorts] = loadVtx(start, step, fin, lostCheck)
%   start step and fin are the initial dataset, step size, and final
%   dataset to load for the vortex postions
%   lostCheck turns on the ability to test for vortex losses and ensure
%   that these vortices do not contribute to the dataset.
%   The Data should be in the format of [X Y Sign UID IsLost] 
```


## psi6.m
Calculate the orientational order parameter defined at point pos between the points `(X,Y)`
```matlab
[nn,idx] = findNN(pos,X,Y,radius); %find number of neighbours and indices of neighbours for (X,Y)
% pos:       Defines the location to calculate the orientational order parameter
% X,Y:       Vector of entire X,Y range of points
% radius:    Radius over which to determine neighbouring points
%Returns
% psi6_pos:  The value of orientational order psi_6 at position pos
% nn:        Number of nearest neighbours
```


## uniqPairIdx_precalc.m

## voronoi2dCellColour.m

## vtxTrajectory.m

## defectTriangulation.m
%Plots delaunay triangulation of input data points, with (5,7) (3,9) and
%(4,8) dislocations indicated. Can be used to calculate the defects numbers with
%no plotting by setting plotit to 0
```matlab
function [h,DefCount] = defectTriangulation(x, y, dx, dimSize, plotit, radius)
%   radius defines the distance from 0,0, for which the defects will be
%considered. This can be used to avoid defects that arise naturally from
%the edge of the triangulation
%   h is a handle for generated plot
%   DefCount is a vector of the total number of defects counted for each
%   type with the index representing the number of connected edges
```



## g6_struct.m

## GPE_2d.m

## latexFig.m

## psi6_DT.m

## quKineticSpec.m

## velField.m

## VtxCorr.m
