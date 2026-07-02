# Assignment 5a: Pandas Fundamentals with Earthquake Data

**At-home assignment — worth 10 points.** When you're done, push your work to your week folder and post a link to your completed scripts on the matching **Courseworks** assignment.

In this assignment you'll exercise core pandas skills on a real-world dataset: the USGS Earthquakes catalog.

:::{admonition} Following along with a screen reader
:class: important
Work through the tasks as `.py` scripts in VS Code — see [Setting up an accessible workflow](../computing_env/accessible_setup.md) and [Working with Python scripts](../computing_env/working_with_scripts.md). Run a script in the Git Bash terminal with `python yourfile.py`, and press `Ctrl+Up` to read the output. You'll need `pandas` and `matplotlib` installed (`pip install pandas matplotlib` if you don't have them), and you must be online — the data is read straight from a URL.
:::

:::{admonition} Learning goals
:class: tip
This assignment exercises the pandas fundamentals from this section:

- Load CSV data directly from a URL into a `DataFrame`
- Parse dates and use them as a time index
- Use `describe`, `nlargest`, and column selection to explore data
- Filter rows with boolean masks
- Create a new column derived from existing ones
- Group by a column and count entries
- Plot histograms and scatter plots directly from a DataFrame
:::

:::{admonition} Working through this assignment
:class: tip
One script for the whole assignment works well (`assignment5a.py` — the tasks build on each other), or one script per task if you prefer smaller runs. Three notes:

- **In a script, a bare expression displays nothing** — wrap anything you want to hear in `print(...)`.
- **Read tables by ear, not as grids.** Where a task says to "display" a DataFrame, don't try to listen to the raw grid that `print(df)` or `df.head()` produces. Use the screen-reader-friendly pattern from the [Loading data lecture](../data/loading_data.md): print `df.shape`, print `df.columns.tolist()`, then loop `for _, row in df.head().iterrows(): print(row.to_dict())` so every value is spoken together with its column name. For a small Series, `print(s.to_dict())` does the same job.
- **Each task has a *Check yourself* note** giving numbers your solution should produce, so you can confirm you're on track without seeing any output grid or plot. For the plotting tasks (8–10), end with `plt.savefig("assignment5a_task8.png")` (matching the task number) so a sighted grader can open the PNGs, and add two or three sentences — in a comment at the bottom of the script — saying what the plot shows, tied to the numbers you printed. You are graded on the code, the printed checks, and the understanding, not on visual polish.

When you're done, follow the [submission instructions](#submission-instructions-5a) at the bottom of the page.
:::

Start your script by importing numpy, pandas, and matplotlib's pyplot (with conventional short aliases).

