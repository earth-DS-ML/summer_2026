# Assignment 6: Xarray with Sea Surface Temperature (SST) data

**At-home assignment — worth 10 points.** When you're done, push your work to your week folder and post a link to your completed scripts on the matching **Courseworks** assignment.

In this lab you'll apply xarray to NOAA's Extended Reconstructed SST (ERSST v5) dataset, accessed over OPeNDAP:
> https://psl.noaa.gov/thredds/dodsC/Datasets/noaa.ersst.v5/sst.mnmean.nc

:::{admonition} Following along with a screen reader
:class: important
Work through the tasks as `.py` scripts in VS Code — see [Setting up an accessible workflow](../computing_env/accessible_setup.md) and [Working with Python scripts](../computing_env/working_with_scripts.md). Run a script in the Git Bash terminal with `python yourfile.py`, and press `Ctrl+Up` to read the output. You'll need `xarray`, `netcdf4`, `dask`, and `matplotlib` installed (`pip install xarray netcdf4 dask matplotlib` if you don't have them), and you must be online — the data is read straight from NOAA's server, and some steps take a few minutes to pull the data down.
:::

:::{admonition} Learning goals
:class: tip
This assignment exercises the xarray skills from this section:

- Open a gridded dataset over OPeNDAP and inspect its metadata (`attrs`, `long_name`)
- Compute time-means and spatial means; plot maps and timeseries
- Build a latitude weighting (cosine of latitude, $\cos\lambda$) and take an area-weighted spatial mean
- Select point locations with `.sel` and combine them along a new `location` dimension with `concat`
- Use `groupby` to build a climatology and anomalies, and `rolling` for a running mean
- Compute the Niño 3.4 ENSO index from SST anomalies
:::

:::{admonition} Working through this assignment
:class: tip
One script per numbered part works well (`assignment6_part1.py`, `assignment6_part2.py`, …), or one big script if you prefer — the parts build on the same opened dataset. Four notes:

- **In a script, a bare expression displays nothing** — wrap anything you want to hear in `print(...)`.
- **Each task has a *Check yourself* note** giving numbers your solution should produce, so you can confirm you're on track without seeing any output or plot. The check numbers on this page were computed against the data as it stood in mid-2026, when the record ran January 1854 through May 2026 — 2069 monthly time steps. **ERSST grows by one month every month**, so your time counts will be a little larger and any number computed over the whole record (means, the ENSO index) can drift by a few hundredths. Treat every check number as "about".
- **The whole-record reductions are slow over OPeNDAP.** Each mean over the full record (parts 2 and 4) pulls all ~133 MB across the network and can take a few minutes. A good pattern: right after opening the dataset *with chunks* (see the warning in task 1.1), load it into memory once with `ds = ds.load()` — that one line takes the few minutes, and everything after it runs instantly. (Loading is only safe *because* you opened with chunks; see the warning.)
- For the plotting tasks, end with `plt.savefig("assignment6_task2_2.png")` (matching the task number) so a sighted grader can open the PNGs, and add two or three sentences — in a comment at the bottom of the script — saying what the plot shows, tied to the numbers you printed. You are graded on the code, the printed checks, and the understanding, not on visual polish.

When you're done, follow the [submission instructions](#submission-instructions-6) at the bottom of the page.
:::

Start by importing NumPy, Matplotlib, and Xarray. Set the default figure size to (12, 6) — in a script, that's a `rcParams` setting:

The following is Python code:

```python
import numpy as np
from matplotlib import pyplot as plt
import xarray as xr

plt.rcParams["figure.figsize"] = (12, 6)
```

End of code.

## 1) Opening data and examining the metadata

### 1.1) Open the dataset and display its contents

:::{admonition} Heads up — open this dataset with `chunks`
:class: warning
This OPeNDAP server quietly returns **all zeros** (you'll get blank maps and flat
timeseries) if you request the whole array in a single shot — which several tasks below
do (the time-mean in 2.1, the spatial mean in 2.3, the ENSO index in part 4). Avoid it by
opening the data in pieces with Dask, so xarray fetches it chunk-by-chunk.

The following is Python code:

~~~python
ds = xr.open_dataset(url, chunks={'time': 120})
~~~

