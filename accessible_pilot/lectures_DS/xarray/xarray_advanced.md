# Xarray Advanced

:::{admonition} Following along with a screen reader
:class: important
Work through the examples as `.py` scripts in VS Code — see [Setting up an accessible workflow](../computing_env/accessible_setup.md) and [Working with Python scripts](../computing_env/working_with_scripts.md). Run a script in the Git Bash terminal with `python yourfile.py`, and press `Ctrl+Up` to read the output.

Two page-specific notes. First, the packages: this lecture needs `xarray` plus the pieces that let it read data over the network — if you haven't already, `pip install xarray netcdf4 dask matplotlib`. Second, **be online**: the sea-surface-temperature examples stream real data from NOAA and IRI servers, and the loading step can take a minute or more depending on your connection. Once loaded, everything else runs fast — so build up one script cumulatively rather than re-running the download for every experiment.
:::

In this lesson we go beyond the basics — **interpolation, `groupby`, `resample`, `rolling`, and `coarsen`** — and apply them to a real sea-surface-temperature dataset to build climatologies and anomalies, the bread and butter of climate analysis.

:::{admonition} In-class assignment — 10 points
:class: note
The **"Try it"** exercises on this page are part of your in-class assignment for this section. Complete them as `.py` scripts, push them to your week folder, and post the link on the matching **Courseworks** assignment. (One 10-point assignment covers all the lecture pages in this section.)
:::

:::{admonition} Checking data and figures without seeing them
:class: tip
Three habits, used throughout this page:

1. In a notebook, a bare expression at the end of a cell displays itself; in a script it displays nothing — so we **wrap those expressions in `print(...)`** throughout. Printed xarray objects are plain linear text that a screen reader handles well.
2. **Every plot is followed by a "What this shows" note** describing the rendered figure in words and numbers, and short **"Check it"** blocks confirm the same facts by printing values instead of looking.
3. The 1-D line plots on this page (timeseries, climatology curves) can also be explored point by point with **[MAIDR](../sci_python/trying_maidr.md)**. The 2-D maps (`pcolormesh`-style plots) can't — for those, use the slicing trick from the NumPy lecture: pull out one point, row, or column and print it. The "Check it" blocks after the maps below do exactly that.

If you want to keep a figure, add `plt.savefig('somename.png')` just before `plt.show()`.
:::

Start a script (say, `xarray_advanced.py`) with the imports used throughout:

The following is Python code:

```python
import numpy as np
import xarray as xr
from matplotlib import pyplot as plt
```

End of code.

## Interpolation

In the previous lesson on Xarray, we learned how to select data based on its dimension coordinates and align data with different coordinates.
But what if we want to estimate the value of the data variables at _different coordinates_.
This is where interpolation comes in.

The following is Python code:

```python
# we write it out explicitly so we can see each point.
x_data = np.array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10])
f = xr.DataArray(x_data**2, dims=['x'], coords={'x': x_data})
print(f)
```

End of code.

The following is the expected output:

```
<xarray.DataArray (x: 11)> Size: 88B
array([  0,   1,   4,   9,  16,  25,  36,  49,  64,  81, 100])
Coordinates:
  * x        (x) int64 88B 0 1 2 3 4 5 6 7 8 9 10
```

End of output.

Read it with `Ctrl+Up`: a DataArray with one dimension `x` of length 11, the eleven squared values 0 through 100, and the coordinate `x` running 0 to 10. Let's plot it:

The following is Python code:

```python
f.plot(marker='o')
plt.show()
```

End of code.

**What this shows.** Eleven round dots connected by straight line segments, tracing the right half of a parabola: from (0, 0) the curve rises gently at first, then ever more steeply, up to (10, 100). The x-axis is labelled `x` (0 to 10); the y-axis runs 0 to 100 with no label.

We can select the value at any point that exists in the coordinate:

The following is Python code:

```python
print(f.sel(x=4).values)
```

End of code.

The following is the expected output:

```
16
```

End of output.

We only have data on the integer points in x.
But what if we wanted to estimate the value at, say, 4.5? Trying to `.sel` it raises an error — here we catch it so the script keeps running, and print the message:

The following is Python code:

```python
try:
    f.sel(x=4.5)
except KeyError as e:
    print('KeyError:', e)
```

End of code.

The following is the expected output:

```
KeyError: "not all values found in index 'x'. Try setting the `method` keyword argument (example: method='nearest')."
```

End of output.

Interpolation to the rescue! The two neighboring points are:

The following is Python code:

```python
print(f.sel(x=4).values, f.sel(x=5).values)
```

End of code.

The following is the expected output:

```
16 25
```

End of output.

The following is Python code:

```python
print(f.interp(x=4.5).values)
```

End of code.

The following is the expected output (halfway between 16 and 25):

```
20.5
```

End of output.

