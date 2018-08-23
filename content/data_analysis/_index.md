+++
title = "Data analysis"
description = ""
weight = 4
alwaysopen = true
+++

GPUE outputs data in a basic ASCII format so it is easy for users to read the data into an auxiliary program and analyze it as necessary; however, GPUE also provides a series of scripts for 2 and 3D analysis with the following functionality:

1. Plotting in 2 dimensions
2. Generation of 2 dimensional slices of 3 dimensional data
3. Generation of `.vbox` or `.vtk` files in 3 dimensional for plotting in blender and paraview, respectively
4. Generation of images that can later be concatenated into a video with standard tools (`ffmpeg`, `ImageMagick`, etc.)

GPUE is primarily a simulation program and thus provides only limited tools necessary to visualize the data.
If the user requires more advanced data analysis, these must be further developed by the user for their specific research purpose.
