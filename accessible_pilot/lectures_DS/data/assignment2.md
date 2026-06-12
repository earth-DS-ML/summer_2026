# Assignment 2: Explore a climate dataset

**At-home assignment — worth 10 points.** When you're done, push your work to your week
folder and post a link to that **folder** on the matching Courseworks assignment.

Pick any climate-related dataset that interests you — could be something you're considering
for your final project, or just something you're curious about. The point here is **practice
with the FAIR + access workflow**, not a commitment to a specific project.

:::{admonition} Doing this assignment with a screen reader
:class: important
Do the steps below in your VS Code setup — the Git Bash terminal for the `git` commands
(press `Alt+F2` to read output), and the VS Code editor for writing the files. The data
loader is a `.py` script you run with `python`. See [Setting up an accessible
workflow](../computing_env/accessible_setup.md).
:::

:::{admonition} Learning goals
:class: tip
This assignment exercises the data-discovery and access skills from this section:

- Locate a dataset in a public scientific repository (Zenodo, NOAA, NASA, IRI, etc.)
- Document a dataset's format, license, provenance, and FAIR-ness
- Load data into Python using `pandas.read_csv`, `xarray.open_dataset`, or `pooch.retrieve`
- Submit work via the week-folder pattern in your `clmt5405-assignments` repo
:::

## Prerequisites

- You've worked through [Formats and metadata](./formats_and_metadata.md) and [Loading
  data](./loading_data.md).
- You have your `clmt5405-assignments` repo set up from Assignment 1, and can push to it.

## What to do

Throughout the steps below, replace `<weekN>` with the current week's folder name (e.g.,
`week1`, `week2`, etc).

1. **Find a dataset.** Look on [Zenodo](https://zenodo.org/), a NOAA or NASA portal, the
   [IRI Data Library](https://iridl.ldeo.columbia.edu/), or anywhere else scientific data
   lives. Pick something with a clear source page that you can document.

2. **Create a `<weekN>` folder inside your `clmt5405-assignments` repo.** (If you don't
   already have your repo locally, `git clone` it down first, then open the folder in VS
   Code.)

The following is a terminal command:

   ~~~
   cd clmt5405-assignments
   mkdir <weekN>
   cd <weekN>
   ~~~

End of command.

3. **Write `dataset.md`** inside `<weekN>/` describing:
   - **What it is** — one sentence about the dataset (variable, region, time period).
   - **Where it lives** — the URL or DOI.
   - **Format** — CSV? NetCDF? Zarr? GeoTIFF?
   - **License** — what does the source page say? (CC-BY, public domain, "no restrictions,"
     etc.)
   - **Provenance** — who created it?
   - **FAIR check** — does it have a persistent identifier? Is the metadata clear? Is the
     format machine-readable? Is the license open?

4. **Create `dataset_load.py`** in the same `<weekN>/` folder. Write a few lines that load
   the dataset using whichever of the three patterns from the [loading
   lecture](./loading_data.md) fits its format (`pandas.read_csv`, `xarray.open_dataset`, or
   `pooch.retrieve`), and `print(...)` something so you can confirm it worked. Run it with
   `python dataset_load.py` and read the output with `Alt+F2` — you should see *something* (a
   DataFrame, a Dataset, or a file path). No analysis needed; this just confirms the access
   works.

5. **Commit and push:**

The following is a terminal command:

   ~~~
   cd ..
   git status
   git add <weekN>/dataset.md <weekN>/dataset_load.py
   git commit -m "Add <weekN> assignment"
   git push
   ~~~

End of command.

Refresh your repo page on GitHub and confirm both files appear in `<weekN>/`.
