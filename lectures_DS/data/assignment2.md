# Assignment 2: Explore a climate dataset

**At-home assignment — worth 10 points.**

Pick any climate-related dataset that interests you — could be something you're considering for your final project, or just something you're curious about. The point here is **practice with the FAIR + access workflow**, not a commitment to a specific project.

:::{admonition} Learning goals
:class: tip
This assignment exercises the data-discovery and access skills from this section:

- Locate a dataset in a public scientific repository (Zenodo, NOAA, NASA, IRI, etc.)
- Document a dataset's format, license, provenance, and FAIR-ness
- Load data into Python using `pandas.read_csv`, `xarray.open_dataset`, or `pooch.retrieve`
- Submit work via the week-folder pattern in your `clmt5405-assignments` repo
:::

## Prerequisites

- You've worked through [Formats and metadata](./formats_and_metadata.md) and [Loading data](./loading_data.md).
- You have your `clmt5405-assignments` repo set up from Assignment 1, and can push to it.

## What to do

Throughout the steps below, replace `<weekN>` with the current week's folder name (e.g., `week1`, `week2`, etc).

1. **Find a dataset.** Look on [Zenodo](https://zenodo.org/), a NOAA or NASA portal, the [IRI Data Library](https://iridl.ldeo.columbia.edu/), or anywhere else scientific data lives. Pick something with a clear source page that you can document.

2. **Create a `<weekN>` folder inside your `clmt5405-assignments` repo.** (On Colab, first `git clone` your repo down if you don't already have it locally.)

   ~~~
   mkdir <weekN>
   cd <weekN>
   ~~~

3. **Write `dataset.md`** inside `<weekN>/` describing:
   - **What it is** — one sentence about the dataset (variable, region, time period).
   - **Where it lives** — the URL or DOI.
   - **Format** — CSV? NetCDF? Zarr? GeoTIFF?
   - **License** — what does the source page say? (CC-BY, public domain, "no restrictions," etc.)
   - **Provenance** — who created it?
   - **FAIR check** — does it have a persistent identifier? Is the metadata clear? Is the format machine-readable? Is the license open?

4. **Create `dataset_load.ipynb`** in the same `<weekN>/` folder. Add one code cell that loads the dataset using whichever of the three patterns from the [loading lecture](./loading_data.md) fits its format (`pandas.read_csv`, `xarray.open_dataset`, or `pooch.retrieve`). Run it — you should see *something* (a DataFrame, a Dataset, or a file path). No analysis needed; this just confirms the access works.

5. **Commit and push:**

   ~~~
   cd ..
   git status
   git add <weekN>/dataset.md <weekN>/dataset_load.ipynb
   git commit -m "Add <weekN> assignment"
   git push
   ~~~

Refresh your repo page on GitHub and confirm both files appear in `<weekN>/`.
