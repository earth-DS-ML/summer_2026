# Overview

This section introduces **Pandas**: an open-source library providing high-performance, easy-to-use data structures and data analysis tools. Pandas is particularly suited to **tabular data** — anything that fits naturally into rows and columns, like an Excel spreadsheet. The `DataFrame` is the workhorse of nearly every data-science workflow in Python.

## What Pandas can do

A few of the headline capabilities (from the [pandas website](http://pandas.pydata.org/)):

- Fast, efficient `DataFrame` for tabular data manipulation with integrated indexing.
- Reading and writing between in-memory data structures and many file formats (CSV, Excel, SQL, HDF5, Parquet, …).
- Intelligent label-based alignment and missing-data handling.
- Reshaping and pivoting of datasets.
- Slicing, boolean filtering, and fancy indexing of large datasets.
- Aggregation and transformation with a powerful `groupby` engine (split-apply-combine).
- High-performance joins and merges.
- Time-series functionality: date ranges, frequency conversion, moving-window statistics.

This section just scratches the surface; the [pandas documentation](http://pandas.pydata.org/pandas-docs/stable/) is the place to dig deeper. Pandas was created by [Wes McKinney](http://wesmckinney.com/); his book [Python for Data Analysis](http://shop.oreilly.com/product/0636920023784.do) (with [code samples](https://github.com/wesm/pydata-book)) is an excellent deeper reference.

## Learning objectives

By the end of this section, you should be able to:

1. **Create and inspect Series and DataFrames** — including index, columns, dtype, and basic descriptive methods (`.info()`, `.describe()`).
2. **Index and slice** — with `.loc` (label-based) and `.iloc` (positional), plus boolean masks for row filtering.
3. **Load tabular data** — `read_csv` with custom separators, missing-value handling, and date parsing.
4. **Add new columns, modify cells, and merge DataFrames/Series** along a shared index.
5. **Work with time-indexed data** — `DatetimeIndex`, slicing by date range, and time-series plotting.
6. **Plot directly from a DataFrame** — `.plot(...)` with line, bar, scatter, hist.
7. **Split-apply-combine with `groupby`** — aggregate, transform, and iterate over groups; build climatologies and anomalies.
8. **Resample and apply rolling statistics** — change time frequency with `resample`, compute moving-window means with `rolling`.

## Pages in this section

- [Pandas Fundamentals](./basic_pandas.ipynb) — Series, DataFrames, indexing, reading CSV files, time indexes, plotting.
- [Pandas: Groupby](./pandas_groupby.ipynb) — split-apply-combine, aggregation, transformation, time grouping, climatology and anomalies.
- [Assignment 5a](./assignment5a.ipynb) — pandas fundamentals on USGS earthquake data.
- [Assignment 5b](./assignment5b.ipynb) — pandas groupby on NOAA hurricane tracks.

:::{admonition} In-class assignment — 10 points
:class: note
Your in-class assignment for this section is to complete the **"Try it"** exercises in the lecture notebooks above. Work through them in your own copy of each notebook, then submit the completed notebook(s) in your week folder. (The two at-home assignments — 5a and 5b — are graded separately, 10 points each.)
:::

## Working through the lectures

Same workflow as previous modules — download each notebook (⬇ icon, top-right) or copy-paste the cells into a fresh notebook in JupyterLab on LEAP or Colab, then step through them. Stop at each **Try it** admonition to experiment in your own cells before moving on.
