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
and read what comes back with `Alt+F2`. Since these print a preview of the data, add a
`print(...)` around the last line (noted in each example) so it shows in the terminal.

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

~~~python
import pandas as pd

url = "https://raw.githubusercontent.com/earth-DS-ML/summer_2026/refs/heads/main/lectures_DS/data/monthly_in_situ_co2_mlo.csv"
columns = ["year", "month", "date_excel", "date", "co2", "co2_seasonally_adjusted",
           "fit", "fit_seasonally_adjusted", "co2_filled", "co2_filled_seasonally_adjusted",
           "station"]
df = pd.read_csv(url, skiprows=64, names=columns, na_values=-99.99)
print(df.head())
~~~

You should see a table with year, month, date, and CO2 columns (read it with `Alt+F2`).
Notice that you didn't have to download anything to your computer — `pandas` streamed the
data from the URL directly into memory.
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

~~~python
import xarray as xr

url = "http://iridl.ldeo.columbia.edu/expert/SOURCES/.NOAA/.NCEP/.CPC/.UNIFIED_PRCP/.GAUGE_BASED/.GLOBAL/.v1p0/.Monthly/.RETRO/.rain/dods"
ds = xr.open_dataset(url, decode_times=False)
print(ds)
~~~

You'll see a `Dataset` object with dimensions `Y` (latitude), `X` (longitude), and `T`
(time). This is **a lot richer** than a pandas DataFrame — it carries coordinates, units,
and other metadata along with the data values. (Reading a `Dataset` summary with a screen
reader takes a moment — it's a structured listing of dimensions, then coordinates, then data
variables, then attributes.)
:::

The `decode_times=False` argument is a workaround — the IRI server uses non-standard time
encoding that xarray can't auto-parse. You'll see this kind of one-off fix often when
working with real scientific datasets. (Reading OPeNDAP also needs a backend — that's the
`pydap` / `netcdf4` package from the install line above.)

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

~~~python
import pooch
import pandas as pd

doi = "doi:10.5281/zenodo.5739406"
fname = "wind_po_hrly.csv"
file_path = pooch.retrieve(
    url=f"{doi}/{fname}",
    known_hash="md5:cf059b73d6831282c5580776ac07309a",
)

print(pd.read_csv(file_path).head())
~~~

The first run downloads the file from Zenodo and saves it to a local cache. Later runs reuse
the cached copy (and verify it hasn't been corrupted). Notice the DOI in the URL — the
dataset is reproducibly addressable by its DOI, not just an arbitrary URL.
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
