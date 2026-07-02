# Pandas: Groupby

:::{admonition} Following along with a screen reader
:class: important
Work through the examples as `.py` scripts in VS Code — see [Setting up an accessible workflow](../computing_env/accessible_setup.md) and [Working with Python scripts](../computing_env/working_with_scripts.md). Run a script in the Git Bash terminal with `python yourfile.py`, and press `Ctrl+Up` to read the output.

This page needs the `pandas`, `matplotlib`, and `pooch` packages (`pip install pandas matplotlib pooch`), and you must be **online**: the earthquake catalog is read straight from a URL, and the weather-station files are downloaded (and cached) from Zenodo the first time you run them.
:::

Often we don't want a single summary for the whole dataset — we want **one value per group**: the average temperature in each *month*, the strongest earthquake in each *country*, the total rainfall in each *year*. `groupby` is the tool for exactly this, and it's one of the most useful things pandas does.

The pattern it follows is called **split–apply–combine**: split the rows into groups, apply a calculation to each group, then combine the results back together. We'll build that intuition on two datasets — first grouping earthquakes **by country**, then grouping a weather station's daily record **by time** to build climatologies and anomalies. Along the way we'll meet `groupby`'s close relatives **`resample`** (grouping by time interval) and **`rolling`** (moving-window calculations).

