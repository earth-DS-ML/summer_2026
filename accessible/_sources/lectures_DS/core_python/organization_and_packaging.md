# Organizing Python Projects

:::{admonition} Following along with a screen reader
:class: important
Work through the examples as `.py` scripts in VS Code — see [Setting up an accessible workflow](../computing_env/accessible_setup.md) and [Working with Python scripts](../computing_env/working_with_scripts.md). Run a script in the Git Bash terminal with `python yourfile.py`, and press `Alt+F2` to read the output.
:::

A complex research project often relies on many different programs and software packages to accomplish the research goals. An important part of data science or scientific computing is deciding how to organize and structure the code you use for data analysis and research. A well-structured project can make you a more efficient and effective researcher. It is also a key component of [scientific reproducibility](http://lorenabarba.com/blog/barbagroup-reproducibility-syllabus/).

Just putting all of your code into git repositories won't magically turn a mess of scripts into a beautiful, well-organized project. More deliberate effort is required.

This chapter is a natural fit for your scripts-based workflow: the whole topic is how to organize Python code into `.py` files — scripts and modules — which is exactly what you are already doing.

## Types of Projects

Not all projects are created equal. There are three common "research code" scenarios in geosciences:

1. **Exploratory analyses**: When exploring a new idea, a single notebook or script is often all we need.
2. **A Single Paper**: The "paper" is a standard unit of scientific output. The code related to a single paper usually belongs together.
3. **Reusable software elements**: In the course of our computing, we often identify specialized routines that we want to package for reuse in other projects, or by other scientists. This is where "scripts" become "software."

This page outlines some suggested practices for each category.

:::{admonition} Working through this page
:class: tip
The first half of this page (project organization and reproducibility) is reading material — no code to run.

The **Modules** section below has runnable code that imports a small `gcdistance.py` module. The full contents of that module are shown in the Modules section — create it yourself as a file named `gcdistance.py` in VS Code, then put the example scripts in the **same folder** so they can import it. Run each script in the Git Bash terminal with `python yourfile.py` and read the output with `Alt+F2`.
:::

:::{admonition} In-class assignment — 10 points
:class: note
The **"Try it"** exercises on this page are part of your in-class assignment for this section. Complete them as `.py` scripts, push them to your week folder, and post the link on the matching **Courseworks** assignment. (One 10-point assignment covers all the lecture material in this section.)
:::

## Exploratory Analysis

When starting something new, you're often motivated to just start coding and get results quickly. This is fine! In the standard course, Jupyter notebooks are used for open-ended exploratory analysis because they keep text, code, and figures in one place. In your scripts-based workflow, the equivalent is a single `.py` file where you write a bit of code, run it, read the output with `Alt+F2`, and keep going — the same quick, iterative loop, in a linear text file your screen reader handles well.

If you find something cool or useful, preserve it. The simplest option is a single script (or notebook) in a folder of your project repo (e.g., the GitHub repo from Assignment 1).

For one-off files you want to share with others, GitHub's [Gist mechanism](https://help.github.com/articles/about-gists/) is also a lightweight option — gists are mini repos you can add a file to at <https://gist.github.com/>.

## A Single Paper

### Scientific Reproducibility

Reproducibility is a cornerstone of the scientific process. Today, almost all earth science relies on some form of computation — from simple statistical analysis to advanced numerical simulation. In principle, computational science should be highly reproducible. In practice, [it often isn't](http://www.nature.com/news/1-500-scientists-lift-the-lid-on-reproducibility-1.19970).

The audience for a reproducible project isn't just other scientists — it's *you*, a year from now, when you need to repeat or build on this work. Extra time spent on reproducibility now pays off later.

A useful framing from one of the earliest papers on the topic:

> An article about computational science … is not the scholarship itself, it's merely scholarship advertisement. The actual scholarship is the complete software development environment and the complete set of instructions which generated the figures.

*Donoho, D. et al. (2009), Reproducible research in computational harmonic analysis, Comp. Sci. Eng. 11(1):8–18, doi: [10.1109/MCSE.2009.15](http://dx.doi.org/10.1109/MCSE.2009.15)*

A few practical principles that follow from this:

1. **Keep track of how each result was produced** — which script, which inputs, which parameters.
2. **Version-control all custom scripts** (Git is doing this for you).
3. **Avoid manual data-manipulation steps** — write a script (or run a block of code) instead of editing the data by hand.
4. **Provide public access** to code, raw data, and analysis steps, so others (and future you) can re-run them.

> *Further reading:* [Sandve et al. (2013)](http://dx.doi.org/10.1371/journal.pcbi.1003285) lists ten specific recommendations for computational reproducibility, and the [Barba-group Reproducibility Syllabus](https://doi.org/10.6084/m9.figshare.4879928.v1) collects best-practice references.

These principles suggest a certain structure for a project.

### Project Layout

A reproducible single-paper project directory structure might look something like this. Read it top to bottom; each line is one file, and the `/` characters separate folders from the files inside them:

**Example:**

~~~
README.md
LICENSE
environment.yml
data/intermediate_results.csv
notebooks/process_raw_data.ipynb
notebooks/figure1.ipynb
notebooks/figure2.ipynb
notebooks/helper.py
manuscript/manuscript.tex
~~~

**End of example.**

So this project has three files at the top level (`README.md`, `LICENSE`, and an `environment.yml` describing the software environment), a `data` folder holding a results file, a `notebooks` folder holding the analysis files and a shared `helper.py` module, and a `manuscript` folder holding the paper itself. In your workflow, the analysis files would simply be `.py` scripts instead of `.ipynb` notebooks — for example `notebooks/figure1.py` — but the organizing idea is identical: code, data, and manuscript each have a clear place.

A great example of such a paper is [Cesar Rocha](https://crocha700.github.io/)'s Upper Ocean Seasonality project: <https://github.com/crocha700/UpperOceanSeasonality>.

## Reusable Software Elements

Scientific software can perhaps be grouped into two categories: single-use "scripts" that are used in a very specific context to do a very specific thing (e.g. to generate a specific figure for a paper), and reusable components which encapsulate a more generic workflow. Once you find yourself repeating the same chunks of code in many different scripts or projects, it's time to start composing reusable software elements.

### Modules

The basic element of reusability in Python is the [module](https://docs.python.org/3/tutorial/modules.html). A module is a `.py` file which contains Python objects that can be *imported* by other scripts or notebooks. Because you are already writing `.py` files, you have in fact been creating things that *could* be modules all along — the only new idea here is importing one file's code into another. Let's illustrate how modules work with a simple example.

A common task in geoscience is to calculate the [great-circle distance](https://en.wikipedia.org/wiki/Great-circle_distance) between two points on the globe. There are several [packages](https://pypi.python.org/pypi/geopy) that could do this for you, but let's write our own as an example of a module.

The formula for great-circle distance computes the angle between two points (given by their latitudes and longitudes) as seen from the center of the Earth, then multiplies that angle by the Earth's radius $r$ to get the distance along the surface. Writing $\phi_1, \phi_2$ for the two latitudes, $\lambda_1, \lambda_2$ for the two longitudes, and $\Delta\lambda = \lambda_2 - \lambda_1$ for the difference in longitude, the distance $d$ is:

$$
d = r \, \arccos\!\big( \sin\phi_1 \sin\phi_2 + \cos\phi_1 \cos\phi_2 \cos\Delta\lambda \big)
$$

(Note that this formula requires 64-bit precision for adequate accuracy.)

Let's write a module to do this calculation. In VS Code, create a file called `gcdistance.py`. (The file should be in the **same directory** as any script that will import it.) Populate it with the following code:

**Code:**

```python
"""
A python module for computing great circle distance
"""
import numpy as np

# approximate radius of Earth
R = 6.371e6

def great_circle_distance(point1, point2):
    """Calculate great-circle distance between two points.
    
    PARAMETERS
    ----------
    point1 : tuple
        A (lat, lon) pair of coordinates in degrees
    point2 : tuple
        A (lat, lon) pair of coordinates in degrees
        
    RETURNS
    -------
    distance : float
    """
    
    # unpack coordinates
    lat1, lon1 = point1
    lat2, lon2 = point2
    
    # unpack and convert everything to radians
    phi1, lambda1, phi2, lambda2 = [np.deg2rad(v) for v in 
                                    (point1 + point2)]
    
    # apply formula
    # https://en.wikipedia.org/wiki/Great-circle_distance
    return R*np.arccos(
        np.sin(phi1)*np.sin(phi2) + 
        np.cos(phi1)*np.cos(phi2)*np.cos(lambda2 - lambda1))
```

**End of code.**

The module begins with a [docstring](https://www.python.org/dev/peps/pep-0257/) (the triple-quoted text at the top) explaining what it does. Then it contains some data (just a constant `R`, the radius of the Earth in meters) and a single function. The function takes two `(lat, lon)` pairs, converts the degree values to radians, and applies the formula above. This is the same `r` and the same formula from the math above, written in code: `R` is the radius $r$, and `np.arccos(...)` is the $\arccos$ of the sines-and-cosines expression.

This `gcdistance.py` is a real, working module — it is part of the course repository. You can write it yourself by typing the code above into a new file in VS Code and saving it as `gcdistance.py`.

Now let's import our module. Create a second file in the same folder — call it `try_gcdistance.py` — and start with:

**Code:**

```python
import gcdistance
help(gcdistance)
```

**End of code.**

Run it with `python try_gcdistance.py` and read the result with `Alt+F2`. The `help()` function prints the module's docstring and the documentation for everything it contains. This prints:

```
Help on module gcdistance:

NAME
    gcdistance - A python module for computing great circle distance

FUNCTIONS
    great_circle_distance(point1, point2)
        Calculate great-circle distance between two points.

        PARAMETERS
        ----------
        point1 : tuple
            A (lat, lon) pair of coordinates in degrees
        point2 : tuple
            A (lat, lon) pair of coordinates in degrees

        RETURNS
        -------
        distance : float

DATA
    R = 6371000.0

FILE
    /path/to/your/gcdistance.py
```

**End of output.**

(The last line, `FILE`, will show the actual path to your own `gcdistance.py`, so it will differ from what is shown here.)

And let's try using it to make a calculation. We'll `print()` the result so it shows up in the terminal — remember that, unlike a notebook, a script only displays a value if you explicitly print it:

**Code:**

```python
import gcdistance
print(gcdistance.great_circle_distance((60, 0), (50, 15)))
```

**End of code.**

This prints:

```
1460007.189049398
```

**End of output.**

The result is the distance in **meters** between the point at latitude 60, longitude 0 and the point at latitude 50, longitude 15 — about 1,460 km.

> *Note:* depending on your NumPy version, the value may print wrapped as `np.float64(1460007.189049398)` instead of the bare number. That is just how newer NumPy displays a floating-point number; the value is the same, and you can do arithmetic on it (such as dividing by 1000) exactly as you would with any number.

We could instead import just the pieces we need, by name. Notice we can pull in both the function and the constant `R`:

**Code:**

```python
from gcdistance import R, great_circle_distance
print(R)
```

**End of code.**

This prints:

```
6371000.0
```

**End of output.**

That is the Earth radius constant, `6.371e6` written out in full. After a `from gcdistance import ...`, you can use `R` and `great_circle_distance` directly, without the `gcdistance.` prefix.

If you change the module while working, a script picks up the change automatically the next time you run it — that is one quiet advantage of the script workflow: every `python try_gcdistance.py` starts fresh and re-reads `gcdistance.py`. In a long-running notebook (or an interactive Python session) this is not automatic; there you would have to restart the kernel or reload the module:

**Code:**

```python
import gcdistance
from importlib import reload
reload(gcdistance)
```

**End of code.**

(Note that a function imported via `from module import func` cannot be reloaded this way.) You generally will not need `reload` in the scripts workflow — it is mentioned here so you recognize it if you see it in notebook-based materials.

Modules are a simple way to share code between different scripts in the same project. *Module files must reside in the same directory as any script which imports them!* This is a big limitation; it means you can't easily share modules between different projects.

Once you have a piece of code that is general-purpose enough to share between projects, you need to create a package.

:::{admonition} Try it
:class: tip
1. In VS Code, create `gcdistance.py` with the module code shown above (or download it from the course repo), and save it.
2. In the **same folder**, create a new file — for example `city_distances.py` — that imports the module and uses `gcdistance.great_circle_distance` to compute the distance between **three pairs of cities** you're interested in. Lat/lon coordinates are easy to find online (search "latitude longitude of <city>"). The result is in meters, so divide by 1000 for kilometers, and `print()` each one.
3. Save, then run it in the Git Bash terminal with `python city_distances.py`, and press `Alt+F2` to read the distances.
4. Cross-check one of your answers against the distance shown on Google Maps.

For example, the distance from New York City `(40.7128, -74.0060)` to London `(51.5074, -0.1278)` comes out to about 5,570 km.
:::

### Aside: Python Style

There are few absolute rules for Python code style, but there is a detailed [recommended style guide](https://www.python.org/dev/peps/pep-0008/). Some especially relevant points are:

- Line length should not exceed 79 characters
- Module names should be `lowercase`
- Function and variable names should be `lower_case_with_underscores`
- Class names should be `CamelCase`

### Beyond modules: packaging, testing, CI

Once your code is general enough to share between projects or with collaborators, the next step is turning it into a **package** — a directory with a particular layout that can be installed with `pip` and used from anywhere. A parallel ecosystem of testing (with `pytest`) and continuous integration (with GitHub Actions) grows naturally alongside it.

This is beyond the scope of the weekly assignments, but there is a reference walkthrough, *Packaging Python Code*, under *Other Geoscience and Advanced Concepts* in the main course materials whenever you're ready.
