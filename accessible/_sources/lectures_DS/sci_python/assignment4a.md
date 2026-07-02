# Assignment 4a: NumPy and Matplotlib

**At-home assignment — worth 10 points.** When you're done, push your work to your week folder and post a link to your completed scripts on the matching Courseworks assignment.

The goal of this assignment is to gain comfort creating, visualizing, and computing with numpy arrays.

:::{admonition} Learning goals
:class: tip
This assignment exercises the scientific Python skills from this section:

- Create new arrays using `linspace` and `arange`
- Compute formulas with numpy arrays (element-wise math + broadcasting)
- Load data from `.npy` files
- Use reductions (`mean`, `std`, `nanmean`, `nanstd`) along specific axes
- Make 1D line plots, scatter plots, and add titles / axis labels
:::

:::{admonition} Working through this assignment with a screen reader
:class: important
Write your solutions as `.py` scripts in VS Code — one script per part is a good split (for example `assignment4a_part1.py` and `assignment4a_part2.py`). Run each with `python <name>.py` in the Git Bash terminal, and read the output with `Ctrl+Up`.

Because this is a *plotting* assignment, two extra notes:

- **Save your figures instead of (or in addition to) showing them.** End each plotting task with `plt.savefig("task_1_2.png")` (pick a name matching the task). The graded product is your *code* plus the saved figures — a sighted grader can open the PNGs, and you can verify each figure the non-visual way, as practiced in the [More Matplotlib lecture](more_matplotlib.md): print the data's shape and statistics, and print the figure's own properties back off it (`ax.get_xlabel()`, `len(ax.lines)`, …).
- **Each task below has a "Check yourself" note** giving the numbers your data should produce, so you can confirm you're on track without seeing the plots.

When you're done, follow the [submission instructions](#submission-instructions) at the bottom of the page.
:::

## Part 1: Creating and Manipulating Arrays

Start your script by importing numpy (as `np`) and matplotlib's `pyplot` (as `plt`).

### 1.1. Create two 2D arrays representing coordinates x, y on the cartesian plane

Both should cover the range (-2, 2) and have 100 points in each direction.

*Check yourself:* both arrays should have shape `(100, 100)` — build them with `np.linspace` and `np.meshgrid`, and print the shapes.

### 1.2. Visualize each 2D array using `pcolormesh`

Use the correct coordinates for the x and y axes.

*Check yourself:* the x-coordinate array holds the same value all the way down each column, so its pcolormesh is a smooth left-to-right gradient (−2 at the left edge to +2 at the right, identical in every row). The y-coordinate array is the same gradient rotated: bottom-to-top, identical in every column. You can confirm the "identical rows/columns" facts directly: `np.array_equal(xx[0], xx[50])` should print `True`.

### 1.3 Compute a smooth 2D field and plot it

Using your `x` and `y` arrays, compute the Gaussian "hill"

$$f = e^{-((x-0.5)^2 + 3y^2)}$$

(use `np.exp(...)`). Then plot `f` on the $x$/$y$ plane with `pcolormesh` and add a colorbar.

*Check yourself:* `f.max()` should print about `0.999`, and `np.unravel_index(f.argmax(), f.shape)` should land at row 49–50, column 62 — that is, at y ≈ 0, x ≈ 0.5, matching the formula (the hill is centered at (0.5, 0)). The plot shows a single bright oval bump there, wider in the x-direction than in y (the `3y**2` term makes it fall off three times faster vertically), fading to dark toward all edges.

### 1.4 Plot the mean of `f` over the x-axis

Average `f` across the x-direction (`f.mean(axis=...)`) so you're left with a 1D profile as a function of `y`. Plot it.

