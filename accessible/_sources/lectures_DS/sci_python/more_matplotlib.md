# More Matplotlib

:::{admonition} Following along with a screen reader
:class: important
Work through the examples as `.py` scripts in VS Code — see [Setting up an accessible workflow](../computing_env/accessible_setup.md) and [Working with Python scripts](../computing_env/working_with_scripts.md). Run a script in the Git Bash terminal with `python yourfile.py`, and press `Ctrl+Up` to read the output.
:::

A figure is how you actually *see* your data — and how you communicate it to everyone else. Matplotlib is the dominant plotting library in Python, and learning to drive it deliberately (rather than by trial and error) pays off every time you plot a temperature record, a map, or a scatter of measurements. In the previous lecture we made quick plots while learning NumPy; here we slow down to understand how Matplotlib is built.

The key idea is the **Figure / Axes** model: a *Figure* is the whole canvas, and each *Axes* is a single plot living on it. Once that clicks, everything else — subplots, labels, colors, 2-D fields, vector fields — follows from it. We'll work through line plots and their styling, then the main ways to visualize 2-D data (`imshow`, `pcolormesh`, `contour`, `quiver`, `streamplot`).

:::{admonition} In-class assignment — 10 points
:class: note
The **"Try it"** exercises on this page are part of your in-class assignment for this section. Complete them as `.py` scripts, push them to your week folder, and post the link on the matching **Courseworks** assignment. (One 10-point assignment covers all the lecture pages in this section.)
:::

:::{admonition} Checking a figure without seeing it
:class: tip
This whole lecture is about producing pictures — so how do you work through it without looking at them? Two habits, used throughout this page:

1. **Every plot below is followed by a "What this shows" note** describing the rendered figure in words and numbers, so you always know what your code just drew.
2. **A figure is a Python object you can interrogate.** Everything you set on a plot can be read back off it with `print()`:

   - `len(fig.axes)` — how many axes (plots) the figure contains.
   - `len(ax.lines)` — how many lines are drawn in an axes.
   - `ax.lines[0].get_color()`, `.get_linestyle()`, `.get_linewidth()` — how a line is styled.
   - `ax.get_xlabel()`, `ax.get_ylabel()`, `ax.get_title()` — the labels you attached.
   - `ax.get_xlim()`, `ax.get_ylim()` — the axis ranges.

   The "Check it" blocks below practice this: they confirm, in plain printed text, the same facts a sighted reader confirms with a glance. This is not a workaround — interrogating figure objects is exactly how programmers write automated tests for plots.

And remember the habit from the NumPy lecture: before trusting any figure, print the **data** you are plotting (`shape`, `min`, `max`, `mean`). If the data is right and the figure-object checks pass, the picture is right.
:::

:::{admonition} Hearing a plot (optional)
:class: note
The line, scatter, and bar plots on this page can also be explored non-visually — moved through point by point with arrow keys, values read as text — using **[MAIDR](./trying_maidr.md)**. MAIDR does not (yet) handle the 2-D map plots in the second half of the page (`pcolormesh`, `contour`, `quiver`); for those, the trick from the NumPy lecture applies — slice out one row or column and examine it as a 1-D line.
:::

## Figure and Axes

The *figure* is the highest level of organization of matplotlib objects. If we want, we can create a figure explicitly. Start a file `figures.py` with the imports we'll use throughout:

The following is Python code:

```python
from matplotlib import pyplot as plt
import numpy as np

fig = plt.figure()
```

End of code.

**What this shows.** Nothing yet! A figure with no axes is a blank canvas — rendered, it is an empty white rectangle. By default it is 6.4 by 4.8 inches; `plt.figure(figsize=(13, 5))` would make a wider, shorter canvas. (In a script, remember that nothing is displayed at all until you call `plt.show()` or save the figure — see the note on the NumPy page.)

**Check it.** The figure knows its own size and contents:

The following is Python code:

```python
print(fig.get_size_inches())
print(len(fig.axes))
```

End of code.

The following is the expected output:

```
[6.4 4.8]
0
```

End of output.

To draw anything, the figure needs an **axes** inside it. We add one with `fig.add_axes([left, bottom, width, height])` — the four numbers are in *figure coordinates* (0 to 1, where 0 is the left/bottom edge of the figure and 1 is the right/top). `[0, 0, 1, 1]` makes the axes fill the entire figure:

The following is Python code:

```python
fig = plt.figure()
ax = fig.add_axes([0, 0, 1, 1])
```

End of code.

**What this shows.** An empty plot frame filling the whole canvas: a rectangle with tick marks 0.0 to 1.0 along both the bottom and left edges, and nothing drawn inside it yet.

With a different bbox we can make the axes smaller — `[0, 0, 0.5, 1]` puts the axes on the left half of the figure:

The following is Python code:

```python
fig = plt.figure()
ax = fig.add_axes([0, 0, 0.5, 1])
```

