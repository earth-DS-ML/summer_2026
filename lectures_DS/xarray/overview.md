# Overview

In the previous module, we saw how Pandas keeps track of the "metadata" surrounding tabular datasets — an *index* for each row and labels for each column. These features, together with Pandas' many routines for data munging and analysis, have made it one of the most popular Python packages in the world.

However, not all Earth and environmental datasets fit the "tabular" (rows-and-columns) model. We often deal with **multidimensional data** (also called *N-dimensional*): data with many independent dimensions or axes. For example, we might represent Earth's surface temperature $T$ as a three-dimensional variable

$$ T(x, y, t) $$

where $x$ is longitude, $y$ is latitude, and $t$ is time.

**Xarray** brings pandas-level convenience to this kind of labelled, multidimensional data — and it is the workhorse for gridded climate data, from model output and reanalysis to satellite fields.

![xarray data model](https://docs.xarray.dev/en/stable/_images/dataset-diagram.png)

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

- [Xarray Fundamentals](./xarray_fundamentals.ipynb) — DataArrays and Datasets, label-based selection, computation, broadcasting, reductions, and loading netCDF/Zarr data.
- [Xarray: Advanced](./xarray_advanced.ipynb) — interpolation, `groupby`, `resample`, `rolling`, and `coarsen`; building climatologies and more.
- [Lab: Xarray with SST data](../../assignments/xarray_lab.ipynb) — apply xarray to NOAA sea-surface-temperature data (the at-home assignment).

:::{admonition} In-class assignment — 10 points
:class: note
Your in-class assignment for this section is to complete the **"Try it"** exercises in the lecture notebooks above. Work through them in your own copy of each notebook. To submit, push your completed notebook(s) to your week folder and post a link to each on the matching **Courseworks** assignment. (The at-home assignment — the SST lab — is graded separately, 10 points.)
:::

## Working through the lectures

Same workflow as previous modules — download each notebook (⬇ icon, top-right) or copy-paste the cells into a fresh notebook in JupyterLab on LEAP or Colab, then step through them. Stop at each **Try it** admonition to experiment in your own cells before moving on.
