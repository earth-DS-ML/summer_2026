# Pandas Fundamentals

:::{admonition} Following along with a screen reader
:class: important
Work through the examples as `.py` scripts in VS Code — see [Setting up an accessible workflow](../computing_env/accessible_setup.md) and [Working with Python scripts](../computing_env/working_with_scripts.md). Run a script in the Git Bash terminal with `python yourfile.py`, and press `Ctrl+Up` to read the output. This page needs `pandas` and `pooch` (`pip install pandas pooch` if you don't have them), and the weather-station section downloads a data file, so be online for that part.
:::

:::{admonition} In-class assignment — 10 points
:class: note
The **"Try it"** exercises on this page are part of your in-class assignment for this section. Complete them as `.py` scripts, push them to your week folder, and post the link on the matching **Courseworks** assignment. (One 10-point assignment covers all the lecture pages in this section.)
:::

One script-writing reminder before we start: in a notebook, a bare expression at the end of a cell displays itself; in a script, a bare expression displays **nothing**. Every example below therefore wraps results in `print(...)` — do the same in your own scripts.

:::{admonition} Reading a table by ear
:class: important
This is the course's *tables* lecture, so the pattern from [Loading data](../data/loading_data.md) matters more than ever. A pandas **DataFrame** normally prints as a grid of aligned columns — quick to scan by eye, but a screen reader reads it as a stream of bare numbers with the column headers left far behind at the top. So throughout this page, when we want to *look at* a table, we print it in a screen-reader-friendly way instead:

```
print(df.shape)                 # (rows, columns)
print(df.columns.tolist())      # the column names
for label, row in df.iterrows():
    print(label, row.to_dict()) # each row as a dict: every value spoken with its column name
```

For small **Series**, `print(s.to_dict())` does the same job in one line. (This approach was worked out and tested with a screen-reader user in this course.)
:::

Let's start by importing the libraries we'll use throughout this lecture: **pandas** (conventionally imported as `pd`), NumPy, and matplotlib's `pyplot`.

The following is Python code:

```python
import pandas as pd
import numpy as np
from matplotlib import pyplot as plt
```

End of code.

## Series

Pandas gives you two main objects to work with: a **Series** (one-dimensional, like a single column of labelled data) and a **DataFrame** (two-dimensional, like a table or spreadsheet). DataFrames are built up from Series — each column of a DataFrame is itself a Series — so we'll start with Series and then build up to DataFrame.

A Series is a one-dimensional array of data with an _index_: labels we use to access each value.

There are many ways to [create a Series](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.html). Let's start with a simple example using planet names and masses.

To create a Series, we pass two lists to `pd.Series(...)`: the data **values**, and an `index` containing the **labels** for those values. A printed Series shows the labels on the left and the values on the right — one label–value pair per line, which reads reasonably well by ear (unlike a DataFrame grid), with a final line reporting the data type:

The following is Python code:

```python
names = ['Mercury', 'Venus', 'Earth']
values = [0.3e24, 4.87e24, 5.97e24]
masses = pd.Series(values, index=names)
print(masses)
```

End of code.

The following is the expected output:

```
Mercury    3.000000e+23
Venus      4.870000e+24
Earth      5.970000e+24
dtype: float64
```

End of output.

An even easier way to listen to a small Series is to print it as a dictionary:

The following is Python code:

```python
print(masses.to_dict())
```

End of code.

The following is the expected output:

```
{'Mercury': 3e+23, 'Venus': 4.87e+24, 'Earth': 5.97e+24}
```

End of output.

Series have built in plotting methods. A pandas plot method returns the matplotlib **Axes** it drew into — catch it in a variable so you can interrogate the figure afterwards, just as in the matplotlib lecture:

The following is Python code:

```python
ax = masses.plot(kind='bar')
plt.show()
```

End of code.

**What this shows.** A bar chart with three blue vertical bars, labelled beneath (in vertically rotated text) Mercury, Venus, and Earth. The y-axis runs 0 to 6 with a "1e24" multiplier printed at its top, so it reads in units of 10²⁴ kg: Mercury's bar is barely visible (0.3), Venus's reaches 4.87, and Earth's is tallest at 5.97.

**Check it.** The bar heights and labels can be read straight off the Axes object:

The following is Python code:

```python
print([float(p.get_height()) for p in ax.patches])
print([t.get_text() for t in ax.get_xticklabels()])
```

End of code.

The following is the expected output:

```
[3e+23, 4.87e+24, 5.97e+24]
['Mercury', 'Venus', 'Earth']
```

End of output.

:::{admonition} Hearing a plot (optional)
:class: note
The bar, line, and scatter plots on this page can also be explored non-visually — moved through point by point with arrow keys, values read as text — using **[MAIDR](../sci_python/trying_maidr.md)**.
:::

Arithmetic operations and most numpy function can be applied to Series.
An important point is that the Series keep their index during such operations — notice the planet names still label the results:

The following is Python code:

```python
print(np.log(masses) / masses**2)
```

End of code.

The following is the expected output:

```
Mercury    6.006452e-46
Venus      2.396820e-48
Earth      1.600655e-48
dtype: float64
```

End of output.

We can access the underlying index object if we need to:

The following is Python code:

```python
print(masses.index)
```

End of code.

The following is the expected output (older pandas versions print `dtype='object'` here instead of `dtype='str'`):

```
Index(['Mercury', 'Venus', 'Earth'], dtype='str')
```

End of output.

### Indexing

We can get values back out using the index via the `.loc` attribute, or by raw position using `.iloc`:

The following is Python code:

```python
print(masses.loc['Earth'])
print(masses.iloc[2])
```

End of code.

The following is the expected output (the same value both times — Earth is the third entry, position 2):

```
5.97e+24
5.97e+24
```

End of output.

We can pass a list or array to loc to get multiple rows back:

The following is Python code:

```python
print(masses.loc[['Venus', 'Earth']].to_dict())
```

End of code.

The following is the expected output:

```
{'Venus': 4.87e+24, 'Earth': 5.97e+24}
```

End of output.

And we can even use slice notation. Note the difference: a **label** slice with `.loc` includes *both* endpoints (all three planets come back), while a **position** slice with `.iloc` excludes the stop position, like ordinary Python slicing:

The following is Python code:

```python
print(masses.loc['Mercury':'Earth'].to_dict())
print(masses.iloc[:2].to_dict())
```

End of code.

The following is the expected output:

```
{'Mercury': 3e+23, 'Venus': 4.87e+24, 'Earth': 5.97e+24}
{'Mercury': 3e+23, 'Venus': 4.87e+24}
```

End of output.

If we need to, we can always get the raw data back out as well:

The following is Python code:

```python
print(masses.values)  # a numpy array
print(masses.index)   # a pandas Index object
```

End of code.

The following is the expected output:

```
[3.00e+23 4.87e+24 5.97e+24]
Index(['Mercury', 'Venus', 'Earth'], dtype='str')
```

End of output.

:::{admonition} Try it
:class: tip
In a fresh script, build your own `Series` from a Python list of values and a list of labels (any topic of your choice — e.g., capital cities mapped to populations). Use `.loc[]` to access one element by label, and `.iloc[]` to access the same element by position. Then slice with `.loc[]` over a *label* range — note that both the start and end labels are included (unlike Python list slicing). Print each result so you can hear it.
:::

## DataFrame

There is a lot more to Series, but they are limited to a single "column". A more useful Pandas data structure is the DataFrame. A DataFrame is basically a bunch of series that share the same index. It's a lot like a table in a spreadsheet.

Now let's build a `DataFrame`. The most common pattern is to start with a Python dictionary mapping column names to lists of values, then pass it to `pd.DataFrame` — and then read it back with the shape / columns / rows-as-dicts pattern:

The following is Python code:

```python
data = {'mass': [0.3e24, 4.87e24, 5.97e24],       # kg
        'diameter': [4879e3, 12_104e3, 12_756e3], # m
        'rotation_period': [1407.6, np.nan, 23.9] # h
       }
df = pd.DataFrame(data, index=['Mercury', 'Venus', 'Earth'])

print(df.shape)
print(df.columns.tolist())
for planet, row in df.iterrows():
    print(planet, row.to_dict())
```

End of code.

The following is the expected output:

```
(3, 3)
['mass', 'diameter', 'rotation_period']
Mercury {'mass': 3e+23, 'diameter': 4879000.0, 'rotation_period': 1407.6}
Venus {'mass': 4.87e+24, 'diameter': 12104000.0, 'rotation_period': nan}
Earth {'mass': 5.97e+24, 'diameter': 12756000.0, 'rotation_period': 23.9}
```

End of output.

Three rows (the planets), three columns (`mass`, `diameter`, `rotation_period`) — and listen to Venus's row: its `rotation_period` is `nan`, because we passed `np.nan` there. Pandas handles missing data very elegantly, keeping track of it through all calculations.

DataFrames come with a lot of built-in inspection and summary methods. A few of the most useful. First `.info()` — its output is a linear listing (not a grid), so it reads well: the index summary, then one line per column giving its name, its count of non-null values, and its dtype:

The following is Python code:

```python
df.info()
```

End of code.

The following is the expected output:

```
<class 'pandas.DataFrame'>
Index: 3 entries, Mercury to Earth
Data columns (total 3 columns):
 #   Column           Non-Null Count  Dtype  
---  ------           --------------  -----  
 0   mass             3 non-null      float64
 1   diameter         3 non-null      float64
 2   rotation_period  2 non-null      float64
dtypes: float64(3)
memory usage: 96.0+ bytes
```

End of output.

Note `rotation_period` reports only **2 non-null** out of 3 entries — that's Venus's missing value again. (`info()` prints its report itself, so it needs no `print(...)` around it.)

A wide range of statistical functions are available on both Series and DataFrames. Each reduces the table to one value per column — a small Series, which we print as a dict:

The following is Python code:

```python
print(df.min().to_dict())
print(df.mean().to_dict())
print(df.std().to_dict())
```

End of code.

The following is the expected output:

```
{'mass': 3e+23, 'diameter': 4879000.0, 'rotation_period': 23.9}
{'mass': 3.713333333333334e+24, 'diameter': 9913000.0, 'rotation_period': 715.75}
{'mass': 3.0067645955966246e+24, 'diameter': 4371743.702460152, 'rotation_period': 978.4236531278258}
```

End of output.

Notice how the missing value is handled: the mean `rotation_period` of 715.75 hours is the average of Mercury and Earth only — the `NaN` was skipped, not treated as zero.

`df.describe()` computes count, mean, standard deviation, min, quartiles, and max for every column at once. It returns a small DataFrame (one row per statistic), so we read it row by row:

The following is Python code:

```python
for stat, row in df.describe().round(2).iterrows():
    print(stat, row.to_dict())
```

End of code.

The following is the expected output:

```
count {'mass': 3.0, 'diameter': 3.0, 'rotation_period': 2.0}
mean {'mass': 3.7133333333333345e+24, 'diameter': 9913000.0, 'rotation_period': 715.75}
std {'mass': 3.0067645955966246e+24, 'diameter': 4371743.7, 'rotation_period': 978.42}
min {'mass': 3e+23, 'diameter': 4879000.0, 'rotation_period': 23.9}
25% {'mass': 2.585e+24, 'diameter': 8491500.0, 'rotation_period': 369.82}
50% {'mass': 4.87e+24, 'diameter': 12104000.0, 'rotation_period': 715.75}
75% {'mass': 5.42e+24, 'diameter': 12430000.0, 'rotation_period': 1061.68}
max {'mass': 5.97e+24, 'diameter': 12756000.0, 'rotation_period': 1407.6}
```

End of output.

We can get a single column as a Series using python's getitem syntax on the DataFrame object — note the `Name: mass` line at the end, showing the Series remembers which column it was:

The following is Python code:

```python
print(df['mass'])
```

End of code.

The following is the expected output:

```
Mercury    3.000000e+23
Venus      4.870000e+24
Earth      5.970000e+24
Name: mass, dtype: float64
```

End of output.

...or using attribute syntax:

The following is Python code:

```python
print(df.mass.to_dict())
```

End of code.

The following is the expected output:

```
{'Mercury': 3e+23, 'Venus': 4.87e+24, 'Earth': 5.97e+24}
```

End of output.

:::{admonition} Try it
:class: tip
Build your own `DataFrame` from a dictionary of columns (any topic — e.g. a few cities with `population`, `area`, and `elevation`), passing an `index` of labels. Include at least one `np.nan` so you can see how pandas reports missing data. Inspect its structure with `.info()` and `.describe()` (read `describe()` row by row with `iterrows()`, as above), then compute a few summary statistics with `.mean()`, `.min()`, and `.std()` — try them on the whole DataFrame and on a single column (e.g. `df['population'].mean()`). Finally, pull a column out as a Series using both `df['col']` and `df.col`.
:::

Indexing works very similar to series. `.loc` with a row label (or `.iloc` with a row position) returns that whole **row** as a Series — its "index" is now the column names, so `to_dict()` reads each value with its column name:

The following is Python code:

```python
print(df.loc['Earth'].to_dict())
print(df.iloc[2].to_dict())
```

End of code.

The following is the expected output (the same row both ways):

```
{'mass': 5.97e+24, 'diameter': 12756000.0, 'rotation_period': 23.9}
{'mass': 5.97e+24, 'diameter': 12756000.0, 'rotation_period': 23.9}
```

End of output.

But we can also specify the column we want to access:

The following is Python code:

```python
print(df.loc['Earth', 'mass'])
print(df.iloc[:2, 0].to_dict())
```

End of code.

The following is the expected output (a single number; then the first two rows of the first column):

```
5.97e+24
{'Mercury': 3e+23, 'Venus': 4.87e+24}
```

End of output.

If we make a calculation using columns from the DataFrame, it will keep the same index. Here we compute each planet's density — mass divided by volume, where volume is $\frac{4}{3} \pi r^3$ with radius $r$ = half the diameter:

The following is Python code:

```python
volume = 4/3 * np.pi * (df.diameter/2)**3
density = df.mass / volume
print(density.round(1).to_dict())
```

End of code.

The following is the expected output (in kilograms per cubic meter — rocky planets all come out around 5000):

```
{'Mercury': 4933.2, 'Venus': 5245.0, 'Earth': 5493.3}
```

End of output.

Which we can easily add as another column to the DataFrame:

The following is Python code:

```python
df['density'] = df.mass / volume

print(df.columns.tolist())
for planet, row in df.iterrows():
    print(planet, row.to_dict())
```

End of code.

The following is the expected output:

```
['mass', 'diameter', 'rotation_period', 'density']
Mercury {'mass': 3e+23, 'diameter': 4879000.0, 'rotation_period': 1407.6, 'density': 4933.216530312946}
Venus {'mass': 4.87e+24, 'diameter': 12104000.0, 'rotation_period': nan, 'density': 5244.977069690924}
Earth {'mass': 5.97e+24, 'diameter': 12756000.0, 'rotation_period': 23.9, 'density': 5493.285577295119}
```

End of output.

## Merging Data

Pandas supports a wide range of methods for merging different datasets. These are described extensively in the [documentation](https://pandas.pydata.org/pandas-docs/stable/merging.html). Here we just give a few examples.

Pandas can merge DataFrames and Series intelligently along their shared index. Let's create a separate `Series` of planet temperatures and combine it with our existing DataFrame. Note it has a **fourth** planet, Mars, that our DataFrame doesn't have:

The following is Python code:

```python
temperature = pd.Series([167, 464, 15, -65],
                     index=['Mercury', 'Venus', 'Earth', 'Mars'],
                     name='temperature')
print(temperature.to_dict())
```

End of code.

The following is the expected output:

```
{'Mercury': 167, 'Venus': 464, 'Earth': 15, 'Mars': -65}
```

End of output.

The following is Python code:

```python
joined = df.join(temperature)

print(joined.shape)
print(joined.columns.tolist())
for planet, row in joined.iterrows():
    print(planet, row.to_dict())
```

End of code.

The following is the expected output:

```
(3, 5)
['mass', 'diameter', 'rotation_period', 'density', 'temperature']
Mercury {'mass': 3e+23, 'diameter': 4879000.0, 'rotation_period': 1407.6, 'density': 4933.216530312946, 'temperature': 167.0}
Venus {'mass': 4.87e+24, 'diameter': 12104000.0, 'rotation_period': nan, 'density': 5244.977069690924, 'temperature': 464.0}
Earth {'mass': 5.97e+24, 'diameter': 12756000.0, 'rotation_period': 23.9, 'density': 5493.285577295119, 'temperature': 15.0}
```

End of output.

By default, `join` keeps the calling DataFrame's index — a *left* join — so the result still has 3 rows and Mars is dropped, since it isn't one of our three planets. The `how` keyword changes this: `how='right'` keeps the *other* object's index instead, so Mars appears (with `NaN` for the columns we don't have for it):

The following is Python code:

```python
joined_right = df.join(temperature, how='right')

print(joined_right.shape)
for planet, row in joined_right.iterrows():
    print(planet, row.to_dict())
```

End of code.

The following is the expected output:

```
(4, 5)
Mercury {'mass': 3e+23, 'diameter': 4879000.0, 'rotation_period': 1407.6, 'density': 4933.216530312946, 'temperature': 167.0}
Venus {'mass': 4.87e+24, 'diameter': 12104000.0, 'rotation_period': nan, 'density': 5244.977069690924, 'temperature': 464.0}
Earth {'mass': 5.97e+24, 'diameter': 12756000.0, 'rotation_period': 23.9, 'density': 5493.285577295119, 'temperature': 15.0}
Mars {'mass': nan, 'diameter': nan, 'rotation_period': nan, 'density': nan, 'temperature': -65.0}
```

End of output.

`reindex` is a more general tool: it conforms a DataFrame to a brand-new index that you supply, inserting rows of `NaN` wherever a label wasn't already present:

The following is Python code:

```python
everyone = df.reindex(['Mercury', 'Venus', 'Earth', 'Mars'])

print(everyone.shape)
for planet, row in everyone.iterrows():
    print(planet, row.to_dict())
```

End of code.

The following is the expected output:

```
(4, 4)
Mercury {'mass': 3e+23, 'diameter': 4879000.0, 'rotation_period': 1407.6, 'density': 4933.216530312946}
Venus {'mass': 4.87e+24, 'diameter': 12104000.0, 'rotation_period': nan, 'density': 5244.977069690924}
Earth {'mass': 5.97e+24, 'diameter': 12756000.0, 'rotation_period': 23.9, 'density': 5493.285577295119}
Mars {'mass': nan, 'diameter': nan, 'rotation_period': nan, 'density': nan}
```

End of output.

:::{admonition} Try it
:class: tip
Create a new `Series` of values indexed by some of the planets — you can include one that isn't in the DataFrame (e.g. Mars or Jupiter) — and `join` it onto the planets DataFrame. Try `how='right'` and compare the result with the default join (print both with the shape / rows-as-dicts pattern). Then call `.reindex(...)` with your own list of planet names and notice how pandas fills in `NaN` for labels that weren't present.
:::

Another powerful way to select data is to pick rows based on a **condition** rather than by their label or position. A comparison like `df.mass > 4e24` is checked for every row and returns a *boolean Series* — a column of `True`/`False` values, one per planet. Placing that inside `df[...]` keeps only the rows where the value is `True`. For example, to get just the planets more massive than `4e24` kg:

The following is Python code:

```python
print((df.mass > 4e24).to_dict())   # the boolean Series itself

adults = df[df.mass > 4e24]
print(adults.shape)
for planet, row in adults.iterrows():
    print(planet, row.to_dict())
```

End of code.

The following is the expected output:

```
{'Mercury': False, 'Venus': True, 'Earth': True}
(2, 4)
Venus {'mass': 4.87e+24, 'diameter': 12104000.0, 'rotation_period': nan, 'density': 5244.977069690924}
Earth {'mass': 5.97e+24, 'diameter': 12756000.0, 'rotation_period': 23.9, 'density': 5493.285577295119}
```

End of output.

Mercury's `False` filtered it out — only Venus and Earth remain. Since that condition is itself just a Series of `True`/`False` values, we can also store it as a new column — a handy way to flag the rows that meet the criterion:

The following is Python code:

```python
df['is_big'] = df.mass > 4e24

print(df.columns.tolist())
for planet, row in df.iterrows():
    print(planet, row.to_dict())
```

End of code.

The following is the expected output:

```
['mass', 'diameter', 'rotation_period', 'density', 'is_big']
Mercury {'mass': 3e+23, 'diameter': 4879000.0, 'rotation_period': 1407.6, 'density': 4933.216530312946, 'is_big': False}
Venus {'mass': 4.87e+24, 'diameter': 12104000.0, 'rotation_period': nan, 'density': 5244.977069690924, 'is_big': True}
Earth {'mass': 5.97e+24, 'diameter': 12756000.0, 'rotation_period': 23.9, 'density': 5493.285577295119, 'is_big': True}
```

End of output.

### Modifying Values

We often want to modify values in a dataframe based on some rule. To modify values, we need to use `.loc` or `.iloc`:

The following is Python code:

```python
df.loc['Earth', 'mass'] = 5.98e+24
df.loc['Venus', 'diameter'] += 1

for planet, row in df.iterrows():
    print(planet, row.to_dict())
```

End of code.

The following is the expected output — listen for Earth's mass, now 5.98e+24, and Venus's diameter, now 12104001 (one meter bigger):

```
Mercury {'mass': 3e+23, 'diameter': 4879000.0, 'rotation_period': 1407.6, 'density': 4933.216530312946, 'is_big': False}
Venus {'mass': 4.87e+24, 'diameter': 12104001.0, 'rotation_period': nan, 'density': 5244.977069690924, 'is_big': True}
Earth {'mass': 5.98e+24, 'diameter': 12756000.0, 'rotation_period': 23.9, 'density': 5493.285577295119, 'is_big': True}
```

End of output.

:::{admonition} Try it
:class: tip
Take the planets DataFrame from above. Use `.loc[]` to get all of Earth's data, then `.iloc[:3]` to get the first three rows. Add a new column called `density_check` computed as `df.mass / (4/3 * np.pi * (df.diameter/2)**3)`, then filter the DataFrame to planets with mass greater than 1e24. Print each result as you go (rows as dicts).
:::

## Plotting

DataFrames have all kinds of [useful plotting](https://pandas.pydata.org/pandas-docs/stable/visualization.html) built in.

The following is Python code:

```python
ax = df.plot(kind='scatter', x='mass', y='diameter', grid=True)
plt.show()
```

End of code.

**What this shows.** A scatter plot with three dots on a light grid. The horizontal axis is labelled "mass" and runs to 6 (with a 1e24 multiplier); the vertical axis is labelled "diameter" and runs from about 0.5 to 1.3 (with a 1e7 multiplier, i.e. meters). The dots rise from left to right: Mercury alone in the bottom-left corner (0.3e24, 4.88e6), Venus and Earth close together in the top-right — heavier planets have bigger diameters. Notice pandas labelled both axes for us, straight from the column names.

**Check it.**

The following is Python code:

```python
print(ax.get_xlabel(), ax.get_ylabel())
print(len(ax.collections[0].get_offsets()))
```

End of code.

The following is the expected output (the axis labels, and the number of plotted points):

```
mass diameter
3
```

End of output.

The following is Python code:

```python
ax = df.plot(kind='bar')
plt.show()
```

End of code.

**What this shows.** A grouped bar chart: one cluster of bars per planet, with a legend listing `mass`, `diameter`, `rotation_period`, and `density` in four colors (the `True`/`False` column `is_big` is left out of the plot). But the y-axis has to stretch to 6×10²⁴ to fit the masses — so only the blue mass bars are actually visible; diameter (about 10⁷), rotation period (about 10³), and density (about 5×10³) are invisibly short at this scale. The lesson: plotting columns with wildly different units on one axis rarely works.

**Check it.**

The following is Python code:

```python
print([c.get_label() for c in ax.containers])
```

End of code.

The following is the expected output:

```
['mass', 'diameter', 'rotation_period', 'density']
```

End of output.

:::{admonition} Try it
:class: tip
Make a scatter plot of `diameter` vs `mass` from the planets DataFrame. Then make a bar chart of each planet's mass. (You can stay with `df.plot(kind='...')`, or use `df.plot.scatter(...)` / `df.plot.bar(...)` — both work.) Check the bar chart without looking by printing `[float(p.get_height()) for p in ax.patches]`.
:::

## Time Indexes

Indexes are very powerful. They are a big part of why Pandas is so useful. There are different indices for different types of data. Time Indexes are especially great!

To build a time-indexed Series we first need a sequence of dates. `pd.date_range` generates one for us: we give it a `start` and `end` date and a frequency `freq` (here `'D'` for one timestamp per day), and it returns a `DatetimeIndex` of evenly spaced timestamps. Below we make two years of daily dates, then use them as the index of a `Series` whose values trace a seasonal sine wave — `.dayofyear` counts 1–365 through the year, so one full cycle spans a year:

The following is Python code:

```python
two_years = pd.date_range(start='2014-01-01', end='2016-01-01', freq='D')
timeseries = pd.Series(np.sin(2 *np.pi *two_years.dayofyear / 365),
                       index=two_years)
timeseries.plot()
plt.show()
```

End of code.

**What this shows.** A smooth blue sine wave spanning two full annual cycles. The x-axis runs from Jan 2014 to Jan 2016, automatically labelled with month names (Jan, Apr, Jul, Oct) and years — pandas produced calendar-aware tick labels without being asked. The curve starts at 0 in January 2014, peaks at +1 around the start of April, falls through 0 in July, bottoms out at −1 around the start of October, and repeats identically through 2015.

**Check it.** Confirm the peak and trough dates and values from the data:

The following is Python code:

```python
print(len(timeseries))
print(timeseries.idxmax(), round(timeseries.max(), 3))
print(timeseries.idxmin(), round(timeseries.min(), 3))
```

End of code.

The following is the expected output:

```
731
2014-04-01 00:00:00 1.0
2014-10-01 00:00:00 -1.0
```

End of output.

We can use python's slicing notation inside `.loc` to select a date range:

The following is Python code:

```python
timeseries.loc['2015-01-01':'2015-07-01'].plot()
plt.show()
```

End of code.

**What this shows.** Just the first half of the 2015 cycle — a single smooth arch: rising from 0 on Jan 1, 2015 to the peak of 1.0 at the start of April, then descending back to 0 at Jul 1. The x-axis now shows the months Jan 2015 through Jul.

The TimeIndex object has lots of useful attributes — for example, the month and day-of-month of every timestamp. These are long (731 entries), so pandas prints the first and last few with `...` in between, and reports the full `length` at the end:

The following is Python code:

```python
print(timeseries.index.month)
print(timeseries.index.day)
```

End of code.

The following is the expected output:

```
Index([ 1,  1,  1,  1,  1,  1,  1,  1,  1,  1,
       ...
       12, 12, 12, 12, 12, 12, 12, 12, 12,  1],
      dtype='int32', length=731)
Index([ 1,  2,  3,  4,  5,  6,  7,  8,  9, 10,
       ...
       23, 24, 25, 26, 27, 28, 29, 30, 31,  1],
      dtype='int32', length=731)
```

End of output.

:::{admonition} Try it
:class: tip
Use `pd.date_range(start='2023-01-01', end='2023-12-31', freq='D')` to create a year of daily timestamps. Wrap them in a `Series` whose values are `np.random.randn(len(...))`. Slice it to get just March through May 2023 (use `.loc['2023-03':'2023-05']`), then call `.plot()` followed by `plt.show()`. Check the slice non-visually: print its length (should be 92 days) and its first and last index entries.
:::

## Reading Data Files: Weather Station Data

In this example, we will use NOAA weather station data from https://www.ncdc.noaa.gov/data-access/land-based-station-data.

The details of files we are going to read are described in this README file (<ftp://ftp.ncdc.noaa.gov/pub/data/uscrn/products/daily01/README.txt>).

We fetch the file with `pooch` (the download tool from the [Loading data](../data/loading_data.md) page) — the first run prints a download-progress message; later runs reuse the locally cached copy silently:

The following is Python code:

```python
import pooch
POOCH = pooch.create(
    path=pooch.os_cache("noaa-data"),
    base_url="doi:10.5281/zenodo.5564850/",
    registry={
        "data.txt": "md5:5129dcfd19300eb8d4d8d1673fcfbcb4",
    },
)
datafile = POOCH.fetch("data.txt")
print(datafile)
```

End of code.

The following is the expected output (the exact path depends on your operating system — on Windows it will start with `C:\Users\...`; what matters is that it ends in `noaa-data` and `data.txt`):

```
/Users/you/Library/Caches/noaa-data/data.txt
```

End of output.

We now have a text file on our hard drive called `data.txt`. Examine it. In the original notebook this was done with the shell command `! head data.txt`; in a script we can just open the file and print its first lines with Python (or run `head` on that printed path in Git Bash):

The following is Python code:

```python
with open(datafile) as f:
    for _ in range(3):
        print(f.readline(), end='')
```

End of code.

The following is the expected output — three very long lines. The first is a header of 28 column names; the next two are data rows of whitespace-separated values:

```
WBANNO LST_DATE CRX_VN LONGITUDE LATITUDE T_DAILY_MAX T_DAILY_MIN T_DAILY_MEAN T_DAILY_AVG P_DAILY_CALC SOLARAD_DAILY SUR_TEMP_DAILY_TYPE SUR_TEMP_DAILY_MAX SUR_TEMP_DAILY_MIN SUR_TEMP_DAILY_AVG RH_DAILY_MAX RH_DAILY_MIN RH_DAILY_AVG SOIL_MOISTURE_5_DAILY SOIL_MOISTURE_10_DAILY SOIL_MOISTURE_20_DAILY SOIL_MOISTURE_50_DAILY SOIL_MOISTURE_100_DAILY SOIL_TEMP_5_DAILY SOIL_TEMP_10_DAILY SOIL_TEMP_20_DAILY SOIL_TEMP_50_DAILY SOIL_TEMP_100_DAILY 
64756 20170101  2.422  -73.74   41.79     6.6    -5.4     0.6     2.2     0.0     8.68 C     7.9    -6.6    -0.5    84.8    30.7    53.7 -99.000 -99.000   0.207   0.152   0.175    -0.1     0.0     0.6     1.5     3.4
64756 20170102  2.422  -73.74   41.79     4.0    -6.8    -1.4    -1.2     0.0     2.08 C     4.1    -7.1    -1.6    91.1    49.1    77.4 -99.000 -99.000   0.205   0.151   0.173    -0.2     0.0     0.6     1.5     3.3
```

End of output.

(It's daily data for 2017 from one station — longitude −73.74, latitude 41.79, which is Millbrook in New York State — with daily temperatures, precipitation, solar radiation, humidity, and soil measurements.)

To read it into pandas, we will use the [read_csv](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.read_csv.html) function. This function is incredibly complex and powerful. You can use it to extract data from almost any text file. However, you need to understand how to use its various options.

With no options, this is what we get. (In a notebook you'd glance at `df.head()` here; the shape and column list tell the same story more directly.)

The following is Python code:

```python
df = pd.read_csv(datafile)
print(df.shape)
print(df.columns.tolist())
```

End of code.

The following is the expected output:

```
(365, 1)
['WBANNO LST_DATE CRX_VN LONGITUDE LATITUDE T_DAILY_MAX T_DAILY_MIN T_DAILY_MEAN T_DAILY_AVG P_DAILY_CALC SOLARAD_DAILY SUR_TEMP_DAILY_TYPE SUR_TEMP_DAILY_MAX SUR_TEMP_DAILY_MIN SUR_TEMP_DAILY_AVG RH_DAILY_MAX RH_DAILY_MIN RH_DAILY_AVG SOIL_MOISTURE_5_DAILY SOIL_MOISTURE_10_DAILY SOIL_MOISTURE_20_DAILY SOIL_MOISTURE_50_DAILY SOIL_MOISTURE_100_DAILY SOIL_TEMP_5_DAILY SOIL_TEMP_10_DAILY SOIL_TEMP_20_DAILY SOIL_TEMP_50_DAILY SOIL_TEMP_100_DAILY ']
```

End of output.

365 rows but only **one** column, whose "name" is the entire header line! Pandas failed to identify the different columns. This is because it expected a standard CSV (comma-separated values) file, but in our file the values are separated by whitespace instead — and not by a single space: the amount of whitespace between columns varies. We tell pandas how the columns are separated with the `sep` keyword.

The value `r'\s+'` is a small *regular expression* — a pattern for matching text. Here `\s` stands for any whitespace character (a space or a tab) and `+` means "one or more in a row," so together they match any run of whitespace, however wide. The leading `r` makes it a *raw string*, which tells Python to leave the backslash alone instead of treating it as a special character.

The following is Python code:

```python
df = pd.read_csv(datafile, sep=r'\s+')
print(df.shape)
print(df.columns.tolist())
```

End of code.

The following is the expected output:

```
(365, 28)
['WBANNO', 'LST_DATE', 'CRX_VN', 'LONGITUDE', 'LATITUDE', 'T_DAILY_MAX', 'T_DAILY_MIN', 'T_DAILY_MEAN', 'T_DAILY_AVG', 'P_DAILY_CALC', 'SOLARAD_DAILY', 'SUR_TEMP_DAILY_TYPE', 'SUR_TEMP_DAILY_MAX', 'SUR_TEMP_DAILY_MIN', 'SUR_TEMP_DAILY_AVG', 'RH_DAILY_MAX', 'RH_DAILY_MIN', 'RH_DAILY_AVG', 'SOIL_MOISTURE_5_DAILY', 'SOIL_MOISTURE_10_DAILY', 'SOIL_MOISTURE_20_DAILY', 'SOIL_MOISTURE_50_DAILY', 'SOIL_MOISTURE_100_DAILY', 'SOIL_TEMP_5_DAILY', 'SOIL_TEMP_10_DAILY', 'SOIL_TEMP_20_DAILY', 'SOIL_TEMP_50_DAILY', 'SOIL_TEMP_100_DAILY']
```

End of output.

Great! It worked — 365 rows and 28 properly separated columns. Listen to a few fields of the first row:

The following is Python code:

```python
first = df[['LST_DATE', 'T_DAILY_MEAN', 'SOIL_MOISTURE_5_DAILY', 'SOIL_MOISTURE_10_DAILY']].iloc[0]
print(first.to_dict())
```

End of code.

The following is the expected output:

```
{'LST_DATE': 20170101.0, 'T_DAILY_MEAN': 0.6, 'SOIL_MOISTURE_5_DAILY': -99.0, 'SOIL_MOISTURE_10_DAILY': -99.0}
```

End of output.

If we look closely, we will see there are lots of -99 and -9999 values in the file — like the two soil-moisture values you just heard. The README file (<ftp://ftp.ncdc.noaa.gov/pub/data/uscrn/products/daily01/README.txt>) tells us that these are values used to represent missing data. Let's tell this to pandas:

The following is Python code:

```python
df = pd.read_csv(datafile, sep=r'\s+', na_values=[-9999.0, -99.0])

first = df[['LST_DATE', 'T_DAILY_MEAN', 'SOIL_MOISTURE_5_DAILY', 'SOIL_MOISTURE_10_DAILY']].iloc[0]
print(first.to_dict())
print(int(df.isna().sum().sum()))
```

End of code.

The following is the expected output:

```
{'LST_DATE': 20170101.0, 'T_DAILY_MEAN': 0.6, 'SOIL_MOISTURE_5_DAILY': nan, 'SOIL_MOISTURE_10_DAILY': nan}
149
```

End of output.

Great. The missing data is now represented by `NaN` — the same two fields now read `nan`, and the second line counts 149 missing cells across the whole table.

What data types did pandas infer?

The following is Python code:

```python
df.info()
```

End of code.

The following is the expected output (with pandas 3.x; older versions print `object` instead of `str` for the one text column, and slightly different memory usage):

```
<class 'pandas.DataFrame'>
RangeIndex: 365 entries, 0 to 364
Data columns (total 28 columns):
 #   Column                   Non-Null Count  Dtype  
---  ------                   --------------  -----  
 0   WBANNO                   365 non-null    int64  
 1   LST_DATE                 365 non-null    int64  
 2   CRX_VN                   365 non-null    float64
 3   LONGITUDE                365 non-null    float64
 4   LATITUDE                 365 non-null    float64
 5   T_DAILY_MAX              364 non-null    float64
 6   T_DAILY_MIN              364 non-null    float64
 7   T_DAILY_MEAN             364 non-null    float64
 8   T_DAILY_AVG              364 non-null    float64
 9   P_DAILY_CALC             364 non-null    float64
 10  SOLARAD_DAILY            364 non-null    float64
 11  SUR_TEMP_DAILY_TYPE      365 non-null    str    
 12  SUR_TEMP_DAILY_MAX       364 non-null    float64
 13  SUR_TEMP_DAILY_MIN       364 non-null    float64
 14  SUR_TEMP_DAILY_AVG       364 non-null    float64
 15  RH_DAILY_MAX             364 non-null    float64
 16  RH_DAILY_MIN             364 non-null    float64
 17  RH_DAILY_AVG             364 non-null    float64
 18  SOIL_MOISTURE_5_DAILY    317 non-null    float64
 19  SOIL_MOISTURE_10_DAILY   317 non-null    float64
 20  SOIL_MOISTURE_20_DAILY   336 non-null    float64
 21  SOIL_MOISTURE_50_DAILY   364 non-null    float64
 22  SOIL_MOISTURE_100_DAILY  359 non-null    float64
 23  SOIL_TEMP_5_DAILY        364 non-null    float64
 24  SOIL_TEMP_10_DAILY       364 non-null    float64
 25  SOIL_TEMP_20_DAILY       364 non-null    float64
 26  SOIL_TEMP_50_DAILY       364 non-null    float64
 27  SOIL_TEMP_100_DAILY      364 non-null    float64
dtypes: float64(25), int64(2), str(1)
memory usage: 80.0 KB
```

End of output.

Listen for the non-null counts: most columns have 364 of 365 values, but the shallow soil-moisture sensors are missing over a month of data (317 non-null). One problem here is that pandas did not recognize the `LST_DATE` column as a date — it's listed as plain `int64` (the number 20170101). Let's help it.

The following is Python code:

```python
df = pd.read_csv(datafile, sep=r'\s+',
                 na_values=[-9999.0, -99.0],
                 parse_dates=[1])
df.info()
```

End of code.

The following is the expected output — identical to before except for column 1 and the dtypes summary, so we show just the changed lines (older pandas prints `datetime64[ns]` rather than `datetime64[us]`):

```
<class 'pandas.DataFrame'>
RangeIndex: 365 entries, 0 to 364
Data columns (total 28 columns):
 #   Column                   Non-Null Count  Dtype         
---  ------                   --------------  -----         
 0   WBANNO                   365 non-null    int64         
 1   LST_DATE                 365 non-null    datetime64[us]
...
dtypes: datetime64[us](1), float64(25), int64(1), str(1)
memory usage: 80.0 KB
```

End of output.

It worked! `LST_DATE` is now a true datetime column. Finally, let's tell pandas to use the date column as the index:

The following is Python code:

```python
df = df.set_index('LST_DATE')

print(df.shape)
print(df.index[0], df.index[-1])
for date, row in df[['T_DAILY_MIN', 'T_DAILY_MEAN', 'T_DAILY_MAX', 'P_DAILY_CALC']].head(2).iterrows():
    print(date.date(), row.to_dict())
```

End of code.

The following is the expected output:

```
(365, 27)
2017-01-01 00:00:00 2017-12-31 00:00:00
2017-01-01 {'T_DAILY_MIN': -5.4, 'T_DAILY_MEAN': 0.6, 'T_DAILY_MAX': 6.6, 'P_DAILY_CALC': 0.0}
2017-01-02 {'T_DAILY_MIN': -6.8, 'T_DAILY_MEAN': -1.4, 'T_DAILY_MAX': 4.0, 'P_DAILY_CALC': 0.0}
```

End of output.

The table now has 27 columns (LST_DATE moved from column to index), and the index runs over the whole of 2017. We can now access values by time. A single day comes back as a Series — printed as one "column-name, value" pair per line, which reads well with a screen reader:

The following is Python code:

```python
print(df.loc['2017-08-07'])
```

End of code.

The following is the expected output:

```
WBANNO                     64756
CRX_VN                     2.422
LONGITUDE                 -73.74
LATITUDE                   41.79
T_DAILY_MAX                 19.3
T_DAILY_MIN                 12.3
T_DAILY_MEAN                15.8
T_DAILY_AVG                 16.3
P_DAILY_CALC                 4.9
SOLARAD_DAILY               3.93
SUR_TEMP_DAILY_TYPE            C
SUR_TEMP_DAILY_MAX          22.3
SUR_TEMP_DAILY_MIN          11.9
SUR_TEMP_DAILY_AVG          17.7
RH_DAILY_MAX                94.7
RH_DAILY_MIN                76.4
RH_DAILY_AVG                89.5
SOIL_MOISTURE_5_DAILY      0.148
SOIL_MOISTURE_10_DAILY     0.113
SOIL_MOISTURE_20_DAILY     0.094
SOIL_MOISTURE_50_DAILY     0.114
SOIL_MOISTURE_100_DAILY    0.151
SOIL_TEMP_5_DAILY           21.4
SOIL_TEMP_10_DAILY          21.7
SOIL_TEMP_20_DAILY          22.1
SOIL_TEMP_50_DAILY          22.2
SOIL_TEMP_100_DAILY         21.5
Name: 2017-08-07 00:00:00, dtype: object
```

End of output.

Or use slicing to get a range — here all of July 2017:

The following is Python code:

```python
july = df.loc['2017-07-01':'2017-07-31']
print(july.shape)
print(round(july['T_DAILY_MEAN'].mean(), 2))
```

End of code.

The following is the expected output (31 days, and a pleasant mean July temperature of about 21 °C):

```
(31, 27)
21.05
```

End of output.

### Quick Statistics

`describe()` is a fast way to get a feel for a freshly loaded dataset: in one call it reports the count, mean, standard deviation, minimum, the 25/50/75% percentiles, and maximum for every numeric column. It's a good first sanity check — unrealistic values or columns full of `NaN` often jump out right away. On a 26-numeric-column table the full report is far too wide to listen to, so check its shape, then read the columns you care about row by row:

The following is Python code:

```python
print(df.describe().shape)

desc = df[['T_DAILY_MIN', 'T_DAILY_MEAN', 'T_DAILY_MAX']].describe().round(2)
for stat, row in desc.iterrows():
    print(stat, row.to_dict())
```

End of code.

The following is the expected output:

```
(8, 26)
count {'T_DAILY_MIN': 364.0, 'T_DAILY_MEAN': 364.0, 'T_DAILY_MAX': 364.0}
mean {'T_DAILY_MIN': 4.04, 'T_DAILY_MEAN': 9.88, 'T_DAILY_MAX': 15.72}
std {'T_DAILY_MIN': 9.46, 'T_DAILY_MEAN': 9.73, 'T_DAILY_MAX': 10.5}
min {'T_DAILY_MIN': -21.8, 'T_DAILY_MEAN': -17.0, 'T_DAILY_MAX': -12.3}
25% {'T_DAILY_MIN': -2.78, 'T_DAILY_MEAN': 2.1, 'T_DAILY_MAX': 6.9}
50% {'T_DAILY_MIN': 4.35, 'T_DAILY_MEAN': 10.85, 'T_DAILY_MAX': 17.45}
75% {'T_DAILY_MIN': 11.9, 'T_DAILY_MEAN': 18.15, 'T_DAILY_MAX': 24.85}
max {'T_DAILY_MIN': 20.7, 'T_DAILY_MEAN': 25.7, 'T_DAILY_MAX': 33.4}
```

End of output.

The numbers pass the sanity check: daily mean temperatures between −17 and +25.7 °C with an annual mean around 10 °C — plausible for New York State.

### Plotting Values

We can now quickly make plots of the data:

The following is Python code:

```python
fig, ax = plt.subplots(ncols=2, nrows=2, figsize=(14,14))

df.iloc[:, 4:8].boxplot(ax=ax[0,0])
df.iloc[:, 10:14].boxplot(ax=ax[0,1])
df.iloc[:, 14:17].boxplot(ax=ax[1,0])
df.iloc[:, 18:22].boxplot(ax=ax[1,1])

ax[1, 1].set_xticklabels(ax[1, 1].get_xticklabels(), rotation=90)
plt.show()
```

End of code.

**What this shows.** A large square canvas with four panels of box-and-whisker plots (each box spans the middle 50% of a column's values, with a green line at the median; the whiskers reach the most extreme values lying within 1.5 box-lengths of the box, and circles beyond them mark outliers). Top-left, the four air-temperature columns: medians around 17.5 (T_DAILY_MAX), 4.3 (T_DAILY_MIN), and 11 (T_DAILY_MEAN and T_DAILY_AVG), whiskers spanning about −22 to +33 °C. Top-right, the surface-temperature columns — only **three** boxes appear, because the fourth column in that slice (`SUR_TEMP_DAILY_TYPE`) is text and boxplot silently skips it; the ground surface has a wider spread than the air, roughly −30 to +46 °C. Bottom-left, relative humidity: RH_DAILY_MAX hugs the top (median 92.9%, with a trail of low outlier circles), RH_DAILY_MIN has median 44.2%, RH_DAILY_AVG 71.5%. Bottom-right, soil moisture at four depths, all between about 0.08 and 0.32 (volumetric fraction), with deeper sensors showing narrower boxes — its long column names are rotated vertical by the `set_xticklabels(..., rotation=90)` line so they don't overlap.

**Check it.** Which columns landed in each panel:

The following is Python code:

```python
print(df.columns[4:8].tolist())
print(df.columns[10:14].tolist())
```

End of code.

The following is the expected output:

```
['T_DAILY_MAX', 'T_DAILY_MIN', 'T_DAILY_MEAN', 'T_DAILY_AVG']
['SUR_TEMP_DAILY_TYPE', 'SUR_TEMP_DAILY_MAX', 'SUR_TEMP_DAILY_MIN', 'SUR_TEMP_DAILY_AVG']
```

End of output.

Pandas is very "time aware":

The following is Python code:

```python
df.T_DAILY_MEAN.plot()
plt.show()
```

End of code.

**What this shows.** A single jagged blue line tracing daily mean temperature through 2017, on an x-axis automatically labelled with month names (Jan 2017 through Dec) and titled `LST_DATE`. The line wobbles day to day by several degrees around a clear seasonal arc: winter values mostly between −15 and +5 °C, climbing through spring to a summer plateau around 15–26 °C from June through September, then falling steeply to the year's minimum of −17 °C on December 31. The maximum is 25.7 °C on June 30. (A single missing day, October 4, makes a tiny break in the line — too small to notice by eye.)

Note: we could also manually create an axis and plot into it:

The following is Python code:

```python
fig, ax = plt.subplots()
df.T_DAILY_MEAN.plot(ax=ax)
ax.set_title('Pandas Made This!')
plt.show()
```

End of code.

**What this shows.** Exactly the same annual temperature curve, now with the title "Pandas Made This!" across the top — demonstrating that pandas plots land on ordinary matplotlib axes you control.

**Check it.**

The following is Python code:

```python
print(ax.get_title())
print(round(df.T_DAILY_MEAN.min(), 1), round(df.T_DAILY_MEAN.max(), 1))
```

End of code.

The following is the expected output:

```
Pandas Made This!
-17.0 25.7
```

End of output.

Passing a list of columns plots them all on one axes, with a legend:

The following is Python code:

```python
ax = df[['T_DAILY_MIN', 'T_DAILY_MEAN', 'T_DAILY_MAX']].plot()
plt.show()
```

End of code.

**What this shows.** Three jagged lines following the same seasonal arc in parallel, stacked in the expected order: T_DAILY_MIN in blue at the bottom, T_DAILY_MEAN in orange in the middle, T_DAILY_MAX in green on top, typically 5–10 °C apart. A legend box names the three. The full range now spans about −22 °C (coldest night) to +33 °C (hottest day).

**Check it.**

The following is Python code:

```python
print([line.get_label() for line in ax.lines])
```

End of code.

The following is the expected output:

```
['T_DAILY_MIN', 'T_DAILY_MEAN', 'T_DAILY_MAX']
```

End of output.

:::{admonition} Try it
:class: tip
Slice the NOAA `df` to a single month of your choice (e.g., `df.loc['2017-06']` — the file covers 2017). Call `.describe()` on that subset to get summary stats, and read a column or two of it by ear as above. Then plot `T_DAILY_MEAN` against the index for that month.
:::

### Resampling

Because pandas understands time, it can **resample** a time series — change how often the data is reported by grouping together all the timestamps that fall in each new time period. Our data is daily, but we might want monthly values; resampling to a monthly frequency gathers all the days in each month so we can summarize them (mean, max, …). It's much like `groupby`, except the groups are consecutive time intervals. The target frequency is given by a short code — below, `'MS'` means "month start" (one value per month); other common codes are `'D'` (daily), `'W'` (weekly), and `'YE'` (year end).

Calling `.resample(...)` returns a *resampler object* — a grouping by time frequency. Apply an aggregation (like `.mean()`) to actually get a smaller DataFrame. (The `select_dtypes(include='number')` step drops the one text column, `SUR_TEMP_DAILY_TYPE` — you can't average text.)

The following is Python code:

```python
rs_obj = df.select_dtypes(include='number').resample('MS')
print(rs_obj)
```

End of code.

The following is the expected output:

```
DatetimeIndexResampler [freq=<MonthBegin>, closed=left, label=left, convention=start, origin=start_day]
```

End of output.

The following is Python code:

```python
mm = rs_obj.mean()
print(mm.shape)
print(mm.index[0], mm.index[-1])
```

End of code.

The following is the expected output:

```
(12, 26)
2017-01-01 00:00:00 2017-12-01 00:00:00
```

End of output.

365 daily rows collapsed to 12 monthly rows, one per month start. Listen to the monthly mean temperature, month by month:

The following is Python code:

```python
for date, val in mm['T_DAILY_MEAN'].round(1).items():
    print(date.date(), val)
```

End of code.

The following is the expected output:

```
2017-01-01 -0.0
2017-02-01 1.4
2017-03-01 -0.1
2017-04-01 11.5
2017-05-01 13.2
2017-06-01 18.8
2017-07-01 21.0
2017-08-01 19.4
2017-09-01 17.7
2017-10-01 14.1
2017-11-01 4.1
2017-12-01 -3.0
```

End of output.

The seasonal cycle is now audible at a glance: around freezing January through March, a jump of 11 degrees into April, a July peak of 21.0 °C, and a slide back below zero in December.

We can chain all of that together:

The following is Python code:

```python
df_mm = df.select_dtypes(include='number').resample('MS').mean()
ax = df_mm[['T_DAILY_MIN', 'T_DAILY_MEAN', 'T_DAILY_MAX']].plot()
plt.show()
```

End of code.

**What this shows.** The same three temperature curves as before, but with only 12 points each the day-to-day jitter is gone — three smooth roughly bell-shaped arcs (min in blue, mean in orange, max in green, legend in the top-left). They rise from January values between about −4 and +4 °C, jump noticeably between March and April, peak in July (monthly mean max reaches about 27 °C, mean 21 °C, min 15 °C), and descend to December values of about −7 to +1.5 °C.

**Check it.**

The following is Python code:

```python
print(len(ax.lines), len(ax.lines[0].get_ydata()))
```

End of code.

The following is the expected output (three lines, twelve monthly points each):

```
3 12
```

End of output.

:::{admonition} Try it
:class: tip
Use `df.select_dtypes(include='number').resample('YE').mean()` to get yearly means (as above, `select_dtypes` drops the one text column, which has no mean) and plot `T_DAILY_MAX` against the resulting index as a line chart. Then do the same with `'10D'` (10-day bins) — the lower-frequency series should look smoother. You can confirm the smoothing without looking: print each resampled series' length (1 yearly value vs. 37 ten-day values) and its standard deviation — the coarser the bins, the smaller the spread.
:::

## Recap

You've now met the two core pandas objects and the everyday ways to work with them:

- **Series and DataFrames** — labelled 1-D and 2-D data, with `.info()` and `.describe()` for a quick look — and the screen-reader-friendly way to read any table: `shape`, `columns.tolist()`, then rows as dicts.
- **Indexing** — `.loc` (by label), `.iloc` (by position), and boolean masks to filter rows by a condition.
- **Building and combining** — adding computed columns, and joining a Series or DataFrame on a shared index.
- **Reading real data** — `read_csv` with `sep`, `na_values`, and `parse_dates`, then setting a column as the index.
- **Time series** — a `DatetimeIndex`, slicing by date, plotting straight from a DataFrame, and a first taste of `resample`.

Next we go deeper into grouping operations — `groupby`, `resample`, and `rolling` — in [Pandas: Groupby](./pandas_groupby.md).
