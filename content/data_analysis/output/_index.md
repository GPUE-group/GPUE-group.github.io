+++
title = "GPUE Output Format"
description = ""
weight = 1
alwaysopen = true
+++

GPUE outputs data in a simple ASCII format where all arrays have been re-indexed and output element-by-element in a 1 dimensional array.
For 1 dimensional simulations, these values can be immediately plotted with separate plotters like gnuplot.
For 2 and 3 dimensional simulations, a certain amount of data analysis is required; however, this should be straightforward depending on the application.

GPUE will automatically output the following files unless the `-f` flag is used:

1. V_0: The potential of the first timestep
2. K_0: The momentum-space components used in the split-operator method
3. Ax_0, Ay_0, Az_0: The gauge field in the x, y, and z directions
3. Bx_0, By_0, Bz_0: The magnetic field in the x, y, and z directions

Note that if the `-J` flag is used, the magnetic fields will be output in polar coordinates.

If the `-W` flag is used, the wavefunction will be output every printstep, which is set by the `-p` flag.
For example:

```
./gpue -e 1000 -W -p 100
```

will run GPUE in real-time for 1000 steps and output the wavefunction every timestep.
In 2 dimensions, this will also output vortex tracking values and in 3 dimensions, this will output an additional `Edges_n` file, which is the sobel filter of the wavefunction density. 
The `Edges_n` file can be used for vortex highlighting in 3 dimensions.

The `-d` flag sets the data directory relative to the gpue executable.
If the data directory does not exist, that directory will be created.

## Reshaping arrays for usage in various scripting languages

If GPUE is run in 2 or 3 dimensions and it is necessary to analyze the data, it may be worthwhile to `reshape` the data.
For example, in python, this might look like:

```
    data = data_dir + "/" + val
    lines = np.loadtxt(data)
    A = np.reshape(lines, (xDim,yDim,zDim));
    return A
```

This will create a 3 dimensional array that can be read as `A[i,j,k]`.
A simlar procedure must be done in 2 dimensions, simply dropping the `k` index and `zDim` from the array indexing and reshaping function, respectively.

## Params.dat file

GPUE holds many variables in an unordered map for usage within the code, itself.
This allows us to keep a name associated with each variable for dynamic equation parsing and also allows us to output each variable into a single file for data analysis later.
All of these variables can be found in the `Params.dat` file in the data directory.
If the user needs a single variable for data analysis (such as `xDim` or `dx`), they can find it there.
An example can be found here:

```
[Params]
dpz=125664
sepMinEpsilon=0
y0_shift=0
a0z=7.62574e-06
x0_shift=0
dy=1.95313e-07
fudge=1
winding=0
mask_2d=0.00015
yMax=2.5e-05
laser_power=0
gdt=0.0001
omegaX=6.283
xMax=2.5e-05
gammaY=1
omega=1
z0_shift=0
omegaZ=6.283
dpy=125664
DX=0
box_size=2.5e-05
interaction=1
thresh_const=1
omegaY=6.283
mass=1.44316e-25
a_s=4.76e-09
angle_sweep=0
a0x=7.62574e-06
a0y=7.62574e-06
dpx=125664
gDenConst=4.6095e-51
dt=0.0001
Rxy=0.36659
zMax=2.5e-05
pyMax=1.6085e+07
pxMax=1.6085e+07
pzMax=1.6085e+07
dz=1.95313e-07
dx=1.95313e-07
plan_dim3=5
plan_3d=6
plan_other2d=2
yDim=256
kill_idx=-1
gsteps=1
esteps=1
zDim=256
plan_dim2=4
plan_2d=1
atoms=1
xDim=256
dimnum=3
printSteps=100
kick_it=0
ramp_type=1
gSize=16777216
device=2
charge=0
result=0
plan_1d=3

```
