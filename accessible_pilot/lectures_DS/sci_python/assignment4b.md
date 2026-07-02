# Assignment 4b: More Matplotlib

**At-home assignment — worth 10 points.** When you're done, push your work to your week folder and post a link to your completed work on the matching Courseworks assignment.

In the standard version of this assignment, each problem shows a *picture* of a finished figure, and the task is to replicate it as closely as possible using only numpy and matplotlib. In this accessible version the task is the same, but each target figure is specified **in words** instead: a detailed description of its layout and content (verified against the actual image), followed by a checklist of the properties your figure must have. You build the figure from the description, verify it programmatically, and save it as a PNG.

:::{admonition} Learning goals
:class: tip
This assignment exercises matplotlib customization skills from this section:

- Build figures with the explicit Figure / Axes (`fig, ax = plt.subplots(...)`) pattern
- Customize line styles, colors, markers, and legends
- Use `contourf` and colorbars to visualize 2D scalar data
- Use scatter plots with color and size to encode multiple variables on a 2D plane
:::

:::{admonition} Working through this assignment with a screen reader
:class: important
Write one `.py` script per problem. For each problem, the code block below the problem statement downloads / prepares the data into numpy arrays — copy it to the top of your script. **Don't worry about how that code works** (some of it uses pandas and xarray, which we haven't covered yet) — just run it and use the resulting arrays in your plots. You are not allowed to use any packages other than numpy and matplotlib *in your own plotting code*. You'll need `pip install pooch pandas xarray netcdf4` for the data-prep code, and be online when you run the scripts.

For each problem, submit three things:

1. **The script**, ending with `plt.savefig("problem1.png")` (etc.) so the figure is saved for a sighted grader.
2. **The printed self-checks** listed under the problem — run them and make sure they pass.
3. **Two or three sentences, in a comment at the bottom of the script, saying what the data shows** — the description of each target figure tells you the story; put it in your own words, tied to the numbers you printed.