*Check yourself:* the profile is a bell curve peaking at y ≈ 0; its maximum value should print about `0.431`. (Which `axis` number averages across x? Print the result's shape — it must be `(100,)` — and remember rows run along y, columns along x.) This is a 1-D line you can also explore with MAIDR.

### 1.5 Plot the mean of `f` over the y-axis

Now average across the y-direction instead, leaving a profile as a function of `x`. Plot it.

*Check yourself:* another bell curve, but now peaking at x ≈ 0.5 (the hill's off-center x position), with a smaller maximum of about `0.253` — smaller because the hill is narrow in y, so averaging over y dilutes it more.

## Part 2: Analyze [ARGO](http://www.argo.ucsd.edu) Data

In this problem, we use real data from ocean profiling floats.
ARGO floats are autonomous robotic instruments that collect Temperature, Salinity, and Pressure data from the ocean. ARGO floats collect one "profile" (a set of measurements at different depths or "levels").

*The original page shows a diagram of the ARGO float cycle. In words:* a float is (1) deployed from a ship, (2) descends to a drifting depth of about 1000 m, (3) drifts freely there for about 10 days, (4) descends further to its profiling depth (2000–6000 m), then (5) **ascends to the surface while measuring** temperature, salinity, and pressure, (6) transmits the collected profile to a satellite at the surface, and (7) starts the next cycle. The result of each cycle is one *profile* — temperature and salinity as functions of depth, like the ones you're about to plot.

Each profile has a single latitude, longitude, and date associated with it, in addition to many different levels.

Let's start by using [pooch](https://www.fatiando.org/pooch/latest/) to download the data files we need for this exercise (install it once with `pip install pooch` if you don't have it, and be online when you run the script). The following code will give you a list of `.npy` files that you can open in the next step.

The following is Python code:

```python
import pooch
url = "https://github.com/earth-DS-ML/summer_2026/raw/refs/heads/main/assignments/more_matplotlib_data/argo_data_example.zip"
files = pooch.retrieve(url, processor=pooch.Unzip(), known_hash="650948c4b84690d370ad6f8aa9279c165f5302d3d9a7d330d3c0232b9d244e13")
print(files)
```

End of code.

This prints a list of seven file paths (the download is cached, so it only fetches once). The file *names* at the ends of the paths are: `date.npy`, `latitude.npy`, `levels.npy`, `longitude.npy`, `pressure.npy`, `salinity.npy`, `temperature.npy`.

### 2.1 Load each data file as a numpy array.

You can use whatever names you want for your arrays, but I recommend

`T`: temperature

`S`: salinity

`P`: pressure

`date`: date

`lat`: latitude

`lon`: longitude

`level`: depth level

**Note**: you have to actually look at the file names (the items in `files`) to know which file corresponds to which variable — print each path, or loop over `files` and match on the name. (Heads-up: the list is not necessarily in the order you'd guess.)

*Check yourself:* load with `np.load(...)`; the date array needs `np.load(f, allow_pickle=True)`.

### 2.2 Examine the shapes of T, S and P compared to `lon`, `lat`, `date` and `level`. How do you think they are related?

Based on the shapes, which dimensions do you think are shared among the arrays?

*Check yourself:* `T`, `S`, and `P` are all `(15, 109)`; `date`, `lat`, and `lon` are `(15,)`; `level` is `(109,)`. So each of the 15 rows is one profile (one surfacing: one date, one position), and each of the 109 columns is one depth level. The dates run from July to September 2017, and the positions cluster near 18–20°N, 59–60°W (the tropical North Atlantic, east of the Caribbean).

### 2.3 Make a plot for each column of data in T, S and P (three plots).

The vertical scale should be the `levels` data. Each plot should have a line for each column of data. It will look messy.

*Check yourself:* "messy" here means 15 overlapping profile lines per plot. Two practical hints: plotting a value-vs-depth profile with depth on the *vertical* axis means calling `plt.plot(T[i, :], level)` — data first, level second; and since level 0 is the surface, add `plt.gca().invert_yaxis()` so the surface appears at the top, the oceanographer's convention. What the temperature plot shows: all 15 lines start warm at the surface (28–29 °C), stay warm through the top ~30 levels, then drop steeply and converge to about 4 °C by the deepest level. You can hear the same story from one profile: print `T[0, :]` — or print its surface and bottom values, `T[0, 0]` and `T[0, -1]`.

### 2.4 Compute the mean and standard deviation of each of T, S and P at each depth in `level`.

*Check yourself:* "at each depth" means averaging across the 15 profiles — which axis is that? The result must have shape `(109,)`. Print the surface (index 0) mean of T: with the plain `T.mean(axis=...)` you should get `nan` — that's not a bug, it's the next task's topic.

### 2.5 Now make three similar plots, but show only the mean T, S and P at each depth. Show [error bars](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.errorbar.html) on each plot using the standard deviations.

*Check yourself:* `plt.errorbar(mean_T, level, xerr=std_T)` draws the mean profile with horizontal whiskers showing the spread at each depth. Because of the NaNs, portions of these plots will be blank or missing — again, that's expected until the next task.

### 2.6 Account For Missing Data

The profiles contain many missing values. These are indicated by the special "Not a Number" value, or `np.nan`.

When you take the mean or standard deviation of data with NaNs in it, the entire result becomes NaN. Instead, if you use the special functions `np.nanmean` and `np.nanstd`, you tell NumPy to ignore the NaNs.

Recalculate the means and standard deviations as in the previous sections using these functions and plot the results.

*Check yourself:* `np.isnan(T).sum()` should print `14` (14 missing values out of 1635). With `np.nanmean`, the surface mean temperature should print about `28.46` with a standard deviation of about `0.33`, dropping to a mean of about `3.66` at the deepest level. The mean surface pressure is about `3.0` (near zero — the surface), and about `2006.8` at the bottom: pressure in decibars is nearly the depth in meters, so this float profiles the top 2 km of ocean.

### 2.7 Create a scatter plot of the `lon`, `lat` positions of the ARGO float.

Use the [plt.scatter](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.scatter.html) function.

*Check yourself:* 15 dots tracing the float's drift. Confirm the ranges from the arrays: `lon.min()` / `lon.max()` should print about `-59.99` / `-58.75` and `lat.min()` / `lat.max()` about `18.30` / `20.21`. For a bonus, color the dots by time order (`c=np.arange(15)`) and add a colorbar to make the drift direction readable.

(submission-instructions)=
## Submission instructions

When you're done, save your completed scripts (and the PNG figures they produce) inside the current week's folder (e.g. `week4/`) in your private `clmt5405-assignments` GitHub repo. Then push the commit:

The following is a terminal command:

```bash
git add week4/
git commit -m "Submit assignment 4a"
git push
```

End of command.

Due Sunday night.
