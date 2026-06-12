# Loading data

:::{admonition} Following along with a screen reader
:class: important
Do each example below as a `.py` script in VS Code (see [Setting up an accessible
workflow](../computing_env/accessible_setup.md) and [Working with Python
scripts](../computing_env/working_with_scripts.md)). Run a script in the Git Bash terminal
with `python yourfile.py`, and press `Alt+F2` to read what comes back. These examples need
a few packages — if you haven't already: `pip install pandas xarray pooch netcdf4 pydap`.
:::

This page walks through **three different ways to get data into Python**, each suited to a
different kind of source. The point isn't to learn the API of any one tool — you'll meet
`pandas` and `xarray` properly in later modules — but to see that "data access" is not one
thing. Different sources call for different approaches.

Why programmatic access matters: **manual download steps break reproducibility**. If your
code loads data from a URL or DOI directly, anyone running it (including future-you) gets
the same data — no guessing about which file you used or where you stored it.

For each example below, put the code in a `.py` script, run it with `python yourfile.py`,
and read what comes back with `Alt+F2`. Each example already wraps its last line in `print(...)` so the result shows in the
terminal — in a script (unlike a notebook), a bare expression prints nothing.

:::{admonition} Reading a table by ear
:class: important
A pandas **DataFrame** normally prints as a grid of aligned columns. That grid is quick to
scan by eye, but a screen reader reads it as a stream of numbers with the column headers left
far behind at the top — very hard to follow. So throughout this course, when we want to
*look at* a table, we print it in a screen-reader-friendly way instead: first its **shape**
and **column names**, then each row as a `dict`, so every value is spoken together with its
column name (`'co2': 315.71`) rather than as a bare number in a grid. (This approach was
worked out and tested with a screen-reader user in this course.)
:::

## 1. CSV over HTTP — `pandas.read_csv`

The simplest case: a CSV file sitting at a public URL. `pandas.read_csv` can read directly
from a URL without you having to download it first.

We'll use atmospheric CO2 measurements from Mauna Loa — the famous [Keeling
Curve](https://scrippsco2.ucsd.edu/), recorded by the Scripps CO2 program since 1958. We
read it from a snapshot hosted in the course repository, because the original Scripps server
isn't reachable from every environment (the LEAP hub can't reach it, for example).

:::{admonition} Try it
:class: tip
In a script, run:

The following is Python code:

~~~python
import pandas as pd

url = "https://raw.githubusercontent.com/earth-DS-ML/summer_2026/refs/heads/main/lectures_DS/data/monthly_in_situ_co2_mlo.csv"
columns = ["year", "month", "date_excel", "date", "co2", "co2_seasonally_adjusted",
           "fit", "fit_seasonally_adjusted", "co2_filled", "co2_filled_seasonally_adjusted",
           "station"]
df = pd.read_csv(url, skiprows=64, names=columns, na_values=-99.99)

df1 = df[["year", "month", "date", "co2"]]
print(f"Shape: {df1.shape}")
print(f"Columns: {df1.columns.tolist()}")

for _, row in df1.head().iterrows():
    print(row.to_dict())
~~~

End of code.

The following is the expected output:

~~~
Shape: (828, 4)
Columns: ['year', 'month', 'date', 'co2']
{'year': 1958.0, 'month': 1.0, 'date': 1958.0411, 'co2': nan}
{'year': 1958.0, 'month': 2.0, 'date': 1958.126, 'co2': nan}
{'year': 1958.0, 'month': 3.0, 'date': 1958.2027, 'co2': 315.71}
{'year': 1958.0, 'month': 4.0, 'date': 1958.2877, 'co2': 317.45}
{'year': 1958.0, 'month': 5.0, 'date': 1958.3699, 'co2': 317.51}
~~~

End of output.

Read it with `Alt+F2`. The first two lines give the table's **shape** (828 rows, 4 columns)
and its **column names**; then each row prints as a `dict`, so you hear every value attached
to its column name instead of a grid of bare numbers. The first two rows show `nan` for
`co2` — the missing values that `na_values=-99.99` converted. (We pick just these four
columns so each row reads cleanly; the full table has 11.) And notice you didn't download
anything — `pandas` streamed the data straight from the URL into memory.
:::

Real files often need a few options. This one opens with a long comment header followed by a
three-row column header — 64 lines in all — so `skiprows=64` jumps past it. We then give
pandas our own column `names`, and `na_values=-99.99` tells it that Scripps's `-99.99`
placeholder means *missing*, so those entries load as `NaN` instead of being mistaken for
real measurements.

> *Reference:* Why this works — most HTTP-served files are public and don't require
> authentication, so `pandas` can fetch them with a plain GET request. For files behind
> logins or paywalls, you'd need additional setup.

## 2. NetCDF over OPeNDAP — `xarray.open_dataset`

For gridded scientific data (temperature, precipitation, sea-surface height, etc.), the
standard format is **NetCDF**. The standard *protocol* for serving NetCDF files over the
network is **OPeNDAP** — it lets you access just the parts of a large dataset you need,
without downloading the whole file.

We'll use the [IRI Data Library](https://iridl.ldeo.columbia.edu/)'s precipitation dataset —
global monthly rain estimates.

:::{admonition} Try it
:class: tip
In a script, run:

The following is Python code:

~~~python
import xarray as xr

url = "http://iridl.ldeo.columbia.edu/expert/SOURCES/.NOAA/.NCEP/.CPC/.UNIFIED_PRCP/.GAUGE_BASED/.GLOBAL/.v1p0/.Monthly/.RETRO/.rain/dods"
ds = xr.open_dataset(url, decode_times=False)
print(ds)
~~~

End of code.

