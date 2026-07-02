# Xarray Fundamentals

:::{admonition} Following along with a screen reader
:class: important
Work through the examples as `.py` scripts in VS Code — see [Setting up an accessible workflow](../computing_env/accessible_setup.md) and [Working with Python scripts](../computing_env/working_with_scripts.md). Run a script in the Git Bash terminal with `python yourfile.py`, and press `Ctrl+Up` to read the output. This page needs a few packages — if you haven't already: `pip install xarray pooch zarr fsspec aiohttp dask` — and you must be **online** when you run the scripts (the data streams over the network).
:::

:::{admonition} In-class assignment — 10 points
:class: note
The **"Try it"** exercises on this page are part of your in-class assignment for this section. Complete them as `.py` scripts, push them to your week folder, and post the link on the matching **Courseworks** assignment. (One 10-point assignment covers all the lecture pages in this section.)
:::

Two script habits before we start. First, a bare expression on the last line of a notebook cell displays itself; in a script it displays **nothing** — so throughout this page we wrap results in `print(...)`. Second, plots don't appear until you call `plt.show()`; where a plot is worth keeping, you can `plt.savefig("myplot.png")` instead.

:::{admonition} Listening to xarray objects
:class: tip
This lecture's biggest accessibility win comes for free: unlike a pandas DataFrame's column grid, an xarray object prints as **structured linear text** — a header, then labelled sections (Dimensions, Coordinates, Data variables, Attributes) with one item per line. NVDA reads it well. Two things to know:

1. The listing can be *long* — for a big array it includes a preview of the data values. Below, we print one `Dataset` in full and walk through how to listen to it, section by section. Everywhere else we prefer **targeted prints**: `ds.sizes` for the dimensions, `list(ds.data_vars)` for the variable names, `.values` on a small slice, or `float(...)` for a single number. These land in the terminal as one short line each — much faster by ear, and they are exactly what you'd use in automated tests.
2. The 1-D line plots on this page (time series and vertical profiles) can be explored point-by-point with **[MAIDR](../sci_python/trying_maidr.md)**. The 2-D colormap plots (`pcolormesh`-style "sections" and maps) can't — but this lecture teaches the perfect substitute: use `.sel()` / `.isel()` to slice a 1-D profile out of any 2-D field, then plot or print *that*. Every "What this shows" note below describes the rendered figure in words and verified numbers.
:::

## Xarray data structures

Like Pandas, xarray has two fundamental data structures:
* a `DataArray`, which holds a single multi-dimensional variable and its coordinates
* a `Dataset`, which holds multiple variables that potentially share the same coordinates

### DataArray

A `DataArray` has four essential attributes:
* `values`: a `numpy.ndarray` holding the array's values
* `dims`: dimension names for each axis (e.g., `('x', 'y', 'z')`)
* `coords`: a dict-like container of arrays (coordinates) that label each point (e.g., 1-dimensional arrays of numbers, datetime objects or strings)
* `attrs`: an `OrderedDict` to hold arbitrary metadata (attributes)

Let's start by constructing some DataArrays manually. Start a file `xarray_fundamentals.py` with the imports we'll use throughout:

The following is Python code:

```python
import numpy as np
import xarray as xr
from matplotlib import pyplot as plt
plt.rcParams['figure.figsize'] = (8, 5)
```

End of code.

A simple DataArray without dimensions or coordinates isn't much use.

The following is Python code:

```python
da = xr.DataArray([9, 0, 2, 1, 0])
print(da)
```

End of code.

The following is the expected output:

```
<xarray.DataArray (dim_0: 5)> Size: 40B
array([9, 0, 2, 1, 0])
Dimensions without coordinates: dim_0
```

End of output.

Three lines: a header naming the object type, its dimensions (an auto-generated `dim_0`, length 5) and memory size; the data values themselves; and a note that `dim_0` has no coordinates.

We can add a dimension name...

The following is Python code:

```python
da = xr.DataArray([9, 0, 2, 1, 0], dims=['x'])
print(da)
```

End of code.

The following is the expected output:

```
<xarray.DataArray (x: 5)> Size: 40B
array([9, 0, 2, 1, 0])
Dimensions without coordinates: x
```

End of output.

But things get most interesting when we add a coordinate:

The following is Python code:

```python
da = xr.DataArray([9, 0, 2, 1, 0],
                  dims=['x'],
                  coords={'x': [10, 20, 30, 40, 50]})
print(da)
```

End of code.

The following is the expected output:

```
<xarray.DataArray (x: 5)> Size: 40B
array([9, 0, 2, 1, 0])
Coordinates:
  * x        (x) int64 40B 10 20 30 40 50
```

End of output.

The last section changed: instead of "Dimensions without coordinates" there is now a `Coordinates:` section listing `x` with its five values. This coordinate has been used to create an _index_, which works very similar to a Pandas index.
In fact, under the hood, Xarray just reuses Pandas indexes.

The following is Python code:

```python
print(da.indexes)
```

End of code.

The following is the expected output:

```
Indexes:
    x        Index([10, 20, 30, 40, 50], dtype='int64', name='x')
```

End of output.

Xarray has built-in plotting, like pandas.

The following is Python code:

```python
da.plot(marker='o')
plt.show()
```

End of code.

**What this shows.** A line plot of the five values against their coordinate: the x-axis is labelled "x" (xarray pulled the label from the coordinate name) and runs 10 to 50; the y-axis runs 0 to 9. The line starts at 9 at x=10, plunges to 0 at x=20, bounces up to 2 at x=30, then eases down through 1 at x=40 to 0 at x=50. A round dot marks each of the five data points.

### Multidimensional DataArray

If we are just dealing with 1D data, Pandas and Xarray have very similar capabilities. Xarray's real potential comes with multidimensional data.

Let's go back to the multidimensional ARGO data we loaded in the numpy lesson.

The following is Python code:

```python
import pooch
url = "https://github.com/earth-DS-ML/summer_2026/raw/refs/heads/main/assignments/more_matplotlib_data/argo_data_example.zip"
files = pooch.retrieve(url, processor=pooch.Unzip(), known_hash="650948c4b84690d370ad6f8aa9279c165f5302d3d9a7d330d3c0232b9d244e13")
files.sort()
for f in files:
    print(f)
```

End of code.

This prints seven file paths, one per line. The long prefix is pooch's cache folder — it depends on your operating system and username (on Windows it lives under `AppData\Local`), so yours will differ from the run below. What matters is the seven file *names* at the end of each path:

The following is the expected output:

```
/Users/dhruvbalwada/Library/Caches/pooch/781f427331dadc92454872cc18f1df31-argo_data_example.zip.unzip/argo_data_example/date.npy
/Users/dhruvbalwada/Library/Caches/pooch/781f427331dadc92454872cc18f1df31-argo_data_example.zip.unzip/argo_data_example/latitude.npy
/Users/dhruvbalwada/Library/Caches/pooch/781f427331dadc92454872cc18f1df31-argo_data_example.zip.unzip/argo_data_example/levels.npy
/Users/dhruvbalwada/Library/Caches/pooch/781f427331dadc92454872cc18f1df31-argo_data_example.zip.unzip/argo_data_example/longitude.npy
/Users/dhruvbalwada/Library/Caches/pooch/781f427331dadc92454872cc18f1df31-argo_data_example.zip.unzip/argo_data_example/pressure.npy
/Users/dhruvbalwada/Library/Caches/pooch/781f427331dadc92454872cc18f1df31-argo_data_example.zip.unzip/argo_data_example/salinity.npy
/Users/dhruvbalwada/Library/Caches/pooch/781f427331dadc92454872cc18f1df31-argo_data_example.zip.unzip/argo_data_example/temperature.npy
```