End of code.

Everything else works exactly as usual — reductions like `.mean()` pull the data in as
needed. (You can combine this with other keywords, e.g. `drop_variables=['time_bnds']`.)
:::

In a script, "display its contents" means `print(ds)` — an xarray `Dataset` prints as plain linear text, which a screen reader handles well (it's long, but it reads top to bottom):

The following is Python code:

```python
url = "https://psl.noaa.gov/thredds/dodsC/Datasets/noaa.ersst.v5/sst.mnmean.nc"
ds = xr.open_dataset(url, chunks={'time': 120})
print(ds)
```

End of code.

This prints the full `Dataset` summary; the attributes listing at the end is abridged here (the real one shows 12 of 39 attributes). The following is the expected output:

```
<xarray.Dataset> Size: 133MB
Dimensions:    (time: 2069, nbnds: 2, lat: 89, lon: 180)
Coordinates:
  * time       (time) datetime64[ns] 17kB 1854-01-01 1854-02-01 ... 2026-05-01
  * lat        (lat) float32 356B 88.0 86.0 84.0 82.0 ... -84.0 -86.0 -88.0
  * lon        (lon) float32 720B 0.0 2.0 4.0 6.0 ... 352.0 354.0 356.0 358.0
Dimensions without coordinates: nbnds
Data variables:
    time_bnds  (time, nbnds) float64 33kB dask.array<chunksize=(120, 2), meta=np.ndarray>
    sst        (time, lat, lon) float32 133MB dask.array<chunksize=(120, 89, 180), meta=np.ndarray>
Attributes: (12/39)
    climatology:                     Climatology is based on 1971-2000 SST, X...
    description:                     In situ data: ICOADS2.5 before 2007 and ...
    ...
    dataset_title:                   NOAA Extended Reconstructed SST V5
```

End of output.

**How to listen to a `Dataset` printout.** Read it with `Ctrl+Up`, top to bottom — it always has the same four sections. First the **dimensions line**: this dataset has `time: 2069` (monthly steps — yours will be more), `nbnds: 2`, `lat: 89`, and `lon: 180`. Then **coordinates**, one line each — `time` runs from 1854-01-01 to the most recent month; `lat` starts at 88.0 and *descends* (north to south) in steps of 2 degrees to −88.0; `lon` runs 0.0 to 358.0 in steps of 2 degrees (an eastward 0–360 convention — remember this for part 4). Then **data variables**: `sst`, the sea surface temperature on `(time, lat, lon)`, and a small helper variable `time_bnds`. The `dask.array<chunksize=(120, 89, 180)...>` note on each variable confirms the data opened in chunks — no values have been downloaded yet. Last come the **attributes** — free-text metadata about the dataset.

*Check yourself:* a compact, stable summary to print —

The following is Python code:

```python
print(dict(ds.sizes))
print(float(ds.lat[0]), float(ds.lat[-1]))
print(float(ds.lon[0]), float(ds.lon[-1]))
```

End of code.

Your `time` count will be larger by however many months have passed since May 2026. The following is the expected output:

```
{'time': 2069, 'nbnds': 2, 'lat': 89, 'lon': 180}
88.0 -88.0
0.0 358.0
```

End of output.

### 1.2) Print out the `long_name` attribute of each variable and coordinate

Hint: the names to loop over are `list(ds.coords) + list(ds.data_vars)`, and each one's attributes live in `ds[name].attrs`. (The dimension `nbnds` has no coordinate variable, so it doesn't appear.)

*Check yourself:* your loop should print five name / long-name pairs —

- `lat` → `Latitude`
- `lon` → `Longitude`
- `time` → `Time`
- `time_bnds` → `Time Boundaries`
- `sst` → `Monthly Means of Sea Surface Temperature`

