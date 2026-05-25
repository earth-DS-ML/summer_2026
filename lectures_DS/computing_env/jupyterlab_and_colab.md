# Intro to JupyterLab and Colab

This course gives you two browser-based environments for running Python and working with files: **JupyterLab** (via our cloud-based JupyterHub) and **Google Colab**. JupyterLab on the hub is the primary environment; Colab is a free fallback you can use any time — particularly useful in the first week before your hub account is provisioned, or if your laptop doesn't have a Unix shell (e.g., Windows without WSL).

## What is a Jupyter notebook?

A Jupyter notebook is a document (a `.ipynb` file) that mixes three things in one place:

- **Code cells** — small blocks of Python (or another language) that you can run independently. Output appears right below the cell.
- **Markdown cells** — formatted text for explanations, equations, links, and images. These don't execute; they just display.
- **Outputs** — printed text, tables, and plots produced by code cells.

When you run a code cell, the code is executed by a **kernel** — a running Python session attached to the notebook. The kernel remembers everything you've defined (variables, imports, functions) until you restart it, so a variable set in cell 3 is still available in cell 7.

This mix of code, prose, and output is what makes notebooks well-suited to data analysis: you write code, look at the result, and explain what's going on, all in the same document.

Notebooks are just files. The tools described below — JupyterLab and Colab — are two different ways to open, edit, and run them.

*To see a notebook in action with no setup, try one at [jupyter.org/try](https://jupyter.org/try). For deeper background, see the [official Jupyter docs](https://docs.jupyter.org/en/latest/start/index.html).*

## JupyterLab

[JupyterLab](https://jupyterlab.readthedocs.io) is the primary tool we'll use to work with notebooks (and to access a Unix terminal, edit files, and more). It runs entirely in your web browser, so there's nothing to install locally. You'll get comfortable with it as we use it throughout the term.

*For a tour of the JupyterLab interface, see the [official user guide](https://jupyterlab.readthedocs.io/en/stable/user/interface.html).*

### Our Course JupyterHub

[JupyterHub](https://jupyter.org/hub) is a multi-user Jupyter environment designed for companies, classrooms and research labs. This course will use a cloud-based JupyterHub environment supported by the [NSF LEAP STC](https://leap.columbia.edu/) and managed by [2i2c](https://2i2c.org/infrastructure/):

[![Launch JupyterHub](https://img.shields.io/badge/jupyterhub-leap.2i2c.cloud-orange?style=for-the-badge&logo=jupyter)](https://leap.2i2c.cloud/)

A lot of documentation about this hub can be found at https://leap-stc.github.io

## Google Colab

[Google Colab](https://colab.research.google.com/) is a free, browser-based notebook environment provided by Google. Notebooks you create in Colab (`.ipynb` files) are saved automatically to a `Colab Notebooks` folder in your Google Drive — that's where to find them. The file browser sidebar inside Colab shows a separate, temporary workspace, *not* your Drive.

For this course, Colab serves as a fallback when:

- Your LEAP JupyterHub account hasn't been provisioned yet (typically the first few days of the term).
- Your laptop is on Windows and you don't have a Unix shell installed.
- Your LEAP account is temporarily unavailable.

Once your LEAP access is active, prefer JupyterLab on the hub — it has the course's standard Python environment, persistent storage, and a smoother git authentication flow.

One gotcha to keep in mind: the Colab runtime is ephemeral — files and installed packages disappear when the session ends. Save anything you want to keep to GitHub or download it.

## Markdown

**Markdown** is the formatting language used in the Markdown cells of Jupyter notebooks. It's a simple way to format text — headings, lists, links, code, images — using plain-text symbols (`#` for headings, `*` for emphasis, and so on). It's also used for standalone `.md` documents — [here's the Markdown source of this page on GitHub](https://github.com/earth-DS-ML/summer_2026/blob/main/lectures_DS/computing_env/jupyterlab_and_colab.md?plain=1).

*For syntax, see the [Markdown Guide](https://www.markdownguide.org/basic-syntax).*

## Python Environments

On the LEAP hub everyone uses a common environment, which is inherited from these [docker images](https://github.com/pangeo-data/pangeo-docker-images/). Colab also provides a pre-installed scientific Python stack.

A thorough description of how to manage your own environment when needed can be found in: https://earth-env-data-science.github.io/lectures/environment/python_environments.html

## In-class practice

Work through these in your environment of choice — LEAP if you have access, Colab otherwise. About 15 minutes; ask if you get stuck.

1. **Log in.** Open your environment in a browser — LEAP at [leap.2i2c.cloud](https://leap.2i2c.cloud), or Colab at [colab.research.google.com](https://colab.research.google.com). Get to a screen where you can create a new notebook.

2. **Create a notebook.** Make a new Python notebook named `hello.ipynb`.

3. **Run a code cell.** Type `print("Hello from " + "your environment here")` into a code cell and run it. Confirm the output appears beneath the cell.

4. **Add a Markdown cell.** Insert a Markdown cell, write a top-level heading (e.g., `# My first notebook`) and a short paragraph below it. Render the cell and confirm the heading appears larger than the paragraph.

5. **Find your file.**
   - **JupyterLab:** open the file browser sidebar and confirm `hello.ipynb` is listed in your home directory.
   - **Colab:** open Google Drive in another browser tab. Your notebook should be in a folder called `Colab Notebooks`.