End of output.

We will manually load each of these variables into a numpy array.
If this seems repetitive and inefficient, that's the point!
NumPy itself is not meant for managing groups of inter-related arrays.
That's what Xarray is for!

The following is Python code:

```python
P = np.load(files[4]).T
S = np.load(files[5]).T
T = np.load(files[6]).T
date = np.load(files[0])
lat = np.load(files[1])
levels = np.load(files[2])
lon = np.load(files[3])
```

End of code.

**Check it.** Before building anything, confirm what we're holding — three 2-D arrays (109 depth levels by 15 profiles) and four 1-D coordinate arrays:

The following is Python code:

```python
print(S.shape, T.shape, P.shape)
print(levels.shape, date.shape, lat.shape, lon.shape)
```

End of code.

The following is the expected output:

```
(109, 15) (109, 15) (109, 15)
(109,) (15,) (15,) (15,)
```

End of output.

Let's organize the data and coordinates of the salinity variable into a DataArray.

The following is Python code:

```python
da_salinity = xr.DataArray(S, dims=['level', 'date'],
                           coords={'level': levels,
                                   'date': date},)
print(da_salinity)
```

End of code.

The following is the expected output:

```
<xarray.DataArray (level: 109, date: 15)> Size: 7kB
array([[35.179, 35.334, 35.594, ..., 36.126, 36.082, 36.157],
       [35.18 , 35.333, 35.594, ..., 36.128, 36.081, 36.155],
       [35.18 , 35.333, 35.594, ..., 36.13 , 36.081, 36.155],
       ...,
       [34.986, 34.985, 34.983, ..., 34.991, 34.99 , 34.991],
       [34.985, 34.983, 34.982, ..., 34.99 , 34.989, 34.989],
       [   nan, 34.98 ,    nan, ..., 34.989,    nan,    nan]],
      shape=(109, 15), dtype=float32)
Coordinates:
  * level    (level) int64 872B 0 1 2 3 4 5 6 7 ... 102 103 104 105 106 107 108
  * date     (date) datetime64[ns] 120B 2017-07-13T06:53:00 ... 2017-09-21T06...
```

End of output.

Notice how numpy abbreviates the data preview — a `...` stands in for the middle rows and columns — and how each coordinate line gives the name, its dimension in parentheses, the dtype, the memory size, and the first and last few values with `...` eliding the middle.

The following is Python code:

```python
da_salinity.plot(yincrease=False)
plt.show()
```

End of code.

**What this shows.** A 2-D colormap plot (a `pcolormesh`) of the salinity section: date across the x-axis (mid-July to late September 2017), level on the y-axis — and because of `yincrease=False`, level 0 is at the **top** and 108 at the bottom, like depth in the ocean. An unlabelled colorbar on the right spans the data range, roughly 34.2 to 37.4, in the default colormap (dark purple = fresh, yellow = salty). The story: the top eight levels are moderately fresh (mostly 34.2–35.6, with a dark-purple fresh patch in early August, and a saltier teal patch — near 36.2 — from mid-September); a bright-yellow salty core sits at levels roughly 12–25 (above 37); salinity then fades downward to a dark-purple minimum around levels 55–70; and the deep ocean below is a uniform blue near 35.0. Little white notches along the bottom edge are missing values (NaN) at the deepest level.

**Check it.** Confirm those numbers from the data instead of the picture:

The following is Python code:

```python
print(round(float(da_salinity.min()), 2), round(float(da_salinity.max()), 2))
```

End of code.

The following is the expected output:

```
34.24 37.37
```

End of output.

