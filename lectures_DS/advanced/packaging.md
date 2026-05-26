# Packaging Python Code

This page extends the [Modules section](../core_python/organization_and_packaging.ipynb) from Core Python. There, we showed how a `.py` file (a *module*) can hold reusable functions that you import into a notebook — but modules only work from the same directory. Once you want to share code across projects, with collaborators, or with the wider community, you turn the module into a **package**. This page walks through that step and the surrounding ecosystem of tests and continuous integration.

None of this is required for the weekly assignments — it's reference / further-reading material for when your own research code matures.

## Packages

[Packages](https://docs.python.org/3/tutorial/modules.html#packages) are Python's way of encapsulating reusable code for sharing with others. Packaging is a huge topic — this page just scratches the surface.

You've already interacted with many packages. Browse some of their GitHub repositories to see the structure of a large Python package:

- NumPy: <https://github.com/numpy/numpy>
- Pandas: <https://github.com/pandas-dev/pandas>
- Xarray: <https://github.com/pydata/xarray>

For a smaller, more readable example, see the xrft package: <https://github.com/xgcm/xrft>

These packages all share a common basic structure. Imagine we wanted to turn our `gcdistance` module into a package — it would look like this:

```
README.md
LICENSE
environment.yml
requirements.txt
setup.py
gcdistance/__init__.py
gcdistance/gcdistance.py
gcdistance/tests/__init__.py
gcdistance/tests/test_gcdistance.py
```

The actual package is the `gcdistance/` subdirectory. The other files are auxiliary — they help others understand and install the package:

| File | Purpose |
|------|---------|
| `README.md` | Explain what the package is for |
| `LICENSE` | Defines the legal terms under which others can use the package. [Open source](https://opensource.org/licenses/category) is encouraged. |
| `environment.yml` | A conda environment describing the package's dependencies ([more info](https://conda.io/docs/user-guide/tasks/manage-environments.html#creating-an-environment-from-an-environment-yml-file)) |
| `requirements.txt` | Same idea, but for pip ([more info](https://pip.pypa.io/en/stable/user_guide/#requirements-files)) |
| `setup.py` | A Python script that installs your package ([more info](https://setuptools.readthedocs.io/en/latest/setuptools.html)) |

## The actual package

The directory `gcdistance/` is the actual package. Any directory containing an `__init__.py` file is recognized by Python as a package. The `__init__.py` file can be blank, but it needs to be present. From the parent directory, you can import a module from the package as follows:

```python
from gcdistance import gcdistance
```

This is a bit redundant — that's because the `gcdistance.py` module has the same name as the `gcdistance` package directory.

This import only works from the parent directory; it isn't globally accessible from your Python environment until you install the package (next section).

## `setup.py`

`setup.py` is the file that makes your package installable and globally accessible. Here is a minimal example:

```python
from setuptools import setup

setup(
    name="gcdistance",
    version="0.1.0",
    author="Your Name",
    packages=["gcdistance"],
    install_requires=["numpy"],
)
```

There's a [wide range of options](https://setuptools.readthedocs.io/en/latest/setuptools.html) for `setup.py`. More fields are required if you want to [upload your package to PyPI](https://packaging.python.org/en/latest/tutorials/packaging-projects/) (so others can install it via `pip install gcdistance`).

To install the package locally, run from the root directory:

```bash
pip install .
```

If you plan to keep developing the package, install it in "editable" mode:

```bash
pip install -e .
```

In editable mode, the files are symlinked rather than copied — changes you make to the source take effect immediately without reinstalling.

> **Note:** Modern Python packaging is moving from `setup.py` to a [`pyproject.toml`](https://packaging.python.org/en/latest/guides/writing-pyproject-toml/) file as the canonical place to declare package metadata. `setup.py` still works for now, but new projects often start with `pyproject.toml` from the beginning.

## Testing

A software package needs [tests](https://en.wikipedia.org/wiki/Software_testing) to make sure it works as intended — and continues to work after future changes. Matt Rocklin has a [blog post](http://matthewrocklin.com/blog/work/2016/02/08/tests) on why tests matter.

Tests don't have to be complicated. They are simply checks that verify your code does what it's supposed to do.

To add tests to our project, create `gcdistance/tests/test_gcdistance.py` (and an empty `__init__.py` in the same directory). Here's an example:

```python
import numpy as np
import pytest

from gcdistance.gcdistance import great_circle_distance

def test_great_circle_distance():
    # distance between two identical points should be zero
    assert great_circle_distance((20., 30.), (20., 30.)) == 0

    # distance between New York and London
    new_york = 40.7128, -74.0060
    london = 51.5074, 0.1278
    dist_nyc_london = great_circle_distance(new_york, london)

    # approximate check (floats won't be exactly equal)
    np.testing.assert_allclose(dist_nyc_london, 5.587e6, rtol=1e-5)

    # passing the wrong number of arguments should raise TypeError
    with pytest.raises(TypeError):
        great_circle_distance(1, 2, 3, 4)
```

We use [pytest](https://docs.pytest.org/) to run our tests. Install it with `pip install pytest` if you don't already have it, then run:

```bash
pytest -v
```

from the root directory of your project. You should see a notification that the tests passed. Try changing a test to make it fail, and see what pytest reports.

## Continuous Integration with GitHub Actions

You can configure automatic testing by integrating GitHub with [GitHub Actions](https://docs.github.com/en/actions). Every time you push commits, your tests run automatically in the cloud, and GitHub flags any failures on the pull request page.

A minimal setup is a single YAML file at `.github/workflows/test.yml`:

```yaml
name: tests
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.11"
      - run: pip install -e . pytest
      - run: pytest
```

Commit this file alongside your code, push to GitHub, and the next push triggers a test run. You'll get a green checkmark (or a red X) next to each commit on the GitHub web UI.

> Older course material on the web sometimes references **Travis CI** — that was the previous-generation CI service and has been largely replaced by GitHub Actions for open-source Python projects.