Interpolation uses [scipy.interpolate](https://docs.scipy.org/doc/scipy/reference/interpolate.html) under the hood.
There are different modes of interpolation:

The following is Python code:

```python
# default
print(f.interp(x=4.5, method='linear').values)
print(f.interp(x=4.5, method='nearest').values)
print(f.interp(x=4.5, method='cubic').values)
```

End of code.

The following is the expected output:

```
20.5
16.0
20.25
```

End of output.

Linear draws a straight line between the neighbors (20.5); nearest just grabs the closest point (16, the value at x=4); cubic fits a smooth curve — and since the data really is x², cubic recovers the exact value 4.5² = 20.25.

We can interpolate to a whole new coordinate at once:

The following is Python code:

```python
x_new = x_data + 0.5

f_interp_linear = f.interp(x=x_new, method='linear')
f_interp_cubic = f.interp(x=x_new, method='cubic')

f.plot(marker='o', label='original')
f_interp_linear.plot(marker='o', label='linear')
f_interp_cubic.plot(marker='o', label='cubic')
plt.legend()
plt.show()
```

End of code.

**What this shows.** Three dotted curves lying essentially on top of one another along the same parabola — blue "original" markers at the integers, orange "linear" and green "cubic" markers at the half-integers (0.5, 1.5, … 9.5), with a legend in the top-left. At this scale the tiny difference between linear and cubic (20.5 vs 20.25 at x=4.5) is invisible; the green cubic markers are drawn last and mostly cover the orange. The interpolated curves stop at 9.5 — nothing is drawn at 10.5, for the reason the next block prints.

Note that values outside of the original range are not supported:

The following is Python code:

```python
print(f_interp_linear.values)
```

End of code.

The following is the expected output:

```
[ 0.5  2.5  6.5 12.5 20.5 30.5 42.5 56.5 72.5 90.5  nan]
```

End of output.

The last requested point, x = 10.5, is outside the original 0–10 range, so it comes back as `nan` — interpolation does not extrapolate.

:::{note}
You can apply interpolation to any dimension, and even to multiple dimensions at a time.
(Multidimensional interpolation only supports `mode='nearest'` and `mode='linear'`.)
But keep in mind that _Xarray has no built-in understanding of geography_.
If you use `interp` on lat / lon coordinates, it will just perform naive interpolation of the lat / lon values.
More sophisticated treatment of spherical geometry requires another package such as [xesmf](https://xesmf.readthedocs.io/).
:::

:::{admonition} Try it
:class: tip
Take the `f = x**2` DataArray (or build your own). Use `.interp()` to estimate its value at a non-integer point (e.g. `x=4.5`), comparing `method='linear'`, `'nearest'`, and `'cubic'` — print the three values. Then interpolate `f` onto a whole new set of x-values and plot the original vs. interpolated curves (print `f_interp.values` too, to check the numbers without looking at the plot).
:::

## Groupby

Xarray copies Pandas' very useful groupby functionality, enabling the "split / apply / combine" workflow on xarray DataArrays and Datasets. In the first part of the lesson, we will learn to use groupby by analyzing sea-surface temperature data.

First we load a dataset. We will use the [NOAA Extended Reconstructed Sea Surface Temperature (ERSST) v5](https://www.ncdc.noaa.gov/data-access/marineocean-data/extended-reconstructed-sea-surface-temperature-ersst-v5) product, a widely used and trusted gridded compilation of of historical data going back to 1854.

Since the data is provided via an [OPeNDAP](https://en.wikipedia.org/wiki/OPeNDAP) server, we can load it directly without downloading anything. **This is the slow step** — a minute or more — and the `chunks` argument matters, as the comment explains:

The following is Python code:

```python
url = 'https://psl.noaa.gov/thredds/dodsC/Datasets/noaa.ersst.v5/sst.mnmean.nc'
# Read the data in small time-chunks (uses dask). Pulling the whole array in
# one OPeNDAP request makes this THREDDS server return all-zeros (blank maps);
# chunking fetches it piece-by-piece, then .load() brings it into memory.
ds = xr.open_dataset(url, drop_variables=['time_bnds'], chunks={'time': 120})
ds = ds.sel(time=slice('1960', '2018')).load()
print(ds)
```

End of code.

The following is the expected output:

```
<xarray.Dataset> Size: 45MB
Dimensions:  (time: 708, lat: 89, lon: 180)
Coordinates:
  * time     (time) datetime64[ns] 6kB 1960-01-01 1960-02-01 ... 2018-12-01
  * lat      (lat) float32 356B 88.0 86.0 84.0 82.0 ... -82.0 -84.0 -86.0 -88.0
  * lon      (lon) float32 720B 0.0 2.0 4.0 6.0 8.0 ... 352.0 354.0 356.0 358.0
Data variables:
    sst      (time, lat, lon) float32 45MB -1.8 -1.8 -1.8 -1.8 ... nan nan nan
Attributes: (12/39)
    climatology:                     Climatology is based on 1971-2000 SST, X...
    description:                     In situ data: ICOADS2.5 before 2007 and ...
    keywords_vocabulary:             NASA Global Change Master Directory (GCM...
    keywords:                        Earth Science > Oceans > Ocean Temperatu...
    instrument:                      Conventional thermometers
    source_comment:                  SSTs were observed by conventional therm...
    ...                              ...
    comment:                         SSTs were observed by conventional therm...
    summary:                         ERSST.v5 is developed based on v4 after ...
    dataset_title:                   NOAA Extended Reconstructed SST V5
    _NCProperties:                   version=2,netcdf=4.6.3,hdf5=1.10.5
    data_modified:                   2026-06-03
    DODS_EXTRA.Unlimited_Dimension:  time
```

End of output.

This is the one place on this page where we print a whole `Dataset`, so here is how to listen to it with `Ctrl+Up`, block by block. Line 1 gives the object type and its total size (45 MB). The **Dimensions** line is the skeleton: `time: 708` (monthly steps, 59 years × 12 months), `lat: 89`, `lon: 180`. Under **Coordinates**, each line starts with a star (meaning it's an index you can `.sel` on), then the name, its dimension, dtype, and the first and last few values: `time` runs 1960-01-01 to 2018-12-01, `lat` from 88 down to −88 in 2-degree steps, `lon` from 0 to 358. Under **Data variables** there is a single variable, `sst`, with dims `(time, lat, lon)` in float32, and a preview of values — it starts at −1.8 (polar water at the freezing point) and ends in `nan` (land). Last come the **Attributes** — free-text metadata; there are 39, and the printout shows the first six and last six with `...` in between. (One of them, `data_modified`, will show whatever date NOAA last updated the file — yours may be later than the one printed here.)

Let's do some basic visualizations of the data, just to make sure it looks reasonable.

The following is Python code:

```python
ds.sst[0].plot(vmin=-2, vmax=30)
plt.show()
```

End of code.

**What this shows.** A global map, titled `time = 1960-01-01`, on axes labelled Longitude [degrees_east] (0 to 358) and Latitude [degrees_north] (−88 to 88). Color encodes SST from −2 °C (dark purple) to 30 °C (bright yellow), with the colorbar labelled "Monthly Means of Sea Surface Temperature [degC]". A broad yellow band of 28–30 °C water straddles the equator — widest in the western Pacific warm pool — cooling through green (15–20 °C) in the mid-latitudes to dark purple near both poles, where the ocean sits at its freezing point of about −1.8 °C. The continents are blank white: SST is only defined over the ocean, and land is `nan`.

The same plot can be made with `isel` — `ds.sst[0]` and `ds.sst.isel(time=0)` are two spellings of the same selection:

The following is Python code:

```python
ds.sst.isel(time=0).plot(vmin=-2, vmax=30)
plt.show()
```

End of code.

**What this shows.** The identical map.

**Check it.** A map you can't hear — but you can interrogate the exact same field with point values and statistics (warm at the equator, freezing at the pole, and the overall range):

The following is Python code:

```python
print(round(float(ds.sst[0].sel(lat=0, lon=200)), 1))
print(round(float(ds.sst[0].sel(lat=88, lon=200)), 1))
print(round(float(ds.sst[0].max()), 1), round(float(ds.sst[0].min()), 1))
```

End of code.

The following is the expected output:

```
26.4
-1.8
30.9 -1.8
```

End of output.

Note that xarray correctly parsed the time index, resulting in a Pandas datetime index on the time dimension:

The following is Python code:

```python
print(ds.time.dtype)
print(ds.time.values[0], ds.time.values[-1])
```

End of code.

The following is the expected output:

```
datetime64[ns]
1960-01-01T00:00:00.000000000 2018-12-01T00:00:00.000000000
```

End of output.

The following is Python code:

```python
ds.sst.sel(lon=300, lat=50).plot()
plt.show()
```

End of code.

**What this shows.** The full 708-month temperature history of one grid point in the northwest Atlantic (50°N, 300°E — south of Newfoundland; xarray auto-titles the plot with the selection, `lat = 50.0 [degrees_north], lon = 300.0 [degree…`). The line is a dense, regular sawtooth: every single year it swings from about −1.8 °C in winter up to summer peaks of roughly 12–17 °C and back. The annual cycle so completely dominates the plot that you cannot see any long-term change in it.

As we can see from the plot, the timeseries at any one point is totally dominated by the seasonal cycle. We would like to remove this seasonal cycle (called the "climatology") in order to better see the long-term variations in temperature. We will accomplish this using **groupby**.

The syntax of Xarray's groupby is almost identical to Pandas. We will first apply groupby to a single DataArray. (In a notebook you would type `ds.sst.groupby?` to read its documentation; in a script, `help(ds.sst.groupby)` prints the same text to the terminal.)

### Split Step

The most important argument is `group`: this defines the unique values we will use to "split" the data for grouped analysis. We can pass either a DataArray or a name of a variable in the dataset. Lets first use a DataArray. Just like with Pandas, we can use the time index to extract specific components of dates and times. Xarray uses a special syntax for this `.dt`, called the `DatetimeAccessor`.

The full `ds.time.dt.month` array has 708 entries — instead of printing it all, listen to the first fourteen of each, enough to hear the pattern:

The following is Python code:

```python
print(ds.time.dt.month.values[:14])
print(ds.time.dt.year.values[:14])
```

End of code.

The following is the expected output:

```
[ 1  2  3  4  5  6  7  8  9 10 11 12  1  2]
[1960 1960 1960 1960 1960 1960 1960 1960 1960 1960 1960 1960 1961 1961]
```

End of output.

`month` counts 1 through 12 and then wraps back to 1; `year` repeats 1960 twelve times and then moves on to 1961. Every one of the 708 time steps gets a month label — that label is what we will group on.

We can use these arrays in a groupby operation:

The following is Python code:

```python
gb = ds.sst.groupby(ds.time.dt.month)
print(gb)
```

End of code.

The following is the expected output:

```
<DataArrayGroupBy, grouped over 1 grouper(s), 12 groups in total:
    'month': UniqueGrouper('month'), 12/12 groups with labels 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12>
```

End of output.

Xarray also offers a more concise syntax when the variable you're grouping on is already present in the dataset. This is identical to the previous line:

The following is Python code:

```python
gb = ds.sst.groupby('time.month')
print(gb)
```

End of code.

The following is the expected output:

```
<DataArrayGroupBy, grouped over 1 grouper(s), 12 groups in total:
    'month': UniqueGrouper('month'), 12/12 groups with labels 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12>
```

End of output.

Now that the data are split, we can manually iterate over the group. The iterator returns the key (group name) and the value (the actual dataset corresponding to that group) for each group.

The following is Python code:

```python
for group_name, group_da in gb:
    # stop iterating after the first loop
    break
print(group_name)
print(group_da.sizes)
```

End of code.

The following is the expected output:

```
1
Frozen({'time': 59, 'lat': 89, 'lon': 180})
```

End of output.

The first group's name is `1` — January — and its data is every January in the record: 59 time steps (one per year, 1960–2018), on the full 89 × 180 spatial grid.

The following is Python code:

```python
group_da.mean('time').plot()
plt.show()
```

End of code.

**What this shows.** A world map of the average of all 59 Januaries. Because we didn't fix `vmin`/`vmax` this time, xarray noticed the data spans both signs and switched to a red–blue *diverging* colormap centered on zero, with the colorbar running −30 to +30 °C. The ocean is therefore painted almost entirely in reds: deep red through the tropics (mid-to-upper 20s — about 26.8 °C at the equator in the mid-Pacific), lighter red in mid-latitudes (about 8.7 °C at 60°N in the far-north Atlantic), fading to near-white and faintly blue at the poles, just below 0 °C.

### Map & Combine

Now that we have groups defined, it's time to "apply" a calculation to the group. Like in Pandas, these calculations can either be:
- _aggregation_: reduces the size of the group
- _transformation_: preserves the group's full size

At the end of the apply step, xarray will automatically combine the aggregated / transformed groups back into a single object.

:::{warning}
Xarray calls the "apply" step `map`. This is different from Pandas!
:::

The most fundamental way to apply is with the `.map` method (again, `help(gb.map)` prints its documentation).

#### Aggregations

`.map` accepts as its argument a function. We can pass an existing function:

The following is Python code:

```python
monthly_global = gb.map(np.mean)
print(monthly_global.sizes)
print(monthly_global.values)
```

End of code.

The following is the expected output:

```
Frozen({'month': 12})
[13.659839 13.768812 13.765059 13.68421  13.642344 13.712949 13.921948
 14.094091 13.982345 13.691327 13.506662 13.529607]
```

End of output.

Twelve numbers, one per month — the global-mean SST of all Januaries, all Februaries, and so on, each around 13.5–14.1 °C. Because we specified no extra arguments (like `axis`) the function was applied over all space and time dimensions. This is not what we wanted. Instead, we could define a custom function. This function takes a single argument--the group dataset--and returns a new dataset to be combined:

The following is Python code:

```python
def time_mean(a):
    return a.mean(dim='time')

print(gb.map(time_mean).sizes)
```

End of code.

The following is the expected output:

```
Frozen({'month': 12, 'lat': 89, 'lon': 180})
```

End of output.

Now the 708-month time axis has collapsed to a 12-month `month` axis, but the spatial grid survives: a mean map for each calendar month. Let's plot January's:

The following is Python code:

```python
gb.map(time_mean).sel(month=1).plot()
plt.show()
```

End of code.

**What this shows.** Exactly the same red-toned January-mean map as before (deep red tropics, pale poles, diverging colorbar −30 to +30), now titled `month = 1` — confirming that `.map(time_mean)` computed, for each month group, the same mean we took by hand from the first group.

Like Pandas, xarray's groupby object has many built-in aggregation operations (e.g. `mean`, `min`, `max`, `std`, etc):

The following is Python code:

```python
# this does the same thing as the previous code block
sst_mm = gb.mean('time')
print(sst_mm.sizes)
```

End of code.

The following is the expected output:

```
Frozen({'month': 12, 'lat': 89, 'lon': 180})
```

End of output.

**Check it.** The shortcut and the explicit `.map` really are the same object, value for value:

The following is Python code:

```python
print(sst_mm.equals(gb.map(time_mean)))
```

End of code.

The following is the expected output:

```
True
```

End of output.

So we did what we wanted to do: calculate the climatology at every point in the dataset. Let's look at the data a bit.

_Climatology at a specific point in the North Atlantic_

The following is Python code:

```python
sst_mm.sel(lon=300, lat=50).plot()
plt.show()
```

End of code.

**What this shows.** A single smooth curve over months 1–12: the average year at our northwest-Atlantic point. It starts at −0.7 °C in January, bottoms out at −1.5 °C in February–March, climbs steeply through spring and summer to a sharp peak of 14.0 °C in August (month 8), then falls back through 4.0 °C in November to 1.3 °C in December. Note the warmest water comes in August, not at the June solstice — the ocean's seasons lag the sun by about two months.

**Check it.** Print the twelve climatology values behind the curve:

The following is Python code:

```python
print(sst_mm.sel(lon=300, lat=50).values.round(1))
```

End of code.

The following is the expected output:

```
[-0.7 -1.5 -1.5 -0.3  2.2  6.6 11.6 14.  11.7  7.6  4.   1.3]
```

End of output.

_Zonal Mean Climatology_

The following is Python code:

```python
sst_mm.mean(dim='lon').transpose().plot.contourf(levels=12, vmin=-2, vmax=30)
plt.show()
```

End of code.

**What this shows.** A filled-contour plot with month (1–12) across the bottom and latitude (−88 to 88) up the side — every longitude averaged away, so this is "temperature by latitude, through the year". A bright yellow band of 27–30 °C water (zonal mean ≈ 27.4 °C at the equator) fills the tropics between roughly 20°S and 20°N all year, drifting slightly northward in August–September; nested green and teal bands step down through the mid-latitudes; dark purple (below about 1 °C) caps the far south — everything poleward of about 62°S — year-round, while in the north the dark cap only covers the winter half of the year and retreats poleward in late summer (at 60°N the zonal mean climbs to about 10 °C in August), and a white strip south of about 75°S marks where there is no ocean at all (Antarctica). The northern-hemisphere bands bulge warm in August–September and the southern-hemisphere bands in February–March — the two hemispheres' seasons in opposite phase.

_Difference between January and July Climatology_

The following is Python code:

```python
(sst_mm.sel(month=1) - sst_mm.sel(month=7)).plot(vmax=10)
plt.show()
```

End of code.

**What this shows.** A world map on a red–blue scale from −10 to +10 °C with arrow tips on the colorbar (values beyond the limits get the end colors). The entire northern hemisphere is blue — January is colder than July — palest near the equator and deepening past −6 °C in the mid-latitude northwest Pacific (about −6.2 °C at 40°N, 160°E) to off-scale extremes in enclosed marginal seas — nearly −16 °C in the Baltic at 60°N, and the single biggest swing, about −23 °C, in the land-locked Caspian Sea near 46°N. The southern hemisphere is its red mirror image (January is austral summer), reaching about +5 °C at 40°S in the South Atlantic and up to around +10 °C in spots near 40°S. A narrow near-white band hugs the equator: the tropical ocean barely notices the seasons, while mid-latitude oceans swing hugely.

#### Transformations

Now we want to _remove_ this climatology from the dataset, to examine the residual, called the _anomaly_, which is the interesting part from a climate perspective.
Removing the seasonal climatology is a perfect example of a transformation: it operates over a group, but doesn't change the size of the dataset. Here is one way to code it.

The following is Python code:

```python
def remove_time_mean(x):
    return x - x.mean(dim='time')

ds_anom = ds.groupby('time.month').map(remove_time_mean)
print(ds_anom.sizes)
```

End of code.

The following is the expected output:

```
Frozen({'time': 708, 'lat': 89, 'lon': 180})
```

End of output.

The dataset kept its full 708-month length — each January had the mean January subtracted from it, each February the mean February, and so on.

:::{note}
In the above example, we applied `groupby` to a `Dataset` instead of a `DataArray`.
:::

The following is Python code:

```python
ds_anom.sst.sel(lon=300, lat=50).plot()
plt.show()
```

End of code.

**What this shows.** The same point as before, transformed. The relentless seasonal sawtooth is gone; what remains is an irregular, noisy line wandering around zero between about −2.2 and +2.9 °C. The series sits mostly below zero through the 1960s–1980s, then drifts upward from the late 1990s, with its tallest spikes — near +2.9 °C — in the summers of 2012 and 2014. This is the climate signal the seasonal cycle was hiding.

**Check it.** Compare the first and last decades of the anomaly series:

The following is Python code:

```python
anom_pt = ds_anom.sst.sel(lon=300, lat=50)
print(round(float(anom_pt.sel(time=slice('1960', '1969')).mean()), 2))
print(round(float(anom_pt.sel(time=slice('2009', '2018')).mean()), 2))
```

End of code.

The following is the expected output (this point averaged 1.7 °C warmer in 2009–2018 than in the 1960s):

```
-0.64
1.09
```

End of output.

Xarray makes these sorts of transformations easy by supporting _groupby arithmetic_.
This concept is easiest explained with an example:

The following is Python code:

```python
gb = ds.groupby('time.month')
ds_anom_arith = gb - gb.mean(dim='time')
print(ds_anom_arith.sizes)
```

End of code.

The following is the expected output:

```
Frozen({'lat': 89, 'lon': 180, 'time': 708})
```

End of output.

**Check it.** Same size — and the same numbers as the `.map` version, everywhere (groupby arithmetic also attaches a new `month` coordinate along the time axis, which is why we compare values rather than whole objects). We keep the result as our `ds_anom`:

The following is Python code:

```python
print(np.allclose(ds_anom_arith.sst.values, ds_anom.sst.values, equal_nan=True))
ds_anom = ds_anom_arith
```

End of code.

The following is the expected output:

```
True
```

End of output.

Now we can view the climate signal without the overwhelming influence of the seasonal cycle.

_Timeseries at a single point in the North Atlantic_

The following is Python code:

```python
ds_anom.sst.sel(lon=300, lat=50).plot()
plt.show()
```

End of code.

**What this shows.** The identical anomaly timeseries as before — noisy line between about −2.2 and +2.9 °C, drifting upward after 2000 — as it must be, since the two methods agree.

_Difference between Jan. 1 2018 and Jan. 1 1960_

The following is Python code:

```python
(ds_anom.sel(time='2018-01-01') - ds_anom.sel(time='1960-01-01')).sst.plot()
plt.show()
```

End of code.

**What this shows.** A patchwork world map on a red–blue scale (values span about −2.1 to +5.1 °C; the map mean is +0.5 °C). Most of the ocean is light red, with the strongest reds along a band near 40°S and across the Arctic edge near 80°N, but pale-blue patches — places *cooler* in January 2018 than January 1960 — are scattered through the central South Pacific and Southern Ocean. Comparing two individual months like this is noisy: single months carry a lot of internal variability, a caution against reading climate change off two snapshots.

:::{admonition} Try it
:class: tip
Using the SST `ds`, build the monthly **climatology** with `ds.sst.groupby('time.month').mean('time')` and plot it at a point of your choice (also print its 12 values with `.values.round(1)`). Then compute the **anomaly** with groupby arithmetic (`gb - gb.mean('time')`) and plot its timeseries at the same point — the seasonal cycle should be gone. To confirm that by ear rather than by eye: print the standard deviation of the raw series and of the anomaly series (`round(float(series.std()), 2)`) — removing the seasonal cycle should shrink it substantially.
:::

## Groupby-Related: Resample, Rolling, Coarsen

### Resample

Resample in xarray is nearly identical to Pandas.
**It can be applied only to time-index dimensions.** Here we compute the five-year mean.
It is effectively a group-by operation, and uses the same basic syntax.
Note that resampling changes the length of the output arrays: `ds_anom` has 708 monthly steps going in.

The following is Python code:

```python
ds_anom_resample = ds_anom.resample(time='5YE').mean(dim='time')
print(ds_anom_resample.sizes)
print(ds_anom_resample.time.values.astype('datetime64[D]'))
```

End of code.

The following is the expected output:

```
Frozen({'time': 13, 'lat': 89, 'lon': 180})
['1960-12-31' '1965-12-31' '1970-12-31' '1975-12-31' '1980-12-31'
 '1985-12-31' '1990-12-31' '1995-12-31' '2000-12-31' '2005-12-31'
 '2010-12-31' '2015-12-31' '2020-12-31']
```

End of output.

708 months collapsed to 13 five-year averages (`'5YE'` means bins that **e**nd every 5th **y**ear-end). Each label is the end of its bin: the 1965-12-31 entry averages 1961–1965, and the first and last bins are partial (1960 alone; 2016–2018).

The following is Python code:

```python
ds_anom.sst.sel(lon=300, lat=50).plot()
ds_anom_resample.sst.sel(lon=300, lat=50).plot(marker='o')
plt.show()
```

End of code.

**What this shows.** The noisy blue monthly anomaly with a smooth orange line on top, its 13 round markers spaced five years apart. The orange line starts around −0.4 °C, dips to its lowest point, −0.9 °C, in the mid-1960s, wanders between −0.7 and 0 through the 1970s–1990s, then climbs steadily to a peak of +1.26 °C at the 2015 marker, ending at +1.03 °C. The five-year averaging strips away the month-to-month noise and makes the warming after 2000 unmistakable.

**Check it.** The thirteen five-year means at this point:

The following is Python code:

```python
print(ds_anom_resample.sst.sel(lon=300, lat=50).values.round(2))
```

End of code.

The following is the expected output:

```
[-0.43 -0.9  -0.3  -0.73 -0.29 -0.04 -0.26 -0.39  0.22  0.17  0.71  1.26
  1.03]
```

End of output.

The following is Python code:

```python
(ds_anom_resample.sel(time='2015-01-01', method='nearest') -
 ds_anom_resample.sel(time='1965-01-01', method='nearest')).sst.plot()
plt.show()
```

End of code.

**What this shows.** The five-year-averaged 2011–2015 block minus the 1961–1965 block. Unlike the noisy two-January map earlier, now almost the entire ocean is some shade of red — warming nearly everywhere, map mean about +0.5 °C — with the deepest red blob (about +2.8 °C) in the northwest Atlantic near 45°N (the field's single largest value, +2.9 °C, is a small patch near the Bering Strait), and only small pale-blue patches (about −1 °C at most) in parts of the Southern Ocean. Averaging in time turns the patchwork into a coherent warming pattern.

### Rolling

Rolling is also similar to pandas.
It does not change the length of the arrays.
Instead, it allows a moving window to be applied to the data at each point.

The following is Python code:

```python
ds_anom_rolling = ds_anom.rolling(time=60, center=True).mean()
print(ds_anom_rolling.sizes)
```

End of code.

The following is the expected output:

```
Frozen({'lat': 89, 'lon': 180, 'time': 708})
```

End of output.

**Check it.** Still 708 time steps — but a centered 60-month window needs 30 months of data on each side, so the points too close to the ends come out as `nan`:

The following is Python code:

```python
print(int(ds_anom_rolling.sst.sel(lon=300, lat=50).isnull().sum()))
```

End of code.

The following is the expected output (30 missing at the start plus 29 at the end):

```
59
```

End of output.

The following is Python code:

```python
ds_anom.sst.sel(lon=300, lat=50).plot(label='monthly anom')
ds_anom_resample.sst.sel(lon=300, lat=50).plot(marker='o', label='5 year resample')
ds_anom_rolling.sst.sel(lon=300, lat=50).plot(label='12 month rolling mean', color='k')
plt.legend()
plt.show()
```

End of code.

**What this shows.** All three views of the same point on one plot, with a legend in the top-left: the noisy blue monthly series, the orange five-year resample with its 13 markers, and a smooth black curve that hugs the orange line while wiggling a bit more, staying between about −0.9 and +1.3 °C. The black curve starts in mid-1962 and stops in mid-2016 — those are the `nan` edges we just counted. (One thing to notice: the legend text says "12 month rolling mean", but the window in the code is `time=60` — a 60-month, i.e. 5-year, rolling mean, which is why it is as smooth as the 5-year resample. Trust the code, not the label!) The key contrast: `rolling` keeps the full monthly time axis and slides its window along it, while `resample` collapses each block to a single point.

:::{admonition} Try it
:class: tip
Take the SST anomaly at a single point. `resample(time='1YE').mean()` it to annual means and plot, then overlay a `rolling(time=12, center=True).mean()` of the monthly series on the same axes. How do the two smoothings differ? (Print `.sizes` of each result: resampling shortens the time axis to 59, rolling keeps 708.)
:::

## Coarsen

`coarsen` is a simple way to reduce the size of your data along one or more axes.
It is very similar to `resample` when operating on time dimensions; the key difference is that `coarsen` only operates on fixed blocks of data, irrespective of the coordinate values, while `resample` actually looks at the coordinates to figure out, e.g. what month a particular data point is in.

For regularly-spaced monthly data beginning in January, the following should be equivalent to annual resampling.
However, results would different for irregularly-spaced data.

The following is Python code:

```python
ds_coarse_time = ds.coarsen(time=12).mean()
print(ds_coarse_time.sizes)
print(ds_coarse_time.time.values[0], ds_coarse_time.time.values[-1])
```

End of code.

The following is the expected output:

```
Frozen({'time': 59, 'lat': 89, 'lon': 180})
1960-06-16T08:00:00.000000000 2018-06-16T12:00:00.000000000
```

End of output.

708 months in fixed blocks of 12 gives 59 annual means, and each block's time label is the average of its twelve timestamps — mid-June of each year.

Coarsen also works on spatial coordinates (or any coordinates).

The following is Python code:

```python
ds_coarse = ds.coarsen(lon=4, lat=4, boundary='pad').mean()
print(ds_coarse.sizes)
ds_coarse.sst.isel(time=0).plot(vmin=2, vmax=30, figsize=(12, 5), edgecolor='k')
plt.show()
```

End of code.

The following is the expected output:

```
Frozen({'time': 708, 'lat': 23, 'lon': 45})
```

End of output.

**What this shows.** The January-1960 world map rebuilt from big tiles. Coarsening by 4 in both directions turns the 2°-resolution grid into 8° blocks — 45 across by 23 down — and `edgecolor='k'` outlines every block in black, so the map looks like a mosaic of large squares: yellow tiles along the equator, teal and green through the mid-latitudes, dark purple rows at the top and bottom, with white gaps where land is. Same pattern as the full-resolution map, at a fraction of the size — like turning down an image's resolution. (`boundary='pad'` handles the fact that 89 latitudes don't divide evenly by 4.)

:::{admonition} Try it
:class: tip
Use `ds.coarsen(lon=4, lat=4, boundary='pad').mean()` to coarsen the SST map and plot one time step. Then use `ds.coarsen(time=12).mean()` to make annual-block means, and compare with the `resample` result above (print the first few `time` values of each — coarsen labels its blocks mid-year, resample labels them at year-end).
:::

## An Advanced Example

In this example we will show a realistic workflow with Xarray.
We will
- Load a "basin mask" dataset
- Interpolate the basins to our SST dataset coordinates
- Group the SST by basin
- Convert to Pandas Dataframe and plot mean SST by basin

The mask comes from the World Ocean Atlas, served over OPeNDAP by the IRI Data Library (another short network pause here):

The following is Python code:

```python
basin = xr.open_dataset('http://iridl.ldeo.columbia.edu/SOURCES/.NOAA/.NODC/.WOA09/.Masks/.basin/dods')
print(basin.sizes)
print(list(basin.data_vars))
```

End of code.

The following is the expected output:

```
Frozen({'Z': 33, 'Y': 180, 'X': 360})
['basin']
```

End of output.

One data variable, `basin`, on a 1-degree grid (180 × 360) with 33 depth levels `Z` from 0 down to 5500 m. Each grid cell holds a numeric *basin code* — which ocean basin that cell belongs to. The dimensions are named `X` and `Y`, so we rename them to match our SST dataset:

The following is Python code:

```python
basin = basin.rename({'X': 'lon', 'Y': 'lat'})
print(basin.sizes)
```

End of code.

The following is the expected output:

```
Frozen({'Z': 33, 'lat': 180, 'lon': 360})
```

End of output.

We only need the surface level:

The following is Python code:

```python
basin_surf = basin.basin[0]
print(basin_surf.sizes)
print(basin_surf.attrs['long_name'], '|', basin_surf.attrs['units'])
```

End of code.

The following is the expected output:

```
Frozen({'lat': 180, 'lon': 360})
basin code | ids
```

End of output.

The following is Python code:

```python
basin_surf.plot()
plt.show()
```

End of code.

**What this shows.** A world map titled `Z = 0.0 [m]` whose colorbar ("basin code [ids]") runs 1 to about 56 — but the colors are *categories*, not amounts. Because the three big basins have the lowest codes (Atlantic = 1, Pacific = 2, Indian = 3), they all sit at the dark-purple end and are nearly indistinguishable. Two lighter slate-blue bands stand out: the Southern Ocean (code 10) ringing the globe south of about 50°S, and the Arctic Ocean (code 11) along the top. The only bright yellow features are two small high-code seas: the Bay of Bengal (code 56, east of India) and the Caspian Sea (code 53). Land is white. The 1-degree grid makes the coastlines noticeably crisper than our 2-degree SST maps.

**Check it.** How many distinct basin codes actually appear at the surface, and which?

The following is Python code:

```python
codes = np.unique(basin_surf.values[~np.isnan(basin_surf.values)])
print(len(codes))
print(codes)
```

End of code.

The following is the expected output:

```
14
[ 1.  2.  3.  4.  5.  6.  7.  8.  9. 10. 11. 12. 53. 56.]
```

End of output.

The mask is on a different (finer) grid than our SST data, so we interpolate it onto the SST grid — with `method='nearest'`, because basin codes are categories: averaging code 1 (Atlantic) with code 3 (Indian) to get 2 (Pacific!) would be nonsense. `interp_like` is a convenient variant of `interp` that targets another object's coordinates:

The following is Python code:

```python
basin_surf_interp = basin_surf.interp_like(ds.sst, method='nearest')
print(basin_surf_interp.sizes)
basin_surf_interp.plot(vmax=10)
plt.show()
```

End of code.

The following is the expected output:

```
Frozen({'lat': 89, 'lon': 180})
```

End of output.

**What this shows.** The same basin map, now on the coarser 89 × 180 SST grid, replotted with `vmax=10` so the big basins finally separate: the Atlantic (1) is darkest purple, the Pacific (2) dark blue-purple, the Indian Ocean (3) a clearly lighter blue. The Southern Ocean and Arctic bands now saturate to yellow at the top of the compressed scale (codes 10 and 11), as do the small high-code patches like the Bay of Bengal and Caspian (56 and 53, far off-scale — the colorbar has an arrow tip for values above 10).

Now we can group the SST by basin code. Any DataArray sharing dimensions with the data can serve as the grouper — here's `.first()` (the first value in each group), mainly to see the shape of what comes back:

The following is Python code:

```python
sst_by_basin_first = ds.sst.groupby(basin_surf_interp).first()
print(sst_by_basin_first.sizes)
print(sst_by_basin_first.basin.values)
```

End of code.

The following is the expected output:

```
Frozen({'time': 708, 'basin': 14})
[ 1.  2.  3.  4.  5.  6.  7.  8.  9. 10. 11. 12. 53. 56.]
```

End of output.

The two spatial dimensions have been replaced by a new `basin` dimension of length 14 — the 14 codes we found at the surface — while time survives. Now the aggregation we actually want, the mean:

The following is Python code:

```python
basin_mean_sst = ds.sst.groupby(basin_surf_interp).mean()
print(basin_mean_sst.sizes)
```

End of code.

The following is the expected output:

```
Frozen({'time': 708, 'basin': 14})
```

End of output.

Averaging over time as well gives one number per basin, which we convert to a Pandas DataFrame. (As always in this course, we read a DataFrame by ear: shape, column names, then each row as labelled values — not the printed grid.)

The following is Python code:

```python
df = basin_mean_sst.mean('time').to_dataframe()
print(df.shape)
print(df.columns.tolist())
for basin_id, row in df.iterrows():
    print(basin_id, round(float(row['sst']), 2))
```

End of code.

The following is the expected output:

```
(14, 1)
['sst']
1.0 19.29
2.0 21.18
3.0 21.13
4.0 19.85
5.0 8.13
6.0 15.08
7.0 28.49
8.0 26.62
9.0 0.31
10.0 1.55
11.0 -0.82
12.0 12.09
53.0 14.34
56.0 28.47
```

End of output.

Fourteen rows — the basin code as the index, and one column `sst` with each basin's long-term mean temperature. But which basin is which? The mask's `CLIST` attribute is a legend: one basin name per line, in code order. We split it into a list and build a Pandas Series indexed by code:

The following is Python code:

```python
import pandas as pd
basin_names = basin_surf.attrs['CLIST'].split('\n')
basin_df = pd.Series(basin_names, index=np.arange(1, len(basin_names)+1))
print(len(basin_df))
for i in [1, 2, 3]:
    print(i, basin_df.loc[i])
```

End of code.

The following is the expected output (58 names in all; here are the first three):

```
58
1 Atlantic Ocean
2 Pacific Ocean 
3 Indian Ocean
```

End of output.

Joining the names onto our table turns the codes into words:

The following is Python code:

```python
df = df.join(basin_df.rename('basin_name'))
print(df.columns.tolist())
for basin_id, row in df.iterrows():
    print(basin_id, row['basin_name'], round(float(row['sst']), 2))
```

End of code.

The following is the expected output:

```
['sst', 'basin_name']
1.0 Atlantic Ocean 19.29
2.0 Pacific Ocean  21.18
3.0 Indian Ocean 21.13
4.0 Mediterranean Sea 19.85
5.0 Baltic Sea 8.13
6.0 Black Sea 15.08
7.0 Red Sea 28.49
8.0 Persian Gulf 26.62
9.0 Hudson Bay 0.31
10.0 Southern Ocean 1.55
11.0 Arctic Ocean -0.82
12.0 Sea of Japan 12.09
53.0 Caspian Sea 14.34
56.0 Bay of Bengal 28.47
```

End of output.

A physically sensible ranking: the enclosed tropical seas — Red Sea (28.5 °C) and Bay of Bengal (28.5 °C), with the Persian Gulf close behind (26.6) — are the warmest; the three great basins cluster near 19–21 °C; and the polar waters bring up the rear, with Hudson Bay at 0.3 °C, the Southern Ocean at 1.5 °C, and the Arctic Ocean below freezing at −0.8 °C. Finally, the same table as a bar chart:

The following is Python code:

```python
ax = df.plot.bar(y='sst', x='basin_name')
plt.tight_layout()
plt.show()
```

End of code.

**What this shows.** Fourteen blue vertical bars, one per basin, with the basin names printed at an angle beneath them and a small legend box reading "sst". The skyline is jagged: near-identical tall bars for Red Sea and Bay of Bengal (28.5) with the Persian Gulf just below, a middle range of bars around 12–21 for the major oceans and mid-latitude seas, a stubby Baltic bar at 8, near-invisible bars for Hudson Bay and the Southern Ocean, and one bar — the Arctic Ocean — dipping *below* the zero line.

**Check it.** Read the bar heights back off the axes, in plotting order:

The following is Python code:

```python
print([round(float(b.get_height()), 1) for b in ax.containers[0]])
```

End of code.

The following is the expected output:

```
[19.3, 21.2, 21.1, 19.8, 8.1, 15.1, 28.5, 26.6, 0.3, 1.5, -0.8, 12.1, 14.3, 28.5]
```

End of output.

## Recap

This lecture applied xarray to a real SST dataset and covered its more advanced tools:

- **Interpolation** — `.interp()` to estimate values at new coordinates (`linear`/`nearest`/`cubic`), in one or more dimensions.
- **Groupby** — split-apply-combine on grids: building a monthly **climatology** and removing it to get **anomalies**, with both `.map(...)` and groupby arithmetic.
- **Resample, rolling, coarsen** — change time resolution by calendar (`resample`), smooth with a moving window (`rolling`), or average fixed blocks (`coarsen`).
- **A full workflow** — masking SST by ocean basin (interpolating a basin mask onto the grid), grouping by basin, and summarizing into a table and bar chart.
- **And the non-visual habit:** every map on this page was double-checked by printing point values, extremes, and group sizes — the same facts the pictures show, in numbers you can hear.

With pandas and xarray you now have the core toolkit for tabular and gridded data. The [SST lab (Assignment 6)](./assignment6.md) is your at-home assignment.