Data for this assignment in .csv format downloaded from the [USGS Earthquakes Database](https://earthquake.usgs.gov/earthquakes/search/) is available at:

https://raw.githubusercontent.com/earth-DS-ML/summer_2026/refs/heads/main/lectures_DS/data/usgs_earthquakes_2025.csv

You don't need to download this file. You can open it directly with Pandas.

## 1) Use Pandas' read_csv function directly on this URL to open it as a DataFrame

(Don't use any special options). Display the first few rows and the DataFrame info.

Display the first few rows with the row-dict pattern from the note above. `df.info()` is already screen-reader-friendly: it prints one line per column — column name, non-null count, dtype — so you can arrow through it or read it with `Ctrl+Up`.

*Check yourself:* `df.shape` prints `(10654, 22)` — 10,654 earthquakes, 22 columns. `df.columns.tolist()` starts with `['time', 'latitude', 'longitude', 'depth', 'mag', ...]`. In the info listing, the `time` and `updated` columns show up as plain strings (dtype `object`, or `str` in newer pandas versions), *not* as dates — that's the problem the next task fixes. The first row is a magnitude 4.9 quake whose `place` is `'206 km SSW of ‘Ohonua, Tonga'`.

## 2) Re-read the data in such a way that all date columns are identified as dates and the earthquake ID is used as the index

Verify that this worked using the `head` and `info` functions.

*Check yourself:* two columns need date-parsing — you found them in the previous task's info listing. After the re-read, `df.index.name` prints `id`, and `df.shape` is now `(10654, 21)` (the `id` column moved into the index, so one fewer column). In `df.info()`, both date columns now report a `datetime64` dtype (pandas may add a unit and timezone, like `datetime64[ns, UTC]` — the `datetime64` part is what matters). Print `df['time'].min()` and `df['time'].max()`: the catalog runs from January 10 to June 17, 2025.

## 3) Use `describe` to get the basic statistics of all the columns

Note the highest and lowest magnitude of earthquakes in the database.

`describe` returns a DataFrame of its own — 8 statistics by 12 numeric columns — so read it one column at a time as a dict rather than as a grid, for example `print(df.describe()['mag'].round(2).to_dict())`.

*Check yourself:* the lowest magnitude in the catalog is `2.5` and the highest is `7.7`. The mean magnitude is about `3.86` and the median (the `50%` entry) is `4.2`.

## 4) Use `nlargest` to get the top 20 earthquakes by magnitude

https://pandas.pydata.org/pandas-docs/stable/generated/pandas.Series.nlargest.html

The result is a Series indexed by earthquake ID — print it with `.to_dict()` so each ID is spoken together with its magnitude.

*Check yourself:* 20 values, running from `7.7` at the top down to `6.3` at the bottom, with `7.6` in second place. The top entry is id `us7000pn9s` at magnitude 7.7 — look up its full row with `df.loc[...]` and print `row.to_dict()`: it is the March 28, 2025 Mandalay, Burma (Myanmar) earthquake.

## 5) Extract the state or country using Pandas [text data functions](https://pandas.pydata.org/pandas-docs/stable/text.html)

Examine the structure of the `place` column. The state / country information seems to be in there. How would you get it out?

Add it as a new column to the dataframe called `country`. Note that some of the "countries" are actually U.S. states.

*Check yourself:* print a few `place` values first (`df['place'].head().tolist()`) — the location name sits after the comma, as in `'206 km SSW of ‘Ohonua, Tonga'`. The `.str` methods you want split on the comma, take the last piece, and strip the leading space. The first five values of your new `country` column should be `'Tonga'`, `'Nevada'`, `'Tonga'`, `'Nevada'`, `'CA'`. One wrinkle: 1,148 of the place strings contain no comma at all (for example `'southern Mid-Atlantic Ridge'`), so the whole string comes through as the "country" — that's expected here; leave them as they are.

## 6) Display each unique value from the new column

https://pandas.pydata.org/pandas-docs/stable/generated/pandas.Series.unique.html

*Check yourself:* there are `228` unique values — print `len(...)` to confirm. The full array is long to listen to; printing the first ten as a list (`list(u[:10])`) is enough to hear the mix of countries, U.S. states, and comma-less oddities like `'southern Mid-Atlantic Ridge'`.

## 7) Create a filtered dataset that contains only earthquakes with magnitude greater than 4

Store it in a new variable (e.g. `df_big`) — you'll reuse it in the next tasks.

*Check yourself:* `len(df_big)` prints `5923` — a bit over half of the 10,654 events survive the cut.

## 8) Using the filtered dataset (magnitude > 4), count the number of earthquakes in each country/state. Make a bar chart of this number for the top 5 locations with the most earthquakes

Location name on the x axis, Earthquake count on the y axis.

Print the counts before you plot them — a five-entry Series reads perfectly as a dict.

*Check yourself:* the top-5 counts, as a dict, are `{'Indonesia': 602, 'Papua New Guinea': 518, 'Greece': 377, 'Japan': 295, 'Philippines': 238}`. The bar chart shows five bars stepping steadily down from left to right, Indonesia tallest at 602. Confirm the figure without looking: `len(ax.patches)` is `5`, and `[p.get_height() for p in ax.patches]` prints the same five counts in the same order. Save it with `plt.savefig("assignment5a_task8.png")`.

For interest: run the same count on the *unfiltered* data — Alaska jumps to first place with 2,394 events. Keep that thought for tasks 9 and 10.

## 9) Make a histogram of the distribution of the Earthquake magnitudes

https://pandas.pydata.org/pandas-docs/version/0.23/generated/pandas.DataFrame.hist.html
https://matplotlib.org/api/_as_gen/matplotlib.pyplot.hist.html

Do one subplot for the filtered and one for the unfiltered dataset.
Use a Logarithmic scale. What sort of relationship do you see?

Hint: `fig, axes = plt.subplots(1, 2)` gives you the two panels; the log scale is either `log=True` in the `hist` call or `ax.set_yscale('log')` after it.

*Check yourself:* you can hear the shape of the histogram directly from counts in whole-magnitude bands — print `((df['mag'] >= m) & (df['mag'] < m + 1)).sum()` for m = 4, 5, 6, 7: you should get `5491`, `665`, `45`, and `4`. Each step up of one magnitude unit cuts the count by roughly a factor of ten, which is why the filtered histogram on a log-count axis falls off as a nearly straight line from magnitude 4 to 7.7 — a straight line on a log axis means an exponential relationship (seismologists call this the Gutenberg–Richter law; that's the relationship the question is after). The unfiltered panel shows the same straight tail plus a second bump below magnitude 3 with a dip near 3.5–4 — a catalog artifact, not physics: only densely instrumented regions (mostly U.S. networks) report the tiny quakes. Save the figure as `assignment5a_task9.png`.

Histograms and bar charts like these are exactly the kind of plots you can also explore by sound with MAIDR — see [Trying MAIDR](../sci_python/trying_maidr.md).

## 10) Visualize the locations of earthquakes by making a scatterplot of their latitude and longitude

Use a two-column subplot with both the filtered and unfiltered datasets. Color the points by magnitude. Make it pretty.

What difference do you note between the filtered and unfiltered datasets?

Hint: `plt.subplots(1, 2, figsize=(12, 5), sharey=True)` for the layout; pass `c=df['mag']` (with a small marker size like `s=4`) to `scatter`, and add a colorbar labelled with the magnitude.

*Check yourself:* the dots span longitude −180 to 180 and latitude about −73 to +87 — print `df['longitude'].min()`, `df['longitude'].max()`, and the same for latitude to confirm. With one shared colorbar, `len(fig.axes)` prints `3` (two map panels plus the colorbar). What the panels show: both trace the boundaries of Earth's tectonic plates — narrow chains of dots down the west coast of the Americas, through the Mediterranean and the Himalayas, and around the western Pacific "Ring of Fire". The difference: the *unfiltered* panel is dominated by dense clusters of dark, low-magnitude dots over Alaska, California–Nevada, and Puerto Rico — of the 4,449 quakes below magnitude 4, about 4,000 are in U.S. states and territories (Alaska alone has 2,244), because that's where the dense seismometer networks are. In the *filtered* panel those clusters all but vanish (Alaska keeps only 128 events, Puerto Rico 4), leaving the plate boundaries drawn evenly around the whole globe. Save the figure as `assignment5a_task10.png`, and put this filtered-versus-unfiltered story, in your own words, in the comment at the bottom of your script.

(submission-instructions-5a)=
## Submission instructions

When you're done, save your completed scripts (and the PNG figures they produce) as `assignment5a` files inside the current week's folder in your private `clmt5405-assignments` GitHub repo. Then push the commit:

The following is a terminal command:

```bash
git add <weekN>/
git commit -m "Submit assignment 5a"
git push
```

End of command.

Due Sunday night.
