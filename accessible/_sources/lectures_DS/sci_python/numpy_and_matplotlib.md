# NumPy and Matplotlib

:::{admonition} Following along with a screen reader
:class: important
Work through the examples as `.py` scripts in VS Code — see [Setting up an accessible workflow](../computing_env/accessible_setup.md) and [Working with Python scripts](../computing_env/working_with_scripts.md). Run a script in the Git Bash terminal with `python yourfile.py`, and press `Alt+F2` to read the output.
:::

This lecture introduces **NumPy**, *the fundamental package for scientific computing with Python* — the fast, N-dimensional array library that almost everything else in the scientific Python ecosystem is built on.

- Website: <https://numpy.org/>
- GitHub: <https://github.com/numpy/numpy>

To build intuition, this lecture often **visualizes** the arrays it creates, using **Matplotlib** — introduced partway down this page, in the *Visualizing Arrays with Matplotlib* section, right before the first plots appear. Wherever a plot appears below, it is followed by a **"What this shows"** note describing the result in words and numbers, and usually by a **"Check it"** block — code that confirms the same conclusion directly from the array, no eyes required.

:::{admonition} Checking an array without a picture
:class: tip
For sighted readers, the quick plots in this lecture act as an *array microscope* — a two-second glance to confirm an operation did what they think it did. You can get the same confirmation directly from the array, and this page practices that style at every step:

- **Shape first.** `print(a.shape)` before and after an operation — most mistakes change the shape.
- **Spot-check values.** Print a value you can predict (`a[0, 0]`), or test a whole slice at once with `np.allclose(...)` / `np.array_equal(...)`.
- **Summary statistics.** `a.min()`, `a.max()`, `a.mean()` — the non-visual equivalent of glancing at a colorbar.
- **1D profiles.** Slice out one row or column and you have an ordinary 1D array: `a[25, :]` is row 25. You can print it, plot it as a line with `plt.plot(...)`, or explore that line plot by sound with [MAIDR](./trying_maidr.md) — MAIDR handles line plots well, though not (yet) 2D heatmaps like `pcolormesh`.

These checks are not a workaround — printing shapes and verifying values is exactly how experienced programmers debug arrays, with or without sight.
:::

:::{admonition} A note on how output works
:class: note
The original lecture is a Jupyter notebook, where the value of the last line of a cell is shown automatically — so it often relies on a bare expression like `a.shape` to display a result. In a script that does **not** happen: nothing is shown unless you `print()` it. So as you copy these examples into `.py` files, **wrap the thing you want to see in `print(...)`**. Each "This prints:" block below shows what you'll hear after running the code and pressing `Alt+F2`.
:::

:::{admonition} In-class assignment — 10 points
:class: note
The **"Try it"** exercises on this page (and on the companion Matplotlib page) are part of your in-class assignment for this section. Complete them as `.py` scripts, push them to your week folder, and post the link on the matching **Courseworks** assignment.
:::
## Importing and Examining a New Package

