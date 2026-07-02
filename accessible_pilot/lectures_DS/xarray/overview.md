# Overview

In the previous module, we saw how Pandas keeps track of the "metadata" surrounding tabular datasets — an *index* for each row and labels for each column. These features, together with Pandas' many routines for data munging and analysis, have made it one of the most popular Python packages in the world.

However, not all Earth and environmental datasets fit the "tabular" (rows-and-columns) model. We often deal with **multidimensional data** (also called *N-dimensional*): data with many independent dimensions or axes. For example, we might represent Earth's surface temperature $T$ as a three-dimensional variable

$$ T(x, y, t) $$

where $x$ is longitude, $y$ is latitude, and $t$ is time.

**Xarray** brings pandas-level convenience to this kind of labelled, multidimensional data — and it is the workhorse for gridded climate data, from model output and reanalysis to satellite fields.

*The original page shows a diagram of the xarray data model. In words:* the diagram depicts one **Dataset** and everything inside it. On the left are two 3-D cubes of gridded numbers — one labelled `temperature`, one labelled `precipitation`. These are the **data variables**. Next to them are two flat 2-D grids labelled `latitude` and `longitude` — the **coordinates**, which give a physical meaning (a location) to each grid cell of the cubes. On the right are three axis arrows labelled `x`, `y`, and `t` — the **dimensions** (and their indexes) that all these arrays share — plus a single lone point labelled `reference_time`, a scalar coordinate with no dimension at all. Braces underneath group the pieces: the cubes and the coordinate grids are all "Variables"; `latitude` and `longitude` are bracketed as "Coordinates"; the axes are bracketed as "Dimensions (& Indexes)". A wider bracket labelled **DataArray** spans *one* data variable together with its coordinates and dimensions — that is xarray's basic object. The widest bracket, spanning everything, is labelled **Dataset**: a collection of DataArrays that share dimensions and coordinates.

:::{admonition} A note on maps and this accessible version
:class: important
This is the section where the data goes **multi-dimensional** — and where the course's figures turn from line plots into **2-D maps** (`pcolormesh`, `contourf`, and friends). Producing those maps is a normal coding skill, and you'll learn it. But "reading" a map visually isn't how you'll work, and [MAIDR](../sci_python/trying_maidr.md) — the sound-and-text chart explorer from the sci-python section — does **not** handle 2-D maps (it works for the line plots and histograms you'll also meet here). So in this accessible version, wherever the course would have you look at a map, we instead **(a)** describe the rendered figure in words and verified numbers (the "What this shows" notes), and **(b)** replace map-reading with two habits you'll practice throughout: the **1-D profile trick** — slice out a single latitude row, longitude column, or time series from the field and plot or print it as a line — and **targeted prints** — pull the value at a named location with `.sel(...)`, or a mean, minimum, or maximum, straight to the terminal. Conveniently, xarray's labelled selection is exactly the tool that makes both habits easy.
:::

## Learning objectives

Because xarray is central to gridded-data analysis in the Earth and environmental sciences, we give it a full section. By the end you should be able to:

### Lesson 1: Xarray Fundamentals

#### Dataset Creation

1. Describe the core xarray data structures, the `DataArray` and the `Dataset`, and the components that make them up, including: Data Variables, Dimensions, Coordinates, Indexes, and Attributes
1. Create xarray `DataArrays` and `Datasets` out of raw numpy arrays
1. Create xarray objects with and without indexes
1. Load xarray datasets from netCDF files and OPeNDAP servers
1. View and set attributes

#### Basic Indexing and Interpolation

1. Select data by position using `.isel` with values or slices
1. Select data by label using `.sel` with values or slices
1. Select timeseries data by date/time with values or slices
1. Use nearest-neighbor lookups with `.sel`
1. Mask data with `.where`
1. Interpolate data in one and several dimensions

#### Basic Computation

1. Do basic arithmetic with DataArrays and Datasets
1. Use numpy universal functions on DataArrays and Datasets, or use corresponding built-in xarray methods
1. Combine multiple xarray objects in arithmetic operations and understand how they are broadcast / aligned
1. Perform aggregation (reduction) along one or multiple dimensions of a DataArray or Dataset

#### Basic Plotting

1. Use built-in xarray plotting for 1D and 2D DataArrays
1. Customize plots with options

### Lesson 2: Advanced Usage

#### Xarray's groupby, resample, and rolling

1. Split xarray objects into groups using `groupby`
1. Apply reduction operations to groups (e.g. mean)
1. Apply non-reducing functions to groups (e.g. standardize)
1. Use `groupby` with time coordinates (e.g. to create climatologies)
1. Use arithmetic between `GroupBy` objects and regular DataArrays / Datasets
1. Use `groupby_bins` to aggregate data in bins
1. Use `resample` on time dimensions
1. Use `rolling` to apply rolling aggregations

#### Merging and Combining Datasets

1. Concatenate DataArrays and Datasets along a new or existing dimension
1. Merge multiple datasets with different variables
1. Add a new data variable to an existing Dataset

#### Reshaping Data

1. Transpose dimension order
1. Swap coordinates
1. Expand and squeeze dimensions
1. Convert between DataArray and Dataset
1. Use `stack` and `unstack` to transform data

#### Advanced Computations

1. Use `differentiate` to take derivatives of data
1. Use `apply_ufunc` to apply custom or specialized operations to data

## Pages in this section

- [Xarray Fundamentals](./xarray_fundamentals.md) — DataArrays and Datasets, label-based selection, computation, broadcasting, reductions, and loading netCDF/Zarr data — with every figure described in words and "Check it" prints along the way.
- [Xarray: Advanced](./xarray_advanced.md) — interpolation, `groupby`, `resample`, `rolling`, and `coarsen`; building climatologies and more.
- [Assignment 6: Xarray with SST data](./assignment6.md) — apply xarray to NOAA sea-surface-temperature data (the at-home assignment), with "check yourself" numbers for every task.

:::{admonition} In-class assignment — 10 points
:class: note
Your in-class assignment for this section is to complete the **"Try it"** exercises in the lecture pages above. Complete them as `.py` scripts, push them to your week folder, and post the link on the matching **Courseworks** assignment. (The at-home assignment — Assignment 6, the SST lab — is graded separately, 10 points.)
:::

## Working through the lectures

Same workflow as previous modules — work through the pages as **Python scripts** in VS Code: type the examples into a `.py` file, run it in your Git Bash terminal with `python yourfile.py`, and read the output with `Ctrl+Up`. Stop at each **Try it** admonition to experiment in your own script before moving on. If you haven't set up VS Code yet, start with [Setting up an accessible workflow](../computing_env/accessible_setup.md) and [Working with Python scripts](../computing_env/working_with_scripts.md).

Two practical notes for this section. First, the lectures pull real climate data over the network (netCDF via OPeNDAP servers and cloud Zarr stores), so **be online** when you run the scripts. Second, they need a few extra packages beyond numpy, matplotlib, and pandas — install them once with:

The following is a terminal command:

```bash
pip install xarray netcdf4 pooch zarr fsspec aiohttp dask
```

End of command.
