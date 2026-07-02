# Overview

This section introduces **Pandas**: an open-source library providing high-performance,
easy-to-use data structures and data analysis tools. Pandas is particularly suited to
**tabular data** — anything that fits naturally into rows and columns, like an Excel
spreadsheet. The `DataFrame` is the workhorse of nearly every data-science workflow in
Python.

:::{admonition} A note on tables and this accessible version
:class: important
Pandas is all about **tables**, and a table normally prints as a grid of aligned columns —
quick to scan by eye, but read aloud it becomes a stream of bare numbers with the column
headers left far behind at the top. So throughout this section we use the
screen-reader-friendly inspection pattern introduced in
[Loading data](../data/loading_data.md) ("Reading a table by ear"): print the table's
**shape** and **column names** first, then read rows one at a time as `dict`s, so every
value is spoken together with its column name. The lecture pages still teach the standard
pandas methods (`df.head()` and friends), and then immediately show this accessible way to
*read* the result. Where a page makes line, bar, scatter, or histogram plots, each figure
is described in words and numbers, and those chart types can also be explored by sound and
text with [MAIDR](../sci_python/trying_maidr.md).
:::

## What Pandas can do

A few of the headline capabilities (from the [pandas website](http://pandas.pydata.org/)):

- Fast, efficient `DataFrame` for tabular data manipulation with integrated indexing.
- Reading and writing between in-memory data structures and many file formats (CSV, Excel,
  SQL, HDF5, Parquet, …).
- Intelligent label-based alignment and missing-data handling.
- Reshaping and pivoting of datasets.
- Slicing, boolean filtering, and fancy indexing of large datasets.
- Aggregation and transformation with a powerful `groupby` engine (split-apply-combine).
- High-performance joins and merges.
- Time-series functionality: date ranges, frequency conversion, moving-window statistics.

This section just scratches the surface; the
[pandas documentation](http://pandas.pydata.org/pandas-docs/stable/) is the place to dig
deeper. Pandas was created by [Wes McKinney](http://wesmckinney.com/); his book
[Python for Data Analysis](http://shop.oreilly.com/product/0636920023784.do) (with
[code samples](https://github.com/wesm/pydata-book)) is an excellent deeper reference.

## Learning objectives

By the end of this section, you should be able to:

1. **Create and inspect Series and DataFrames** — including index, columns, dtype, and
   basic descriptive methods (`.info()`, `.describe()`).
2. **Index and slice** — with `.loc` (label-based) and `.iloc` (positional), plus boolean
   masks for row filtering.
3. **Load tabular data** — `read_csv` with custom separators, missing-value handling, and
   date parsing.
4. **Add new columns, modify cells, and merge DataFrames/Series** along a shared index.
5. **Work with time-indexed data** — `DatetimeIndex`, slicing by date range, and
   time-series plotting.
6. **Plot directly from a DataFrame** — `.plot(...)` with line, bar, scatter, hist.
7. **Split-apply-combine with `groupby`** — aggregate, transform, and iterate over groups;
   build climatologies and anomalies.
8. **Resample and apply rolling statistics** — change time frequency with `resample`,
   compute moving-window means with `rolling`.

## Pages in this section

- [Pandas Fundamentals](./basic_pandas.md) — Series, DataFrames, indexing, reading CSV
  files, time indexes, plotting — with the read-a-table-by-ear pattern used throughout.
- [Pandas: Groupby](./pandas_groupby.md) — split-apply-combine, aggregation,
  transformation, time grouping, and building climatologies and anomalies from real data.
- [Assignment 5a](./assignment5a.md) — at-home: pandas fundamentals on USGS earthquake
  data, with "check yourself" numbers for every task.
- [Assignment 5b](./assignment5b.md) — at-home: pandas groupby on NOAA hurricane tracks,
  verified by code and printed checks rather than by eye.

## Working through the lectures

Work through the pages as **Python scripts** in VS Code — type the examples into a `.py`
file, run them in your Git Bash terminal with `python yourfile.py`, and read the output
with `Ctrl+Up`. The **Try it** boxes invite you to experiment. If you haven't set up VS
Code yet, start with
[Setting up an accessible workflow](../computing_env/accessible_setup.md) and
[Working with Python scripts](../computing_env/working_with_scripts.md).

:::{admonition} In-class assignment — 10 points
:class: note
Your in-class assignment for this section is to complete the **"Try it"** exercises in the
lecture pages above. Complete them as `.py` scripts, push them to your week folder, and
post the link to that folder on the matching **Courseworks** assignment. (The two at-home
assignments — 5a and 5b — are graded separately, 10 points each.)
:::