(That's the order the hinted loop visits them in; a different approach may visit them in a different order.)

## 2) Basic reductions, arithmetic, means, and plotting

### 2.1) Calculate the time-mean of the entire dataset

Because the data is chunked, the mean is *lazy* — nothing downloads until you ask for actual values (by printing a number, plotting, or calling `.compute()` / `.load()`). The first time you do, the whole record comes over the network, so expect a wait unless you pre-loaded as suggested at the top.

*Check yourself:* the time-mean of `sst` is a 2-D field with `{'lat': 89, 'lon': 180}` — the `time` dimension is gone. Print a few values with `float(...)`: at `lon=200, lat=0` (central equatorial Pacific) the time-mean is about `27.0` °C. Over the whole map the minimum is about `-1.8` °C (polar oceans, pinned near the freezing point of seawater) and the maximum about `29.3` °C. About 31% of the grid is `NaN` — that's land, and it's why `.mean()`'s default `skipna=True` matters here.

### 2.2) Plot a map of the time-mean that was generated in the above part

Calling `.plot()` on the 2-D result draws the map; in a script, finish with `plt.savefig("assignment6_task2_2.png")` and/or `plt.show()`.

**What your plot should show.** A world map of long-term mean SST: longitude 0–358 across the x-axis (labelled "Longitude [degrees_east]"), latitude 88 down to −88 on the y-axis (labelled "Latitude [degrees_north]"), with a colorbar on the right labelled "Monthly Means of Sea Surface Temperature [degC]". Continents are blank white gaps (NaN). The ocean reads as broad horizontal bands: a dark-red band of 25–29 °C water spanning the tropics roughly 20°S–20°N, fading through mid-latitude oranges to pale, near-zero water poleward of about 55–60° in both hemispheres, with a faint blue tinge right against Antarctica and in the Arctic (slightly below 0 °C). Two finer features to know about: the warmest broad expanse of open ocean (about 29.3 °C along the equator) is the *west* Pacific "warm pool" near longitude 150 (the single warmest grid cell, also about 29.3 °C, is actually tucked into the Red Sea near longitude 42, latitude 18), and along the equator the *eastern* Pacific is distinctly cooler (about 24.3 °C at longitude 270) — the equatorial "cold tongue", which becomes important in part 4.

A map like this can't be explored with a screen reader directly — but a 1-D slice of it can. Print the equatorial band to "hear" the warm pool and cold tongue:

*Check yourself:* select the time-mean at `lat=0` and print `float(...)` at `lon=150` and `lon=270`: about `29.3` and `24.3` °C — the Pacific is about 5 °C colder at its eastern equatorial edge than at its western warm pool. The zonal (all-longitude) mean at `lat=-60` is about `0.8` °C.

### 2.3) Calculate a spatial mean for each time, and plot this as a timeseries

*Check yourself:* the result has only `time` left — 2069 values (and counting). Its overall mean is about `13.6` °C, and the monthly values range from about `12.9` to `14.8` °C.

**What your plot should show.** A single line spanning 1854 to the present, y-axis about 12.9–14.8 °C. Up close it's a dense zigzag — a seasonal cycle about 0.5 °C tall repeating every year. The longer story: the line drifts down to its coolest around 1900–1910 (annual means near 13.2 °C), recovers, and then climbs steadily and ever more steeply from about 1980 onward — the most recent years average about 14.3 °C, the warmest in the record. Save it as `assignment6_task2_3.png`. (Line plots like this can also be explored point-by-point by sound with [MAIDR](../sci_python/trying_maidr.md).)

### 2.4) Create a `weight` array proportional to $\cos(\lambda)$

Think carefully about radians vs. degrees.

The goal here is to realize that the dataset is provided on a $2^\circ \times 2^\circ$ lat-lon grid. But we know that the distance between longitude lines shrinks as we approach the poles.

This means that if we take a naive mean (as done in the above part), we are overemphasizing the influence of the regions away from the equator (since their area is smaller but they contribute the same to the mean). So here we plan to appropriately weight our data.

*Check yourself:* your weight array should be 1-D along `lat` with 89 values. The cosine of latitude ($\cos\lambda$) is `1.0` at the equator, `0.5` at 60° north or south, and only about `0.035` at ±88° — the polar rows should count for almost nothing. If your weight at 60° is not 0.5, you fed degrees to a function expecting radians (`np.deg2rad` is your friend).

### 2.5) Redo the calculation of the spatial mean for each time, and plot this as a timeseries

Hint: xarray can do the bookkeeping for you — look up `.weighted()` — or you can build the weighted mean by hand from multiply, sum, and divide (be careful that the NaN land points must be excluded from the denominator too).

*Check yourself:* the weighted timeseries is about `4.3` °C **warmer** than the unweighted one everywhere — overall mean about `17.9` °C, monthly values from about `17.2` to `19.0`, the last year of data averaging about `18.7` °C. That's the point of the task: the naive mean over-counts the small, cold high-latitude cells; weighting by area hands the vote back to the vast warm tropics. The seasonal zigzag also shrinks (about 0.36 °C peak-to-trough in the weighted climatology versus 0.52 unweighted).

**What your plot should show.** The same shape of story as 2.3 — coolest around 1900–1910, sharp warming after 1980 — but the whole curve now rides between about 17.2 and 19.0 °C. Plotting both series on one axes (worth doing) shows two roughly parallel curves separated by a constant ~4.3 °C gap, the weighted one on top with visibly smaller wiggles. Save it as `assignment6_task2_5.png`.

## 3) Selecting and Merging Data

For the next problem, use the following approximate locations of four different locations in the ocean:

- **Equatorial Pacific (EP):** longitude 250 E, latitude 0 N
- **Southern Ocean (SO):** longitude 50 E, latitude 60 S
- **North Atlantic (NAtl):** longitude 300 E, latitude 30 N
- **Arabian Sea (AS):** longitude 60 E, latitude 20 N

### 3.1) Create a dataset for each point from the global dataset

Hint: `.sel` with `method='nearest'` is the robust way to grab a point (here all four happen to sit exactly on grid points — remember the grid is every 2 degrees, longitude 0–360 east, and south latitudes are negative).

*Check yourself:* each point dataset has only `time` left as a dimension. The full-record mean SST at the four points: EP about `24.5` °C, SO about `-0.6` °C, NAtl about `24.1` °C, AS about `26.2` °C.

### 3.2) Combine these four datasets into a new dataset with the new dimension `location`

Create a new dimension coordinate to hold the location name.
Display the combined dataset.

*Check yourself:* `print(dict(ds_locs.sizes))` gives `{'location': 4, 'time': 2069}` (plus `nbnds: 2` if you kept `time_bnds`), and `print(ds_locs.location.values.tolist())` speaks your four location names, e.g. `['EP', 'SO', 'NAtl', 'AS']` (`.tolist()` converts them to plain Python strings, which print cleanly). In the `print(ds_locs)` listing, `location` now appears in the coordinates section and `sst` is on `(location, time)`.

### 3.3) Plot the SST at each location as a timeseries over the last 3 years

Hint: the last 3 years is the last 36 monthly steps — `isel(time=slice(-36, None))` gets them without knowing today's date. Plotting a 2-D DataArray with `.plot.line(x='time')` draws one line per location with a legend.

*Check yourself:* 36 time steps per location (for the data as of this writing, June 2023 through May 2026). Print each location's `mean` / `min` / `max` over the window, rounded to one decimal: EP about `25.3` / `22.6` / `27.7`; SO about `0.0` / `-1.4` / `1.4`; NAtl about `25.1` / `21.4` / `29.4`; AS about `27.2` / `24.8` / `29.9`.

**What your plot should show.** Four lines over three annual cycles, with a legend naming the locations. The Southern Ocean line sits alone near the bottom, hugging 0 °C (−1.4 to +1.4), peaking in February — Southern Hemisphere summer, opposite in phase to the others. The other three ride between about 21 and 30 °C: the North Atlantic swings the widest (about 8 °C, warmest in August–September, coolest in late winter (February–March)); the Arabian Sea swings about 5 °C and peaks early, in May, before the summer monsoon winds cool the sea surface; the Equatorial Pacific barely swings at all with the seasons (its April highs and September lows differ by under 4 °C) — its changes are mostly ENSO, year to year, which is where part 4 goes. Save it as `assignment6_task3_3.png`. (Another good plot to explore point-by-point with MAIDR.)

## 4) Group by and ENSO: Reproduce the SST curve from the figure below

*The original page shows NOAA's Oceanic Niño Index figure (from the National Centers for Environmental Information). In words:* the figure is titled "SST Anomaly in Nino 3.4 Region (5N-5S, 120-170W)". Its x-axis is Year, 1950 to 2020; its y-axis is "Anomaly in Degrees C", from −3 to +3. A single black curve — labelled "3mth running mean" in the legend — wiggles above and below zero, filled orange where it is above zero and cyan where below. Two dashed horizontal lines mark the thresholds: red at +0.5 °C ("El Nino Threshold") and blue at −0.5 °C ("La Nina Threshold"). The curve crosses the thresholds every few years — the El Niño / La Niña cycle. The tallest orange peaks come in 1972–73, 1982–83, 1997–98, and 2015–16, the last two reaching about +2.4 to +2.5 °C; the deepest cyan troughs, around −1.5 to −2 °C, come in the mid-1950s, 1973–75, 1988–89, 1999–2000, and 2010–11.

You don't have to match the stylistic details, or use different colors above and below zero, just the "3mth running mean" curve.

Here we will calculate the NINO 3.4 index of El Niño variability (which is the quantity shown in the above plot) and use it to analyze datasets.

- The Niño 3.4 region is defined as the region between +/- 5 deg. lat, 170 W - 120 W lon.
- Warm or cold phases of the Oceanic Niño Index are defined by a five consecutive 3-month running mean of sea surface temperature (SST) anomalies in the Niño 3.4 region that is above the threshold of +0.5°C (warm), or below the threshold of -0.5°C (cold). This is known as the Oceanic Niño Index (ONI).

(Note that "anomaly" means that the seasonal cycle, also called the "climatology" has been removed.)

Also for this part: Drop the `time_bnds` variable as we did in class and trim the data to 1950 onward for this assignment.

Two mechanical hints: this dataset's longitudes run 0–360 east, so 170 W – 120 W is longitude 190 to 240; and since `lat` descends from north to south, a latitude `slice` must give the northern value first.

*Check yourself, step by step:*

- Trimmed to 1950 onward, the record had `917` months at this writing (yours: more).
- The Niño 3.4 box is 5 latitude points (4, 2, 0, −2, −4) by 26 longitude points (190 through 240) — print `dict(...sizes)` to confirm `{'lat': 5, 'lon': 26}` alongside `time`.
- The climatology (mean over years for each of the 12 calendar months, via `groupby`) leaves a `month: 12` dimension; subtracting it from the monthly data (again via `groupby`) gives anomalies with the full `time` dimension back.
- After averaging the anomalies over the box and taking a 3-month running mean (`rolling`, centered), spot-check some famous ENSO winters, e.g. `float(index.sel(time='1997-12-01'))`. With the whole 1950-onward record as the climatology we got: December 1997 about `+2.5`, December 2015 about `+2.8` (the record maximum), December 1982 about `+2.3`, December 2023 about `+2.1`, December 1988 about `-1.8`, and a record minimum of about `-2.1` in late 1955. Your values will differ by a few hundredths — the climatology shifts as months are added — and they sit a little above NOAA's official ONI table for recent events (the official index uses sliding 30-year climatology windows; ours uses one fixed window). A centered 3-month rolling mean also leaves a `nan` at the very first and last time step — that's normal.

**What your plot should show.** Your reproduction of the NOAA curve, extended to the present: an irregular wiggle about zero from 1950 on, oscillating between roughly −2 and +2.8 with a period of a few years. The spikes named above should be audible in the numbers and visible in the line: the tallest peaks at 2015–16 (about +2.8), 1997–98 (about +2.5), 1982–83, 1972–73, and 2023–24 (about +2.1); the deepest troughs (about −1.5 to −2.1) in 1955, 1973–75, 1988–89, 1999–2000, and 2010–11. Adding the ±0.5 threshold lines with `ax.axhline` makes a nice finishing touch. Save it as `assignment6_task4.png`, and put a couple of sentences in a comment saying which El Niño and La Niña events your index found.

(submission-instructions-6)=
## Submission instructions

When you're done, save your completed scripts (and the PNG figures they produce) as `assignment6` files inside the current week's folder in your private `clmt5405-assignments` GitHub repo, then push the commit:

The following is a terminal command:

```bash
git add <weekN>/
git commit -m "Submit the Xarray SST lab"
git push
```

End of command.

Then post a link to your completed work on the matching **Courseworks** assignment. Due Sunday night.