This will be our first experience with *importing* a package that is not part of the Python [standard library](https://docs.python.org/3/library/).

**Code:**

```python
import numpy as np
```

**End of code.**

What did we just do? We **imported** a package. This makes new variables (mostly functions) available to use in our script — we access them with the `np.` prefix, as you'll see throughout this page.

We can use `dir()` to see the variables we currently have access to — `np` should now be one of them.

**Code:**

```python
print(dir())
```

**End of code.**

This prints a list of the names defined in your script right now. The exact list depends on what else you've written, but `'np'` will be one of the entries.

NumPy has hundreds of functions and types — `dir(np)` lists them all (too many to read out loud). A common pattern is to filter that list for names containing a keyword you care about. This is a list comprehension, like the ones from the Core Python material:

**Code:**

```python
print([name for name in dir(np) if 'lin' in name])
```

**End of code.**

This prints:

```
['linalg', 'linspace']
```

**End of output.**

So the two NumPy names containing `'lin'` are `linalg` (the linear-algebra sub-package) and `linspace` (a function we'll use below).

You can check the version of any package via its `__version__` attribute — useful when reproducing results or filing bug reports:

**Code:**

```python
print(np.__version__)
```

**End of code.**

This prints a version string such as `2.4.4` (yours may differ).

There is no way we could explicitly teach each of these functions. The NumPy documentation is crucial:
<https://numpy.org/doc/stable/reference/>.

:::{admonition} Try it
:class: tip
Create `examine.py`. Import the `math` module (`import math`). Use `print(dir(math))` to peek at what's inside. Pick a function that looks interesting (e.g., `log`, `factorial`, `sqrt`) and call `help()` on it to read its signature and docstring — add a line like `help(math.sqrt)`, run the script, and read the help text with `Alt+F2`.
:::

## NDArrays

The core class is the NumPy **ndarray** (n-dimensional array).

Compared to a regular Python `list`, NumPy arrays have a few key differences:

- NumPy arrays can have N dimensions (lists, tuples, etc. are 1D).
- NumPy arrays hold values of a single datatype (e.g. `int`, `float`); lists can hold anything.
- NumPy optimizes numerical operations on arrays — NumPy is *fast!*

Let's create a 1D array from a Python list, then inspect its properties.

**Code:**

```python
a = np.array([9, 0, 2, 1, 0])
print(a)
```

**End of code.**

This prints:

```
[9 0 2 1 0]
```

**End of output.**

Every array has a `dtype` (the type of its elements) and a `shape` (its size along each dimension):

**Code:**

```python
print(a.dtype)
print(a.shape)
```

**End of code.**

This prints:

```
int64
(5,)
```

**End of output.**

So the elements are 64-bit integers, and the shape is `(5,)` — a 1D array of length 5. (The trailing comma is how Python writes a one-element tuple.)

The shape is returned as a **tuple**, which means we can index it like any other tuple — useful when we want to grab a specific dimension's length.

**Code:**

```python
print(type(a.shape))
```

**End of code.**

This prints:

```
<class 'tuple'>
```

**End of output.**

Arrays can also be **multi-dimensional** and have an explicit dtype. Here we pass a list of lists and ask for 64-bit floats:

**Code:**

```python
b = np.array([[5, 3, 1, 9], [9, 2, 3, 0]], dtype=np.float64)
print(b.dtype, b.shape)
```

**End of code.**

This prints:

```
float64 (2, 4)
```

**End of output.**

So `b` is a 2-row, 4-column array of floats.

:::{note}
The fastest-varying dimension is the **last** dimension; the outer level of the hierarchy is the first dimension. (This is called "C-style" indexing.)
:::

:::{admonition} Try it
:class: tip
Create `ndarrays.py`. Build three arrays:

- A 1D array from `[1, 2, 3, 4, 5]`.
- A 2D array from `[[1, 2], [3, 4]]`.
- A 3D array of your choice (a list of lists of lists).

Print the `shape` and `dtype` of each with `print(...)`. Then create a 1D array that includes a decimal value (e.g. `[1, 2, 3.5]`) and confirm the dtype is now `float64`.
:::

## Array Creation

There are several ways to build arrays in NumPy beyond passing a list to `np.array`. The functions below come up constantly:

- `np.zeros` / `np.ones` / `np.full` — blank arrays of a given shape.
- `np.arange` / `np.linspace` / `np.logspace` — evenly-spaced values along a range.
- `np.meshgrid` — combine 1D arrays into 2D coordinate grids.

**Code:**

```python
c = np.zeros((9, 9))
d = np.ones((3, 6, 3), dtype=np.complex128)
e = np.full((3, 3), np.pi)
e = np.ones_like(c)
f = np.zeros_like(d)
```

**End of code.**

These create, in order: a 9×9 array of zeros; a 3×6×3 array of ones holding complex numbers; a 3×3 array filled with the value of pi; then `np.ones_like` and `np.zeros_like`, which make new arrays of ones and zeros that copy the **shape and dtype** of an existing array (`c` and `d` here).

`arange` works very similarly to `range`, but it populates the array "eagerly" (i.e. immediately), rather than generating the values upon iteration:

**Code:**

```python
print(np.arange(10))
```

**End of code.**

This prints:

```
[0 1 2 3 4 5 6 7 8 9]
```

**End of output.**

`arange` is left inclusive, right exclusive, just like `range`, but also works with floating-point numbers:

**Code:**

```python
print(np.arange(2, 4, 0.25))
```

**End of code.**

This prints:

```
[2.   2.25 2.5  2.75 3.   3.25 3.5  3.75]
```

**End of output.**

A frequent need is to generate an array of N numbers, evenly spaced between two values. That is what `linspace` is for:

**Code:**

```python
print(np.linspace(2, 4, 20))
```

**End of code.**

This prints 20 values evenly spaced from 2 to 4 (inclusive of both ends):

```
[2.         2.10526316 2.21052632 2.31578947 2.42105263 2.52631579
 2.63157895 2.73684211 2.84210526 2.94736842 3.05263158 3.15789474
 3.26315789 3.36842105 3.47368421 3.57894737 3.68421053 3.78947368
 3.89473684 4.        ]
```

**End of output.**

The first value is exactly 2, the last is exactly 4, and the spacing between consecutive values is constant (about 0.105).

Log-spaced values are useful when working with quantities spanning multiple orders of magnitude (e.g., frequencies, energy scales):

**Code:**

```python
print(np.logspace(1, 2, 10))
```

**End of code.**

This prints 10 values spaced evenly on a logarithmic scale, from 10¹ to 10²:

```
[ 10.          12.91549665  16.68100537  21.5443469   27.82559402
  35.93813664  46.41588834  59.94842503  77.42636827 100.        ]
```

**End of output.**

The first value is 10, the last is 100, and each value is a constant *ratio* larger than the previous one (not a constant difference).

NumPy also has utilities for generating multi-dimensional arrays. `meshgrid` creates 2D arrays out of a combination of 1D arrays. Meshgrid essentially tiles the 1D arrays to a shape that is the combined shape of the inputs:

**Code:**

```python
x = np.linspace(-2*np.pi, 2*np.pi, 100)
y = np.linspace(-np.pi, np.pi, 50)
xx, yy = np.meshgrid(x, y)
print(x.shape, y.shape)
print(xx.shape, yy.shape)
```

**End of code.**

This prints:

```
(100,) (50,)
(50, 100) (50, 100)
```

**End of output.**

So the 1D inputs have lengths 100 and 50, and `meshgrid` produces two 2D arrays of shape `(50, 100)` — 50 rows (one per `y` value) and 100 columns (one per `x` value). `xx` holds the x-coordinate at every grid point, and `yy` holds the y-coordinate. We'll reuse `x`, `y`, `xx`, and `yy` throughout the rest of this page.

:::{admonition} Try it
:class: tip
Create `creation.py`. Use `np.linspace` to create an array of 100 evenly-spaced values between 0 and 1, and `np.arange` to create the integers from 0 to 10. Print the `dtype` and `shape` of each. Then use `np.meshgrid` to build a 2D grid from these two arrays and print its shape.
:::

## Indexing

Basic indexing in NumPy is similar to lists — single brackets, zero-based, and negative indices count from the end. The key extension is that for N-dimensional arrays, you separate the index for each dimension with a comma. Slicing (e.g. `[2:5]`, `[:, 0]`) and boolean masks also work, as we'll see below.

**Code:**

```python
print(y[10], y[1:5], y[-5])
```

**End of code.**

This prints (using the `y` array from the meshgrid section above):

```
-1.8593099378388573 [-3.01336438 -2.88513611 -2.75690784 -2.62867957] 2.628679567289418
```

**End of output.**

So `y[10]` is the single value at index 10, `y[1:5]` is a 4-element slice (indices 1 through 4), and `y[-5]` is the fifth value from the end.

For multi-dimensional arrays, use a comma to separate the index for each dimension:

**Code:**

```python
print(xx[0, 0], xx[-1, -1], xx[3, -5])
```

**End of code.**

This prints:

```
-6.283185307179586 6.283185307179586 5.775453161144872
```

**End of output.**

`xx[0, 0]` is the top-left corner (the smallest x, about −2π), `xx[-1, -1]` is the bottom-right corner (the largest x, about +2π), and `xx[3, -5]` is the value in row 3, fifth column from the end.

Slicing returns whole rows or columns:

**Code:**

```python
print(xx[0].shape, xx[:, -1].shape)
```

**End of code.**

This prints:

```
(100,) (50,)
```

**End of output.**

`xx[0]` is the first **row** (length 100, one per column), and `xx[:, -1]` is the last **column** (length 50, one per row).

You can take **ranges** across multiple dimensions:

**Code:**

```python
print(xx[3:10, 30:40].shape)
```

**End of code.**

This prints:

```
(7, 10)
```

**End of output.**

Rows 3 through 9 (7 rows) and columns 30 through 39 (10 columns) give a 7×10 sub-block.

There are many advanced ways to index arrays. You can [read about them](https://numpy.org/doc/stable/reference/arrays.indexing.html) in the manual. Here is one example — a **boolean mask**. The expression `xx < 0` builds an array of `True`/`False` the same shape as `xx`, and indexing with it keeps only the elements where the mask is `True`:

**Code:**

```python
idx = xx < 0
print(xx[idx].shape)
```

**End of code.**

This prints:

```
(2500,)
```

**End of output.**

Note that a boolean index always returns a **flat (1D) array**, regardless of the input shape. Here `xx` has 50 × 100 = 5000 elements, and exactly half of them are negative, giving 2500. The `ravel` method does the same flattening explicitly:

**Code:**

```python
print(xx.ravel().shape)
```

**End of code.**

This prints:

```
(5000,)
```

**End of output.**

:::{admonition} Try it
:class: tip
Create `indexing.py` and rebuild `x`, `y`, `xx`, `yy` with `np.meshgrid` as above. Get the first row of `xx`, its last column, and a central 3×3 block via slicing, printing the shape of each. Then use a **boolean mask** to extract all values of `xx` that are greater than zero — print the shape of the result.
:::

## Visualizing Arrays with Matplotlib

It can be hard to work with big arrays without seeing them, so we turn arrays into pictures with **Matplotlib**, the plotting library the course uses throughout. To import its plotting interface:

**Code:**

```python
from matplotlib import pyplot as plt
```

**End of code.**

:::{note}
When you run a Matplotlib script from the terminal, the figure only appears if you ask for it. Add `plt.show()` at the end of the script to open the plot window, or `plt.savefig("figure.png")` to write it to a file. The course notebook shows plots automatically; in a script you call one of these yourself. The "What this shows" notes mean you don't need to open the window to follow the lesson.
:::

:::{admonition} Exploring plots by sound and text
:class: tip
Matplotlib plots are images, so a screen reader cannot read them directly — that is exactly why every plot on this page is paired with a "What this shows" note and a "Check it" block. As an experiment we're evaluating, many of these charts can also be made **explorable non-visually** — navigated by keyboard and played as sound — using a tool called **MAIDR**. See [Trying MAIDR](./trying_maidr.md) for how to set that up and try it yourself.
:::

For plotting a 1D array as a line, we use the `plot` command:

**Code:**

```python
plt.plot(x)
plt.show()
```

**End of code.**

**What this shows.** With a single argument, `plt.plot` puts the array's *index* (0, 1, 2, …, 99) on the horizontal axis and its *value* on the vertical axis. Since `x = np.linspace(-2*np.pi, 2*np.pi, 100)`, the result is a straight line rising steadily from about −6.28 at the left (index 0) to about +6.28 at the right (index 99). It's a visual confirmation that `linspace` produces evenly-spaced values.

**Check it.** The same confirmation, from the array itself — the endpoints, and whether all 99 consecutive differences (`np.diff(x)`) are equal:

**Code:**

```python
print(x[0], x[-1])
print(np.allclose(np.diff(x), np.diff(x)[0]))
```

**End of code.**

This prints:

```
-6.283185307179586 6.283185307179586
True
```

**End of output.**

Equal differences everywhere is exactly what "a straight line" means.

There are many ways to visualize 2D data. Here we use `pcolormesh`, which draws a 2D array as a grid of colored cells (a "heatmap"), with color encoding each cell's value:

**Code:**

```python
plt.pcolormesh(xx)
plt.show()
```

**End of code.**

**What this shows.** `xx` holds the x-coordinate at every point of the grid, so its value depends only on the column, not the row. The heatmap is therefore a smooth gradient running left to right: dark/low (about −6.28) at the left edge, brightening steadily to high (about +6.28) at the right edge — and identical in every row, so each thin vertical strip of the image is a single color.

**Check it.** "Depends only on the column" means any two rows are identical:

**Code:**

```python
print(np.allclose(xx[0, :], xx[25, :]))
print(xx[0, 0], xx[0, -1])
```

**End of code.**

This prints:

```
True
-6.283185307179586 6.283185307179586
```

**End of output.**

Row 0 and row 25 match exactly, and a single row runs from −2π to +2π — it is just a copy of `x`.

**Code:**

```python
plt.pcolormesh(yy)
plt.show()
```

**End of code.**

**What this shows.** `yy` holds the y-coordinate, which depends only on the row. So this heatmap is the same idea rotated 90 degrees: a smooth gradient running bottom to top — low (about −3.14) along the bottom edge, rising to high (about +3.14) at the top edge, identical in every column. (Note that `pcolormesh` draws row 0 at the *bottom* of the image.)

**Check it.**

**Code:**

```python
print(np.allclose(yy[:, 0], yy[:, 25]))
print(yy[0, 0], yy[-1, 0])
```

**End of code.**

This prints:

```
True
-3.141592653589793 3.141592653589793
```

**End of output.**

Any two columns are identical, and a single column runs from −π to +π — a copy of `y`.

:::{admonition} Try it
:class: tip
Create `plotting.py`, rebuilding `x`, `y`, `xx`, `yy` with `np.meshgrid` as above. Use `plt.plot` to plot `np.cos(x)` against `x` (`plt.plot(x, np.cos(x))`). Then use `plt.pcolormesh` to visualize the 2D array `xx + yy`. End the script with `plt.show()`. (Don't worry about axis labels yet — those come later.) If you've set up MAIDR, try exploring these plots non-visually too.
:::

## Array Operations

There are a huge number of operations available on arrays. All the familiar arithmetic operators are applied on an **element-by-element** basis.

### Basic Math

NumPy provides "universal functions" like `np.sin` that act on every element of an array at once:

**Code:**

```python
f = np.sin(x)
plt.plot(x, f)
plt.show()
```

**End of code.**

**What this shows.** This plots `sin(x)` against `x`, where `x` runs from −2π to +2π. The curve is a smooth sine wave that completes **two full oscillations** across the range: it passes through zero at x = −2π, −π, 0, π, and 2π, peaks at +1, and dips to −1, in the classic wave shape.

Now let's compute a function of two variables — a 2D surface. We use the 2D `xx`/`yy` arrays (from `meshgrid` earlier) so the function is evaluated at every grid point:

**Code:**

```python
f = np.sin(xx) * np.cos(0.5 * yy)
plt.pcolormesh(xx, yy, f)
plt.show()
```

**End of code.**

**What this shows.** `f` now has shape `(50, 100)`, one value per grid point. Passing `xx, yy, f` to `pcolormesh` plots it with real coordinates on the axes (x from −2π to +2π, y from −π to +π). The pattern is a grid of "blobs": along the x-direction it oscillates between bright (+) and dark (−) following `sin(x)` (two full cycles), while along the y-direction the amplitude is gently modulated by `cos(0.5·y)`, which is strongest (close to 1) near the middle row (y = 0) and tapers toward the top and bottom edges. We reuse this `f` (shape `(50, 100)`) in the sections below.

**Check it.** Everything the plot shows is verifiable straight from the array:

**Code:**

```python
print(f.shape)
print(f.min(), f.max())
print(np.abs(f[25, :] - np.sin(x)).max())
```

**End of code.**

This prints:

```
(50, 100)
-0.9993604085457249 0.9993604085457249
0.0005137191281501252
```

**End of output.**

The shape says one value per grid point; the min/max say the values span roughly −1 to +1. The third line checks the *structure*: `f[25, :]` is the middle row — the row closest to y = 0, where `cos(0.5·y) ≈ 1` — and it differs from plain `sin(x)` by at most 0.0005. That slice is also your **1D profile**: an ordinary length-100 array, so

**Code:**

```python
plt.plot(x, f[25, :])
plt.show()
```

**End of code.**

draws the familiar two-cycle sine wave — a line plot you can read with `print`, or explore by sound with MAIDR. Slicing a single row or column out of a 2D array and examining it as a curve is one of the most useful moves in this whole lecture: it turns a heatmap question into a 1D question.

:::{admonition} Try it
:class: tip
Create `basic_math.py` with `x`, `y`, `xx`, `yy` rebuilt. Compute a 2D Gaussian on the grid: `gaussian = np.exp(-(xx**2 + yy**2))`. Visualize it with `plt.pcolormesh(xx, yy, gaussian)` then `plt.show()`. Where is the array largest, and where is it smallest? (Hint: `exp(0) = 1` at the center where `xx` and `yy` are both 0, and the values fall toward 0 far from the center.) Then answer the same question *without* the picture: print `gaussian.max()` and `np.unravel_index(gaussian.argmax(), gaussian.shape)` (the row and column of the maximum — it should be near the center of the grid), and plot the middle-row profile with `plt.plot(x, gaussian[25, :])` — a bell curve centered at x = 0.
:::

## Manipulating Array Dimensions

Once you have an array, you often need to reshape it without changing the underlying data — to swap the dimension order (transpose), reorganize the layout (`reshape`), repeat the array (`tile`), or add a new axis (with `None`) so it lines up with a higher-dimensional array. These all return a *view* or new array; they don't usually copy the data.

Swapping the dimension order is accomplished by calling `transpose`:

**Code:**

```python
f_transposed = f.transpose()
print(f_transposed.shape)
plt.pcolormesh(f_transposed)
plt.show()
```

**End of code.**

This prints:

```
(100, 50)
```

**End of output.**

**What this shows.** Transposing turns the `(50, 100)` array into `(100, 50)` — rows become columns and vice versa. The heatmap looks like the earlier `f` plot rotated/flipped across its diagonal: the blob pattern that ran left-to-right now runs top-to-bottom.

**Check it.** Transposing means every element swaps its row and column index — `f_transposed[j, i]` equals `f[i, j]` for any pair of indices:

**Code:**

```python
print(f[3, 10], f_transposed[10, 3])
```

**End of code.**

This prints the same number twice:

```
0.18253780301831585 0.18253780301831585
```

**End of output.**

We can also manually change the shape of an array — as long as the new shape has the **same number of elements**. `f` has 50 × 100 = 5000 elements. Watch what happens when the new shape doesn't match:

**Code:**

```python
g = np.reshape(f, (8, 9))
```

**End of code.**

This raises an error, because 8 × 9 = 72, not 5000. The last line of the traceback reads:

```
ValueError: cannot reshape array of size 5000 into shape (8,9)
```

**End of output.**

This is a useful error to recognize: when `reshape` complains, it almost always means your target shape multiplies out to a different number of elements than the array actually has.

A shape that *does* multiply to 5000 works fine. But be careful with reshaping data — you can accidentally lose the structure of the data:

**Code:**

```python
g = np.reshape(f, (200, 25))
print(g.shape)
plt.pcolormesh(g)
plt.show()
```

**End of code.**

This prints:

```
(200, 25)
```

**End of output.**

**What this shows.** The reshape succeeds (200 × 25 = 5000), but it has scrambled the spatial pattern. NumPy refills the new `(200, 25)` array by reading `f`'s values in order, so the smooth blobs from before are sliced and re-wrapped — the heatmap appears as fine horizontal striping rather than smooth blobs, no longer resembling the original surface. A reminder that reshaping reorganizes numbers without respecting their original 2D meaning.

**Check it.** Reshaping never changes the values or their order — only where the row breaks fall:

**Code:**

```python
print(np.allclose(f.ravel(), g.ravel()))
print(np.allclose(g[0, :], f[0, :25]))
```

**End of code.**

This prints:

```
True
True
```

**End of output.**

Flattened out, the two arrays are identical. But `g`'s first row holds only the first 25 values of `f`'s first row — each original row of 100 values now wraps across four rows of 25. That re-wrapping is exactly why the spatial pattern scrambles.

We can also "tile" an array to repeat it many times:

**Code:**

```python
f_tiled = np.tile(f, (3, 2))
print(f_tiled.shape)
plt.pcolormesh(f_tiled)
plt.show()
```

**End of code.**

This prints:

```
(150, 200)
```

**End of output.**

**What this shows.** `np.tile(f, (3, 2))` stacks the `f` pattern **3 times vertically and 2 times horizontally**, giving a `(150, 200)` array. The heatmap shows the same blob pattern repeated as a 3-by-2 grid of identical copies, like wallpaper.

**Check it.** Every 50×100 block should be an exact copy of `f`:

**Code:**

```python
print(np.array_equal(f_tiled[:50, :100], f))
print(np.array_equal(f_tiled[100:150, 100:200], f))
```

**End of code.**

This prints `True` twice — the first line checks the block in the first 50 rows and 100 columns, the second checks the block in the last 50 rows and last 100 columns.

Another common need is to add an extra dimension to an array. This can be accomplished via indexing with `None`. Start by checking the shape of `x`:

**Code:**

```python
print(x.shape)
```

**End of code.**

This prints:

```
(100,)
```

**End of output.**

Insert a new length-1 axis at the front:

**Code:**

```python
print(x[None, :].shape)
```

**End of code.**

This prints:

```
(1, 100)
```

**End of output.**

You can insert several new axes at once:

**Code:**

```python
print(x[None, :, None, None].shape)
```

**End of code.**

This prints:

```
(1, 100, 1, 1)
```

**End of output.**

Each `None` in the index inserts a new length-1 dimension at that position. This is the key trick behind **broadcasting**, which is next.

:::{admonition} Try it
:class: tip
Create `dimensions.py` with `f` rebuilt (`f = np.sin(xx) * np.cos(0.5*yy)`). Transpose `f` and print the shape. Then reshape it to `(10, 500)` and print the shape (check: 10 × 500 = 5000). Finally, add a new leading axis so the shape becomes `(1, 50, 100)` — use `f[None, :, :]` and verify with `.shape`.
:::

## Broadcasting

Not all the arrays we want to work with will have the same size. One approach would be to manually "expand" our arrays to all be the same size, e.g. using `tile`. **Broadcasting** is a more efficient way to combine arrays of different sizes. NumPy has specific rules for how broadcasting works. These can be confusing but are worth learning if you plan to work with NumPy data a lot.

The core concept of broadcasting is telling NumPy which dimensions are supposed to line up with each other.

:::{admonition} Broadcasting, described
:class: note
The original course shows a diagram here. In words: imagine writing the two arrays' shapes one above the other, **right-aligned** (lining up their last/trailing dimensions). NumPy then compares the shapes column by column, moving right to left, and applies the rules below. Wherever a shape has a length-1 dimension (or no dimension at all), that array is "stretched" — its single row or column is reused — to match the other array's length in that position. The result has, in each position, the larger of the two lengths.
:::

**General Broadcasting Rules.** When performing operations on two arrays, NumPy compares their shapes element-wise starting from the trailing dimensions (right to left). It follows these rules:

- If the dimensions are equal, they're compatible.
- If one of the dimensions is 1, it's "stretched" to match the other.
- If the dimensions are unequal and neither is 1, the arrays are not broadcastable.

Here `f` has shape `(50, 100)` and `x` has shape `(100,)`. Lining them up on the right, the trailing dimensions are 100 and 100 — they match — and `f`'s leading 50 has no counterpart, so `x` is reused for every row:

**Code:**

```python
print(f.shape, x.shape)
g = f * x
print(g.shape)
```

**End of code.**

This prints:

```
(50, 100) (100,)
(50, 100)
```

**End of output.**

So multiplying a `(50, 100)` array by a `(100,)` array gives a `(50, 100)` result — each row of `f` is multiplied element-wise by `x`. We'll keep this `g` (shape `(50, 100)`) for the reductions section.

**Code:**

```python
plt.pcolormesh(f)
plt.show()
```

**End of code.**

**What this shows.** This is the original blob surface `f` (sine in x, modulated by cosine in y), shown here for comparison with `g` below.

**Code:**

```python
plt.pcolormesh(g)
plt.show()
```

**End of code.**

**What this shows.** `g = f * x` multiplies each column of the pattern by that column's `x` value, which runs from about −6.28 on the left to +6.28 on the right. Down the **middle** of the plot (x ≈ 0) the values are washed out to near zero, and the amplitude grows as you move outward — but it does *not* keep growing all the way to the edges: `sin(x)` itself returns to zero at x = ±2π, so the outermost columns fade back toward zero too. The most extreme cells sit about three-quarters of the way out, near x ≈ ±4.9, where the array reaches its minimum of about −4.8; bright maxima of about +1.8 sit near x ≈ ±2. And because multiplying by a *negative* `x` flips the sign of the whole left half, the pattern is mirror-symmetric about the center instead of simply repeating.

**Check it.** Broadcasting claims that each column of `g` equals `f`'s column times that column's single `x` value — test it directly for, say, column 12:

**Code:**

```python
print(np.allclose(g[:, 12], f[:, 12] * x[12]))
```

**End of code.**

This prints:

```
True
```

**End of output.**

And the middle row of `g` is a 1D profile of the whole structure. Since `f`'s middle row is nearly `sin(x)`, this row is nearly `x·sin(x)`:

**Code:**

```python
plt.plot(x, g[25, :])
plt.show()
```

**End of code.**

**What this shows.** A mirror-symmetric W-shaped curve: zero at x = 0, rising to about +1.8 near x = ±2, plunging to about −4.8 near x = ±4.9, and returning to zero at the edges (x = ±2π). One curve captures the whole story — the quiet center, the strong lobes three-quarters of the way out, and the quiet edges.

However, if the last (trailing) dimensions are *not* the same, NumPy cannot just automatically figure it out. Here `y` has shape `(50,)`, and its 50 does **not** match `f`'s trailing dimension of 100:

**Code:**

```python
print(f.shape, y.shape)
h = f * y
```

**End of code.**

This raises an error. The last line of the traceback reads:

```
ValueError: operands could not be broadcast together with shapes (50,100) (50,)
```

**End of output.**

We can help NumPy by adding an extra dimension to `y` at the end (with `None`). Then `y` has shape `(50, 1)`, whose leading 50 lines up with `f`'s leading 50, and whose trailing 1 is stretched to 100:

**Code:**

```python
print(f.shape, y[:, None].shape)
h = f * y[:, None]
print(h.shape)
```

**End of code.**

This prints:

```
(50, 100) (50, 1)
(50, 100)
```

**End of output.**

**Code:**

```python
plt.pcolormesh(h)
plt.show()
```

**End of code.**

**What this shows.** `h = f * y[:, None]` multiplies each **row** of the pattern by that row's `y` value, which runs from about −3.14 at the bottom to +3.14 at the top. The middle row (y ≈ 0) is washed out to near zero, and the amplitude grows as you move away from it — but, as with `g`, not all the way out: the `cos(0.5·y)` factor in `f` vanishes at y = ±π, so the top and bottom edges fade back to zero as well. The strongest lobes sit a little over halfway out, near y ≈ ±1.7, where the values reach about ±1.12. And because the lower half is multiplied by *negative* y values, every lobe in the lower half has the opposite sign of the lobe directly above it in the upper half — a sign-flipped checkerboard, the row-wise counterpart of the `g` plot.

**Check it.** Same column test as before, plus a **column profile** this time — column 12 sits where `f` is near its maximum, so `h[:, 12]` traces the vertical structure cleanly:

**Code:**

```python
print(np.allclose(h[:, 12], f[:, 12] * y))
plt.plot(y, h[:, 12])
plt.show()
```

**End of code.**

The `print` gives `True`. **What the plot shows:** an antisymmetric curve along y — zero at the bottom edge (y = −π), dipping to about −1.12 at y ≈ −1.7, crossing zero exactly at y = 0, peaking at about +1.12 at y ≈ +1.7, and returning to zero at the top edge. The two halves are equal and opposite, which is the sign flip the heatmap shows.

:::{admonition} Try it
:class: tip
Create `broadcasting.py`. Build the surface `f = np.sin(xx) * np.cos(0.5*yy)` two ways and compare. First the 2D way (using `xx`, `yy`). Then rebuild it from the **1D** `x` and `y` arrays plus broadcasting: add a `None` axis on each in the right place so that `np.sin(x[None, :]) * np.cos(0.5 * y[:, None])` has shape `(50, 100)`. Verify the two results match — e.g. `print(np.allclose(f_2d, f_broadcast))` should print `True`.
:::

## Reduction Operations

In scientific data analysis, we usually start with a lot of data and want to **reduce** it down in order to make plots or summary tables. Operations that reduce the size of NumPy arrays are called "reductions". There are many different reduction operations; here we look at some of the most common.

The usual statistical reductions — sum, mean, standard deviation — work as you'd expect when applied to the whole array. (These use the `g` array from the broadcasting section, shape `(50, 100)`.)

**Code:**

```python
print(g.sum())
print(g.mean())
print(g.std())
```

**End of code.**

This prints (your exact digits may differ slightly):

```
-3083.038387807155
-0.616607677561431
1.6402280119141424
```

**End of output.**

So summing all 5000 elements gives about −3083, the mean is about −0.62, and the standard deviation is about 1.64. Each of these collapses the entire array to a single number.

A key property of NumPy reductions is the ability to operate on just **one axis**. First, here's `g` as a heatmap with a colorbar (the colorbar is the labeled color scale that maps colors to values):

**Code:**

```python
plt.pcolormesh(g)
plt.colorbar()
plt.show()
```

**End of code.**

**What this shows.** This is the `g = f * x` surface again (washed out down the center, strongest about three-quarters of the way toward the sides), now with a colorbar so you can read off the value range — the data runs from about −4.8 to +1.8, with the most negative cells in the two dark lobes near x ≈ ±4.9.

**Check it.** The non-visual equivalent of reading a colorbar is asking the array for its range directly:

**Code:**

```python
print(g.min(), g.max())
```

**End of code.**

This prints:

```
-4.810205904698232 1.8137647229935456
```

**End of output.**

We can reduce along **specific axes**. `axis=0` reduces along the first dimension (down the rows of `g`), leaving the **column** means — one value per column, so a length-100 result. `axis=1` reduces along the second dimension (across the columns), leaving the **row** means — a length-50 result.

**Code:**

```python
g_ymean = g.mean(axis=0)
g_xmean = g.mean(axis=1)
print(g_ymean.shape, g_xmean.shape)
```

**End of code.**

This prints:

```
(100,) (50,)
```

**End of output.**

**Code:**

```python
plt.plot(x, g_ymean)
plt.show()
```

**End of code.**

**What this shows.** `g_ymean` is the average over all 50 rows, one value per x-column, plotted against `x` (−2π to +2π). Averaging over y does **not** flatten the structure, because `g`'s oscillation runs along x — every column of `g` is `x·sin(x)` times `cos(0.5·y)`, and averaging the rows just replaces the cosine factor by its mean, about 0.62. So the curve is a scaled-down copy of the `x·sin(x)` W-shape from the broadcasting section: zero at x = 0 and at x = ±2π, local peaks of about +1.1 near x = ±2, and deep minima of about −3.0 near x = ±4.9, mirror-symmetric about the center.

**Check it.** The claimed extremes, straight from the array:

**Code:**

```python
print(g_ymean.min(), g_ymean.max())
```

**End of code.**

This prints:

```
-3.001540809618145 1.1317787518811862
```

**End of output.**

Notice what just happened, because it is the central move of non-visual data analysis: a reduction turned a 2D heatmap question into a **1D line plot** — exactly the kind of plot you can print as numbers or explore by sonification with MAIDR.

**Code:**

```python
plt.plot(g_xmean, y)
plt.show()
```

**End of code.**

**What this shows.** Here the averaged row values `g_xmean` are on the **horizontal** axis and `y` (−π to +π) is on the **vertical** axis (the arguments are swapped to make `y` the vertical coordinate). This time the average runs *along* the oscillation, and it does not cancel either: averaging `x·sin(x)` over the full −2π..+2π range gives about −1, because its negative lobes occur at larger |x| and outweigh the positive ones. Each row's mean is therefore about `−cos(0.5·y)`, and the curve is a smooth C-shaped bow opening to the right: it starts at 0 at the bottom (y = −π), bulges left to its most negative value of about −0.99 at the middle (y = 0), and returns to 0 at the top (y = +π). All values are ≤ 0.

**Check it.**

**Code:**

```python
print(g_xmean.min(), g_xmean.max())
```

**End of code.**

This prints:

```
-0.9881624404319173 -6.053860223869245e-17
```

**End of output.**

The first number is the −0.99 bulge at y = 0. The second looks odd, but read the `e-17` suffix: it is −6×10⁻¹⁷, i.e. zero up to floating-point round-off — the curve's value at the two ends.

Reductions can also operate on **multiple axes at once** — useful for higher-dimensional data:

**Code:**

```python
arr3d = np.ones((100, 50, 25))
print(arr3d.mean(axis=(0, 1, 2)))   # reduce across all three axes
```

**End of code.**

This prints:

```
1.0
```

**End of output.**

`arr3d` is filled entirely with ones, so the mean over all three axes is exactly `1.0`. (Reducing over every axis is the same as `arr3d.mean()` with no axis argument.)

:::{admonition} Try it
:class: tip
Create `reductions.py` with `g` rebuilt. Compute its overall sum, mean, and standard deviation (each in a `print`). Then take the mean **along axis 0** (the column means) and plot it against `x`. Take the standard deviation **along axis 1** (the row stds, `g.std(axis=1)`) and plot it against `y`. End with `plt.show()`.
:::

## Data Files

It's often useful to save a NumPy array to disk so you can reload it later or share it with someone else. NumPy provides a simple `.npy` binary format for this, plus loaders for plain text and other common formats.

Save the array `g` to a file:

**Code:**

```python
np.save('g.npy', g)
```

**End of code.**

This writes a file named `g.npy` in your working directory. It produces no output.

:::{warning}
NumPy `.npy` files are convenient for temporary data, but are not a robust archival format. Later we'll meet NetCDF, the recommended format for earth and environmental data.
:::

Load it back into a new variable:

**Code:**

```python
g_loaded = np.load('g.npy')
print(g_loaded)
```

**End of code.**

This prints the array you saved — the same `(50, 100)` block of numbers as `g`.

NumPy provides `np.testing.assert_equal` to check that two arrays are element-wise equal. To confirm that it really checks equality, here's a **deliberate failure** — we scale `g_loaded` by `0.5` first, so the arrays no longer match:

**Code:**

```python
np.testing.assert_equal(g, g_loaded*0.5)
```

**End of code.**

This raises an error. The traceback begins with:

```
AssertionError:
Arrays are not equal
```

**End of output.**

(It then prints details about how many elements differ and by how much.)

Now the real comparison, which **passes**:

**Code:**

```python
np.testing.assert_equal(g, g_loaded)
```

**End of code.**

No output means the assertion **passed** — the loaded array is element-wise equal to the original. (Unlike a bare expression in a notebook, a passing assertion prints nothing; silence here is success.)

:::{admonition} Try it
:class: tip
Create `datafiles.py` with `arr3d = np.ones((100, 50, 25))`. Save the 3D array to a file called `arr3d.npy` with `np.save`. Load it back into a new variable with `np.load` and verify they're equal using `np.testing.assert_equal(arr3d, arr3d_loaded)`. If it prints nothing, the round-trip worked.
:::

## Recap

In this lecture you met **NumPy** and **Matplotlib**, the foundations of scientific Python:

- **NumPy** gives you the `ndarray`: a fast, N-dimensional, single-dtype container. You learned to **create** arrays (`np.array`, `np.zeros`/`np.ones`/`np.full`, `np.arange`, `np.linspace`/`np.logspace`, `np.meshgrid`), **inspect** them (`.dtype`, `.shape`), **index and slice** them (comma-separated per dimension, plus boolean masks), do **element-wise math** (`np.sin`, `*`, etc.), **manipulate dimensions** (`transpose`, `reshape`, `tile`, and `None` to add axes), combine different-sized arrays with **broadcasting**, **reduce** them (`sum`, `mean`, `std`, optionally `axis=...`), and **save/load** them (`np.save`, `np.load`).
- You also met **Matplotlib**: `plt.plot` for 1D lines, `plt.pcolormesh` for 2D heatmaps, and `plt.show()` / `plt.savefig()` to display or save a figure from a script. A later page, *More Matplotlib*, goes deeper into figure and axis control and more plot types; for exploring charts by sound and text, see [Trying MAIDR](./trying_maidr.md).
- Just as importantly, you practiced **checking arrays without a picture**: shape before and after, spot-checking predictable values, `np.allclose`/`np.array_equal` on whole slices, `min`/`max`/`mean` as a textual colorbar, and slicing out **1D profiles** (a single row, column, or axis-reduction) to turn a 2D question into a curve you can print or sonify. This habit is how you verify your own work in the assignments — and how working scientists debug arrays, sighted or not.

The NumPy documentation is your best reference as you go: <https://numpy.org/doc/stable/reference/>.
