# Overview

This section is about **data** — the formats and conventions you'll meet when working
with climate and environmental datasets, and the practical ways to get data into Python.

## Learning objectives

By the end of this section, you should be able to:

1. **Recognize common data formats** in climate science — tabular (CSV, Parquet), gridded
   (NetCDF, Zarr), and raster (GeoTIFF).
2. **Read metadata** and understand why it's essential to a dataset's usefulness.
3. **Explain the FAIR principles** (Findable, Accessible, Interoperable, Reusable) and why
   they matter for scientific data.
4. **Access data from different sources** — over HTTP, via OPeNDAP, and via persistent
   identifiers (DOIs) with Pooch.
5. **Find a DOI** for a dataset and use it to retrieve the data reproducibly.

## Pages in this section

- [Formats and metadata](./formats_and_metadata.md) — what kinds of data exist, how
  they're described, and what FAIR means.
- [Loading data](./loading_data.md) — practical try-its for the main access patterns, done
  as scripts.
- [Assignment 2](./assignment2.md) — explore and document a climate dataset of your choice.

:::{admonition} Following along with a screen reader
:class: important
The *Formats and metadata* page is conceptual reading — its diagrams have been replaced
with text descriptions. The *Loading data* page has hands-on examples: do them as `.py`
scripts in VS Code, run them in the Git Bash terminal with `python yourfile.py`, and read
the output with `Alt+F2` (see [Setting up an accessible
workflow](../computing_env/accessible_setup.md)). This section also uses a few extra Python
packages — install them once with:

The following is a terminal command:

~~~
pip install pandas xarray pooch netcdf4 pydap
~~~

End of command.
:::