End of code.

**What this shows.** The same empty plot frame, but tall and narrow, occupying only the left half of the canvas; the right half of the figure is blank white space.

We can add **multiple** axes to one figure by calling `add_axes` with different bboxes:

The following is Python code:

```python
fig = plt.figure()
ax1 = fig.add_axes([0, 0, 0.5, 1])
ax2 = fig.add_axes([0.5, 0, 0.3, 0.5], facecolor='g')
```

End of code.

**What this shows.** Two plot frames on one canvas: the tall empty frame on the left half as before, and next to it a smaller frame — 30% of the figure's width, the bottom 50% of its height — whose background is filled solid green (`facecolor='g'`).

**Check it.**

The following is Python code:

```python
print(len(fig.axes))
print(ax2.get_facecolor())
```

End of code.

The following is the expected output (the tuple is the RGBA code for pure green):

```
2
(0.0, 0.5, 0.0, 1)
```

End of output.

### Subplots

`subplots` is the standard way to create multiple axes in a regular grid. Pass `nrows` and `ncols` to lay out the grid — matplotlib returns a single `Axes` if there's only one, or a NumPy array of `Axes` if there are several.

Calling `subplots` on a figure carves it up into a regular grid of axes — here, 2 rows × 3 columns:

The following is Python code:

```python
fig = plt.figure()
axes = fig.subplots(nrows=2, ncols=3)
```

End of code.

**What this shows.** Six identical empty plot frames arranged in two rows of three, evenly spaced on the canvas, each with its own 0-to-1 tick labels.

If we make the figure smaller, the axes labels will overlap — matplotlib doesn't auto-resize them:

The following is Python code:

```python
fig = plt.figure(figsize=(1, 1))
axes = fig.subplots(nrows=2, ncols=3)
```

End of code.

**What this shows.** The same 2×3 grid crammed onto a 1-inch-square canvas: the six frames shrink to tiny boxes and their tick labels collide into an unreadable jumble. The lesson: matplotlib draws exactly what you ask, readable or not.

Notice that `subplots(nrows=2, ncols=3)` returned a **2D array** of `Axes` objects — we can inspect it directly, or index into it like any NumPy array:

The following is Python code:

```python
print(axes.shape)
print(axes[0, 0])
```

End of code.

The following is the expected output:

```
(2, 3)
Axes(0.125,0.53;0.227941x0.35)
```

End of output.

