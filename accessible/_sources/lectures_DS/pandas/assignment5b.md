# Assignment 5b: Pandas Groupby with Hurricane Data

:::{admonition} Following along with a screen reader
:class: important
Work through this assignment as `.py` scripts in VS Code — see [Setting up an accessible workflow](../computing_env/accessible_setup.md) and [Working with Python scripts](../computing_env/working_with_scripts.md). Run a script in the Git Bash terminal with `python yourfile.py`, and press `Ctrl+Up` to read the output. You need to be online once, to download the dataset (about 322 MB — see the download step below); after that everything runs from the local file.
:::

**At-home assignment — worth 10 points.** When you're done, push your work to your week folder and post a link to your completed work on the matching **Courseworks** assignment.

In this assignment you'll apply `groupby` and time-series operations to the NOAA IBTrACS hurricane dataset.

:::{admonition} Learning goals
:class: tip
This assignment exercises the groupby and time-series skills from this section:

- Read a large CSV file with custom column-type handling
- Rename columns and inspect unique values
- Filter and `nlargest` operations on a `DataFrame`
- Use `groupby` to count, aggregate, and iterate over groups
- Plot scatter plots, bar charts, and hexbin plots from a DataFrame
- Set a datetime index and use it for time-based filtering
- Compute climatologies and anomalies with `groupby` + `transform`
:::

:::{admonition} Working through this assignment with a screen reader
:class: important
Work in one script, `assignment5b.py`, and add each numbered task under a comment like `# Task 3`. The dataset loads from the local file in roughly 20–30 seconds, so re-running the whole script after each addition is fine. Remember: in a script, a bare expression on its own line (like `len(df)` or `df.head()`) displays nothing — wrap anything you want to read in `print(...)`. And to *read* a DataFrame result, use the ear-friendly pattern from the [Loading data](../data/loading_data.md) page — print the shape, the column names, and then rows as dicts — never a raw table grid.

For each task, submit three things:

1. **The code**, with each plot task ending in `plt.savefig("task05.png")` (etc.) so the figure is saved for a sighted grader.
2. **Printed checks.** Every task below has a *Check yourself* note with values I computed from the real data — print the same quantities from your solution and make sure they match.
3. **A sentence or two of interpretation** for the plot tasks, in a comment: what does the figure/number pattern show? Tie it to the numbers you printed.

You are graded on the code, the checks, and the understanding — not on pixel-perfect visual matching. When you're done, follow the [submission instructions](#submission-instructions-5b) at the bottom of the page.
:::

:::{admonition} Hearing a plot (optional)
:class: note
The bar charts, scatter plots, and line plots you make in this assignment can be explored point-by-point by sound and text with **[MAIDR](../sci_python/trying_maidr.md)**. The `hexbin` plot in task 8 is a 2-D density map that MAIDR does not handle — for that one, check yourself with printed counts instead (the task tells you how).
:::

## Getting the data

We'll use a CSV file of the [NOAA IBTrACS](https://www.ncdc.noaa.gov/ibtracs/index.php?name=ibtracs-data) hurricane dataset — every recorded tropical-cyclone track position since the 1840s. The original notebook reads it straight from the URL with `pd.read_csv(url, ...)`; that works, but the file is about 322 MB, and in the script workflow every `python assignment5b.py` run would re-download it. So download it **once** in the terminal instead, into the folder where your script lives:

The following is a terminal command:

```bash
curl -L -O 'https://www.ncei.noaa.gov/data/international-best-track-archive-for-climate-stewardship-ibtracs/v04r00/access/csv/ibtracs.ALL.list.v04r00.csv'
```

End of command.

This takes a few minutes depending on your connection; you only do it once. (All the *Check yourself* numbers on this page were computed from the v04r00 file, last updated by NOAA in May 2024. If NOAA refreshes the file, totals could shift slightly.)

