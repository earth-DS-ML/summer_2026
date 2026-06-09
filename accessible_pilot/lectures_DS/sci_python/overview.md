# Overview

This section introduces the **scientific Python ecosystem** that powers most data analysis
in earth and environmental science: **NumPy** for numerical arrays, and **Matplotlib** for
visualization. Both libraries underpin everything that comes after (pandas, xarray,
scikit-learn, …), so the fundamentals here pay off across the rest of the course.

:::{admonition} A note on visualization and this accessible version
:class: important
This is the first section where the course leans heavily on **plots**. *Producing* a plot
with Matplotlib is a normal coding skill, and you'll learn it. But "reading" a plot
visually isn't how you'll work — so in this accessible version, wherever the course would
have you look at a figure, we instead **(a)** describe what the data shows in words and
numbers, and **(b)** point you to tools that let you explore a chart by **sound and text**.
The main one is a pilot we're trying out, called MAIDR — see [Trying
MAIDR](./trying_maidr.md). Your feedback on what helps will shape how we build the rest of
this section.
:::

## Learning objectives

By the end of this section, you should be able to:

1. **Create and inspect NumPy arrays** — using `np.array`, `np.arange`, `np.linspace`,
   `np.zeros`, `np.ones`, and check shape and dtype.
2. **Index and slice arrays** — basic indexing, slicing, fancy indexing, and boolean masks,
   on both 1D and 2D arrays.
3. **Vectorize operations** — apply element-wise math and reductions (`mean`, `sum`, `std`)
   along specific axes.
4. **Reshape and combine arrays** — using `reshape`, `transpose`, `tile`, and adding new
   axes via `[None, :]`.
5. **Understand broadcasting** — the rules and common gotchas.
6. **Build a Matplotlib figure** — using the explicit Figure / Axes mental model
   (`fig, ax = plt.subplots()`).
7. **Make common plots** — line, scatter, bar — and understand what each is meant to
   reveal about the data.

## Pages in this section

- [NumPy](./numpy.md) — creating, indexing, and operating on arrays (visualized along the
  way, with accessible descriptions).
- [Visualizing Arrays with Matplotlib](./matplotlib.md) — the Matplotlib basics for plotting
  arrays.
- [Trying MAIDR](./trying_maidr.md) — a pilot tool for exploring Matplotlib charts by sound,
  text, and braille.

*(The remaining, more visualization-heavy pages — More Matplotlib, and Assignments 4a and
4b — are being adapted with this same approach and will appear here as they're ready.)*

## Working through the lectures

Work through the pages as **Python scripts** in VS Code — type the examples into a `.py`
file, run them in your Git Bash terminal with `python yourfile.py`, and read the output
with `Alt+F2`. The **Try it** boxes invite you to experiment. If you haven't set up VS Code
yet, start with [Setting up an accessible workflow](../computing_env/accessible_setup.md)
and [Working with Python scripts](../computing_env/working_with_scripts.md).

:::{admonition} In-class assignment — 10 points
:class: note
Your in-class assignment for this section is to complete the **"Try it"** exercises in the
lecture pages above, as `.py` scripts. To submit, push your scripts to your week folder and
post a link to that folder on the matching **Courseworks** assignment. (The two at-home
assignments, 4a and 4b, are graded separately, 10 points each.)
:::
