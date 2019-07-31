+++
title = "GPUE Output Format"
description = ""
weight = 1
alwaysopen = true
+++

GPUE outputs data via HDF5 in a single `output.h5` file containing simulation data and parameters.

To output the wavefunction every printStep (`-p N`), set the `-W` flag.

For example,

```
./gpue -e 1000 -W -p 100
```

Will run GPUE in real-time for 1000 steps and output the wavefunction every 100 steps.

The `-d` flag sets the data directory relative to the executor, and will create the given directory if it does not exist.
This defaults to `data/`

## Reading the output in various languages

The HDF5 output format can be read by many languages using their respective HDF5 bindings.
Here we will look at how to read the data in Python and in Julia.

The following examples open the output file, and read the wavefunction and some of the simulation's attributes

Python:
```python
# Import the hdf5 python binding
import h5py

# Open the file in read-only mode
f = h5py.File("data/output.h5", "r")

# Load the wavefunction
wfc_dataset = f["/WFC/CONST/0"]

# Compose the dataset into a complex-valued numpy array
wfc = wfc_dataset["re"] + wfc_dataset["im"] * 1j

# Load the dimension parameters
# Since default values are not stored on the output, pass a default into get
wfc_num = f.attrs.get("wfc_num", default=1)
xDim = f.attrs.get("xDim", 256)
yDim = f.attrs.get("yDim", 256)

# Compare the shape
print((wfc_num, xDim, yDim) == (wfc.shape)) # True if wfc is 2D
```

Julia:
```julia
# Import the hdf5 julia binding
using HDF5

# Open the file in read-only mode
f = h5open("data/output.h5", "r")

# Load the wavefunction dataset
wfc_dataset = f["/WFC/CONST/0"]

# Load the wavefunction as a flat array, since HDF5.jl doesn't support indexing on compositely typed datasets
wfc_arr = read(f, "/WFC/CONST/0")

# Convert the wavefunction array to complex numbers
wfc_arr = map(x -> x.data[1] + x.data[2] * 1im, wfc_arr)

# Reshape the wavefunction array to the correct dimensions
wfc_arr = reshape(wfc_arr, size(w))


# Load the attributes on the file
attr_obj = attrs(f)

# Load the dimension parameters
# Since default values are not stored on the output, set defaults and check for attribute existence
wfc_num = 1
xDim = 256
yDim = 256

# Load wfc_num
if (exists(attr_obj, "wfc_num"))
  wfc_num = read(attr_obj, "wfc_num")[0]
end

# Load xDim
if (exists(attr_obj, "xDim"))
  xDim = read(attr_obj, "xDim")[0]
end

# Load yDim
if (exists(attr_obj, "yDim"))
  yDim = read(attr_obj, "yDim")[0]
end

# Compare the shape
@assert size(wfc_arr) == (xDim, yDim, wfc_num)
```

For more on hdf5 storage, see [the official documentation.](https://portal.hdfgroup.org/display/HDF5/Introduction+to+HDF5)

The documentation for the HDF5 bindings are found here: [Python](http://docs.h5py.org/en/stable/), [Julia](https://github.com/JuliaIO/HDF5.jl)

