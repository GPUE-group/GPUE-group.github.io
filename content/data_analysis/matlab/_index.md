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
Returns angle between 2 points on a plane.
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
Calculate all unique pairings of the orientational correlations from the 
orientational order parameters, and create a struct to return that holds 
the distance between paired elements, and the respective orientational order values.

```matlab
function [S] = uniqPairIdx_precalc(R,psi6p)
% R:        Vectors of R=[X Y] values for points.
% psi6p:    Orientational order values defined over the range of points
%Return:
% S:        Struct of orientational corder values cor0,cor1 and distance
%            between elements
```

## voronoi2dCellColour.m
Determine the Voronoi diagram of the input data vortex position data, and colour the
cells with the value of the orientational correlations defined at each
site (col=1), or with the area of the cell (col=0).

```matlab
function [p6cp6, area, avg_area, num_edges, var]=voronoi2dCellColour(x,y,radius,edgeCol,dx,colScheme)
%vorCellColour 
%x,y: are the locations of the sites
%radius: is the area over which to perform the triangulation. This should
%     avoid too large values to ensure the traingulation does not run off to
%     infinity
%edgeCol: Defines the Voronoi diagram edge colours for edge indexed pair. edgeCol<0 is 
%     red, edgeCol==0 is black, edgeCol>0 is white
%dx: this is the increment size of the data
%colScheme: defines the colouring scheme, 1 for orientational correlations on the site 
%     with 6-fold symmetry, 0 for cell area 
% Returns:  p6cp6 = local values of correlations
%           area = cell areas
%           avg_area = average area of cells
%           num_edges = cell edges count
%           var = variance
```

Testcase: Generate a 2D grid centred on 0 and calculate voronoi diagram:
```matlab
x=linspace(-1,1,20); y=x;
 radius = 0.5; dx = 1; q = ones(length(x)*length(y),1);
 voronoi2dCellColour(kron(x',ones(length(y),1)),kron(ones(length(x),1),y'),0.75,zeros(length(x)^2,1),1,0);

```

## vtxTrajectory.m
Generates trajectory plots of 2D vortex data from the postprocessed vortex positions as loaded by `loadVtx.m`. Since the processed data assumes an initial starting position the data at 0 is named vort_arr_0, while postprocessed data is vort_ord_xxx. Plotting assumes the data to be centered at 0 with for half $dimSize$ of the system.
```matlab
function [vorts] = vtxTrajectory(start, step, fin, initial,threeD, dx, dimSize, dt)
%   Start step and fin are the initial, stepsize and final datasets
%   initial indicates if you wish to highlight the 
%   threeD also plots aspacetime (XYT) diagram in 3D.
%   dx is the stepsize along one dimension and assumes the same over x,y
%   dimSize is the length of the dimensions, and assumes x==y
%   dt is the time increment for the spacetime diagram.
```


## defectTriangulation.m
Plots delaunay triangulation of input data points, with (5,7) (3,9) and
(4,8) dislocations indicated. Can be used to calculate the defects numbers with
no plotting by setting `plotit` to 0

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
Determine the orientational correlation function between every pairing of points, and sorts the results based on distance between points. This gives $g\_6(r)$.
```matlab
function [g6C] = g6_struct(X,Y,rad)
% X,Y:   Vectors of x,y values for points.
% rad:   Radius in which to examine for neighbouring points.
%Return:
% g6C:   Matrix of g6 values and parameters
```

## GPE_2d.m
This script is an example code for simulating a rotating BEC using the split-operator based Gross--Pitaevskii equation. This simulation code is merely a test-case for simple functionality tests of the Gross--Pitaevskii equation.


## latexFig.m
LaTeXify the plot axes for an existing figure. This sets the plotting axis on supplied figure to be LaTeX formatted.

```matlab
function latexFig(gca, fontSize, cbar, xtick, ytick, xticklabels, yticklabels)
%   {x,y}tick can be used to overwrite the automatically defined tick
%   locations
%   {x,y}ticklabels overwrites the labels at the positions defined by
%   {x,y}tick values
%   This assumes that the Latin Modern fonts are installed
%   Fontsize sets the size of the text
%   cbar enables the colorbar if needed
```
Example usage:
```matlab
pcolor(rand(10,10));
latexFig(gca, 30, 1, 2:2:10, 1:2:10, {'a1','b1','c1','d1','e1'}, {'a2','b2','c2','d2','e2'})
```

## psi6_DT.m
`psi6.m` variant for use in Delaunay triangulation/Voronoi cell calculation routines. Use of `psi6.m` is preferred.


## quKineticSpec.m
Evalutes the quantum kinetic energy spectrum (see [O'Riordan, 2017](https://oist.repo.nii.ac.jp/?action=pages_view_main&active_action=repository_view_main_item_detail&item_id=182&item_no=1&page_id=15&block_id=79) for specific details). Used by `kinSpec.m` for evaluation of both quantum and classical kinetic energy spectra in compressible and incompressible cases.

```matlab
function [varargout] = quKineticSpec(Psir,m,dx,q,avg,iii)

% Ek = quKineticSpec(Psir,m,dx,q,avg,iii), 
% Psir N-dim wavefunction in r space with grid spacing dx and mass m
% q = include phase for quantum spectrum, 1=yes,0=no; 
% avg = perform angled average. Likely always want this to be yes, 1
% iii output index for naming the figure files. Useful when looped with
% kinSpec script
```

## velField.m
Calculates the velocity field of wavefunction, and plots the magnitude and direction of the velocity field for the given wavefunction in 2D.

```matlab
function [] = velField(wfc0,dx,m,incr,x,y,normed, lims)
% dx is the increment along x, and assumes dx==dy
% m is the mass of the atomic species
% incr is the increment over which to calculate field direction. Too low
%     and it may be very dense, too high and very sparse. Start at 1, and
%     increase until happy
% x,y are the grid spacings along the x and y axis
% normed specifies whether to normalise the vector directions. 1 if yes, 0 otherwise
% lims is [xMin xMax yMin yMax]. Hides the edge garbage. Otherwise, []
```

## VtxCorr.m
Plot the Voronoi diagram over the range of values spanned by dataSteps, and print the results to file. Essentially wraps
voronoi2dCellColour and saves the resulting plots, with mean, variance and standard deviation values.

```matlab
function [] = VtxCorr(dataSteps, dx, dimSize, radius, printIt, edgeCol)
%
%dataSteps: The range of values to be plotted (e.g. [1e3 1e4 5e6])
%dx:        this is the spatial increment size of the data
%dimSize:   The number of elements along one dimension. Used to calculate
%               max and min spatial values. Assumes x==y
%radius:    defines the bounded region to calculate the Voronoi diagram and all 
%               quantities. Useful to avoid cells tending to infinity.
%               Assumes a circularly symmetric dataset.
%printIt:   1 if plots are to be saved, 0 otherwise.
%edgeCol:   Defines the Voronoi diagram edge colours for edge indexed pair. 
%               edgeCol==0 is black, edgeCol>0 is white
% Returns
%%Testcase: Generate diagrams for data at steps 1e3 1e4 and 1e5
% dx=1e-4; dimSize=1024; radius = 200*dx; printIt = 0; edgeCol = 1;
% VtxCorr([1e3 1e4 1e5], dx, dimSize, radius, printIt, edgeCol)
```