You'll see a `Dataset` object with dimensions `Y` (latitude), `X` (longitude), and `T`
(time). This is **a lot richer** than a pandas DataFrame — it carries coordinates, units,
and other metadata along with the data values. (Reading a `Dataset` summary with a screen
reader takes a moment — it's a structured listing of dimensions, then coordinates, then data
variables, then attributes.)
:::

The `decode_times=False` argument is a workaround — the IRI server uses non-standard time
encoding that xarray can't auto-parse. You'll see this kind of one-off fix often when
working with real scientific datasets. (Reading OPeNDAP also needs a backend; in most
installs `netcdf4` handles it, with `pydap` as a fallback if your `netcdf4` lacks OPeNDAP
support — both are in the install line above.)

> *Reference:* OPeNDAP is widely supported by NetCDF tools (xarray, the `netCDF4` library,
> ncview, etc.). Most major climate data providers (NOAA, NASA, ECMWF, IRI, …) serve their
> datasets over OPeNDAP.

## 3. A file from Zenodo by DOI — `pooch`

Sometimes you need to **download** the actual file — for example, to use it with a tool that
doesn't speak HTTP, or to work offline. The [Pooch](https://www.fatiando.org/pooch/) library
is the recommended tool for this — it caches the file locally, checks it against a hash to
detect corruption, and re-uses the cached copy on later runs.

The interesting case: pulling a file from a Zenodo dataset using its DOI. This is the
FAIR-canonical way to access a published dataset.

:::{admonition} Try it
:class: tip
In a script, run:

The following is Python code:

~~~python
import pooch
import pandas as pd

doi = "doi:10.5281/zenodo.5739406"
fname = "wind_po_hrly.csv"
file_path = pooch.retrieve(
    url=f"{doi}/{fname}",
    known_hash="md5:cf059b73d6831282c5580776ac07309a",
)

df = pd.read_csv(file_path)
print(f"Shape: {df.shape}")
print(f"Columns: {df.columns.tolist()}")

for _, row in df.head(2).iterrows():
    print(row.to_dict())
~~~

End of code.

The following is the expected output:

~~~
Shape: (8760, 9)
Columns: ['date.time', 'ny_1_onshore', 'ny_2_onshore', 'newe_onshore', 'mw_onshore', 'newe_offshore', 'ny_offshore', 'rfce_offshore', 'srvc_offshore']
{'date.time': '1/1/2011 0:00', 'ny_1_onshore': 0.725240495, 'ny_2_onshore': 0.450003616, 'newe_onshore': 0.693996266, 'mw_onshore': 0.646463288, 'newe_offshore': 0.759451462, 'ny_offshore': 0.528019073, 'rfce_offshore': 0.353946234, 'srvc_offshore': 0.189322848}
{'date.time': '1/1/2011 1:00', 'ny_1_onshore': 0.702001288, 'ny_2_onshore': 0.446461542, 'newe_onshore': 0.677913327, 'mw_onshore': 0.68888967, 'newe_offshore': 0.767230863, 'ny_offshore': 0.530821811, 'rfce_offshore': 0.357297022, 'srvc_offshore': 0.21296533}
~~~

End of output.

Same shape/columns/rows pattern as before (here showing the first two rows of an 8760-row,
9-column table of hourly wind data). The first run downloads the file from Zenodo and caches
it locally; later runs reuse the cached copy and verify it against the hash. Notice the DOI
in the URL — the dataset is reproducibly addressable by its DOI, not just an arbitrary URL.
:::

> *Reference:* Pooch supports many remote-data patterns beyond DOIs (plain HTTP URLs, S3
> buckets, FTP, etc.) and is the standard download manager in many scientific Python
> packages. See its [documentation](https://www.fatiando.org/pooch/) for details.

## Recap

You've now seen three styles of data access:

| Pattern | Use it for | Python tool |
|---|---|---|
| `pandas.read_csv(url)` | Small public CSVs over HTTP | `pandas` |
| `xarray.open_dataset(opendap_url)` | Large gridded scientific data (NetCDF) over the network | `xarray` |
| `pooch.retrieve(...)` | Downloading specific files (e.g., by DOI from Zenodo) | `pooch` |

Most real workflows mix and match — you might use OPeNDAP to inspect a dataset, then Pooch
to download a specific subset you need offline.

## Where else to find data

The three patterns above cover most situations you'll meet in this course, but the
data-access landscape has been changing fast. A lot of newer climate datasets are now hosted
on **cloud object storage** (S3, GCS, Azure) rather than traditional HTTP or OPeNDAP servers.
The cloud-native equivalent of NetCDF + OPeNDAP is **Zarr** stored on a cloud bucket,
accessed with `xarray.open_zarr(...)` (with [`fsspec`](https://filesystem-spec.readthedocs.io/en/latest/)
doing the network plumbing).

You don't need to use any of these for the course, but they're worth knowing about —
especially when you're looking for project data:

- **[Earthmover Marketplace](https://app.earthmover.io/marketplace)** — curated cloud-hosted
  climate datasets in Zarr / Icechunk format, designed for fast analysis-ready access.
- **[LEAP Pangeo Catalog](https://catalog.leap.columbia.edu/)** — datasets curated by the
  NSF LEAP STC (the same group that runs our course JupyterHub); often already on the hub for
  low-latency access.
- **[Microsoft Planetary Computer](https://planetarycomputer.microsoft.com/)** — large
  catalog of satellite imagery and climate datasets hosted on Azure, indexed via the
  [STAC](https://stacspec.org/) spec.
- **[NASA `earthaccess`](https://earthaccess.readthedocs.io/)** — Python package that
  handles auth + S3 access for NASA's Earthdata Cloud.

When you start looking for data for your final project, browsing these catalogs is often the
fastest way to find something good — each comes with its own setup (a Python package,
sometimes an account).