These notes draw on the [pandas groupby documentation](http://pandas.pydata.org/pandas-docs/stable/groupby.html); the split-apply-combine framing comes from a [paper by Hadley Wickham](https://www.jstatsoft.org/article/view/v040i01).

:::{admonition} In-class assignment — 10 points
:class: note
The **"Try it"** exercises on this page are part of your in-class assignment for this section. Complete them as `.py` scripts, push them to your week folder, and post the link on the matching **Courseworks** assignment. (One 10-point assignment covers all the lecture pages in this section.)
:::

:::{admonition} Reading the results by ear
:class: tip
Two habits from earlier pages carry through this whole lecture:

1. **In a script, nothing displays unless you `print()` it.** The original notebook version of this lecture ends many cells with a bare expression like `df.head()`; in a script that shows nothing. Every example below already wraps what you should hear in `print(...)`.
2. **Never read a DataFrame as a printed grid.** As on the [Loading data](../data/loading_data.md) page, we inspect tables by printing the **shape**, the **column names**, and then a few rows as dicts (`row.to_dict()`), so each value is spoken next to its column name. Conveniently, most `groupby` results are small Series — and a small Series prints beautifully as a dict with `print(s.to_dict())`. You'll hear that pattern constantly below.

The bar charts, histograms, and line plots on this page can also be explored point-by-point by sound and text with **[MAIDR](../sci_python/trying_maidr.md)**, and every plot below is followed by a "**What this shows**" note describing the rendered figure in words and numbers.
:::

We start with our usual imports. The `rcParams` line just makes the default figure canvas larger:

The following is Python code:

```python
import numpy as np
from matplotlib import pyplot as plt
import pandas as pd

plt.rcParams['figure.figsize'] = (12, 7)
```

End of code.

First we read the earthquake data from the previous assignment. Two extra steps set up the rest of the lesson: we derive a `country` column from the free-text `place` field, and we split the catalog by magnitude into the larger events (`df`, magnitude > 4) and the smaller ones (`df_small`, magnitude < 4), so we can compare the two groups later. Then we inspect the result the screen-reader-friendly way — shape, column names, and the first few rows as dicts (printing just four of the 22 columns so each row reads cleanly):

The following is Python code:

```python
df = pd.read_csv('https://raw.githubusercontent.com/earth-DS-ML/summer_2026/refs/heads/main/lectures_DS/data/usgs_earthquakes_2025.csv',
                 parse_dates=['time'], index_col='id')
df['country'] = df.place.str.split(', ').str[-1]
df_small = df[df.mag < 4]
df = df[df.mag > 4]

print(df.shape)
print(df.columns.tolist())
for _, row in df[['time', 'mag', 'place', 'country']].head(3).iterrows():
    print(row.to_dict())
```

End of code.

The following is the expected output:

```
(5923, 22)
['time', 'latitude', 'longitude', 'depth', 'mag', 'magType', 'nst', 'gap', 'dmin', 'rms', 'net', 'updated', 'place', 'type', 'horizontalError', 'depthError', 'magError', 'magNst', 'status', 'locationSource', 'magSource', 'country']
{'time': Timestamp('2025-06-17 12:00:22.773000+0000', tz='UTC'), 'mag': 4.9, 'place': '206 km SSW of ‘Ohonua, Tonga', 'country': 'Tonga'}
{'time': Timestamp('2025-06-17 09:16:30.483000+0000', tz='UTC'), 'mag': 4.8, 'place': '196 km S of ‘Ohonua, Tonga', 'country': 'Tonga'}
{'time': Timestamp('2025-06-17 08:36:22.986000+0000', tz='UTC'), 'mag': 5.3, 'place': '42 km ENE of Barcelona, Philippines', 'country': 'Philippines'}
```

End of output.

Notice how the derived `country` column works: `'206 km SSW of ‘Ohonua, Tonga'` becomes `'Tonga'` — the last comma-separated piece of the `place` string.

Before grouping, let's get oriented — how many large events are in `df`, and what does the distribution of magnitudes look like?

The following is Python code:

```python
print(len(df))
```

End of code.

The following is the expected output:

```
5923
```

End of output.

The following is Python code:

```python
df.mag.plot.hist()
plt.show()
```

End of code.

**What this shows.** A histogram of the `mag` column: ten blue bars spanning magnitudes 4.0 to 7.7, with "Frequency" on the y-axis. The distribution is strongly right-skewed. The two leftmost bars dominate — about 2100 events between magnitude 4.0 and 4.4, and about 2550 between 4.4 and 4.8 — the third bar drops to about 870, and the counts collapse quickly after that: only about twenty events above magnitude 6.2, and just four of magnitude 7 or greater. Small earthquakes are far more common than big ones.

**Check it.** The same facts, straight from the data — the count in each of ten bins, then the bin edges:

The following is Python code:

```python
counts, edges = np.histogram(df.mag, bins=10)
print(counts.tolist())
print(edges.round(2).tolist())
```

End of code.

The following is the expected output:

```
[2125, 2546, 869, 211, 105, 46, 9, 8, 1, 3]
[4.02, 4.39, 4.76, 5.12, 5.49, 5.86, 6.23, 6.6, 6.96, 7.33, 7.7]
```

End of output.

## An Example

What if we wanted to know which **country** had the most earthquakes? `groupby` does this cleanly — it groups all rows that share a value, then we can apply an aggregation (here, `count`) to summarize each group:

The following is Python code:

```python
df.groupby('country').mag.count().nlargest(20).plot(kind='bar', figsize=(12, 6))
plt.show()
```

End of code.

**What this shows.** A bar chart of twenty vertical blue bars, tallest first, with country names as rotated labels along the x-axis. Indonesia leads with 602 events, followed by Papua New Guinea (518), Greece (377), Japan (295), the Philippines (238), Tonga (231), and Chile (217); the counts then taper smoothly down to China (76) in twentieth place. Most of the names trace the Pacific "Ring of Fire".

**Check it.** Print the top of that same Series as a dict:

The following is Python code:

```python
print(df.groupby('country').mag.count().nlargest(5).to_dict())
```

End of code.

The following is the expected output:

```
{'Indonesia': 602, 'Papua New Guinea': 518, 'Greece': 377, 'Japan': 295, 'Philippines': 238}
```

End of output.

And the same count for the *smaller* events (`df_small`, magnitude below 4) — notice how the ranking of countries shifts:

The following is Python code:

```python
df_small.groupby('country').mag.count().nlargest(20).plot(kind='bar', figsize=(12, 6))
plt.show()
```

End of code.

**What this shows.** The same layout, but a completely different story: one bar towers over everything — Alaska, with 2244 events, more than four times the runner-up Puerto Rico (487), then "CA" (450), New Mexico (160), and Hawaii (138), trailing off through a list of other US states and territories. Nearly every name is in the United States: quakes below magnitude 4 mostly get *cataloged* where the dense US seismic network can detect them, so this chart is as much about where the sensors are as about where earthquakes are. (Note also that "CA" and "California" appear as *separate* bars — the `country` column inherits every inconsistency of the free-text `place` field.)

**Check it.**

The following is Python code:

```python
print(df_small.groupby('country').mag.count().nlargest(5).to_dict())
```

End of code.

The following is the expected output:

```
{'Alaska': 2244, 'Puerto Rico': 487, 'CA': 450, 'New Mexico': 160, 'Hawaii': 138}
```

End of output.

## What Happened?

Let's break down what `groupby` actually does. The `.groupby(...)` call doesn't immediately compute anything — it returns a `GroupBy` object that holds the grouping logic. Computation happens when you call an aggregation method on it (`.count()`, `.mean()`, `.aggregate(...)`, etc.). This pattern is sometimes called **split-apply-combine**:

1. **Split** the data into groups based on a column or function.
2. **Apply** a function (aggregate, transform, or filter) to each group independently.
3. **Combine** the results back into a single DataFrame or Series.

We can group by any Series — here, the `country` column itself. (It's a Series of 5923 country names, indexed by event id; we print its first five entries as a dict.)

The following is Python code:

```python
print(df.country.head().to_dict())
print(len(df.country))
```

End of code.

The following is the expected output:

```
{'us6000qkt1': 'Tonga', 'us6000qksf': 'Tonga', 'us6000qks2': 'Philippines', 'us6000qkrz': 'Russia', 'us6000qks0': 'southern Mid-Atlantic Ridge'}
5923
```

End of output.

The following is Python code:

```python
print(df.groupby(df.country))
```

End of code.

The hexadecimal address after `at` will be different on your machine — it's just where the object lives in memory.

The following is the expected output:

```
<pandas.api.typing.DataFrameGroupBy object at 0x10677bb00>
```

End of output.

There is a shortcut for doing this with dataframes: you just pass the column name:

The following is Python code:

```python
print(df.groupby('country'))
```

End of code.

The following is the expected output (again, your memory address will differ):

```
<pandas.api.typing.DataFrameGroupBy object at 0x104e63320>
```

End of output.

### The `GroupBy` object

When we call `groupby`, we get back a `GroupBy` object. Note what *hasn't* happened: no counting, no averaging — nothing has been computed yet:

The following is Python code:

```python
gb = df.groupby('country')
print(gb)
```

End of code.

The following is the expected output (your memory address will differ):

```
<pandas.api.typing.DataFrameGroupBy object at 0x104e63320>
```

End of output.

The length tells us how many groups were found:

The following is Python code:

```python
print(len(gb))
```

End of code.

The following is the expected output:

```
194
```

End of output.

All of the groups are available as a dictionary via the `.groups` attribute:

The following is Python code:

```python
groups = gb.groups
print(len(groups))
```

End of code.

The following is the expected output:

```
194
```

End of output.

Its keys are the 194 group labels — the country names, in alphabetical order. The original notebook prints all of them; here we print the first ten:

The following is Python code:

```python
print(list(groups.keys())[:10])
```

End of code.

The following is the expected output:

```
['2025 Drake Passage Earthquake', 'Afghanistan', 'Alaska', 'Algeria', 'Anguilla', 'Antigua and Barbuda', 'Arctic Ocean', 'Argentina', 'Armenia', 'Ascension Island region']
```

End of output.

Listen to those keys: alongside real countries like Afghanistan sit oddities like `'2025 Drake Passage Earthquake'` and `'Arctic Ocean'` — whatever happened to come after the last comma in the `place` string. Real data is messy.

### Selecting and iterating over groups

The `.groups` attribute is a regular Python dictionary — its keys are the group labels and its values are the row indices in each group. You can look up a single group by name:

The following is Python code:

```python
afghanistan_rows = groups['Afghanistan']
print(len(afghanistan_rows))
print(list(afghanistan_rows[:5]))
```

End of code.

The following is the expected output:

```
51
['us6000qkkm', 'us6000qjhm', 'us6000qiif', 'us6000qhyi', 'us6000qh0s']
```

End of output.

So Afghanistan had 51 large earthquakes in this catalog, and the value is an index of their row labels (event ids).

You can also loop over the groups directly. Each step of the loop gives you the group's key and the sub-DataFrame of its rows. (The notebook version uses `display(group.head())` here — `display` only exists in Jupyter, so in a script we print the rows as dicts instead. The `break` stops the loop after the first group.)

The following is Python code:

```python
for key, group in gb:
    print(f'The key is "{key}"')
    print(group.shape)
    for _, row in group[['time', 'mag', 'place']].head().iterrows():
        print(row.to_dict())
    break
```

End of code.

The following is the expected output:

```
The key is "2025 Drake Passage Earthquake"
(1, 22)
{'time': Timestamp('2025-05-02 12:58:26.014000+0000', tz='UTC'), 'mag': 7.4, 'place': '2025 Drake Passage Earthquake'}
```

End of output.

The first group (alphabetically) is that odd `'2025 Drake Passage Earthquake'` key — a "country" containing exactly one row, a magnitude-7.4 event whose `place` string had no comma-separated country at all.

Finally, you can pull out one group as its own DataFrame with `get_group`:

The following is Python code:

```python
chile = gb.get_group('Chile')
print(chile.shape)
for _, row in chile[['time', 'mag', 'place']].head().iterrows():
    print(row.to_dict())
```

End of code.

The following is the expected output:

```
(217, 22)
{'time': Timestamp('2025-06-16 08:19:27.151000+0000', tz='UTC'), 'mag': 4.4, 'place': '46 km NE of San Pedro de Atacama, Chile'}
{'time': Timestamp('2025-06-16 00:51:17.310000+0000', tz='UTC'), 'mag': 4.4, 'place': '13 km WSW of La Tirana, Chile'}
{'time': Timestamp('2025-06-15 13:30:08.837000+0000', tz='UTC'), 'mag': 4.3, 'place': '28 km ENE of Calama, Chile'}
{'time': Timestamp('2025-06-11 23:53:41.988000+0000', tz='UTC'), 'mag': 4.1, 'place': '89 km NNW of Calama, Chile'}
{'time': Timestamp('2025-06-09 00:59:17.293000+0000', tz='UTC'), 'mag': 4.1, 'place': '26 km NNE of La Ligua, Chile'}
```

End of output.

:::{admonition} Try it
:class: tip
Take `df`. Use `groupby` to count the number of earthquakes per country and plot the top 10 as a bar chart (`df.groupby('country').mag.count().nlargest(10).plot.bar()`, then `plt.show()`). To hear the same ranking without the figure, print the ten counts with `.to_dict()`. Then use `gb.get_group('Chile')` to peek at the rows for a specific country — print its `shape` and a few rows as dicts, as above.
:::

## Aggregation

Now that we know how to create a `GroupBy` object, let's learn how to do aggregation on it.

One way is to use the `.aggregate` method, which accepts another function as its argument. The result is automatically combined into a new dataframe with the group key as the index — 194 rows, one per country. We print its shape and the first three rows (three columns each, so they read cleanly):

The following is Python code:

```python
agg_max = gb.aggregate('max')
print(agg_max.shape)
for country, row in agg_max[['time', 'mag', 'depth']].head(3).iterrows():
    print(country, row.to_dict())
```

End of code.

The following is the expected output:

```
(194, 21)
2025 Drake Passage Earthquake {'time': Timestamp('2025-05-02 12:58:26.014000+0000', tz='UTC'), 'mag': 7.4, 'depth': 10.0}
Afghanistan {'time': Timestamp('2025-06-16 10:34:13.071000+0000', tz='UTC'), 'mag': 5.7, 'depth': 230.241}
Alaska {'time': Timestamp('2025-06-14 14:32:06.018000+0000', tz='UTC'), 'mag': 6.2, 'depth': 205.536}
```

End of output.

By default, the operation is applied to every column — the max *time*, the max *magnitude*, the max *depth*, all independently. That's usually not what we want. We can use both `.` or `[]` syntax to select a specific column to operate on. Then we get back a series.

The following is Python code:

```python
print(gb.mag)
```

End of code.

The following is the expected output (a `SeriesGroupBy` — the same grouping, restricted to one column; your memory address will differ):

```
<pandas.api.typing.SeriesGroupBy object at 0x112e82f60>
```

End of output.

The following is Python code:

```python
max_mag = gb.mag.aggregate('max')
print(len(max_mag))
print(max_mag.head().to_dict())
```

End of code.

The following is the expected output:

```
194
{'2025 Drake Passage Earthquake': 7.4, 'Afghanistan': 5.7, 'Alaska': 6.2, 'Algeria': 4.8, 'Anguilla': 4.5}
```

End of output.

One number per country: the strongest earthquake in each. Any aggregation function works the same way — here `'sum'` and `'mean'` (we `round` before printing so the values read cleanly):

The following is Python code:

```python
print(gb.mag.aggregate('sum').head().round(2).to_dict())
print(gb.mag.aggregate('mean').head().round(2).to_dict())
```

End of code.

The following is the expected output:

```
{'2025 Drake Passage Earthquake': 7.4, 'Afghanistan': 221.7, 'Alaska': 583.3, 'Algeria': 4.8, 'Anguilla': 13.1}
{'2025 Drake Passage Earthquake': 7.4, 'Afghanistan': 4.35, 'Alaska': 4.56, 'Algeria': 4.8, 'Anguilla': 4.37}
```

End of output.

Chaining `.nlargest(10)` onto the result sorts it and keeps the top ten — which countries had the strongest single event?

The following is Python code:

```python
print(gb.mag.aggregate('max').nlargest(10).to_dict())
```

End of code.

The following is the expected output:

```
{'Burma (Myanmar) Earthquake': 7.7, 'Cayman Islands': 7.6, '2025 Drake Passage Earthquake': 7.4, 'Tonga': 7.0, 'Papua New Guinea': 6.9, 'Reykjanes Ridge': 6.9, 'Japan': 6.8, 'Macquarie Island region': 6.8, 'Burma (Myanmar)': 6.7, 'New Zealand': 6.7}
```

End of output.

The magnitude-7.7 event at the top is the devastating March 2025 Myanmar earthquake — filed under the label `'Burma (Myanmar) Earthquake'` (and note `'Burma (Myanmar)'` appears separately further down: messy place strings again).

There are shortcuts for common aggregation functions — `.max()`, `.min()`, `.mean()`, `.std()` instead of `.aggregate('max')` and so on:

The following is Python code:

```python
print(gb.mag.max().nlargest(10).to_dict())
print(gb.mag.min().nsmallest(10).to_dict())
print(gb.mag.mean().nlargest(10).round(2).to_dict())
print(gb.mag.std().nlargest(10).round(2).to_dict())
```

End of code.

The following is the expected output (four dicts: largest maxima, smallest minima, largest means, largest standard deviations):

```
{'Burma (Myanmar) Earthquake': 7.7, 'Cayman Islands': 7.6, '2025 Drake Passage Earthquake': 7.4, 'Tonga': 7.0, 'Papua New Guinea': 6.9, 'Reykjanes Ridge': 6.9, 'Japan': 6.8, 'Macquarie Island region': 6.8, 'Burma (Myanmar)': 6.7, 'New Zealand': 6.7}
{'Puerto Rico': 4.02, 'U.S. Virgin Islands': 4.05, 'CA': 4.06, 'Dominican Republic': 4.06, 'Afghanistan': 4.1, 'Alaska': 4.1, 'Argentina': 4.1, 'Australia': 4.1, 'Bahamas': 4.1, 'Bolivia': 4.1}
{'Burma (Myanmar) Earthquake': 7.7, '2025 Drake Passage Earthquake': 7.4, 'Western Caribbean Sea': 5.4, 'north of Franz Josef Land': 5.3, 'Macquarie Island region': 5.23, 'southeast central Pacific Ocean': 5.2, 'Morocco': 5.1, 'Pacific-Antarctic Ridge': 5.07, 'South Atlantic Ocean': 5.05, 'east of the South Sandwich Islands': 5.0}
{'Cayman Islands': 0.98, 'U.S. Virgin Islands': 0.91, 'Macquarie Island region': 0.9, 'southwest of Africa': 0.76, 'Revilla Gigedo Islands region': 0.73, 'Svalbard and Jan Mayen': 0.68, 'India region': 0.64, 'northwest of the Kuril Islands': 0.64, 'Panama': 0.62, 'Reykjanes Ridge': 0.61}
```

End of output.

We can also apply multiple functions at once — the result is a small DataFrame with one column per function:

The following is Python code:

```python
mag_stats = gb.mag.aggregate(['min', 'max', 'mean'])
print(mag_stats.shape)
print(mag_stats.columns.tolist())
for country, row in mag_stats.head().round(2).iterrows():
    print(country, row.to_dict())
```

End of code.

The following is the expected output:

```
(194, 3)
['min', 'max', 'mean']
2025 Drake Passage Earthquake {'min': 7.4, 'max': 7.4, 'mean': 7.4}
Afghanistan {'min': 4.1, 'max': 5.7, 'mean': 4.35}
Alaska {'min': 4.1, 'max': 6.2, 'mean': 4.56}
Algeria {'min': 4.8, 'max': 4.8, 'mean': 4.8}
Anguilla {'min': 4.2, 'max': 4.5, 'mean': 4.37}
```

End of output.

The following is Python code:

```python
mag_stats.nlargest(10, 'mean').plot(kind='bar')
plt.show()
```

End of code.

**What this shows.** A grouped bar chart: the ten countries with the highest *mean* magnitude along the x-axis, each with three side-by-side bars — min in blue, max in orange, mean in green — and a legend in the top-right. For the first two groups, "Burma (Myanmar) Earthquake" (all bars at 7.7) and "2025 Drake Passage Earthquake" (all at 7.4), the three bars are identical heights: those "countries" contain a single event, so min = max = mean. Several other groups are single-event oddities too, but "Macquarie Island region" (min 4.4, max 6.8) and "Pacific-Antarctic Ridge" (min 4.5, max 6.3) show a wide gap between their blue and orange bars. A ranking by mean magnitude mostly surfaces places with very few recorded events — a nice lesson in why you should always check group sizes.

**Check it.** The ten group labels on the x-axis, in plotted order:

The following is Python code:

```python
print(mag_stats.nlargest(10, 'mean').index.tolist())
```

End of code.

The following is the expected output:

```
['Burma (Myanmar) Earthquake', '2025 Drake Passage Earthquake', 'Western Caribbean Sea', 'north of Franz Josef Land', 'Macquarie Island region', 'southeast central Pacific Ocean', 'Morocco', 'Pacific-Antarctic Ridge', 'South Atlantic Ocean', 'east of the South Sandwich Islands']
```

End of output.

:::{admonition} Try it
:class: tip
Group the earthquakes by country and use `.aggregate(['min', 'max', 'mean'])` on the `mag` column to get all three statistics at once. Then sort by the mean magnitude in descending order and grab the top 10 countries. Print the result rows as dicts, as above — you should hear the same ten countries as in the Check-it.
:::

## Transformation

The key difference between aggregation and transformation is that aggregation returns a *smaller* object than the original, indexed by the group keys, while *transformation* returns an object with the same index (and same size) as the original object. Groupby + transformation is used when applying an operation that requires information about the whole group.

In this example, we standardize the earthquakes in each country so that the distribution has zero mean and unit variance. We do this by first defining a function called `standardize` and then passing it to the `transform` method.

I admit that I don't know why you would want to do this. `transform` makes more sense to me in the context of time grouping operation. See below for another example.

The following is Python code:

```python
def standardize(x):
    return (x - x.mean()) / x.std()

mag_standardized_by_country = gb.mag.transform(standardize)
print(len(mag_standardized_by_country))
print(mag_standardized_by_country.head().round(3).to_dict())
```

End of code.

The following is the expected output:

```
5923
{'us6000qkt1': 0.668, 'us6000qksf': 0.419, 'us6000qks2': 2.158, 'us6000qkrz': 0.643, 'us6000qks0': 0.91}
```

End of output.

Same length as `df` (5923 — one value per *row*, not per group), and the values are now in "standard deviations above/below that country's mean": the first event, a magnitude 4.9 in Tonga, is 0.668 standard deviations above the Tongan average.

**Check it.** Within any one country, the transformed values should have mean 0 and standard deviation 1:

The following is Python code:

```python
chile_std = mag_standardized_by_country[df.country == 'Chile']
print(round(chile_std.mean(), 10))
print(round(chile_std.std(), 10))
```

End of code.

The following is the expected output:

```
-0.0
1.0
```

End of output.

:::{admonition} Try it
:class: tip
Define a function `standardize(x)` that returns `(x - x.mean()) / x.std()`. Use it with `df.groupby('country').mag.transform(standardize)` and plot a histogram of the result (`.plot.hist()`, then `plt.show()`) — magnitudes should now be approximately centered on zero within each country. To confirm without the figure, print the overall `.mean()` (close to 0) and repeat the Chile check above for a country of your choice.
:::

## Time Grouping

We already saw how pandas has a strong built-in understanding of time. This capability is even more powerful in the context of `groupby`. With datasets indexed by a pandas `DateTimeIndex`, we can easily group and resample the data using common time units.

To get started, let's load the timeseries data we already explored in past lessons — three years (2016–2018) of daily weather-station measurements from Millbrook, New York. The first run downloads the four files from Zenodo and caches them (you may hear pooch's download progress messages); later runs reuse the cache. Note this block reuses the name `df` — from here on, `df` is the weather data, not the earthquakes.

The following is Python code:

```python
import pooch

POOCH = pooch.create(
    path=pooch.os_cache("noaa-data"),
    base_url="doi:10.5281/zenodo.5553029/",
    registry={
        "HEADERS.txt": "md5:2a306ca225fe3ccb72a98953ded2f536",
        "CRND0103-2016-NY_Millbrook_3_W.txt": "md5:eb69811d14d0573ffa69f70dd9c768d9",
        "CRND0103-2017-NY_Millbrook_3_W.txt": "md5:b911da727ba1bdf26a34a775f25d1088",
        "CRND0103-2018-NY_Millbrook_3_W.txt": "md5:5b61bc687261596eba83801d7080dc56",
    },
)

with open(POOCH.fetch("HEADERS.txt")) as fp:
    headers = fp.read().split("\n")[1].split(" ")

dframes = []
for year in range(2016, 2019):
    fname = f"CRND0103-{year}-NY_Millbrook_3_W.txt"
    df = pd.read_csv(POOCH.fetch(fname), parse_dates=[1],
                     names=headers, header=None, sep=r"\s+",
                     na_values=[-9999.0, -99.0])
    dframes.append(df)

df = pd.concat(dframes)
df = df.set_index("LST_DATE")

print(df.shape)
print(df.columns.tolist()[:10])
for date, row in df[['T_DAILY_MEAN', 'T_DAILY_MAX', 'T_DAILY_MIN', 'P_DAILY_CALC']].head(3).iterrows():
    print(date.date(), row.to_dict())
```

End of code.

The following is the expected output (we print only the first 10 of the 28 column names, and three rows of the four columns we'll use most — daily mean/max/min temperature in degrees Celsius and daily precipitation in millimeters):

```
(1096, 28)
['WBANNO', 'CRX_VN', 'LONGITUDE', 'LATITUDE', 'T_DAILY_MAX', 'T_DAILY_MIN', 'T_DAILY_MEAN', 'T_DAILY_AVG', 'P_DAILY_CALC', 'SOLARAD_DAILY']
2016-01-01 {'T_DAILY_MEAN': 1.5, 'T_DAILY_MAX': 3.4, 'T_DAILY_MIN': -0.5, 'P_DAILY_CALC': 0.0}
2016-01-02 {'T_DAILY_MEAN': -0.4, 'T_DAILY_MAX': 2.9, 'T_DAILY_MIN': -3.6, 'P_DAILY_CALC': 0.0}
2016-01-03 {'T_DAILY_MEAN': 1.6, 'T_DAILY_MAX': 5.1, 'T_DAILY_MIN': -1.8, 'P_DAILY_CALC': 0.0}
```

End of output.

This timeseries has daily resolution — 1096 rows is three years of days — and the daily plots are somewhat noisy.

The following is Python code:

```python
df.T_DAILY_MEAN.plot()
plt.show()
```

End of code.

**What this shows.** A noisy blue line spanning January 2016 to December 2018 with three clear annual cycles: each summer the daily-mean temperature climbs into the mid-20s °C (topping out at 28.1), and each winter it plunges below zero, bottoming out at −18.4 °C in February 2016. On top of the smooth seasonal swing the line jitters up and down by 5–10 degrees from day to day — that jitter is the weather.

**Check it.**

The following is Python code:

```python
print(df.T_DAILY_MEAN.min(), df.T_DAILY_MEAN.max())
```

End of code.

The following is the expected output:

```
-18.4 28.1
```

End of output.

A common way to analyze such data in climate science is to create a "climatology," which contains the average values in each month or day of the year. We can do this easily with groupby. Recall that `df.index` is a pandas `DateTimeIndex` object. (Its printed form below is several lines long but linear — listen for the first dates, an ellipsis, the last dates, then `dtype`, `name`, and `length=1096`.)

The following is Python code:

```python
print(df.index)
```

End of code.

The following is the expected output:

```
DatetimeIndex(['2016-01-01', '2016-01-02', '2016-01-03', '2016-01-04',
               '2016-01-05', '2016-01-06', '2016-01-07', '2016-01-08',
               '2016-01-09', '2016-01-10',
               ...
               '2018-12-22', '2018-12-23', '2018-12-24', '2018-12-25',
               '2018-12-26', '2018-12-27', '2018-12-28', '2018-12-29',
               '2018-12-30', '2018-12-31'],
              dtype='datetime64[us]', name='LST_DATE', length=1096, freq=None)
```

End of output.

A `DatetimeIndex` can hand us any component of every date at once — here, the month number of each of the 1096 days:

The following is Python code:

```python
print(df.index.month)
```

End of code.

The following is the expected output:

```
Index([ 1,  1,  1,  1,  1,  1,  1,  1,  1,  1,
       ...
       12, 12, 12, 12, 12, 12, 12, 12, 12, 12],
      dtype='int32', name='LST_DATE', length=1096)
```

End of output.

Now we group the numeric columns by that month number and take the mean. The result has just 12 rows — one per month. We print its shape, and then listen to one column as a dict, keyed by month number (1=January, 2=February, etc.):

The following is Python code:

```python
monthly_climatology = df.select_dtypes(include='number').groupby(df.index.month).mean()
print(monthly_climatology.shape)
print(monthly_climatology.T_DAILY_MEAN.round(2).to_dict())
```

End of code.

The following is the expected output:

```
(12, 27)
{1: -2.1, 2: 0.71, 3: 2.46, 4: 8.3, 5: 14.85, 6: 18.73, 7: 22.05, 8: 21.41, 9: 18.06, 10: 11.94, 11: 4.1, 12: -1.07}
```

End of output.

There's the seasonal cycle in twelve numbers: January averages −2.1 °C, July 22.05 °C.

The following is Python code:

```python
monthly_climatology.T_DAILY_MAX.plot()
plt.show()
```

End of code.

**What this shows.** A single smooth line over an x-axis running from 1 to 12 (the month number): the average daily-maximum temperature starts near 2.9 °C in January, rises steadily through spring, peaks at 28.6 °C in July, and descends to 3.6 °C in December. One clean arch — the 1096 noisy days have collapsed into 12 averaged points.

Each row in this new dataframe represents the average values for the months (1=January, 2=February, etc.)

We can apply more customized aggregations, as with any groupby operation. Below we keep the mean of the mean, max of the max, and min of the min for the temperature measurements. This result is small enough to print in full, one month per line:

The following is Python code:

```python
monthly_T_climatology = df.groupby(df.index.month).aggregate({'T_DAILY_MEAN': 'mean',
                                                              'T_DAILY_MAX': 'max',
                                                              'T_DAILY_MIN': 'min'})
for month, row in monthly_T_climatology.round(2).iterrows():
    print(month, row.to_dict())
```

End of code.

The following is the expected output:

```
1 {'T_DAILY_MEAN': -2.1, 'T_DAILY_MAX': 16.9, 'T_DAILY_MIN': -26.0}
2 {'T_DAILY_MEAN': 0.71, 'T_DAILY_MAX': 24.9, 'T_DAILY_MIN': -24.7}
3 {'T_DAILY_MEAN': 2.46, 'T_DAILY_MAX': 26.8, 'T_DAILY_MIN': -16.5}
4 {'T_DAILY_MEAN': 8.3, 'T_DAILY_MAX': 30.6, 'T_DAILY_MIN': -11.3}
5 {'T_DAILY_MEAN': 14.85, 'T_DAILY_MAX': 33.4, 'T_DAILY_MIN': -1.6}
6 {'T_DAILY_MEAN': 18.73, 'T_DAILY_MAX': 33.8, 'T_DAILY_MIN': 3.4}
7 {'T_DAILY_MEAN': 22.05, 'T_DAILY_MAX': 35.7, 'T_DAILY_MIN': 8.2}
8 {'T_DAILY_MEAN': 21.41, 'T_DAILY_MAX': 34.5, 'T_DAILY_MIN': 6.0}
9 {'T_DAILY_MEAN': 18.06, 'T_DAILY_MAX': 32.7, 'T_DAILY_MIN': 0.3}
10 {'T_DAILY_MEAN': 11.94, 'T_DAILY_MAX': 29.4, 'T_DAILY_MIN': -4.1}
11 {'T_DAILY_MEAN': 4.1, 'T_DAILY_MAX': 22.2, 'T_DAILY_MIN': -15.9}
12 {'T_DAILY_MEAN': -1.07, 'T_DAILY_MAX': 16.9, 'T_DAILY_MIN': -21.8}
```

End of output.

The following is Python code:

```python
ax = monthly_T_climatology.plot(marker='o')
plt.show()
```

End of code.

**What this shows.** Three lines with circle markers over months 1–12, named in a legend. T_DAILY_MAX (orange, on top) runs from 16.9 °C in January up to 35.7 in July; T_DAILY_MEAN (blue, in the middle) from −2.1 up to 22.05; T_DAILY_MIN (green, at the bottom) from −26.0 up to 8.2. All three arch in phase — warmest in July, coldest in January. The band between the outer curves is wide (over 40 degrees in winter) because we took the max-of-max and min-of-min: these are each month's record extremes over three years, bracketing the mean, not typical highs and lows.

**Check it.** Interrogate the axes object we captured:

The following is Python code:

```python
print(len(ax.lines))
print([line.get_label() for line in ax.lines])
```

End of code.

The following is the expected output:

```
3
['T_DAILY_MEAN', 'T_DAILY_MAX', 'T_DAILY_MIN']
```

End of output.

If we want to do it on a finer scale, we can group by day of year.

The following is Python code:

```python
daily_T_climatology = df.groupby(df.index.dayofyear).aggregate({'T_DAILY_MEAN': 'mean',
                                                                'T_DAILY_MAX': 'max',
                                                                'T_DAILY_MIN': 'min'})
print(daily_T_climatology.shape)
daily_T_climatology.plot(marker='.')
plt.show()
```

End of code.

This first prints the new shape — 366 groups, one per day of the year (including February 29).

The following is the expected output:

```
(366, 3)
```

End of output.

**What this shows.** The same three-colored curves — orange max on top, blue mean in the middle, green min at the bottom — but now over an x-axis of day-of-year, 1 to 366, drawn with small dot markers. The overall arch is the same (cold at both ends, peaking around day 200, in late July), but each curve zigzags up and down by several degrees from one day to the next: every point averages at most three values (one per year), so the day-of-year climatology is much noisier than the monthly one.

### Calculating anomalies

A common mode of analysis in climate science is to remove the climatology from a signal to focus only on the "anomaly" values. This can be accomplished with transformation — the same `transform` idea as before, now with time as the grouping key: from each day, subtract the mean of its calendar month.

The following is Python code:

```python
def remove_climatology(x):
    return x - x.mean()

anomaly = df.groupby(df.index.month).T_DAILY_MEAN.transform(remove_climatology)
print(len(anomaly))
for date, value in anomaly.head().items():
    print(date.date(), round(value, 2))
```

End of code.

The following is the expected output:

```
1096
2016-01-01 3.6
2016-01-02 1.7
2016-01-03 3.7
2016-01-04 -4.8
2016-01-05 -8.2
```

End of output.

Again the transform returns one value per *row* — 1096 daily anomalies. January 1, 2016 (daily mean 1.5 °C) was 3.6 degrees warmer than an average January day (−2.1 °C).

The following is Python code:

```python
anomaly.plot()
plt.show()
```

End of code.

**What this shows.** A noisy blue line oscillating around zero across the full 2016–2018 record. The seasonal arch is gone — no summer humps, no winter dips — leaving day-to-day and week-to-week weather swings that mostly stay within about ±10 °C. The largest excursions are a cold spike to −19.1 °C in early 2016 and a warm spike to +15.6 °C in early 2018, and warm and cold anomalies occur in every season.

**Check it.** Anomalies should average to (essentially) zero, and the extremes match the description:

The following is Python code:

```python
print(round(anomaly.mean(), 4), round(anomaly.min(), 2), round(anomaly.max(), 2))
```

End of code.

The following is the expected output:

```
0.0 -19.11 15.59
```

End of output.

:::{admonition} Try it
:class: tip
Compute a **daily** climatology of `T_DAILY_MEAN` by grouping on `df.index.dayofyear` and taking the mean. Plot it as a line chart. Compare its shape to the monthly climatology shown above — the daily curve will be noisier but pick up sub-monthly structure. (A non-visual comparison: print the daily climatology's value for day 1 and day 200 and compare them with the January and July numbers you heard in the monthly climatology dict.)
:::

### Resampling

We met `resample` at the end of the previous page (*Pandas Fundamentals*) as a way to change a time series' resolution. Now that we understand `groupby`, we can name what `resample` actually is: **a `groupby` over time bins**. Instead of grouping rows by the value in a column, it groups them by which time interval they fall into — each month, each year, and so on — then applies an aggregation. It's the same split-apply-combine idea, just with time as the grouping key.

The bin size is given using pandas [offset-alias](http://pandas.pydata.org/pandas-docs/stable/timeseries.html#offset-aliases) syntax (e.g. `'ME'` = month end, `'YE'` = year end). Below we resample to a monthly frequency, taking the mean over each month. Note the difference from the monthly *climatology* above: that had 12 rows (all Januaries averaged together); this has 36 — January 2016, February 2016, and so on each get their own row, labelled by the month's end date.

The following is Python code:

```python
monthly_means = df.select_dtypes(include='number').resample('ME').mean()
print(monthly_means.shape)
for date, value in monthly_means.T_DAILY_MEAN.head(6).round(2).items():
    print(date.date(), value)
monthly_means.plot(y='T_DAILY_MEAN', marker='o')
plt.show()
```

End of code.

The following is the expected output:

```
(36, 27)
2016-01-31 -1.86
2016-02-29 -0.26
2016-03-31 6.25
2016-04-30 7.73
2016-05-31 14.58
2016-06-30 18.8
```

End of output.

**What this shows.** A line with circle markers, one point per month-end, from January 2016 to December 2018 — the seasonal cycle again, but far smoother than the daily plot. Summer peaks reach about 22–23 °C (highest in July 2016 and August 2018), winter minima vary a lot from year to year — from only about −0.7 °C (December 2016) down to the coldest monthly mean, about −4.4 °C, in January 2018. A small legend box labels the line "T_DAILY_MEAN".

Just as easily, we can take the standard deviation *within* each month instead of the mean:

The following is Python code:

```python
df.select_dtypes(include='number').resample('ME').std().plot(y='T_DAILY_MEAN', marker='o')
plt.show()
```

End of code.

**What this shows.** The same 36 month-end points, but now showing each month's internal spread, between about 2.4 and 7.6 °C — and the pattern is *opposite* to the mean: the standard deviation is largest in winter and smallest in summer. Winter temperatures at this station are far more variable than summer ones.

**Check it.** Which months were the most and least variable?

The following is Python code:

```python
monthly_std = df.select_dtypes(include='number').resample('ME').std().T_DAILY_MEAN
print(monthly_std.idxmax().date(), round(monthly_std.max(), 2))
print(monthly_std.idxmin().date(), round(monthly_std.min(), 2))
```

End of code.

The following is the expected output:

```
2016-02-29 7.64
2018-08-31 2.43
```

End of output.

Just like with `groupby`, we can apply any aggregation function to our `resample` operation.

The following is Python code:

```python
df.select_dtypes(include='number').resample('ME').max().plot(y='T_DAILY_MAX', marker='o')
plt.show()
```

End of code.

**What this shows.** One marker per month: the warmest afternoon of each month. It follows the seasonal cycle — peaking at 35.7 °C in July 2016, with winter values mostly between 13 and 17 °C — but the winter portion is jagged: isolated spikes of 23.5 °C in February 2017 and 24.9 °C in February 2018 mark mid-winter warm spells that a monthly *mean* would have hidden.

:::{admonition} Try it
:class: tip
Use `df.resample('YE').max()` to get the yearly maximum and plot `T_DAILY_MAX` against the resulting yearly index. With only three years there are just three values — printing them as a dict is at least as informative as the plot. Compare with the monthly resampling shown above — what trend, if any, do you see?
:::

### Rolling Operations

The final category of operations applies to "rolling windows". (See [rolling](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.rolling.html) documentation.) We specify a function to apply over a moving window along the index. We specify the size of the window and, optionally, the weights. We also use the keyword `center` to tell pandas whether to center the operation around the midpoint of the window. Unlike `resample`, rolling keeps one value per original row — each day's value becomes the average of the 30 days around it.

The following is Python code:

```python
df.rolling(30, center=True).T_DAILY_MEAN.mean().plot()
df.rolling(30, center=True, win_type='triang').T_DAILY_MEAN.mean().plot()
ax = plt.gca()
plt.show()
```

End of code.

**What this shows.** Two nearly overlapping smooth curves tracing the three-year seasonal cycle, roughly between −9 and +24 °C: blue for the plain 30-day average, orange for the triangle-weighted version (`win_type='triang'` weights the center of each window most heavily). The triangle-weighted curve is a little smoother and swings slightly wider at the extremes — in the January 2018 cold snap it dips to about −9.5 °C while the plain average bottoms out near −7.4. Both lines also *break* — short gaps in late 2017 and in autumn 2018 where the curve simply stops and restarts. The Check-it explains why.

**Check it.** Two lines are on the axes; and the record contains 2 missing days of `T_DAILY_MEAN`, which poison every 30-day window that touches them (plus the half-windows at the very start and end of the record) — 89 NaN values in the smoothed series, which plot as gaps:

The following is Python code:

```python
print(len(ax.lines))
smoothed = df.rolling(30, center=True).T_DAILY_MEAN.mean()
print(df.T_DAILY_MEAN.isna().sum())
print(smoothed.isna().sum())
```

End of code.

The following is the expected output:

```
2
2
89
```

End of output.

Any aggregation works on a rolling window, not just the mean — here, a running 30-day maximum:

The following is Python code:

```python
df.rolling(30, center=True).T_DAILY_MEAN.max().plot()
plt.show()
```

End of code.

**What this shows.** A staircase-like line between 5.5 and 28.1 °C: long flat plateaus connected by abrupt jumps, rising each spring and falling each autumn, with the same two short gaps from the missing data. That staircase shape is exactly what a moving maximum does — the value stays constant for as long as the same warm day remains the warmest inside the 30-day window, then jumps when a warmer day enters or the record-holder falls out.

:::{admonition} Try it
:class: tip
Use `df.rolling(7, center=True).T_DAILY_MEAN.mean()` to compute a 7-day moving average and plot it. Then overlay the raw daily series on the same axes (call `.plot()` twice before `plt.show()`, or use `fig, ax = plt.subplots()` and call `.plot(ax=ax)` for each). Confirm the overlay non-visually with `len(ax.lines)` (should be 2), and compare `.std()` of the smoothed and raw series — what does smoothing reveal that the raw data hides?
:::

## Recap

This page was all about **split–apply–combine**:

- **`groupby` on a column** — grouping earthquakes by country, then summarizing each group with **aggregation** (`count`, `mean`, `.aggregate([...])`).
- **Transformation** — returning a value for every row using whole-group information (standardizing within each group; removing a climatology to get anomalies).
- **Time grouping** — grouping by parts of a `DatetimeIndex` to build a **climatology**.
- **`resample`** — a `groupby` over time bins, for changing time resolution.
- **`rolling`** — moving-window calculations for smoothing.

These same `groupby` / `resample` / `rolling` ideas carry straight into the next section, **Xarray**, where they work on labelled N-dimensional arrays.