You are graded on the code, the checks, and the understanding — not on pixel-perfect visual matching. (The original images are on the [main course version of this page](https://earth-ds-ml.github.io/summer_2026/lectures_DS/sci_python/assignment4b.html), for any sighted helper who wants to compare.)
:::

## Problem 1: Line plots

In this problem, we will plot some daily weather data from a NOAA station in [Millbrook, NY](https://www.ncdc.noaa.gov/cdo-web/datasets/GHCND/stations/GHCND:US1NYDT0008/detail). A full description of this dataset is available at: <https://www.ncdc.noaa.gov/data-access/land-based-station-data>

The code below uses pandas to download the data and populate a bunch of numpy arrays (`t_daily_min`, `t_daily_max`, etc.). Copy it, run it, and then use the numpy arrays to re-create the figure described below.

The following is Python code:

```python
import pooch
POOCH = pooch.create(
    path=pooch.os_cache("noaa-data"),
    # Use the figshare DOI
    base_url="doi:10.5281/zenodo.5553029/",
    registry={
        "HEADERS.txt": "md5:2a306ca225fe3ccb72a98953ded2f536",
        "CRND0103-2016-NY_Millbrook_3_W.txt": "md5:eb69811d14d0573ffa69f70dd9c768d9",
        "CRND0103-2017-NY_Millbrook_3_W.txt": "md5:b911da727ba1bdf26a34a775f25d1088",
        "CRND0103-2018-NY_Millbrook_3_W.txt": "md5:5b61bc687261596eba83801d7080dc56",
        "CRND0103-2019-NY_Millbrook_3_W.txt": "md5:9b814430612cd8a770b72020ca4f2b7d",
        "CRND0103-2020-NY_Millbrook_3_W.txt": "md5:cd8de6d5445024ce35fcaafa9b0e7b64"
    },
)


import pandas as pd

with open(POOCH.fetch("HEADERS.txt")) as fp:
    data = fp.read()
lines = data.split('\n')
headers = lines[1].split(' ')

dframes = []
for year in range(2016, 2019):
    fname = f'CRND0103-{year}-NY_Millbrook_3_W.txt'
    df = pd.read_csv(POOCH.fetch(fname), parse_dates=[1],
                     names=headers, header=None, sep=r'\s+',
                     na_values=[-9999.0, -99.0])
    dframes.append(df)

df = pd.concat(dframes)
df = df.set_index('LST_DATE')

#########################################################
#### BELOW ARE THE VARIABLES YOU SHOULD USE IN THE PLOTS!
#### (numpy arrays)
#### NO PANDAS ALLOWED!
#########################################################

t_daily_min = df.T_DAILY_MIN.values
t_daily_max = df.T_DAILY_MAX.values
t_daily_mean = df.T_DAILY_MEAN.values
p_daily_calc = df.P_DAILY_CALC.values
soil_moisture_5 = df.SOIL_MOISTURE_5_DAILY.values
soil_moisture_10 = df.SOIL_MOISTURE_10_DAILY.values
soil_moisture_20 = df.SOIL_MOISTURE_20_DAILY.values
soil_moisture_50 = df.SOIL_MOISTURE_50_DAILY.values
soil_moisture_100 = df.SOIL_MOISTURE_100_DAILY.values
date = df.index.values
```

End of code.

(To hear the variable names and units in this dataset, you can also print them: `units = lines[2].split(' ')`, then loop `for name, unit in zip(headers, units): print(name, unit)`.)

**The target figure, in words.** One tall figure of **three stacked panels** that share the same x-axis: time, from January 2016 through December 2018 (1096 days of daily data).

- **Top panel — temperature.** A shaded light-gray band spans, for every day, the range from that day's minimum to its maximum temperature (made with `ax.fill_between(date, t_daily_min, t_daily_max)`); a magenta line for the daily mean runs through the middle of the band. A legend in the top-right corner names them "daily range" and "daily mean". The y-axis is labelled "Temperature [$^\circ$C]". The curve rises and falls through **three annual cycles**: summer peaks near +25 to +30 °C, winter dips below −10 °C (the extremes in the record are −26.0 and +35.7 °C).
- **Middle panel — precipitation.** Daily precipitation as a series of thin blue vertical spikes rising from a zero baseline (a simple `ax.plot` of the array works, though the spiky look comes naturally either way). Most days are small (under 10 mm/day), with isolated storm spikes — the largest reach 66 mm/day, one in February 2016 and a near-equal one in March 2017. Y-axis label: "Precipitation [mm/day]".
- **Bottom panel — soil moisture.** **Five** lines, one per measurement depth (5, 10, 20, 50, 100 cm), with a legend in the lower left naming each. Values range from about 0.04 to 0.32 m³/m³; y-axis label "Soil Moisture [$m^3/m^3$]", x-axis label "Date". The story in the lines: the shallow depths swing sharply — jumping with every rain event and drying out hard in late summer — while the deepest (100 cm) line is the smoothest and stays in a narrow 0.11–0.22 band.

**Checklist / self-checks.** Print and confirm:

- `len(fig.axes)` is `3`, and `len(t_daily_mean)` is `1096`.
- `np.nanmin(t_daily_min)` ≈ `-26.0`, `np.nanmax(t_daily_max)` ≈ `35.7`, `np.nanmax(p_daily_calc)` ≈ `66.0`.
- The bottom axes contains 5 lines: `len(fig.axes[2].lines)` is `5`.
- Each panel's `get_ylabel()` returns the label from the description.

Hint: `fig, axes = plt.subplots(nrows=3, figsize=(8, 10), sharex=True)` is a good skeleton — `sharex=True` is what links the three time axes.

## Problem 2: Contour Plots

Now we will visualize some global temperature data from the NCEP-NCAR atmospheric reanalysis.

The following is Python code:

```python
import xarray as xr
ds_url = 'http://iridl.ldeo.columbia.edu/SOURCES/.NOAA/.NCEP-NCAR/.CDAS-1/.MONTHLY/.Diagnostic/.surface/.temp/dods'
ds = xr.open_dataset(ds_url, decode_times=False)

#########################################################
#### BELOW ARE THE VARIABLES YOU SHOULD USE IN THE PLOTS!
#### (numpy arrays)
#### NO XARRAY ALLOWED!
#########################################################

temp = ds.temp[-1].values - 273.15
lon = ds.X.values
lat = ds.Y.values
```

End of code.

Note: `ds.temp[-1]` grabs the **most recent month** in the dataset, which updates continuously — so your exact values (and which hemisphere looks coldest) depend on the season when you run it. The *structure* described below is always there.

**The target figure, in words.** One wide figure with **two panels side by side — the left one about three times wider than the right** (`gridspec_kw={'width_ratios': [3, 1]}`).

- **Left panel — a filled-contour map of surface temperature**, titled "Current Global Temperature". Longitude 0–360 runs along the x-axis (labelled "Longitude"), latitude −90 to 90 up the y-axis (labelled "Latitude"). It's drawn with `contourf` using a dark-to-light colormap like `magma` — near-black at the coldest values through purple and orange to pale yellow at the warmest — with a vertical colorbar (labelled "$^\circ$C", roughly −30 to +40, with arrow tips for values beyond) beside the panel. The geography reads as horizontal bands: pale warm colors through the tropics, darkening toward both poles. In the target image (made from a northern-summer month) Antarctica at the bottom is a solid near-black band below −30 °C. On top of the filled colors sits a **single white contour line at exactly 0 °C** — the freezing line — tracing a ring around Antarctica near 60°S and a wandering line across the Arctic near 75–80°N. Dotted horizontal gridlines mark the latitudes.
- **Right panel — the zonal mean**, titled "Zonal Mean Temperature": a single black line of the temperature averaged over all longitudes (`temp.mean(axis=1)`), plotted **against latitude on the vertical axis** so it reads like a pole-to-pole profile, with a grid. In the target image it starts near −60 °C at the South Pole, climbs steeply across the Southern Hemisphere, flattens into a broad warm plateau near +25 to +28 °C in the tropics, and falls gently to around 0 °C at the North Pole. The two ends are strikingly asymmetric — that's real (Antarctica is a high, icy continent), though how extreme it looks depends on the month you fetched.

**Checklist / self-checks.** Print and confirm:

- `temp.shape` is `(94, 192)`; `len(lat)` is `94`, `len(lon)` is `192` (so the zonal mean — the average over longitude — is `temp.mean(axis=1)`, shape `(94,)`).
- `np.nanmin(temp)` and `np.nanmax(temp)` — expect very roughly −50s and +30s (season-dependent).
- The tropics are the warm plateau: print the zonal mean at the equator (`lat` index 46 or 47) — expect ~+25 to +28 °C.
- `len(fig.axes)` is `3` (two panels + the colorbar counts as an axes).
- The white freezing line is one extra call: `ax.contour(lon, lat, temp, [0], colors='w')`.

## Problem 3: Scatter plots

Here we will make a map plot of earthquakes from a USGS catalog of historic large earthquakes. Color the earthquakes by log10(depth) and adjust the marker size to be magnitude$^4$/100.

The following is Python code:

```python
import numpy as np
import pooch
fname = pooch.retrieve(
    "https://rabernat.github.io/research_computing/signif.txt.tsv.zip",
    known_hash='22b9f7045bf90fb99e14b95b24c81da3c52a0b4c79acf95d72fbe3a257001dbb',
    processor=pooch.Unzip()
)[0]

earthquakes = np.genfromtxt(fname, delimiter='\t')
depth = earthquakes[:, 8]
magnitude = earthquakes[:, 9]
latitude = earthquakes[:, 20]
longitude = earthquakes[:, 21]
```

End of code.

**The target figure, in words.** A single large panel titled "Location of Significant Earthquakes With Size Indicating Relative Magnitude". It is a world map made of scatter dots: longitude −180 to 180 on the x-axis (labelled "Longitude (°)"), latitude about −60 to 73 on the y-axis (labelled "Latitude (°)"), with a light grid. Each dot is one earthquake. The dots are not spread evenly — they **trace the boundaries of Earth's tectonic plates**: a dense line down the west coast of the Americas, a broad arc through the Mediterranean, the Himalayas and Indonesia, and chains outlining the western Pacific "Ring of Fire". Dot *color* encodes `np.log10(depth)` on the default viridis colormap, explained by a colorbar labelled "Depth [m]": most dots are mid-scale teal-green (shallow-to-mid depths), with scattered bright-yellow dots — the deepest quakes, down to 678 km, concentrated where plates dive under one another (western South America, the western Pacific). Dot *size* encodes `magnitude**4 / 100`, so great quakes stand out strongly: a magnitude 9.5 dot has about 13 times the area of a magnitude 5 dot (and over 1200 times that of the smallest, magnitude 1.6).

**Checklist / self-checks.** Print and confirm:

- `earthquakes.shape` is `(5959, 47)` — 5959 events.
- `np.nanmax(depth)` is `678.0`; `np.nanmin(magnitude)` / `np.nanmax(magnitude)` are `1.6` / `9.5`.
- About half the depth values are missing: `np.isnan(depth).sum()` prints `2945` (dots with no depth simply get no color — and `np.log10` will warn about zeros/NaNs; that warning is expected here, not a bug).
- After plotting: `ax.get_title()` returns the title, and `len(fig.axes)` is `2` (panel + colorbar).

## Submission instructions

When you're done, save your scripts and the three PNG figures inside the current week's folder in your private `clmt5405-assignments` GitHub repo. Then push the commit:

The following is a terminal command:

```bash
git add week4/
git commit -m "Submit assignment 4b"
git push
```

End of command.

Due Sunday night.
