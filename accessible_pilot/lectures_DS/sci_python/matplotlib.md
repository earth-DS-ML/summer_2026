# Visualizing Arrays with Matplotlib

:::{admonition} Following along with a screen reader
:class: important
Work through the examples as `.py` scripts in VS Code — see [Setting up an accessible
workflow](../computing_env/accessible_setup.md) and [Working with Python
scripts](../computing_env/working_with_scripts.md). Run a script in the Git Bash terminal
with `python yourfile.py`, and press `Alt+F2` to read the output.
:::

This page introduces **Matplotlib**, the library the course uses to visualize arrays. It is
a companion to the [NumPy](./numpy.md) page — the examples here use arrays built there, so
recreate them at the top of your script:

```python
import numpy as np
x = np.linspace(-2*np.pi, 2*np.pi, 100)
y = np.linspace(-np.pi, np.pi, 50)
xx, yy = np.meshgrid(x, y)
```

It can be hard to work with big arrays without seeing them, so Matplotlib lets you turn an
array into a picture.

:::{admonition} Exploring plots by sound and text
:class: tip
Matplotlib plots are images, so they aren't directly readable with a screen reader. On this
page (and wherever the NumPy page plots an array) each plotting snippet is followed by a
short **"What this shows"** note that describes, in words and numbers, what the plot reveals
about the underlying data — so you get the same insight without seeing the picture. As an
experiment we're evaluating, many of these charts can also be made **explorable
non-visually** — navigated by keyboard and played as sound — using a tool called **MAIDR**.
See [Trying MAIDR](./trying_maidr.md) for how to set that up and try it yourself.
:::

You write and run the plotting code exactly as scripts. To import Matplotlib's plotting
interface:

```python
from matplotlib import pyplot as plt
```

:::{note}
When you run a Matplotlib script from the terminal, the figure only appears if you ask for
it. Add `plt.show()` at the end of the script to open the plot window, or
`plt.savefig("figure.png")` to write it to a file. The course notebook shows plots
automatically; in a script you call one of these yourself. The "What this shows" notes mean
you don't need to open the window to follow the lesson.
:::

For plotting a 1D array as a line, we use the `plot` command:

```python
plt.plot(x)
plt.show()
```

**What this shows.** With a single argument, `plt.plot` puts the array's *index* (0, 1, 2,
…, 99) on the horizontal axis and its *value* on the vertical axis. Since
`x = np.linspace(-2*np.pi, 2*np.pi, 100)`, the result is a straight line rising steadily
from about −6.28 at the left (index 0) to about +6.28 at the right (index 99). It's a visual
confirmation that `linspace` produces evenly-spaced values.

There are many ways to visualize 2D data. Here we use `pcolormesh`, which draws a 2D array
as a grid of colored cells (a "heatmap"), with color encoding each cell's value:

```python
plt.pcolormesh(xx)
plt.show()
```

**What this shows.** `xx` holds the x-coordinate at every point of the grid, so its value
depends only on the column, not the row. The heatmap therefore looks like smooth vertical
stripes: dark/low (about −6.28) down the left edge, increasing left-to-right to bright/high
(about +6.28) at the right edge, identical in every row.

```python
plt.pcolormesh(yy)
plt.show()
```

**What this shows.** `yy` holds the y-coordinate, which depends only on the row. So this
heatmap is horizontal stripes instead of vertical: low (about −3.14) along the bottom row,
increasing to high (about +3.14) at the top row, identical in every column.

:::{admonition} Try it
:class: tip
Create `plotting.py`. Recreate `x`, `y`, `xx`, `yy` (the setup block above), then use
`plt.plot` to plot `np.cos(x)` against `x` (`plt.plot(x, np.cos(x))`). Then use
`plt.pcolormesh` to visualize the 2D array `xx + yy`. End the script with `plt.show()`.
(Don't worry about axis labels yet — those come later.) If you've set up MAIDR, try
exploring these plots non-visually too.
:::

## Recap

**Matplotlib** is how the course visualizes arrays — `plt.plot` for 1D lines,
`plt.pcolormesh` for 2D heatmaps — with `plt.show()` to display a figure or `plt.savefig()`
to save one. Because plots are images, every plot in this section is paired with a
data-first "What this shows" description, and points to [Trying MAIDR](./trying_maidr.md)
for exploring charts by sound and text. A later page, *More Matplotlib*, goes deeper into
figure and axis control and more plot types.
