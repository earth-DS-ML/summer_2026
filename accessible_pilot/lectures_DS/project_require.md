# Final Project Requirements

:::{admonition} Following along with a screen reader
:class: important
Work through the examples as `.py` scripts in VS Code — see [Setting up an accessible workflow](./computing_env/accessible_setup.md) and [Working with Python scripts](./computing_env/working_with_scripts.md). Run a script in the Git Bash terminal with `python yourfile.py`, and press `Ctrl+Up` to read the output. This page has no code to work through — it lays out the final-project requirements — but two parts matter especially for the accessible workflow: the note under **Technical Requirements** on building the required `.ipynb` notebook from a plain `.py` script, and the written-description rule for figures under **Analysis Requirements**.
:::

## Team size and project structure

Work in **groups of 2–3 people** around a shared project (a common topic and repository). Groups are preferred, but you may work on your own if you would rather. **Each member contributes their own notebook**, carrying out a complementary piece of the work — this can be a different (but related) dataset, or a different analysis of the same data. Every member is still expected to understand the *entire* group project, not just their own piece.

## Learning Goals

The goal of the final project is to assess your ability to combine and apply the skills you have learned in class in the context of a real-world research/data-analysis problem. Our class has mostly focused on tools for data analysis and visualization, so this must be the focus of your final project. Specifically, we seek to assess your ability to do the following tasks:

*   Discover and download real datasets in standard formats (e.g. CSV, netCDF, zarr etc)
*   Load the data into Pandas or Xarray, performing any necessary data cleanup (dealing with missing values, proper time encoding, etc.) along the way.
*   Perform a realistic analysis — for example, grouping, aggregating, and applying formulas or other computations.
*   Visualize your results in well-formatted plots.

## Dataset Requirements

Your datasets can involve data collected by yourself or your lab, or can come from a public data repository. Ideally, your choice of dataset should be driven by your own interests. It is acceptable (and encouraged) to have your final project for this class build on an ongoing research or applied project. _However, it is not acceptable to have your final project overlap with the final project for another class._