Attributes can be used to store metadata. What metadata should you store? The [CF Conventions](http://cfconventions.org/Data/cf-conventions/cf-conventions-1.7/cf-conventions.html#_description_of_the_data) are a great resource for thinking about climate metadata. Below we define two of the required CF-conventions attributes.

The following is Python code:

```python
da_salinity.attrs['units'] = 'PSU'
da_salinity.attrs['standard_name'] = 'sea_water_salinity'
print(da_salinity.attrs)
```

End of code.

The following is the expected output:

```
{'units': 'PSU', 'standard_name': 'sea_water_salinity'}
```

End of output.

(If you print the full `da_salinity` again, its listing is the same as before plus a new `Attributes:` section at the very end, with one line per attribute.)

Now if we plot the data again, the name and units are automatically attached to the figure.

The following is Python code:

```python
da_salinity.plot()
plt.show()
```

End of code.

**What this shows.** The same salinity section with two differences. First, the colorbar is now labelled "sea_water_salinity [PSU]" — built automatically from the attributes we just set. Second, we didn't pass `yincrease=False` this time, so the y-axis is back to matplotlib's default: level 0 at the *bottom*, 108 at the top — the picture is vertically flipped, with the salty yellow band now near the bottom of the frame.

### Datasets

A Dataset holds many DataArrays which potentially can share coordinates. In analogy to pandas:

    pandas.Series : pandas.Dataframe :: xarray.DataArray : xarray.Dataset

Constructing Datasets manually is a bit more involved in terms of syntax. The Dataset constructor takes three arguments:

* `data_vars` should be a dictionary with each key as the name of the variable and each value as one of:
  * A `DataArray` or Variable
  * A tuple of the form `(dims, data[, attrs])`, which is converted into arguments for Variable
  * A pandas object, which is converted into a `DataArray`
  * A 1D array or list, which is interpreted as values for a one dimensional coordinate variable along the same dimension as it's name
* `coords` should be a dictionary of the same form as data_vars.
* `attrs` should be a dictionary.

Let's put together a Dataset with temperature, salinity and pressure all together

The following is Python code:

```python
argo = xr.Dataset(
    data_vars={
        'salinity':    (('level', 'date'), S),
        'temperature': (('level', 'date'), T),
        'pressure':    (('level', 'date'), P)
    },
    coords={
        'level': levels,
        'date': date
    }
)
print(argo)
```

End of code.

The following is the expected output:

```
<xarray.Dataset> Size: 21kB
Dimensions:      (level: 109, date: 15)
Coordinates:
  * level        (level) int64 872B 0 1 2 3 4 5 6 ... 103 104 105 106 107 108
  * date         (date) datetime64[ns] 120B 2017-07-13T06:53:00 ... 2017-09-2...
Data variables:
    salinity     (level, date) float32 7kB 35.18 35.33 35.59 ... 34.99 nan nan
    temperature  (level, date) float32 7kB 27.97 28.2 28.31 ... 3.688 nan nan
    pressure     (level, date) float32 7kB 3.0 3.0 3.0 3.0 ... 2.005e+03 nan nan
```

End of output.

**How to listen to a Dataset listing.** This is *the* structure you'll print constantly in this course, so it's worth learning its shape by ear. Reading top to bottom:

1. **Header** — `<xarray.Dataset> Size: 21kB`: the object type and its total memory footprint.
2. **Dimensions line** — every dimension name with its length: `level: 109, date: 15`.
3. **Coordinates section** — one line per coordinate. Each line reads: the name (`level`), the dimension it lies along in parentheses (`(level)`), the dtype (`int64`), its memory size, then a preview of the first and last values with `...` eliding the middle. The `*` at the start of a line means that coordinate is an **index** you can select along (more on this soon).
4. **Data variables section** — one line per variable, same format: name, dims in parentheses, dtype, size, value preview. Here you can hear that all three variables share the `(level, date)` dimensions, and that the previews end in `nan` — missing values at depth.
5. **Attributes section** — absent here, but when present it comes last, one `name: value` line per attribute.

For a quick check you rarely need the whole listing — `print(argo.sizes)` gives you line 2, and `print(list(argo.data_vars))` gives the variable names.

What about lon and lat? We forgot them in the creation process, but we can add them after the fact.

The following is Python code:

```python
argo.coords['lon'] = lon
print(argo.sizes)
```

End of code.

The following is the expected output:

```
Frozen({'level': 109, 'date': 15, 'lon': 15})
```

End of output.

That was not quite right — `lon` came in as a brand-new *dimension* of its own (hear it in the sizes: `lon: 15` alongside `level` and `date`). We want lon to have dimension `date`, since there is one longitude per profile:

The following is Python code:

```python
del argo['lon']
argo.coords['lon'] = ('date', lon)
argo.coords['lat'] = ('date', lat)
print(argo.coords)
```

End of code.

The following is the expected output:

```
Coordinates:
  * level    (level) int64 872B 0 1 2 3 4 5 6 7 ... 102 103 104 105 106 107 108
  * date     (date) datetime64[ns] 120B 2017-07-13T06:53:00 ... 2017-09-21T06...
    lon      (date) float64 120B -59.99 -59.8 -59.7 ... -58.78 -58.75 -58.83
    lat      (date) float64 120B 20.21 20.16 19.92 19.65 ... 18.52 18.33 18.3
```

End of output.

Now `lon` and `lat` are listed `(date)` — one value per profile — and the dimensions are back to just `level` and `date`. The `*` symbol before `level` and `date` indicates that they are "dimension coordinates" (they describe the coordinates associated with data variable axes) while `lon` and `lat` — no `*` — are "non-dimension coordinates". We can make any variable a non-dimension coordinate.

### Coordinates vs. Data Variables

Data variables can be modified through arithmetic operations or other functions. Coordinates are always kept the same.

The following is Python code:

```python
argo_scaled = argo * 10000
print(argo_scaled.salinity.values[0, :3])
print(argo.salinity.values[0, :3])
print(argo_scaled.level.values[:5])
```

End of code.

The following is the expected output:

```
[351790.   353340.   355940.03]
[35.179 35.334 35.594]
[0 1 2 3 4]
```

End of output.

The salinity values in the multiplied Dataset are ten thousand times bigger, but the `level` coordinate is untouched — still 0, 1, 2, 3, 4. Multiplying a Dataset multiplies every *data variable* and leaves every *coordinate* alone. (And note the original `argo` is unchanged — the arithmetic returned a new object.)

:::{admonition} Try it
:class: tip
Build your own `DataArray` from a small NumPy array: give it a named `dims` and a `coords` entry, attach `units` and `standard_name` attributes, and plot it. Then bundle two arrays that share a dimension into a single `Dataset`.
:::

## Selecting Data (Indexing)

We can always use regular numpy indexing and slicing on DataArrays

The following is Python code:

```python
print(argo.salinity[2])
```

End of code.

The following is the expected output:

```
<xarray.DataArray 'salinity' (date: 15)> Size: 60B
array([35.18 , 35.333, 35.594, 35.607, 34.236, 34.345, 34.311, 35.272,
       35.086, 34.863, 35.026, 35.773, 36.13 , 36.081, 36.155],
      dtype=float32)
Coordinates:
  * date     (date) datetime64[ns] 120B 2017-07-13T06:53:00 ... 2017-09-21T06...
    lon      (date) float64 120B -59.99 -59.8 -59.7 ... -58.78 -58.75 -58.83
    lat      (date) float64 120B 20.21 20.16 19.92 19.65 ... 18.52 18.33 18.3
    level    int64 8B 2
```

End of output.

Indexing `[2]` took row 2 along the first dimension (`level`), leaving a 1-D array over `date` — and listen to the end of the coordinates: `level` is still there, but as a *scalar* coordinate recording which level this slice came from.

The following is Python code:

```python
argo.salinity[2].plot()
plt.show()
```

End of code.

**What this shows.** A time series of salinity at level 2 across the 15 profile dates, mid-July to late September 2017, with axes labelled "date" and "salinity" and the title "level = 2" — xarray recorded what we selected. The curve rises from 35.18 to about 35.6 in late July, plunges to its minimum of 34.24 at the start of August, stays fresh (around 34.3) through mid-August, then recovers unevenly to its maximum of about 36.16 by the final profile on September 21.

The following is Python code:

```python
profile = argo.salinity[:, 10]
print(profile.sizes)
print(profile.date.values)
print(profile.values[:6])
```

End of code.

The following is the expected output:

```
Frozen({'level': 109})
2017-09-01T06:53:00.000000000
[35.025 35.026 35.026 35.025 35.025 35.027]
```

End of output.

`[:, 10]` takes column 10 — all 109 levels of the profile measured on September 1st. (Printing the full object would read out all 109 values; the sizes line plus the first six values tells us what we have.)

The following is Python code:

```python
argo.salinity[:, 10].plot()
plt.show()
```

End of code.

**What this shows.** Since this slice varies along `level`, `.plot()` puts level on the x-axis (0 to 108) and salinity on the y-axis (34.8 to 37.3) — a profile lying on its side. The curve is flat at 35.0 for levels 0 through 8, shoots up to its maximum of 37.29 at level 17, then declines — steeply between levels 40 and 60 — to a minimum near 34.85 around level 62–66, and flattens back near 35.0 for all the deep levels. The title reads "date = 2017-09-01T06:53:00, lon = -59.24, lat =..." — the scalar coordinates of the profile we picked, straight from the metadata.

However, it is often much more powerful to use xarray's `.sel()` method to use label-based indexing.

The following is Python code:

```python
print(argo.salinity.sel(level=2))
```

End of code.

The following is the expected output:

```
<xarray.DataArray 'salinity' (date: 15)> Size: 60B
array([35.18 , 35.333, 35.594, 35.607, 34.236, 34.345, 34.311, 35.272,
       35.086, 34.863, 35.026, 35.773, 36.13 , 36.081, 36.155],
      dtype=float32)
Coordinates:
  * date     (date) datetime64[ns] 120B 2017-07-13T06:53:00 ... 2017-09-21T06...
    lon      (date) float64 120B -59.99 -59.8 -59.7 ... -58.78 -58.75 -58.83
    lat      (date) float64 120B 20.21 20.16 19.92 19.65 ... 18.52 18.33 18.3
    level    int64 8B 2
```

End of output.

**Check it.** Same result as positional `[2]` — but selected by *label*, not position:

The following is Python code:

```python
print(argo.salinity.sel(level=2).equals(argo.salinity[2]))
```

End of code.

The following is the expected output:

```
True
```

End of output.

The following is Python code:

```python
argo.salinity.sel(level=2).plot()
plt.show()
```

End of code.

**What this shows.** Exactly the same figure as `argo.salinity[2].plot()` above — the same time-series curve, the same "level = 2" title.

Label-based selection really shines with dates. Our `date` coordinate holds real datetime values:

The following is Python code:

```python
print(argo.date)
```

End of code.

The following is the expected output:

```
<xarray.DataArray 'date' (date: 15)> Size: 120B
array(['2017-07-13T06:53:00.000000000', '2017-07-18T06:49:00.000000000',
       '2017-07-23T06:49:00.000000000', '2017-07-28T06:47:00.000000000',
       '2017-08-02T06:49:00.000000000', '2017-08-07T06:50:00.000000000',
       '2017-08-12T06:53:00.000000000', '2017-08-17T06:52:00.000000000',
       '2017-08-22T06:55:00.000000000', '2017-08-27T06:57:00.000000000',
       '2017-09-01T06:53:00.000000000', '2017-09-06T06:48:00.000000000',
       '2017-09-11T06:49:00.000000000', '2017-09-16T06:55:00.000000000',
       '2017-09-21T06:52:00.000000000'], dtype='datetime64[ns]')
Coordinates:
  * date     (date) datetime64[ns] 120B 2017-07-13T06:53:00 ... 2017-09-21T06...
    lon      (date) float64 120B -59.99 -59.8 -59.7 ... -58.78 -58.75 -58.83
    lat      (date) float64 120B 20.21 20.16 19.92 19.65 ... 18.52 18.33 18.3
```

End of output.

Fifteen timestamps, one every five days from July 13 to September 21, 2017 — this Argo float surfaced and reported on a five-day cycle. So we can select a profile by its calendar date:

The following is Python code:

```python
prof = argo.salinity.sel(date='2017-07-23')
print(prof.sizes)
print(prof.values[:4])
```

End of code.

The following is the expected output:

```
Frozen({'level': 109, 'date': 1})
[[35.594]
 [35.594]
 [35.594]
 [35.593]]
```

End of output.

Passing just the day, `'2017-07-23'`, matched the full timestamp `2017-07-23T06:49:00` — xarray treats a partial date string as "everything within that day", so the result keeps a length-1 `date` dimension (hear it in the sizes line, and in each value printing inside its own brackets).

The following is Python code:

```python
argo.salinity.sel(date='2017-07-23').plot(y='level', yincrease=False)
plt.show()
```

End of code.

**What this shows.** A single vertical profile, plotted the oceanographer's way: salinity on the x-axis (roughly 34.9 to 37.3) and level on the y-axis with level 0 at the top, increasing downward — thanks to `y='level', yincrease=False`. Read top to bottom: the curve hangs near 35.6 for the top eight levels, swings far right to its maximum of 37.31 near level 18, then curls back left, crossing to a minimum near 34.83 around level 60–65, and finishes as a nearly vertical line at 35.0 all the way down to level 108. The title carries the date and float position.

`.sel()` also supports slicing. Unfortunately we have to use a somewhat awkward syntax, but it still works.

The following is Python code:

```python
sub = argo.salinity.sel(date=slice('2017-07-01', '2017-08-01'))
print(sub.sizes)
print(sub.date.values)
```

End of code.

The following is the expected output:

```
Frozen({'level': 109, 'date': 4})
['2017-07-13T06:53:00.000000000' '2017-07-18T06:49:00.000000000'
 '2017-07-23T06:49:00.000000000' '2017-07-28T06:47:00.000000000']
```

End of output.

The slice from July 1 to August 1 caught the four July profiles.

The following is Python code:

```python
argo.salinity.sel(date=slice('2017-07-01', '2017-08-01')).plot()
plt.show()
```

End of code.

**What this shows.** A pcolormesh section of just those four profiles: date on the x-axis (July 11 through 30), level on the y-axis with 0 at the *bottom* (we didn't flip it here), and a colorbar labelled "salinity" from about 34.9 to 37.3. Each profile is a wide vertical stripe, and all four show the same structure we know: the bright-yellow salty band at levels roughly 13–25 (near the bottom of the picture), the dark-purple salinity minimum around levels 55–70 above it.

`.sel()` also works on the whole Dataset

The following is Python code:

```python
print(argo.sel(date='2017-07-23'))
```

End of code.

The following is the expected output:

```
<xarray.Dataset> Size: 2kB
Dimensions:      (level: 109, date: 1)
Coordinates:
  * level        (level) int64 872B 0 1 2 3 4 5 6 ... 103 104 105 106 107 108
  * date         (date) datetime64[ns] 8B 2017-07-23T06:49:00
    lon          (date) float64 8B -59.7
    lat          (date) float64 8B 19.92
Data variables:
    salinity     (level, date) float32 436B 35.59 35.59 35.59 ... 34.98 nan
    temperature  (level, date) float32 436B 28.31 28.31 28.31 ... 3.699 nan
    pressure     (level, date) float32 436B 3.0 4.0 5.0 ... 1.983e+03 nan
```

End of output.

One selection cut *all three* variables down to that single date at once — that's the point of bundling them into a Dataset.

:::{admonition} Try it
:class: tip
Using the `argo` Dataset, select data three ways: a single `level` by position with `.isel`, a single `date` by label with `.sel`, and a range of dates with `.sel(date=slice(...))`. Plot one of them.
:::

## Computation

Xarray dataarrays and datasets work seamlessly with arithmetic operators and numpy array functions.

The following is Python code:

```python
temp_kelvin = argo.temperature + 273.15
temp_kelvin.plot(yincrease=False)
plt.show()
```

End of code.

**What this shows.** The temperature section in the now-familiar layout (date across, level 0 at top), with the colorbar labelled "temperature" running from about 277 to 302 — Kelvin now, not Celsius. Warm yellow (around 300 K) fills the top ten levels; teal mid-depths follow; and below level 60 everything is dark purple, around 277–280 K (that is, 4–7 °C).

**Check it.** The arithmetic really did shift every value by 273.15:

The following is Python code:

```python
print(round(float(argo.temperature[0, 0]), 2), round(float(temp_kelvin[0, 0]), 2))
```

End of code.

The following is the expected output:

```
27.97 301.12
```

End of output.

We can also combine multiple xarray datasets in arithmetic operations

The following is Python code:

```python
g = 9.8
buoyancy = g * (2e-4 * argo.temperature - 7e-4 * argo.salinity)
buoyancy.plot(yincrease=False)
plt.show()
```

End of code.

**What this shows.** The same section shape once more, but the colorbar now runs from about −0.234 to −0.178. Buoyancy is highest (yellow-green, near −0.18) in the warm upper ten levels and decreases downward to its lowest values (dark purple, near −0.233) below level 60 — light water sitting on top of dense water, a stably stratified ocean. Note what just happened: one line of code combined two different variables, and xarray lined their coordinates up for us.

:::{admonition} Try it
:class: tip
Convert `argo.temperature` to Kelvin (add 273.15) and plot it. Then compute a new field that combines two variables — e.g. a simple buoyancy-like expression from temperature and salinity — and plot that too.
:::

## Broadcasting, Alignment, and Combining Data

### Broadcasting

Broadcasting arrays in numpy is a nightmare. It is much easier when the data axes are labeled!

This is a useless calculation, but it illustrates how perfoming an operation on arrays with different coordinates will result in automatic broadcasting

The following is Python code:

```python
level_times_lat = argo.level * argo.lat
print(level_times_lat.sizes)
print(level_times_lat.values[0, :3])
print(level_times_lat.values[108, :3])
```

End of code.

The following is the expected output:

```
Frozen({'level': 109, 'date': 15})
[0. 0. 0.]
[2182.896 2177.28  2151.684]
```

End of output.

`level` is 1-D over `level` and `lat` is 1-D over `date` — yet their product is a full 2-D array over `(level, date)`. Xarray broadcast them against each other automatically, by *name*, with no reshaping and no axis-counting. Row 0 is all zeros (level 0 times anything), and row 108 is 108 times the latitudes (about 20.2 down to 18.3).

The following is Python code:

```python
level_times_lat.plot()
plt.show()
```

End of code.

**What this shows.** A 2-D colormap over date (x) and level (y, 0 at bottom), forming a smooth vertical ramp: dark purple (0) along the bottom rising to yellow (about 2180) along the top. Across dates there's barely any variation — the latitude only drifts from 20.2 down to 18.3 over the two months, so the top of the ramp dims very slightly from left to right.

### Alignment

If you try to perform operations on DataArrays that share a dimension name, Xarray will try to _align_ them first.
This works nearly identically to Pandas, except that there can be multiple dimensions to align over.

To see how alignment works, we will create some subsets of our original data.

The following is Python code:

```python
argo.salinity.isel(level=slice(0, 30)).plot(yincrease=False)
plt.show()
```

End of code.

**What this shows.** A zoom into the top 30 levels only, level 0 at the top. The top eight rows are the fresher blue surface layer — with the dark-purple fresh patch in early August and a teal-green patch after mid-September — and everything below level 12 or so is the salty yellow-green core (36.5–37.4). Colorbar "salinity", about 34.2 to 37.4.

The following is Python code:

```python
sa_surf = argo.salinity.isel(level=slice(0, 20))
sa_mid = argo.salinity.isel(level=slice(10, 30))
print(sa_surf.level.values)
print(sa_mid.level.values)
```

End of code.

The following is the expected output:

```
[ 0  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19]
[10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29]
```

End of output.

Two overlapping subsets: `sa_surf` covers levels 0–19, `sa_mid` covers levels 10–29, and they share levels 10–19.

By default, when we combine multiple arrays in mathematical operations, Xarray performs an "inner join".

The following is Python code:

```python
print((sa_surf * sa_mid).level)
```

End of code.

The following is the expected output:

```
<xarray.DataArray 'level' (level: 10)> Size: 80B
array([10, 11, 12, 13, 14, 15, 16, 17, 18, 19])
Coordinates:
  * level    (level) int64 80B 10 11 12 13 14 15 16 17 18 19
```

End of output.

Only the ten overlapping levels survive.

The following is Python code:

```python
(sa_surf * sa_mid).plot(yincrease=False)
plt.show()
```

End of code.

**What this shows.** A short, ten-row section — levels 10 through 19 only, the overlap. And listen to the colorbar: it runs from about 1300 to 1390, because these values are salinity *times* salinity (roughly 36 squared), not salinity. Within the band, the top rows and a few dates (early August, mid-September) are darker (lower products), with brighter yellow below.

We can override this behavior by manually aligning the data

The following is Python code:

```python
sa_surf_outer, sa_mid_outer = xr.align(sa_surf, sa_mid, join='outer')
print(sa_surf_outer.level)
```

End of code.

The following is the expected output:

```
<xarray.DataArray 'level' (level: 30)> Size: 240B
array([ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11, 12, 13, 14, 15, 16, 17,
       18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29])
Coordinates:
  * level    (level) int64 240B 0 1 2 3 4 5 6 7 8 ... 21 22 23 24 25 26 27 28 29
```

End of output.

As we can see, missing data (NaNs) have been filled in where the array was extended.

The following is Python code:

```python
sa_surf_outer.plot(yincrease=False)
plt.show()
```

End of code.

**What this shows.** Levels 0–29 on the y-axis (0 at top), but colored cells only for levels 0–19: the familiar blue surface layer over the yellow salty band. The bottom third of the plot — levels 20–29 — is blank white: the NaN padding the outer join created.

The following is Python code:

```python
sa_mid_outer.plot(yincrease=False)
plt.show()
```

End of code.

**What this shows.** The complementary picture: the top third (levels 0–9) is blank white, and data fills levels 10–29 — a mottled yellow-green field, with the colorbar now spanning a narrow 36.1 to 37.3.

We can also use `join='right'` and `join='left'`.

### Combining Data: Concat and Merge

The ability to combine many smaller arrays into a single big Dataset is one of the main advantages of Xarray.
To take advantage of this, we need to learn two operations that help us combine data:
- `xr.concat`: to concatenate multiple arrays into one bigger array along their dimensions
- `xr.merge`: to combine multiple different arrays into a dataset

First let's look at concat. Let's re-combine the subsetted data from the previous step.

The following is Python code:

```python
sa_surf_mid = xr.concat([sa_surf, sa_mid], dim='level')
print(sa_surf_mid.sizes)
```

End of code.

The following is the expected output:

```
Frozen({'level': 40, 'date': 15})
```

End of output.

:::{warning}
Xarray won't check the values of the coordinates before `concat`. It will just stick everything together into a new array.
:::

In this case, we had overlapping data. We can see this by looking at the `level` coordinate.

The following is Python code:

```python
print(sa_surf_mid.level)
```

End of code.

The following is the expected output:

```
<xarray.DataArray 'level' (level: 40)> Size: 320B
array([ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11, 12, 13, 14, 15, 16, 17,
       18, 19, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25,
       26, 27, 28, 29])
Coordinates:
  * level    (level) int64 320B 0 1 2 3 4 5 6 7 8 ... 21 22 23 24 25 26 27 28 29
```

End of output.

Listen carefully to the values: 0 up to 19, then back to 10, then up to 29 — levels 10 through 19 appear **twice**.

The following is Python code:

```python
plt.plot(sa_surf_mid.level.values, marker='o')
plt.show()
```

End of code.

**What this shows.** A sawtooth: the line climbs steadily 0, 1, 2, … up to 19 (indices 0–19 on the x-axis), then drops abruptly back to 10 at index 20 and climbs again from 10 to 29 across indices 20–39. Round markers show every point. The sudden drop is the concatenation seam — the duplicated levels.

We can also concat data along a _new_ dimension, e.g.

The following is Python code:

```python
sa_concat_new = xr.concat([sa_surf, sa_mid], dim='newdim')
print(sa_concat_new.sizes)
```

End of code.

The following is the expected output:

```
Frozen({'newdim': 2, 'level': 30, 'date': 15})
```

End of output.

(When you run this you may also hear a **FutureWarning** read out from the terminal, saying that a future version of xarray will change the default `join` from `'outer'` to `'exact'` and recommending you set `join` explicitly. It's safe to ignore for now — but it's telling you exactly what this example demonstrates: the two arrays had different levels, and xarray silently outer-joined them.)

The following is Python code:

```python
plt.figure(figsize=(12, 4))

plt.subplot(121)
sa_concat_new.isel(newdim=0).plot()

plt.subplot(122)
sa_concat_new.isel(newdim=1).plot()

plt.tight_layout()
plt.show()
```

End of code.

**What this shows.** Two panels side by side, each with its own colorbar, and this time level 0 is at the *bottom* (we didn't flip the y-axis). Left panel (`newdim=0`, the surface subset): colored cells fill levels 0–19 — the blue/purple fresher surface water in the bottom rows, the yellow salty band above it — and levels 20–29 are blank white across the top. Right panel (`newdim=1`, the mid subset): blank white below level 10, mottled yellow-green data for levels 10–29 above it. The white regions are the NaN padding from the outer join along `level`.

Note that the data were aligned using an _outer_ join along the non-concat dimensions.

Instead of specifying a new dimension name, we can pass a new Pandas index object explicitly to `concat`.
This will create a new dimension coordinate and corresponding index.

We can merge both DataArrays and Datasets.

The following is Python code:

```python
print(xr.merge([argo.salinity, argo.temperature]))
```

End of code.

The following is the expected output:

```
<xarray.Dataset> Size: 14kB
Dimensions:      (level: 109, date: 15)
Coordinates:
  * level        (level) int64 872B 0 1 2 3 4 5 6 ... 103 104 105 106 107 108
  * date         (date) datetime64[ns] 120B 2017-07-13T06:53:00 ... 2017-09-2...
    lon          (date) float64 120B -59.99 -59.8 -59.7 ... -58.78 -58.75 -58.83
    lat          (date) float64 120B 20.21 20.16 19.92 ... 18.52 18.33 18.3
Data variables:
    salinity     (level, date) float32 7kB 35.18 35.33 35.59 ... 34.99 nan nan
    temperature  (level, date) float32 7kB 27.97 28.2 28.31 ... 3.688 nan nan
```

End of output.

If the data are not aligned, they will be aligned before merge.
We can specify the join options in `merge`.

The following is Python code:

```python
print(xr.merge([
    argo.salinity.sel(level=slice(0, 30)),
    argo.temperature.sel(level=slice(30, None))
]))
```

End of code.

(When you run this, the same FutureWarning about `join` may be read out first.)

The following is the expected output:

```
<xarray.Dataset> Size: 14kB
Dimensions:      (level: 109, date: 15)
Coordinates:
  * level        (level) int64 872B 0 1 2 3 4 5 6 ... 103 104 105 106 107 108
  * date         (date) datetime64[ns] 120B 2017-07-13T06:53:00 ... 2017-09-2...
    lon          (date) float64 120B -59.99 -59.8 -59.7 ... -58.78 -58.75 -58.83
    lat          (date) float64 120B 20.21 20.16 19.92 ... 18.52 18.33 18.3
Data variables:
    salinity     (level, date) float32 7kB 35.18 35.33 35.59 ... nan nan nan
    temperature  (level, date) float32 7kB nan nan nan nan ... nan 3.688 nan nan
```

End of output.

Hear the difference in the previews: salinity now *ends* in NaNs (it only had levels 0–30) and temperature *starts* with NaNs (it only had levels 30 and deeper) — the outer join padded each variable where it had no data.

The following is Python code:

```python
print(xr.merge([
    argo.salinity.sel(level=slice(0, 30)),
    argo.temperature.sel(level=slice(30, None))
], join='left'))
```

End of code.

The following is the expected output:

```
<xarray.Dataset> Size: 4kB
Dimensions:      (level: 31, date: 15)
Coordinates:
  * level        (level) int64 248B 0 1 2 3 4 5 6 7 ... 23 24 25 26 27 28 29 30
  * date         (date) datetime64[ns] 120B 2017-07-13T06:53:00 ... 2017-09-2...
    lon          (date) float64 120B -59.99 -59.8 -59.7 ... -58.78 -58.75 -58.83
    lat          (date) float64 120B 20.21 20.16 19.92 ... 18.52 18.33 18.3
Data variables:
    salinity     (level, date) float32 2kB 35.18 35.33 35.59 ... 36.68 36.6
    temperature  (level, date) float32 2kB nan nan nan nan ... 18.87 18.9 18.56
```

End of output.

With `join='left'`, the result keeps only the *first* argument's levels (0–30 — hear `level: 31` in the dimensions line).

:::{admonition} Try it
:class: tip
Make two overlapping depth-subsets of `argo.salinity` (e.g. levels 0–20 and 10–30). Multiply them and look at the resulting `level` coordinate (an inner join). Then `xr.align(..., join='outer')` them and compare. Finally, `xr.merge` salinity and temperature into one Dataset.
:::

## Reductions

Just like in numpy, we can reduce xarray DataArrays along any number of axes:

The following is Python code:

```python
print(argo.temperature.mean(axis=0))
```

End of code.

The following is the expected output:

```
<xarray.DataArray 'temperature' (date: 15)> Size: 60B
array([13.342323, 13.270844, 13.319009, 13.242446, 13.278371, 13.28787 ,
       13.168018, 13.237453, 12.954085, 13.05355 , 13.058639, 12.794382,
       12.734872, 12.942316, 12.913232], dtype=float32)
Coordinates:
  * date     (date) datetime64[ns] 120B 2017-07-13T06:53:00 ... 2017-09-21T06...
    lon      (date) float64 120B -59.99 -59.8 -59.7 ... -58.78 -58.75 -58.83
    lat      (date) float64 120B 20.21 20.16 19.92 19.65 ... 18.52 18.33 18.3
```

End of output.

Averaging over `axis=0` (the levels) leaves one mean temperature per date — all near 13 °C.

The following is Python code:

```python
tbar = argo.temperature.mean(axis=1)
print(tbar.sizes)
print(tbar.values[:4].round(2))
print(tbar.values[-4:].round(2))
```

End of code.

The following is the expected output:

```
Frozen({'level': 109})
[28.46 28.46 28.46 28.46]
[3.77 3.74 3.71 3.66]
```

End of output.

Averaging over `axis=1` (the dates) instead leaves one time-mean per level — about 28.5 °C at the surface down to 3.7 °C at the deepest levels.

However, rather than performing reductions on axes (as in numpy), we can perform them on dimensions. This turns out to be a huge convenience

The following is Python code:

```python
argo_mean = argo.mean(dim='date')
print(argo_mean)
```

End of code.

The following is the expected output:

```
<xarray.Dataset> Size: 2kB
Dimensions:      (level: 109)
Coordinates:
  * level        (level) int64 872B 0 1 2 3 4 5 6 ... 103 104 105 106 107 108
Data variables:
    salinity     (level) float32 436B 35.27 35.27 35.27 ... 34.98 34.98 34.98
    temperature  (level) float32 436B 28.46 28.46 28.46 ... 3.743 3.707 3.663
    pressure     (level) float32 436B 3.0 4.0 5.0 ... 1.986e+03 2.007e+03
```

End of output.

One call reduced *every* variable in the Dataset over `date` — no axis numbers, no guessing. The `date` dimension (and the lon/lat coordinates that depended on it) are simply gone.

The following is Python code:

```python
argo_mean.salinity.plot(y='level', yincrease=False)
plt.show()
```

End of code.

**What this shows.** The fifteen profiles collapsed into one time-mean profile: salinity (34.8–37.3) on the x-axis, level on the y-axis with 0 at the top. The mean profile starts near 35.27 at the surface, kinks sharply right to its maximum of 37.27 at level 18, bends smoothly back left to its minimum of 34.83 near level 64, then hangs almost perfectly vertical at 34.98 down to level 108.

The following is Python code:

```python
argo_std = argo.std(dim='date')
argo_std.salinity.plot(y='level', yincrease=False)
plt.show()
```

End of code.

**What this shows.** The standard deviation of salinity across the 15 dates, same profile layout, x-axis 0 to about 0.63. All the variability is near the surface: the curve reads about 0.62 at level 0, peaks at 0.63 around level 7, and collapses to under 0.15 by level 15. Small wiggles between about 0.03 and 0.13 follow through levels 15–55, and below level 75 the curve hugs zero (under 0.01) — the deep ocean's salinity barely changed between July and September.

### Weighted Reductions

Sometimes we want to perform a reduction (e.g. a mean) where we assign different weight factors to each point in the array.
Xarray supports this via [weighted array reductions](http://xarray.pydata.org/en/stable/user-guide/computation.html#weighted-array-reductions).

As a toy example, imagine we want to weight values in the upper ocean more than the lower ocean.
We could imagine creating a weight array exponentially proportional to pressure as follows:

The following is Python code:

```python
mean_pressure = argo.pressure.mean(dim='date')
p0 = 250  # dbar
weights = np.exp(-mean_pressure / p0)
weights.plot()
plt.show()
```

End of code.

**What this shows.** An exponential decay against level (x-axis 0 to 108): the weight is 0.99 at level 0, has a small shoulder near level 8, falls to 0.26 by level 40, and is effectively zero (0.0003) by level 108. One quirk worth hearing: the y-axis is labelled "pressure", because the `weights` array inherited its name from the `mean_pressure` it was computed from. (Also notice we fed a DataArray to the *numpy* function `np.exp` and got a DataArray back, coordinates intact.)

**Check it.**

The following is Python code:

```python
print(round(float(weights[0]), 3), round(float(weights[40]), 3), round(float(weights[108]), 5))
```

End of code.

The following is the expected output:

```
0.988 0.259 0.00033
```

End of output.

The weighted mean over the `level` dimensions is calculated as follows:

The following is Python code:

```python
temp_weighted_mean = argo.temperature.weighted(weights).mean('level')
```

End of code.

Comparing to the unweighted mean, we see the difference:

The following is Python code:

```python
temp_weighted_mean.plot(label='weighted')
argo.temperature.mean(dim='level').plot(label='unweighted')
plt.legend()
plt.show()
```

End of code.

**What this shows.** Two nearly flat time series, far apart on the plot: the blue "weighted" curve wobbles between 23.2 and 24.1 °C along the top, while the orange "unweighted" curve sits between 12.7 and 13.3 °C near the bottom; a legend names them. The weighted mean is about ten degrees warmer because the exponential weights emphasize the warm upper ocean, while the plain mean counts each of the 109 levels equally — including the cold deep.

**Check it.** Print both series (rounded) instead of eyeballing them:

The following is Python code:

```python
print(temp_weighted_mean.values.round(2))
print(argo.temperature.mean(dim='level').values.round(2))
```

End of code.

The following is the expected output:

```
[23.84 23.91 23.81 23.87 24.04 24.08 24.08 23.98 23.38 23.79 23.75 23.22
 23.21 23.54 23.54]
[13.34 13.27 13.32 13.24 13.28 13.29 13.17 13.24 12.95 13.05 13.06 12.79
 12.73 12.94 12.91]
```

End of output.

:::{admonition} Try it
:class: tip
Compute the time-mean and time-standard-deviation of `argo.temperature` with `.mean(dim='date')` and `.std(dim='date')`, and plot the mean profile. Then build a weight array of your choice and compute a depth-**weighted** mean of temperature; plot weighted vs. unweighted.
:::

## Loading Data from netCDF Files

NetCDF (Network Common Data Format) is the most widely used format for distributing geoscience data. NetCDF is maintained by the [Unidata](https://www.unidata.ucar.edu/) organization.

Below we quote from the [NetCDF website](https://www.unidata.ucar.edu/software/netcdf/docs/faq.html#whatisit):

>NetCDF (network Common Data Form) is a set of interfaces for array-oriented data access and a freely distributed collection of data access libraries for C, Fortran, C++, Java, and other languages. The netCDF libraries support a machine-independent format for representing scientific data. Together, the interfaces, libraries, and format support the creation, access, and sharing of scientific data.
>
>NetCDF data is:
>
> - Self-Describing. A netCDF file includes information about the data it contains.
> - Portable. A netCDF file can be accessed by computers with different ways of storing integers, characters, and floating-point numbers.
> - Scalable. A small subset of a large dataset may be accessed efficiently.
> - Appendable. Data may be appended to a properly structured netCDF file without copying the dataset or redefining its structure.
> - Sharable. One writer and multiple readers may simultaneously access the same netCDF file.
> - Archivable. Access to all earlier forms of netCDF data will be supported by current and future versions of the software.

Xarray was designed to make reading netCDF files in python as easy, powerful, and flexible as possible. (See [xarray netCDF docs](http://xarray.pydata.org/en/latest/io.html#netcdf) for more details.)

Below we load some data from the [LEAP catalog](https://catalog.leap.columbia.edu/). (These are **Zarr** stores — the cloud-native cousin of netCDF you met in the data module — read over plain HTTPS. Opening one only reads the *metadata*, so it's quick; the data values download later, only when a computation or plot actually needs them. That's what `chunks={}` sets up, via a library called dask.)

The following is Python code:

```python
# https://catalog.leap.columbia.edu/feedstock/australian-gridded-climate-data-agcd
store = 'https://ncsa.osn.xsede.org/Pangeo/pangeo-forge/pangeo-forge/AGDC-feedstock/AGCD.zarr'
ds = xr.open_dataset(store, engine='zarr', chunks={})
print(ds)
```

End of code.

The following is the expected output:

```
<xarray.Dataset> Size: 175GB
Dimensions:     (time: 17897, lat: 691, lon: 886)
Coordinates:
  * time        (time) datetime64[ns] 143kB 1971-01-01T09:00:00 ... 2019-12-3...
  * lat         (lat) float32 3kB -44.5 -44.45 -44.4 ... -10.1 -10.05 -10.0
  * lon         (lon) float32 4kB 112.0 112.1 112.1 112.2 ... 156.1 156.2 156.2
Data variables:
    precip      (time, lat, lon) float32 44GB dask.array<chunksize=(40, 691, 886), meta=np.ndarray>
    tmax        (time, lat, lon) float32 44GB dask.array<chunksize=(40, 691, 886), meta=np.ndarray>
    tmin        (time, lat, lon) float32 44GB dask.array<chunksize=(40, 691, 886), meta=np.ndarray>
    vapourpres  (time, lat, lon) float32 44GB dask.array<chunksize=(40, 691, 886), meta=np.ndarray>
Attributes: (12/36)
    Conventions:               CF-1.6, ACDD-1.3
    acknowledgment:            The Australian Government, Bureau of Meteorolo...
    agcd_version:              AGCD (AWAP) v1.0.0 Snapshot (1900-01-01 to 202...
    analysis_components:       0900: the gridded vapour pressure value at 9am...
    attribution:               Data should be cited as : Australian Bureau of...
    cdm_data_type:             Grid
    ...                        ...
    summary:                   The partial pressure of water vapour in air (v...
    time_coverage_end:         1971-12-31T00:00:00
    time_coverage_start:       1971-01-01T15:00:00
    title:                     Interpolated Vapour Pressure
    url:                       http://www.bom.gov.au/climate/
    uuid:                      e684e0a6-73c7-4522-ab78-a8285ca34b4b
```

End of output.

This is gridded daily Australian climate data: three dimensions (`time`: 17,897 days over 1971–2019; `lat`: 691 points from 44.5°S to 10°S; `lon`: 886 points from 112°E to 156.2°E) and four variables — a **175 GB** Dataset that opened in a second or two, because nothing has been downloaded yet. Each data-variable line now says `dask.array` with a chunk size instead of a value preview. Two more listening notes: the Attributes header reads `(12/36)` — the listing shows only 12 of 36 attributes, with a `...` line in the middle — and every attribute value longer than the line is cut off with `...`.

Plotting one time step *does* download that slice of data (roughly 100 MB here), so give this a minute:

The following is Python code:

```python
ds.tmax.isel(time=-1).plot()
plt.show()
```

End of code.

**What this shows.** A map of Australia: longitude 112–156°E on the x-axis, latitude 44°S to 10°S on the y-axis, and a colorbar labelled "Daily maximum air temperature [degrees_Celsius]" running from about 8 to 47 — the axis labels, colorbar label, and the title ("time = 2019-12-31T09:00:00") all built from metadata. The last day of 2019 fell during Australia's "Black Summer": a huge yellow mass of 40–47 °C maximum temperatures covers the western and central interior (the single hottest pixel, 47.8 °C, is in the northwest inland, near 118°E 23.6°S), cooling through green (25–30 °C) toward the coasts, with the coolest dark purples (under 15 °C) in the far southeast of the map around Tasmania.

**Check it.** The same facts, straight from the data:

The following is Python code:

```python
tmax_last = ds.tmax.isel(time=-1)
print(tmax_last.time.values)
print(round(float(tmax_last.min()), 1), round(float(tmax_last.max()), 1))
```

End of code.

The following is the expected output:

```
2019-12-31T09:00:00.000000000
7.6 47.8
```

End of output.

The following is Python code:

```python
ds.precip.isel(time=-1).plot()
plt.show()
```

End of code.

**What this shows.** The same map frame, but the colorbar now reads "Daily precipitation [mm]", 0 to about 39 — and the map is almost entirely dark purple: zero rain (88% of grid points got under 1 mm that day, deep in that drought-and-fire summer). The exceptions are small bright specks: patches in the tropical north around 130°E 15°S, the day's maximum — a lone bright-yellow spot of about 39 mm — on the central east coast near 153°E 23.6°S, and faint teal traces over Tasmania and the southeast.

The following is Python code:

```python
# https://catalog.leap.columbia.edu/feedstock/aws-noaa-optimum-interpolated-sst
store = 'https://ncsa.osn.xsede.org/Pangeo/pangeo-forge/noaa_oisst/v2.1-avhrr.zarr'
ds = xr.open_dataset(store, engine='zarr', chunks={})
print(ds)
```

End of code.

The following is the expected output:

```
<xarray.Dataset> Size: 241GB
Dimensions:  (time: 14532, zlev: 1, lat: 720, lon: 1440)
Coordinates:
  * time     (time) datetime64[ns] 116kB 1981-09-01T12:00:00 ... 2021-06-14T1...
  * zlev     (zlev) float32 4B 0.0
  * lat      (lat) float32 3kB -89.88 -89.62 -89.38 -89.12 ... 89.38 89.62 89.88
  * lon      (lon) float32 6kB 0.125 0.375 0.625 0.875 ... 359.4 359.6 359.9
Data variables:
    anom     (time, zlev, lat, lon) float32 60GB dask.array<chunksize=(20, 1, 720, 1440), meta=np.ndarray>
    err      (time, zlev, lat, lon) float32 60GB dask.array<chunksize=(20, 1, 720, 1440), meta=np.ndarray>
    ice      (time, zlev, lat, lon) float32 60GB dask.array<chunksize=(20, 1, 720, 1440), meta=np.ndarray>
    sst      (time, zlev, lat, lon) float32 60GB dask.array<chunksize=(20, 1, 720, 1440), meta=np.ndarray>
```

End of output.

(The listing continues with an `Attributes: (12/37)` section, abbreviated just like before — we've trimmed it here.) This is NOAA's quarter-degree daily global sea-surface temperature: a global 720-by-1440 grid, every day from September 1981 to June 2021, 241 GB in all.

The following is Python code:

```python
ds.sst.isel(time=-1, zlev=0).plot()
plt.show()
```

End of code.

**What this shows.** A global map: longitude 0–360 on the x-axis, latitude −90 to 90 on the y-axis, continents blank white (SST is NaN over land), title "time = 2021-06-14T12:00:00, zlev = 0.0 [meters]". The colorbar, labelled "Daily sea surface temperature [Celsius]", spans about −30 to +30 in a red-blue diverging colormap centered at zero — but since the ocean only actually spans −1.8 to 32.9 °C, the map uses only the red half: deep red (28 °C and up) fills a broad band across the tropics, fading through lighter reds in the mid-latitudes to near-white at the poles. It's mid-June, so the dark-red band sits slightly north of the equator.

**Check it.**

The following is Python code:

```python
sst_last = ds.sst.isel(time=-1, zlev=0)
print(round(float(sst_last.min()), 1), round(float(sst_last.max()), 1))
```

End of code.

The following is the expected output:

```
-1.8 32.9
```

End of output.

(That −1.8 °C is the freezing point of seawater — the polar ocean at the ice edge.) And remember the profile trick: a 2-D map like this can always be examined as 1-D lines — for example `ds.sst.isel(time=-1, zlev=0).sel(lat=0.125, method='nearest')` is the equatorial temperature as a function of longitude, ready to plot or print.

The following is Python code:

```python
print(ds.time)
```

End of code.

The following is the expected output:

```
<xarray.DataArray 'time' (time: 14532)> Size: 116kB
array(['1981-09-01T12:00:00.000000000', '1981-09-02T12:00:00.000000000',
       '1981-09-03T12:00:00.000000000', ..., '2021-06-12T12:00:00.000000000',
       '2021-06-13T12:00:00.000000000', '2021-06-14T12:00:00.000000000'],
      shape=(14532,), dtype='datetime64[ns]')
Coordinates:
  * time     (time) datetime64[ns] 116kB 1981-09-01T12:00:00 ... 2021-06-14T1...
Attributes:
    long_name:  Center time of the day
```

End of output.

:::{admonition} Try it
:class: tip
Open one of the LEAP-catalog Zarr datasets above, pick a variable, select its most recent time step with `.isel(time=-1)`, and plot the map. Then check it without looking: print the slice's `time` value and its min and max.
:::

## Recap

You've now met xarray's two core structures and the everyday operations on labelled multidimensional data:

- **DataArray and Dataset** — values plus named `dims`, `coords`, and `attrs`; the multidimensional analogue of pandas' Series/DataFrame.
- **Selecting** — `.isel` (by position) and `.sel` (by label), including date slices and nearest-neighbour lookups.
- **Computation** — arithmetic and NumPy functions that carry coordinates along, with automatic **broadcasting** and **alignment**, plus `concat` and `merge` to combine data.
- **Reductions** — `.mean`/`.std` over *named dimensions* (not axis numbers), including **weighted** reductions.
- **Loading real data** — from netCDF and cloud-native **Zarr** stores.
- **And the non-visual habit:** a Dataset describes itself — `print(ds)`, `ds.sizes`, `list(ds.data_vars)`, and targeted `.sel(...)` prints tell you everything a figure would.

Next, in [Xarray: Advanced](./xarray_advanced.md), we apply `groupby`, `resample`, `rolling`, and `coarsen` to build climatologies and more.

## Xarray Tutorials

You can find a nice tutorial to fundamentals of Xarray on youtube [here](https://www.youtube.com/watch?v=a339Q5F48UQ&list=PLNemzZpJM7lUu_iGP_lA2m7SeSUwKSIvR&index=9).

If you prefer reading there is a [Xarray in 45 mins](https://tutorial.xarray.dev/overview/xarray-in-45-min)

And much much more: <https://tutorial.xarray.dev/intro.html> and <https://www.youtube.com/playlist?list=PLNemzZpJM7lUu_iGP_lA2m7SeSUwKSIvR>