Start your script by importing numpy, pandas, and matplotlib's pyplot, then load the local file with the following custom options. The trailing lines replace the original notebook's `df.head()` with the ear-readable inspection pattern:

The following is Python code:

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

fname = 'ibtracs.ALL.list.v04r00.csv'   # the file you downloaded with curl
df = pd.read_csv(fname, parse_dates=['ISO_TIME'], usecols=range(12),
                 skiprows=[1], na_values=[' ', 'NOT_NAMED'],
                 keep_default_na=False, dtype={'NAME': str})

print(df.shape)
print(df.columns.tolist())
for _, row in df.head(3).iterrows():
    print(row.to_dict())
```

End of code.

The following is the expected output:

```
(716165, 12)
['SID', 'SEASON', 'NUMBER', 'BASIN', 'SUBBASIN', 'NAME', 'ISO_TIME', 'NATURE', 'LAT', 'LON', 'WMO_WIND', 'WMO_PRES']
{'SID': '1842298N11080', 'SEASON': 1842, 'NUMBER': 1, 'BASIN': 'NI', 'SUBBASIN': 'BB', 'NAME': nan, 'ISO_TIME': Timestamp('1842-10-25 03:00:00'), 'NATURE': 'NR', 'LAT': 10.9, 'LON': 80.3, 'WMO_WIND': nan, 'WMO_PRES': nan}
{'SID': '1842298N11080', 'SEASON': 1842, 'NUMBER': 1, 'BASIN': 'NI', 'SUBBASIN': 'BB', 'NAME': nan, 'ISO_TIME': Timestamp('1842-10-25 06:00:00'), 'NATURE': 'NR', 'LAT': 10.8709, 'LON': 79.8265, 'WMO_WIND': nan, 'WMO_PRES': nan}
{'SID': '1842298N11080', 'SEASON': 1842, 'NUMBER': 1, 'BASIN': 'NI', 'SUBBASIN': 'BB', 'NAME': nan, 'ISO_TIME': Timestamp('1842-10-25 09:00:00'), 'NATURE': 'NR', 'LAT': 10.8431, 'LON': 79.3524, 'WMO_WIND': nan, 'WMO_PRES': nan}
```

End of output.

A quick tour of what you just heard: 716,165 rows and 12 columns. Each row is one *position report* — a storm observed every few hours — not one hurricane; the `SID` column is the storm identifier that ties a track's rows together. `usecols=range(12)` keeps only the first 12 of the file's 160-plus columns; `skiprows=[1]` skips a second header row of units (it says `WMO_WIND` is in knots and `WMO_PRES` in millibars); and `na_values=[' ', 'NOT_NAMED']` turns blanks and the `NOT_NAMED` placeholder into `NaN` — which is why the oldest storm in the file, an 1842 cyclone in the Bay of Bengal, prints `'NAME': nan`.

Basin Key: (NI - North Indian, SI - South Indian, WP - Western Pacific, SP - Southern Pacific, EP - Eastern Pacific, NA - North Atlantic)

How many rows does this dataset have?

The following is Python code:

```python
print(len(df))
```

End of code.

The following is the expected output:

```
716165
```

End of output.

How many North Atlantic hurricanes are in this dataset? (Careful: rows are position reports. To count *hurricanes*, count the unique storm identifiers.)

*Check yourself:* the `NA` basin has **126,392 rows** but only **2,344 unique storms** (unique `SID` values). If your two numbers match these, you've understood the row-versus-storm distinction that the whole assignment leans on.

## 1) Get the unique values of the `BASIN`, `SUBBASIN`, and `NATURE` columns

*Check yourself:* `BASIN` has **7** unique values: `NI`, `SI`, `NA`, `EP`, `WP`, `SP`, and the rare `SA` (South Atlantic — only 3 storms ever recorded there). `SUBBASIN` has **9** unique values and `NATURE` has **6** (`NR`, `TS`, `ET`, `SS`, `MX`, `DS`). Hint: `.unique()` on a column returns an array; wrap it like `print(df.BASIN.unique().tolist())` to hear it as a clean list.

## 2) Rename the `WMO_WIND` and `WMO_PRES` columns to `WIND` and `PRES`

*Check yourself:* after renaming, `print(df.columns.tolist())` should end with `..., 'LAT', 'LON', 'WIND', 'PRES'`. Hint: `df.rename(columns={...})` returns a *new* DataFrame — remember to assign it back to `df`.

## 3) Get the 10 largest rows in the dataset by `WIND`

To read the result by ear, don't print the grid — loop over the rows and print just the columns that matter, for example `print(row['NAME'], row['SEASON'], row['WIND'])` inside `for _, row in result.iterrows():`.

*Check yourself:* the strongest single report is **PATRICIA (2015) at 185 knots**; Patricia fills the top 3 rows (185, 180, 180). The rest of the top 10 are ALLEN (1980, 165), three reports from an unnamed 1935 storm (160 each — its name prints as `nan` because `NOT_NAMED` became `NaN`; it's the famous Labor Day hurricane), GILBERT (1988), LINDA (1997), and WILMA (2005), all at 160. The 10th-largest value is **160.0**.

You will notice some names are repeated.

## 4) Group the data on `SID` and get the 10 largest hurricanes by `WIND`

*Check yourself:* now each storm appears once (its maximum wind). The top 10 winds are **185, 165, then five storms at 160, then three at 155** knots — in order: PATRICIA (2015), ALLEN (1980), the unnamed 1935 storm, GILBERT (1988), LINDA (1997), WILMA (2005), DORIAN (2019), MITCH (1998), RITA (2005), RICK (2009). Hint: group on `SID`, aggregate `WIND` with `max`, then `nlargest`.

## 5) Make a bar chart of the wind speed of the 20 strongest-wind hurricanes

Use the name on the x-axis.

Two real-data traps here, both verified: **(a)** two of the 20 storms (from 1935 and 1932) have `NaN` names, and `ax.bar` raises `TypeError` if a tick label is `NaN` — replace missing names first (e.g. `.fillna('UNNAMED')`). **(b)** If you pass the names directly as the bar positions, matplotlib treats equal labels as the *same category* and draws both bars at the same x position — a sighted viewer sees only 19 bars, with one hidden behind another. Note `len(ax.patches)` still prints 20 in this buggy version, so the patch count alone won't catch it; use the range(20) pattern below. The robust pattern is `ax.bar(range(20), winds)` and then set the tick labels with `ax.set_xticks(range(20), names)`. Save it with `plt.savefig("task05.png")`.

*Check yourself:* `len(ax.patches)` should print `20` (one patch per bar). The first bar (PATRICIA) has height **185**, the 20th (KATRINA, 2005) has height **150**. A correct chart, in words: one towering first bar at 185, a step down to 165, bars 3 through 7 level at 160, bars 8 through 11 at 155, and the last nine bars all flat at 150 — a steep drop then a long plateau.

## 6) Plot the count of all datapoints by Basin

as a bar chart

*Check yourself:* the counts, largest to smallest, are `WP`: **238,485**, `SI`: **163,710**, `NA`: **126,392**, `SP`: **68,233**, `EP`: **63,434**, `NI`: **55,792**, and `SA`: **119**. In the chart the Western Pacific bar is about one and a half times the South Indian one, and the South Atlantic bar is invisibly small. Save it as `task06.png`.

## 7) Plot the count of unique hurricanes by Basin

as a bar chart.

*Check yourself:* unique storms per basin are `WP`: **4,289**, `SI`: **3,061**, `NA`: **2,344**, `NI`: **1,785**, `EP`: **1,657**, `SP`: **1,321**, `SA`: **3**. Hint: `.nunique()` is an aggregation like any other. Notice the ordering change from task 6 — the North Indian basin jumps ahead of the Eastern and Southern Pacific, because its storms are numerous but its track records are short (fewer position reports per storm). That contrast is worth a sentence in your interpretation comment. Save it as `task07.png`.

## 8) Make a `hexbin` of the location of datapoints in Latitude and Longitude

A tip: pass `bins='log'` to `ax.hexbin` — the busiest hexagon holds about 2,650 points (at hexbin's default grid size) while most hold a handful, so a linear color scale would leave almost the whole map one dark color.

*Check yourself:* print the coordinate ranges: `LAT` runs **-68.5 to 83.0** and `LON` runs **-179.8 to 266.9** — yes, past 180: tracks that cross the dateline keep counting upward. Also print `(df.LAT.abs() < 5).sum()`: only **3,035** of 716,165 points lie within 5 degrees of the equator. A correct figure, in words (verified by rendering): the points fill the tropical and subtropical ocean basins in four big patches — the brightest core sits in the Northwest Pacific east of the Philippines (near 10–25°N, 120–150°E), with other bright regions in the Gulf of Mexico/Caribbean, the Bay of Bengal, and a broad Southern-Hemisphere band from 5–40°S across the Indian and West Pacific oceans. A striking empty stripe runs along the equator (cyclones need the Coriolis force, which vanishes there), and the southeastern Pacific and South Atlantic are almost blank. Since MAIDR can't read a hexbin, these printed range/count checks *are* your reading of the map. Save it as `task08.png`.

## 9) Find Hurricane Katrina (from 2005) and plot its track as a scatter plot

First find the SID of this hurricane.

*Check yourself:* filtering `NAME == 'KATRINA'` and `SEASON == 2005` gives exactly one unique SID: **`'2005236N23285'`**.

Next get this hurricane's group and plot its position as a scatter plot. Use wind speed to color the points.

*Check yourself:* Katrina's track has **64 points**, from **2005-08-23 18:00 to 2005-08-31 06:00**; longitude spans **-89.6 to -75.1**, latitude **23.1 to 40.1**; maximum `WIND` is **150** knots and minimum `PRES` is **902** millibars. A correct figure, in words (verified by rendering): the track starts as dark, low-wind dots (25–30 knots) near the Bahamas at 75°W, 23°N, crosses south Florida near 80°W, then arcs west-southwest into the Gulf of Mexico, where the dots turn bright yellow — the 150-knot peak near 89°W, 26°N — before hooking due north to the Louisiana–Mississippi coast around 89.6°W, 29.5°N and fading back to dark purple inland, ending near 83°W, 40°N. Save it as `task09.png`, with a colorbar for wind speed.

## 10) Make time the index on your dataframe

*Check yourself:* after `set_index`, `print(df.index[0])` gives `1842-10-25 03:00:00` and `print(df.index[-1])` gives `2024-05-27 18:00:00`. One more print worth doing: `df.index.is_monotonic_increasing` is **False** — rows are grouped storm-by-storm and storms overlap in time, so the index is *not* sorted. (Also note `df.index[-1]` is the last *row*, not necessarily the latest *time*, for exactly that reason.)

## 11) Plot the count of all datapoints per year as a timeseries

You should use `resample`

*Check yourself:* resampling to year-start (`'YS'`) and counting gives **183** yearly values (1842 through 2024). Anchors to print and compare: 1900 has **1,958** points, the all-time maximum is **9,189 in 1996**, 2020 has **6,407**, and the final partial year 2024 has just 899. A correct figure, in words (verified by rendering): the line climbs from near zero in the mid-1800s to roughly 2,000–3,500 per year in the early 1900s, rises steeply after the 1940s (aircraft and satellite reconnaissance), oscillates on a high plateau of about 6,000–9,000 from 1970 to 2000 with the 1996 spike on top, eases back to 5,000–7,000 in the 2000s–2010s, and plunges at the very end because 2024 is incomplete. Save it as `task11.png`.

## 12) Plot all tracks from the North Atlantic in 2005

You will probably have to iterate through a `GroupBy` object

*Check yourself:* the North Atlantic in 2005 — the record-breaking Katrina/Rita/Wilma season — has **31 distinct storms** (1,752 rows if you select calendar-year 2005 from the datetime index, e.g. `df.loc['2005']`; filtering `SEASON == 2005` instead gives 1,807 rows because storm Zeta lasted into January 2006 — either way, 31 storms). (27 storms got names that year; IBTrACS also tracks a few unnamed systems.) A correct figure, in words (verified by rendering): a spaghetti plot of about 31 colored lines, almost all born in the band 10–25°N between 100°W and 40°W, most curving northeastward as they leave the tropics; longitudes span **-101 to +7** and latitudes reach **69°N** — one long track crosses the 0° meridian (and a second just reaches it) — storm remnants reaching Europe. Save it as `task12.png`.

## 13) Create a filtered dataframe that contains only data since 1970 from the North Atlantic ("NA") Basin

Use this for the rest of the assignment

*Check yourself:* the filtered DataFrame has shape **(48154, 11)**, spanning 1970-05-17 to 2023-11-18. Hint: a boolean mask works well here — combine `df.index.year >= 1970` with the basin condition. (A time slice like `df.loc['1970':]` also selects the right rows, verified even on this unsorted index — but you need the basin condition anyway, so the mask is the cleaner route.)

## 14) Plot the number of datapoints per day from this filtered dataframe

Make sure you figure is big enough to actually see the plot

*Check yourself:* resampling daily gives **19,544** days; the overall mean is about **2.46** points per day, and the busiest day is **2020-09-17 with 54 points** (seven storms tracked in the Atlantic that day, Paulette through Beta). "Big enough" here means something like `figsize=(12, 4)` — the series is a dense comb of late-summer spikes separated by near-zero winters. Save it as `task14.png`.

## 15) Calculate the climatology of datapoint counts as a function of `dayofyear`

Plot the mean and standard deviation on a single figure

*Check yourself:* grouping the daily counts by `dayofyear` gives **366** values. The mean climatology peaks at **day 251 (September 8) with a mean of about 14.3** points per day and a standard deviation of about **8.6** there; from about day 25 to day 100 (February to mid-April) both curves sit at essentially zero. A correct figure, in words (verified by rendering): two curves rising together out of a flat spring, climbing from June onward to a sharp early-September peak and falling back to near zero by late December — with the standard-deviation curve running *above* the mean through most of the season's shoulders, because hurricane activity is bursty: many quiet days punctuated by very busy ones. Hint: `daily_counts.groupby(daily_counts.index.dayofyear)`. Save it as `task15.png`.

## 16) Use `transform` to calculate the anomaly of daily counts from the climatology

Resample the anomaly timeseries at annual resolution and plot a line with dots as markers.

*Check yourself:* the annual-mean anomaly series has **54** points (1970–2023). The three most positive years are **2005 (+2.35), 2020 (+2.20), and 2023 (+2.03)**, followed by 1995 (+1.61); the most negative are **1983 (-1.73), 1982 (-1.63), and 1977 (-1.53)**. A correct figure, in words (verified by rendering): a jagged dotted line oscillating around zero between roughly -1.7 and +2.4, with the quiet early 1980s dipping lowest, isolated tall spikes at 1995, 2005, 2020, and 2023, and generally more positive values after the mid-1990s. Hint: `transform('mean')` on the `dayofyear` groupby returns a series aligned with the daily one, so you can subtract directly. Save it as `task16.png`.

Which years stand out as having anomalous hurricane activity? Answer in two or three sentences in a comment at the bottom of your script, tied to the numbers above.

(submission-instructions-5b)=
## Submission instructions

When you're done, save your script `assignment5b.py` and your saved PNG figures inside the current week's folder in your private `clmt5405-assignments` GitHub repo. Then push the commit:

The following is a terminal command:

```bash
git add <weekN>/
git commit -m "Submit assignment 5b"
git push
```

End of command.

Due Sunday night.
