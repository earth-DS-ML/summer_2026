# Overview

This section introduces the **scientific Python ecosystem** that powers most data analysis in earth and environmental science: **NumPy** for numerical arrays, and **Matplotlib** for visualization. Both libraries underpin everything that comes after (pandas, xarray, scikit-learn, …), so spending time on the fundamentals here pays off across the rest of the course.

## Learning objectives

By the end of this section, you should be able to:

1. **Create and inspect NumPy arrays** — using `np.array`, `np.arange`, `np.linspace`, `np.zeros`, `np.ones`, and check shape and dtype.
2. **Index and slice arrays** — basic indexing, slicing, fancy indexing, and boolean masks; on both 1D and 2D arrays.
3. **Vectorize operations** — apply element-wise math and reductions (`mean`, `sum`, `std`) along specific axes.
4. **Reshape and combine arrays** — using `reshape`, `transpose`, `tile`, and adding new axes via `[None, :]`.
5. **Understand broadcasting** — the rules and common gotchas.
6. **Build a Matplotlib figure** — using the explicit Figure / Axes mental model (`fig, ax = plt.subplots()`).
7. **Make and customize common plots** — line, scatter, bar, pcolormesh, contour, quiver — with labels, legends, colors, and annotations.

## Pages in this section

- [NumPy and Matplotlib](./numpy_and_matplotlib.ipynb) — array creation and operations, paired with light Matplotlib usage for visualization.
- [More Matplotlib](./more_matplotlib.ipynb) — the Figure/Axes mental model, customization, and 2D plot types.
- [Assignment 4a](./assignment4a.ipynb) — numpy reductions, broadcasting, and matplotlib on real ARGO float data.
- [Assignment 4b](./assignment4b.ipynb) — replicate three target figures using only numpy and matplotlib.

## Working through the lectures

Both lecture pages are **Jupyter notebooks**. Use the download button in the top-right (⬇ icon) to grab the `.ipynb` file, open it in your environment (JupyterLab on LEAP or Colab), and step through the cells. For the shorter notebooks you can also just copy-paste the cells into a fresh notebook. The **Try it** admonitions invite you to experiment in your own cells before moving on.