For your datasets, you can get them from a few sources:
- We have already played around with a few datasets in class, and there are more coming (look ahead at the material posted on course website). Feel free to base your project around one or few of these if they interest you.
- The material in our [original book](https://earth-env-data-science.github.io/intro.html) towards the end (section on Climate Model Data), which we won't have time to cover in this shorter course, contains examples of other datasets that you may be able to leverage.
- There are [previous projects](https://earth-env-data-science.github.io/projects.html), which you can use as inspiration and even source of the datasets (if the links still work!)
- There are a number of interesting datasets on <https://catalog.leap.columbia.edu/>

Also here are a couple of other links that may also help:

*   [IRI Data Library](http://iridl.ldeo.columbia.edu/)
*   [USGS Data Catalog](https://data.usgs.gov/datacatalog/)
*   [NOAA National Climate Data Center](https://www.ncdc.noaa.gov/)
*   [NASA Earth Science Data](https://earthdata.nasa.gov/)
*   [Earthmover Marketplace](https://app.earthmover.io/marketplace) — curated cloud-hosted (Zarr) climate datasets, analysis-ready
*   [Microsoft Planetary Computer](https://planetarycomputer.microsoft.com/) — large catalog of satellite imagery and climate data on Azure
*   [NASA earthaccess](https://earthaccess.readthedocs.io/) — Python package for NASA's Earthdata Cloud (handles auth + S3)

You may use just one dataset, or you may choose to combine multiple datasets, depending on your question.

## Analysis Requirements

The goal here is the same as with any data-analysis project: to use the data to investigate a question or idea. Your question can come from anywhere — physical science, policy, sustainability, public health, economics, environmental justice — as long as it is something real data can help you explore. To define a good question you will likely draw on your experience _outside_ our class, from your other classes, work, or independent projects. It is also acceptable to use this project to reproduce results from a published study or report that you find interesting, provided you have access to the original data.

Whatever you choose, you should _clearly define the question or hypothesis that you aim to investigate with your analysis_. This will determine what you have to do.

The results of this analysis will be figures — beautiful figures that clearly answer your question / hypothesis. **Each member's notebook should contain at least 3 and no more than 5 figures.** All figures must have titles, clearly labeled axes, informative colormaps / colorbars, and legends, where appropriate.

**Each figure must also be accompanied by a short written description** — two to four plain-language sentences in a markdown cell right below the figure: what is on each axis, what the main curves or fields look like, and what the reader should take away, anchored by a number or two. This is the same "What this shows" treatment used throughout the accessible lectures, and it is good practice for everyone: a figure you cannot summarize in words is a figure you don't fully understand yet, and the description makes your notebook readable for screen-reader users and clearer for every other reader too.

:::{admonition} Checking your figures without seeing them
:class: tip
Every formatting requirement above can be verified programmatically, the way we practiced in [More Matplotlib](./sci_python/more_matplotlib.md): `ax.get_title()`, `ax.get_xlabel()`, `ax.get_ylabel()`, `ax.get_legend()`, `len(fig.axes)`, and printing the data's `shape` / `min` / `max` / `mean` before you trust the plot. For line and scatter plots you can also explore the plotted values by sound and text with [MAIDR](./sci_python/trying_maidr.md); for 2-D maps, slice out a row or column and examine it as a 1-D profile. The numbers you gather this way are exactly the anchors your written figure descriptions need.
:::

## Technical Requirements

Your final project must meet the following technical requirements

*   **One Jupyter notebook per group member** (`.ipynb`, *not* a plain `.py` script), each carrying out a complementary part of the project and each able to stand on its own. The notebook is what you submit and present, so it must be **readable and presentable**: a narrative of markdown cells (explaining the question, the steps, and the figures) interleaved with the code and its output — think of it like the in-class assignment notebooks, not a code file. (Screen-reader users: you do *not* have to work in the notebook interface to meet this — see the admonition right below this list.)
*   Stored in a standalone public github repo
*   Each group member contributes their own commits, under their own GitHub account
*   **Self-contained and reproducible, exactly like our in-class assignment notebooks.** All data is either stored in the repo itself or downloaded / accessed from within the notebook — *no manual download steps*. Anyone should be able to open your repo on the LEAP hub, run the notebook top to bottom, and reproduce your results without doing anything outside the notebook.
*   Complete explanatory text / equations included in each notebook as markdown cells — including the short written description under each figure (see Analysis Requirements above)
*   Each notebook must execute in sequence with no errors (Kernel → Restart & Run All should run cleanly from a fresh kernel)
*   The whole github repo must be configured to run on [LEAP hub](https://leap.2i2c.cloud/)

:::{admonition} Building the required `.ipynb` from a `.py` script (screen-reader path)
:class: important
The deliverable is a readable `.ipynb` notebook for everyone — that policy doesn't change. What can change is how you *produce* it. You can do all of your work in a plain `.py` script and convert it to a notebook at the end.

**Step 1 — write the project as a script with cell markers.** Write your project as an ordinary `.py` script in VS Code, split into cells with `# %%` markers (the "Running code piece by piece" pattern from [Working with Python scripts](./computing_env/working_with_scripts.md)). A line containing `# %%` starts a **code cell**; a line containing `# %% [markdown]` starts a **markdown cell**, whose narrative text you write as `#` comment lines. For example:

The following is Python code:

```python
# %% [markdown]
# # Sea surface temperature and ENSO
# This notebook investigates whether ... (your question here).
# Data: NOAA ERSST v5, loaded over OPeNDAP in the next cell.

# %%
import xarray as xr
# ... analysis code for this step ...
```

End of code.

**Step 2 — develop in the terminal as usual.** Run the script in the Git Bash terminal with `python yourfile.py` and read the output with `Ctrl+Up`, exactly as you have all course. Because it is one linear script, running it top to bottom *is* the "restart and run all" test.

**Step 3 — export to a notebook.** When the script runs cleanly end to end, open the VS Code Command Palette (`Ctrl+Shift+P`) and run **"Jupyter: Export Current Python File as Jupyter Notebook"**. Every `# %%` block becomes a code cell and every `# %% [markdown]` block becomes a markdown cell (the leading `#` comment markers are stripped automatically). Save the exported file as `yourname_project.ipynb` in your repo.

**Step 4 — execute the exported notebook to fill in outputs.** The freshly exported notebook has no outputs stored in it yet. Fill them in — and re-verify the runs-with-no-errors requirement in notebook form — by running it once from the terminal (needs `pip install jupyter`):

The following is a terminal command:

```bash
jupyter nbconvert --to notebook --execute --inplace yourname_project.ipynb
```

End of command.

This runs the notebook top to bottom and saves each cell's printed output and figures into the file, so a grader (or teammate) opening it sees the full narrative: text, code, output, and your figures with their written descriptions.

**Step 5 — push both files.** Push the `.ipynb` *and* the `.py` source to the project repo — the notebook is what is graded and presented, and the script alongside it is good practice (and shows your workflow in the commit history).

If anything about the export looks wrong, ask a teammate or the TA to open the notebook and compare it against your script — and do this well before the deadline, not the night of.
:::

You _must_ use either Pandas or Xarray (or both) in some part of your project. You _may_ use other scientific python libraries as well, if you wish, to facilitate some analysis that is not possible with Xarray / Pandas alone.

Some other libraries you may wish to consider (but not required to) are:

*   [SciPy](https://docs.scipy.org/doc/scipy/reference/) for interpolation, signal processing, spectral analysis, linear algebra, and other general purpose scientific computing routines
*   [Statsmodels](https://github.com/statsmodels/statsmodels) for advanced statistical analysis
*   [Scikit-image](https://scikit-image.org/) for image processing
*   [Scikit-learn](https://scikit-learn.org/stable/) for machine learning
*   [XGCM](https://xgcm.readthedocs.io/en/latest/) for working with finite-volume data from general circulation models
*   [XESMF](https://xesmf.readthedocs.io/en/latest/) for regridding of gridded data
*   [Pyresample](https://pyresample.readthedocs.io/en/latest/) for resampling (reprojection) of satellite data
*   [EOFS](https://ajdawson.github.io/eofs/) for empirical orthogonal function analysis
*   [windspharm](https://ajdawson.github.io/windspharm/latest/) for spherical harmonic analysis of global atmospheric wind data
*   [metpy](https://unidata.github.io/MetPy/latest/index.html) a collection of tools in Python for reading, visualizing, and performing calculations with weather data

## Project Approval

You must have your project — your dataset(s) and general scope — approved by the instructor. Submit a single short proposal by **Sunday 21 June 2026** (as with the weekly assignments, anything not submitted by the deadline receives a zero):

-  Create a new **public** GitHub repo for your project.
-  Add a `README.md` that contains: your group members, a rough title, the question or hypothesis you plan to investigate, links to the dataset(s) you plan to use, and a three-sentence summary of the analysis you plan to do. (It is very important that you have identified your datasets and know where to get them at this stage.)
-  On the **project proposal** assignment on Courseworks, submit **both**: (1) the link to your project repo (URL), and (2) a text entry with the **full names of everyone in your group** (or just your own name, if you are working solo) — entering names is required.

## In-Class Presentations

Presentations take place in week 6, on **Tuesday 30 June and Thursday 2 July 2026** — we'll assign each group to one of the two days.

**Each member presents their own notebook for about 5 minutes**, followed by about **5 minutes of questions for the whole group**. (So a group of three runs ~15 minutes of presentation plus ~5 minutes of Q&A; a pair runs ~10 + 5; on your own, ~5 + 5.) Don't prepare slides; instead, open your notebook from GitHub on the presentation computer and walk us through it. (If the presentation computer doesn't work with your screen-reader setup, you are welcome to present from your own laptop instead — just make sure what you show is the pushed, up-to-date notebook from your GitHub repo.)

Your presentation should be concise and cover:

*   What data did you analyze, and how did you load it?
*   What is the most interesting figure you made? (Show us the figure — and since every figure comes with its written description, reading that description aloud is a fine way to present it.)
*   What was the biggest challenge you faced?

**Q&A — this is part of your evaluation.** After each talk, the instructor and TA will ask **each group member a question or two**, often about a part of the project they did *not* personally present. Everyone is expected to be able to explain any part of the analysis, so make sure you understand the whole project, not just your own piece.

_Don't forget to make your project repo **public** — otherwise we won't be able to see it._

> Note: Try to finish your project before your presentation. But it's okay if it isn't fully complete by then — still present what you have, the challenges you're facing, and your plan to finish. The final code is due that Sunday, 5 July (see below).

## Final submission and evaluation

To wrap up your project, confirm your final submission by **the end of Sunday night, 5 July 2026** (the same Sunday-night pattern as the weekly assignments):

- Make sure all the project notebooks and code are pushed to your public project repo.
- Post a link to your project repo on the **final project** assignment on Courseworks to confirm you are done and don't plan to make further changes — this tells us the project is ready to grade.

**How the project is evaluated.** Your grade reflects both the work itself and — for group projects — each member's individual understanding and contribution:

1. **The project** — the analysis, figures (with their written descriptions), and notebooks (see the requirements above).
2. **The presentation and Q&A** — how clearly the group presents, and how each member answers questions about the project (see above).
3. **Individual contribution** — each member submits and presents their **own notebook** (a complementary piece of the project), committed under their own GitHub account. Your individual notebook is the main evidence of your contribution.

In a group, a member whose understanding (in the Q&A) or contribution (in the commit history) is clearly lacking may receive a lower individual grade than their teammates — so make sure everyone stays involved throughout. (If you're working solo, this individual differentiation simply doesn't apply.)