(The numbers in the `Axes(...)` line are that axes' position on the canvas in figure coordinates — left, bottom; width × height.)

There is a shorthand for doing this all at once.

:::{tip}
**This is our recommended way to create new figures!** From here on, most examples will use the `fig, ax = plt.subplots(...)` pattern.
:::

The following is Python code:

```python
fig, ax = plt.subplots()
print(ax)

fig, axes = plt.subplots(ncols=2, figsize=(8, 4), subplot_kw={'facecolor': 'g'})
print(axes.shape)
```

End of code.

The following is the expected output:

```
Axes(0.125,0.11;0.775x0.77)
(2,)
```

End of output.

**What this shows.** The first call makes one figure with a single empty axes. The second makes a wider figure with two axes side by side, and — because of `subplot_kw={'facecolor': 'g'}` — both plot backgrounds are solid green.

:::{admonition} Try it
:class: tip
Use `plt.subplots(nrows=2, ncols=2, figsize=(8, 8))` to create a 2×2 grid of axes. Print the shape of the returned `axes` object (it's a 2D NumPy array of `Axes`), and print the top-right axes, `axes[0, 1]`. Then change `figsize` to `(4, 4)`, and confirm the canvas size changed by printing `fig.get_size_inches()`.
:::

## Drawing into Axes

All plots are drawn into a specific axes. Matplotlib supports two styles for this — the implicit `plt.plot(...)` style (which uses the most recently created axes), and the **explicit object-oriented style** (`ax.plot(...)`) where you call methods on the axes directly. The OO style is much clearer once you have more than one axes on a figure, and it's what we recommend throughout the rest of this course. (See the [matplotlib coding style guide](https://matplotlib.org/stable/users/explain/figure/api_interfaces.html) for more.)

Let's create some data we'll plot throughout the rest of this lecture — a smooth cosine and a higher-frequency sine on the same x-range:

The following is Python code:

```python
x = np.linspace(-np.pi, np.pi, 100)
y = np.cos(x)
z = np.sin(6*x)
```

End of code.

Keep these three lines near the top of your file — every example below uses them. It's worth knowing these two curves well, since the whole styling section draws them over and over:

- `y = cos(x)` on −π ≤ x ≤ π is a **single smooth arch**: it starts at −1 on the left edge, rises through 0 at x = −π/2, peaks at +1 at x = 0, and descends symmetrically back to −1 at the right edge.
- `z = sin(6x)` on the same range is a **fast wave**: six full up-and-down oscillations between +1 and −1, crossing zero every ~0.52 units of x.

The following is Python code:

```python
fig, ax = plt.subplots()
ax.plot(x, y)
plt.show()
```

End of code.

**What this shows.** The cosine arch just described, drawn as a single smooth line in matplotlib's default blue, on axes that auto-scale to the data (x from about −3.1 to 3.1, y from −1 to 1).

Calling `plt.plot(x, y)` right after creating the axes would draw the identical line, because the implicit style targets the most recently created axes. This starts to matter when we have multiple axes to worry about:

The following is Python code:

```python
fig, axes = plt.subplots(figsize=(8, 4), ncols=2)
ax0, ax1 = axes
ax0.plot(x, y)
ax1.plot(x, z)
plt.show()
```

End of code.

**What this shows.** Two plots side by side: the left one holds the slow cosine arch, the right one the fast six-cycle sine wave. With the explicit `ax0` / `ax1` names there is no ambiguity about which curve lands where.

**Check it.** Each axes knows what's been drawn into it:

The following is Python code:

```python
print(len(ax0.lines), len(ax1.lines))
print(np.allclose(ax1.lines[0].get_ydata(), z))
```

End of code.

The following is the expected output:

```
1 1
True
```

End of output.

:::{admonition} Try it
:class: tip
Create a figure with two side-by-side axes (`fig, axes = plt.subplots(ncols=2, figsize=(8, 4))`). Plot `np.sin(x)` on the left axes and `np.cos(x)` on the right, using the explicit `axes[0].plot(...)` / `axes[1].plot(...)` pattern. Confirm with `len(axes[0].lines)` that each axes got exactly one line.
:::

## Labeling Plots

A plot without labels is hard to interpret. Each `Axes` object has `set_xlabel`, `set_ylabel`, and `set_title` methods that take a string. When axes have their own labels and titles, they can crowd each other on the figure — `plt.tight_layout()` fixes the spacing automatically.

The following is Python code:

```python
fig, axes = plt.subplots(figsize=(8, 4), ncols=2)
ax0, ax1 = axes

ax0.plot(x, y)
ax0.set_xlabel('x')
ax0.set_ylabel('y')
ax0.set_title('x vs. y')

ax1.plot(x, z)
ax1.set_xlabel('x')
ax1.set_ylabel('z')
ax1.set_title('x vs. z')

# squeeze everything in
plt.tight_layout()
plt.show()
```

End of code.

**What this shows.** The same two panels as before, but now each has "x" written under its horizontal axis, "y" (left panel) or "z" (right panel) alongside its vertical axis, and a title above it. `tight_layout()` nudged the panels apart so the right panel's y-label doesn't overlap the left panel.

**Check it.**

The following is Python code:

```python
print(ax0.get_title())
print(ax1.get_ylabel())
```

End of code.

The following is the expected output:

```
x vs. y
z
```

End of output.

:::{admonition} Try it
:class: tip
Take the two-axes figure from the previous Try-it. Add x/y labels and a title to each axes, then call `plt.tight_layout()`. Verify your labels stuck by printing `axes[0].get_title()` and `axes[1].get_xlabel()`.
:::

## Customizing Line Plots

Once you have a basic plot, you'll usually want to control its appearance: which color the line is, whether it's solid or dashed, whether to mark each data point, and how the axes themselves are styled. The sub-sections below walk through the main knobs.

There are several ways to plot two (or more) datasets on the same axes. The first style passes alternating `x, y` pairs to a single `plot` call:

The following is Python code:

```python
fig, ax = plt.subplots()
ax.plot(x, y, x, z)
plt.show()
```

End of code.

**What this shows.** Both curves on one plot: the slow cosine arch in blue and the fast sine wave in orange, crossing each other repeatedly. Blue and orange are the first two colors of matplotlib's default cycle, assigned in plotting order.

The second style calls `plot` separately for each dataset. The result is the same — matplotlib automatically assigns a different color to each line from the default cycle (more on that below):

The following is Python code:

```python
fig, ax = plt.subplots()
ax.plot(x, y)
ax.plot(x, z)
plt.show()
```

End of code.

**What this shows.** The identical picture: cosine in blue (plotted first), sine in orange (second). If you reverse the two `plot` calls, the colors swap — the fast wave becomes blue and the arch orange — because color follows plotting *order*, not the data.

It's simple to switch which arrays go on which axis:

The following is Python code:

```python
fig, ax = plt.subplots()
ax.plot(y, x, z, x)
plt.show()
```

End of code.

**What this shows.** The same two curves turned on their side: now the vertical axis runs from −π to π (the old x), and the horizontal axis from −1 to 1. The blue cosine becomes a smooth arc bowing to the right: it starts at the top-left, sweeps right to touch +1 at mid-height (y = 0), and returns to the bottom-left. The orange sine becomes a dense horizontal zigzag bouncing between the left and right edges all the way down.

A "parametric" graph — plotting one data array against the other, with x nowhere on the plot:

The following is Python code:

```python
fig, ax = plt.subplots()
ax.plot(y, z)
plt.show()
```

End of code.

**What this shows.** A woven, braided pattern of crossing loops (a *Lissajous curve*) filling the square from −1 to 1 in both directions: as x sweeps along, the point (cos x, sin 6x) traces overlapping arcs that cross each other in a lattice. Pretty, but not very interpretable — a reminder that you can plot any array against any other.

### Line Styles

The following is Python code:

```python
fig, axes = plt.subplots(figsize=(16, 5), ncols=3)
axes[0].plot(x, y, linestyle='dashed')
axes[0].plot(x, z, linestyle='--')

axes[1].plot(x, y, linestyle='dotted')
axes[1].plot(x, z, linestyle=':')

axes[2].plot(x, y, linestyle='dashdot', linewidth=5)
axes[2].plot(x, z, linestyle='-.', linewidth=0.5)
plt.show()
```

End of code.

**What this shows.** Three panels, each with the familiar blue arch and orange fast wave, drawn in different styles. Panel 1: both curves dashed — note that the word `'dashed'` and the shorthand `'--'` produce exactly the same dashes. Panel 2: both dotted (`'dotted'` = `':'`). Panel 3: both dash-dot (`'dashdot'` = `'-.'`), but with very different `linewidth` — the blue arch is a thick heavy stroke (width 5) while the orange wave is hair-thin (width 0.5).

**Check it.**

The following is Python code:

```python
print(axes[0].lines[0].get_linestyle())
print(axes[2].lines[0].get_linewidth())
```

End of code.

The following is the expected output (matplotlib stores every dash style in the shorthand form):

```
--
5.0
```

End of output.

### Colors

As described in the [colors documentation](https://matplotlib.org/2.0.2/api/colors_api.html), there are some special codes for commonly used colors:

- b: blue
- g: green
- r: red
- c: cyan
- m: magenta
- y: yellow
- k: black
- w: white

The following is Python code:

```python
fig, ax = plt.subplots()
ax.plot(x, y, color='k')
ax.plot(x, z, color='r')
plt.show()
```

End of code.

**What this shows.** The arch drawn in black (`'k'`) and the fast wave in red (`'r'`), instead of the default blue/orange.

Other ways to specify colors:

The following is Python code:

```python
fig, axes = plt.subplots(figsize=(16, 5), ncols=3)

# grayscale
axes[0].plot(x, y, color='0.8')
axes[0].plot(x, z, color='0.2')

# RGB tuple
axes[1].plot(x, y, color=(1, 0, 0.7))
axes[1].plot(x, z, color=(0, 0.4, 0.3))

# HTML hex code
axes[2].plot(x, y, color='#00dcba')
axes[2].plot(x, z, color='#b029ee')
plt.show()
```

End of code.

**What this shows.** Three panels demonstrating three color systems. Panel 1, grayscale as a string from 0 (black) to 1 (white): the arch is pale light-gray (`'0.8'`), the wave near-black dark gray (`'0.2'`). Panel 2, (red, green, blue) tuples: the arch is a vivid pink-magenta, the wave a dark teal green. Panel 3, HTML hex codes: the arch bright turquoise, the wave bright purple.

There is a default color cycle built into matplotlib — the sequence of colors that `plot` steps through when you don't specify one. You can inspect it directly; it's stored in matplotlib's runtime config (`rcParams`):

The following is Python code:

```python
print(plt.rcParams['axes.prop_cycle'])
```

End of code.

The following is the expected output:

```
cycler('color', ['#1f77b4', '#ff7f0e', '#2ca02c', '#d62728', '#9467bd', '#8c564b', '#e377c2', '#7f7f7f', '#bcbd22', '#17becf'])
```

End of output.

That's **ten** hex codes: the blue and orange you've been seeing, then green, red, purple, brown, pink, gray, olive, cyan. If you call `plot` more times than there are colors in the cycle, matplotlib **wraps around** — colors start repeating:

The following is Python code:

```python
fig, ax = plt.subplots(figsize=(12, 10))
for factor in np.linspace(0.2, 1, 11):
    ax.plot(x, factor*y)
plt.show()
```

End of code.

**What this shows.** Eleven cosine arches of growing amplitude (0.2, 0.28, 0.36, … up to 1.0), nested inside one another like layers of an onion, all crossing zero together at x = ±π/2. Each gets the next color of the cycle — but because there are 11 lines and only 10 colors, the 11th (the tallest arch) **reuses the first color**: both the smallest and the largest arch are the same default blue.

**Check it.** Ask the lines themselves:

The following is Python code:

```python
print(ax.lines[0].get_color(), ax.lines[10].get_color())
print(ax.lines[0].get_color() == ax.lines[10].get_color())
```

End of code.

The following is the expected output:

```
#1f77b4 #1f77b4
True
```

End of output.

### Markers

There are [lots of different markers](https://matplotlib.org/api/markers_api.html) available in matplotlib! Markers draw a symbol at every data point, on top of (or instead of) the connecting line. Here we plot only the first 20 points (`x[:20]` spans −3.14 to −1.94) so the individual markers are distinguishable:

The following is Python code:

```python
fig, axes = plt.subplots(figsize=(12, 5), ncols=2)

axes[0].plot(x[:20], y[:20], marker='.')
axes[0].plot(x[:20], z[:20], marker='o')

axes[1].plot(x[:20], z[:20], marker='^',
             markersize=10, markerfacecolor='r',
             markeredgecolor='k')
plt.show()
```

End of code.

**What this shows.** Left panel: a short blue segment of the cosine (rising gently from −1 to about −0.4) dotted with small point markers, and the orange sine segment (a bit over one full oscillation) beaded with larger circle markers. Right panel: the same sine segment as a blue line, but each of its 20 points now carries a size-10 triangle marker with a red face and black edge.

### Label, Ticks, and Gridlines

You can control exactly where the ticks fall and what text they show — including LaTeX math in labels and titles (write it between `$` signs in a raw string, like `r'$-\pi$'`, which renders as the Greek letter π with a minus sign):

The following is Python code:

```python
fig, ax = plt.subplots(figsize=(12, 7))
ax.plot(x, y)

ax.set_xlabel('x')
ax.set_ylabel('y')
ax.set_title(r'A complicated math function: $f(x) = \cos(x)$')

ax.set_xticks(np.pi * np.array([-1, 0, 1]))
ax.set_xticklabels([r'$-\pi$', '0', r'$\pi$'])
ax.set_yticks([-1, 0, 1])

ax.set_yticks(np.arange(-1, 1.1, 0.2), minor=True)

ax.grid(which='minor', linestyle='--')
ax.grid(which='major', linewidth=2)
plt.show()
```

End of code.

**What this shows.** The cosine arch on a large canvas with a fully customized frame: the x-axis has only three ticks, labelled −π, 0, and π (typeset as math symbols); the y-axis has major ticks at −1, 0, 1 and minor ticks every 0.2 between them. A grid covers the plot — thick solid lines through the major ticks, thin dashed lines through the minor ones — and the title renders the formula f(x) = cos(x) in mathematical type.

**Check it.**

The following is Python code:

```python
print(ax.get_xticks())
```

End of code.

The following is the expected output (the three tick positions, i.e. −π, 0, π):

```
[-3.14159265  0.          3.14159265]
```

End of output.

### Axis Limits

The following is Python code:

```python
fig, ax = plt.subplots()
ax.plot(x, y, x, z)
ax.set_xlim(-5, 5)
ax.set_ylim(-3, 3)
plt.show()
```

End of code.

**What this shows.** Both curves, but now framed loosely: the x-axis runs −5 to 5 even though the data only spans about −3.14 to 3.14, so there's empty space on either side; the y-axis runs −3 to 3, so the curves (which only reach ±1) occupy just the middle third of the plot's height. Setting limits crops or pads the view — it never changes the data.

### Text Annotations

The following is Python code:

```python
fig, ax = plt.subplots()
ax.plot(x, y)
ax.text(-3, 0.3, 'hello world')
ax.annotate('the maximum', xy=(0, 1),
             xytext=(0, 0), arrowprops={'facecolor': 'k'})
plt.show()
```

End of code.

**What this shows.** The cosine arch with two pieces of text placed in data coordinates: the words "hello world" floating on the left side of the plot (at x = −3, y = 0.3), and the words "the maximum" at the center (0, 0) with a thick black arrow pointing straight up from the text to the arch's peak at (0, 1). `text` just places words; `annotate` places words *plus* an arrow from `xytext` to `xy`.

:::{admonition} Try it
:class: tip
Plot `y` and `z` on the same axes with different styles — say, `y` as a dashed red line and `z` as a dotted blue line with circle markers. Then customize:

- set x-ticks at `[-π, -π/2, 0, π/2, π]` using `np.pi * np.array(...)`, with LaTeX labels (`r'$-\pi$'` etc.).
- turn on a major and minor grid.
- add a title using LaTeX math, e.g. `r'$y = \cos(x)$ and $z = \sin(6x)$'`.

Verify without looking: print each line's `get_linestyle()` and `get_color()`, and `ax.get_xticks()` (it should list five values from −3.14 to 3.14).
:::

## Other 1D Plots

Not every 1D plot is a line plot. Two of the most common alternatives are **scatter plots** (each data point is its own marker — useful for showing relationships between two variables) and **bar plots** (for comparing values across discrete categories).

### Scatter Plots

A scatter plot can encode *four* quantities at once: horizontal position, vertical position, marker **color** (the `c=` argument, explained by a colorbar), and marker **size** (`s=`):

The following is Python code:

```python
fig, ax = plt.subplots()

splot = ax.scatter(y, z, c=x, s=(100*z**2 + 5))
fig.colorbar(splot)
plt.show()
```

End of code.

**What this shows.** 100 dots scattered across the square −1 to 1 by −1 to 1: each dot sits at (cos x, sin 6x) for one x value, so the dots trace the same braided Lissajous pattern as before — denser near the left and right edges, where the cosine lingers near ±1. Each dot's color encodes its x value on the default blue-green-yellow colormap (purple for x near −π through yellow for x near +π; a colorbar on the right maps color to value). Each dot's size encodes z²: dots near the top and bottom edges (z ≈ ±1) are about 20 times the area of the tiny dots near the midline.

### Bar Plots

The following is Python code:

```python
labels = ['first', 'second', 'third']
values = [10, 5, 30]

fig, axes = plt.subplots(figsize=(10, 5), ncols=2)
axes[0].bar(labels, values)
axes[1].barh(labels, values)
plt.show()
```

End of code.

**What this shows.** Left panel, vertical bars: three blue columns labelled "first" (height 10), "second" (height 5), and "third" (height 30) — the third towers over the others. Right panel, `barh` turns them into horizontal bars growing rightward, with "first" at the *bottom* and "third" at the top (bar charts list categories bottom-up on the y-axis).

:::{admonition} Try it
:class: tip
Create a scatter plot of `y` against `z`, coloring each point by its `x` value (`c=x`), and add a colorbar. Then make a horizontal bar chart (`ax.barh`) of three labelled values of your choice. To check the bar chart without looking, print the bar lengths back off the axes: `print([b.get_width() for b in ax.containers[0]])`.
:::

## 2D Plotting Methods

For 2D data — gridded values like `f(x, y)` — matplotlib provides several ways to visualize. The right choice depends on whether your data is on a regular grid (use `imshow` or `pcolormesh`), whether you want isolines (`contour` / `contourf`), or whether you have vector data like flow fields (`quiver` / `streamplot`).

We'll use one 2D field for this whole section, so let's understand it once, up front:

The following is Python code:

```python
x1d = np.linspace(-2*np.pi, 2*np.pi, 100)
y1d = np.linspace(-np.pi, np.pi, 50)
xx, yy = np.meshgrid(x1d, y1d)
f = np.cos(xx) * np.sin(yy)
print(f.shape)
```

End of code.

The following is the expected output:

```
(50, 100)
```

End of output.

The field `f = cos(x)·sin(y)` lives on a grid twice as wide (x from −2π to 2π) as it is tall (y from −π to π). Its structure — worth fixing in your mind, because every plot below is a different *view of this same field*:

- It is a checkerboard-like pattern of ten smooth "blobs": **two rows** (one for y > 0, one for y < 0) of **five alternating highs and lows** across the x-direction.
- In the **top half** (y between 0 and π, where sin(y) > 0), `f` follows cos(x): **highs** (+1) centered at x = −2π, 0, +2π and **lows** (−1) at x = ±π.
- In the **bottom half**, sin(y) < 0 flips every sign: lows sit at x = −2π, 0, +2π and highs at x = ±π.
- Along the midline y = 0 (and at the top and bottom edges y = ±π) the field is exactly zero.

**Check it.** Confirm the structure from the array — the value at a top-half high, a top-half low, and the sign flip between halves:

The following is Python code:

```python
print(f[37, 50].round(2))    # x=0,  y=+pi/2: top-half center
print(f[37, 75].round(2))    # x=+pi, y=+pi/2
print(f[12, 50].round(2))    # x=0,  y=-pi/2: bottom half
```

End of code.

The following is the expected output:

```
1.0
-0.99
-1.0
```

End of output.

### imshow

`imshow` displays values directly as a colored image. It treats the input as pixel data with no coordinates — pass `origin='lower'` if you want `y=0` at the bottom (the usual convention for plots):

The following is Python code:

```python
fig, ax = plt.subplots(figsize=(12,4), ncols=2)
ax[0].imshow(f)
ax[1].imshow(f, origin='lower')
plt.show()
```

End of code.

**What this shows.** Two renderings of `f` as an image, in the default colormap (dark purple = low, yellow = high, teal-green in between). The axes show **pixel indices**, not x/y values: columns 0–99 across, rows 0–49 down. The two panels are vertical mirror images of each other. In the left panel (default), row 0 is drawn at the *top* — so the image's top band is the y < 0 half of the field (bright blobs at columns 25 and 75, i.e. x = ±π). In the right panel (`origin='lower'`), row 0 moves to the *bottom*, which puts the field the "right way up." For data (as opposed to photographs), `origin='lower'` is almost always what you want.

### pcolormesh

`pcolormesh` is like `imshow` but uses explicit x/y coordinates, so you can plot on non-uniform grids and the axes show real-world units:

The following is Python code:

```python
fig, ax = plt.subplots(ncols=2, figsize=(12, 5))
pc0 = ax[0].pcolormesh(x1d, y1d, f)
pc1 = ax[1].pcolormesh(xx, yy, f)
fig.colorbar(pc0, ax=ax[0])
fig.colorbar(pc1, ax=ax[1])
plt.show()
```

End of code.

**What this shows.** The same ten-blob checkerboard, but now the axes read in data units — x from −6.28 to 6.28, y from −3.14 to 3.14 — and each panel has a colorbar mapping the colors to values from −1 (dark purple) to +1 (yellow). Top band: yellow blobs at x = −2π, 0, +2π, purple at ±π; bottom band mirrored. Both panels are identical: `pcolormesh` accepts either the 1D coordinate arrays (`x1d`, `y1d`) or the full 2D meshgrid arrays (`xx`, `yy`).

There is one subtlety worth knowing. When `pcolormesh` is given an `(N, M)` value array along with `(N, M)` x/y coordinates, it actually **drops the last row and column** of the values — only the first `(N−1, M−1)` are drawn. Slicing the values explicitly with `[:-1, :-1]` produces the same picture:

The following is Python code:

```python
x_sm, y_sm, f_sm = xx[:10, :10], yy[:10, :10], f[:10, :10]

fig, ax = plt.subplots(figsize=(12,5), ncols=2)
ax[0].pcolormesh(x_sm, y_sm, f_sm, edgecolors='k')
ax[1].pcolormesh(x_sm, y_sm, f_sm[:-1, :-1], edgecolors='k')
plt.show()
```

End of code.

**What this shows.** A zoomed-in corner of the grid (10×10 points from its bottom-left, x from −6.3 to −5.1, y from −3.1 to −2.0), drawn with black edges around every cell so you can count them: a coarse **9-by-9** grid of colored rectangles in *both* panels — passing the full 10×10 `f_sm` (left) or the pre-trimmed 9×9 version (right) makes no difference. The colors run smoothly from bright yellow along the bottom row (values near 0, the maximum in this little corner) to dark purple at the top (values near −0.9). The point: with same-shape coordinates, each value cell is drawn *between* four coordinate points, so the last row and column of values have no home.

Why bother with `pcolormesh` at all, then? Because the coordinates don't have to form a regular grid:

The following is Python code:

```python
y_distorted = y_sm*(1 + 0.1*np.cos(6*x_sm))

plt.figure(figsize=(12,6))
plt.pcolormesh(x_sm, y_distorted, f_sm[:-1, :-1], edgecolors='w')
plt.scatter(x_sm, y_distorted, c='k')
plt.show()
```

End of code.

**What this shows.** The same 9×9 patch of cells, but the y-coordinate of every grid point has been nudged up or down by a cosine wiggle — so the horizontal rows of cells now undulate like a wavy brick wall, bulging upward near the middle of the panel. Black dots mark every actual grid-point location, sitting at the corners of the warped cells (white edges). Curvilinear grids like this are everywhere in climate modelling — think latitude-longitude grids on a curved Earth.

### contour / contourf

`contour` draws isolines (lines of constant value); `contourf` fills the regions between them.

Unlike `pcolormesh`, `contour` works equally well with 1D coordinate arrays or with 2D meshgrid arrays — both produce the same picture:

The following is Python code:

```python
fig, ax = plt.subplots(figsize=(12, 5), ncols=2)
ax[0].contour(x1d, y1d, f)
ax[1].contour(xx, yy, f)
plt.show()
```

End of code.

**What this shows.** Two identical panels. Instead of solid color, each of the ten blobs is now drawn as a set of nested concentric ovals — like a topographic map of ten hills and valleys — with the line colors following the colormap (yellow-green rings around the highs, dark purple-blue rings around the lows). Straight teal lines mark where the field crosses zero: vertical lines at x = ±π/2 and ±3π/2, and a horizontal one along the midline y = 0.

You control how many isolines to draw with a third argument — and you can label the lines with their values (`clabel`) or attach a colorbar:

The following is Python code:

```python
fig, ax = plt.subplots(figsize=(12, 5), ncols=2)

c0 = ax[0].contour(xx, yy, f, 5)
c1 = ax[1].contour(xx, yy, f, 20)

plt.clabel(c0, fmt='%2.1f')
plt.colorbar(c1, ax=ax[1])
plt.show()
```

End of code.

**What this shows.** Left panel, only ~5 contour levels: each blob reduces to two or three nested ovals, and every line carries a small inline number stating its value (−0.8, −0.4, 0.0, 0.4, 0.8). Right panel, 20 levels: each blob becomes a dense bull's-eye of many closely spaced rings, with a colorbar instead of inline labels. Few levels = a coarse summary; many levels = fine detail at the cost of clutter.

`contourf` fills the bands between levels with solid color — and this example also introduces **choosing a colormap** (`cmap`):

The following is Python code:

```python
fig, ax = plt.subplots(figsize=(12, 5), ncols=2)

clevels = np.arange(-1, 1, 0.2) + 0.1

cf0 = ax[0].contourf(xx, yy, f, clevels, cmap='RdBu_r', extend='both')
cf1 = ax[1].contourf(xx, yy, f, clevels, cmap='inferno', extend='both')

fig.colorbar(cf0, ax=ax[0])
fig.colorbar(cf1, ax=ax[1])
plt.show()
```

End of code.

**What this shows.** The ten blobs as filled color patches, ten levels from −0.9 to 0.9. Left panel uses `RdBu_r`, a **diverging** colormap: highs are red, lows are blue, and values near zero are white — ideal when the sign of the data matters (warm/cool anomalies, for instance). So the top band shows red blobs at x = −2π, 0, 2π and blue at ±π, mirrored below, separated by white along the zero line. Right panel uses `inferno`, a **sequential** colormap running black → purple → orange → bright yellow from low to high — better for magnitude-style data. `extend='both'` adds pointed arrow tips to both colorbars, indicating that values beyond the outermost levels (here ±0.9 to ±1) are lumped into the end colors.

### quiver

`quiver` plots a vector field as arrows — useful for flow data like winds, ocean currents, or gradients. We build a two-component field (u, v) related to our `f`:

The following is Python code:

```python
u = -np.cos(xx) * np.cos(yy)
v = -np.sin(xx) * np.sin(yy)

fig, ax = plt.subplots(figsize=(12, 7))
ax.contour(xx, yy, f, clevels, cmap='RdBu_r', extend='both', zorder=0)
ax.quiver(xx[::4, ::4], yy[::4, ::4],
           u[::4, ::4], v[::4, ::4], zorder=1)
plt.show()
```

End of code.

**What this shows.** The red/blue contour rings of `f` drawn faintly underneath, with 325 small black arrows on top (the `[::4, ::4]` slicing keeps every 4th grid point — plotting all 5000 would be an unreadable thicket). The arrows swirl in closed loops around the center of each blob: this (u, v) field circulates **clockwise around the highs and counter-clockwise around the lows**, alternating from cell to cell like interlocking gears. `zorder` controls stacking — contours at the bottom (0), arrows on top (1).

### streamplot

`streamplot` traces flow lines through a vector field — useful for visualizing turbulent flow or current patterns:

The following is Python code:

```python
fig, ax = plt.subplots(figsize=(12, 7))
ax.streamplot(xx, yy, u, v, density=2, color=(u**2 + v**2))
plt.show()
```

End of code.

**What this shows.** The same circulation, but instead of discrete arrows, smooth continuous streamlines: each of the ten cells is filled with nested closed loops, like ten spinning eddies packed in a 2-by-5 grid. The lines are colored by the flow speed (squared), which ranges 0 to 1: bright yellow along the fast outer edges of each eddy, darkening to purple at the still centers, where the speed drops to zero.

:::{admonition} Try it
:class: tip
Compute a 2D Gaussian: `g = np.exp(-(xx**2 + yy**2))` on the meshgrid. Make a figure with **three subplots** showing it via `pcolormesh`, `contourf`, and `imshow(..., origin='lower')`. Give each subplot its own colorbar.

What you should "see", and how to check it without looking: the Gaussian is a single smooth bright bump centered at (0, 0) fading to zero in all directions. Confirm from the array: `g.max()` is 1.0, `np.unravel_index(g.argmax(), g.shape)` lands in the middle of the grid (row 24–25, column 49–50), and the middle-row profile `g[25, :]` is a bell curve you can print — or plot with `plt.plot(x1d, g[25, :])` and explore with MAIDR.
:::

## Recap

You've now seen how Matplotlib is put together and the main tools for using it:

- **The Figure / Axes model** — a Figure is the canvas; each Axes is one plot, created with `plt.subplots()` and drawn into explicitly.
- **Line plots and styling** — colors, line styles, markers, labels, ticks, gridlines, axis limits, and text annotations.
- **Other 1-D plots** — scatter and bar charts.
- **2-D fields** — `imshow`, `pcolormesh`, `contour`/`contourf` for scalar fields, and `quiver`/`streamplot` for vector fields — the building blocks of climate maps.
- **And the non-visual habit:** every property you set on a figure can be printed back off it (`ax.get_title()`, `line.get_color()`, `len(fig.axes)`, …), and the data itself is always checkable with shapes, statistics, and 1-D profiles.

With NumPy and Matplotlib in hand, you're ready for **pandas**, where these arrays gain labels and become tables you can slice, group, and analyze.
