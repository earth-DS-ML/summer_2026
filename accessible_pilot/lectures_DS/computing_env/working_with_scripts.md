# Working with Python scripts

In this course you'll run Python by writing **scripts** — plain text files of Python code
that you run from the terminal — and editing them in **VS Code**. This page explains that
workflow and why we use it.

:::{admonition} Why scripts instead of notebooks?
:class: important
The standard version of this course uses **Jupyter notebooks** in a web browser (on the
LEAP hub or Google Colab). Notebooks are popular because they mix code, results, and
explanation in one visual document — but that same cell-based, visual design is difficult
to navigate with a screen reader. **Scripts give you the same Python and the same results
in a linear, text-based form your screen reader handles well.** Anywhere the main course
shows a notebook, you can copy its code into a `.py` file and run it exactly as described
here.
:::

If you haven't set up VS Code yet, do that first:
[Setting up an accessible workflow](accessible_setup.md).

## What is a script?

A **script** is a file ending in `.py` that contains Python code. When you "run" the
script, Python reads it from top to bottom and does what it says. That's the whole idea —
it's just a list of instructions in a text file.

For example, here is a short script:

~~~python
# lesson1.py
name = "Sam"
print("Hello,", name)

temperature_c = 25
temperature_f = temperature_c * 9 / 5 + 32
print("Temperature in Fahrenheit:", temperature_f)
~~~

Reading top to bottom: it stores a name, prints a greeting, computes a temperature in
Fahrenheit, and prints it. Nothing happens out of order, which makes a script easy to
follow with a screen reader — line by line, exactly as written.

## The write–save–run loop

This is the core cycle you'll repeat constantly:

1. **Write** — type your code into a `.py` file in the VS Code editor.
2. **Save** — press `Ctrl+S`.
3. **Run** — in the VS Code terminal, type `python` followed by the file name, then press
   Enter.
4. **Read the output** — press `Alt+F2` to read what the program printed.

:::{admonition} Try it
:class: tip
1. In VS Code, create a new file (`Ctrl+N`) and save it as `lesson1.py` (`Ctrl+S`).
2. Type the example script above into it, and save again.
3. Open a terminal (`` Ctrl+` ``) and run it:

   ~~~
   python lesson1.py
   ~~~

4. Press `Alt+F2` to read the output. You should hear the greeting and the temperature.
:::

When something is wrong, Python prints an **error message** (a "traceback") that ends with
the type of error and the line number. Read it with `Alt+F2` — the last line is usually
the most useful, and the line number tells you where to look.

## Running code piece by piece (optional)

Sometimes you want to run just part of a script and check the result before continuing.
You can do this without notebooks: put a line containing only `# %%` above a block of code
in a `.py` file, and VS Code treats it as a runnable "cell." You can run one cell at a
time, and the result appears in a panel you can read with `Alt+F2`. This gives you the
step-by-step feel of a notebook while keeping a plain, linear `.py` file. It needs the
**Jupyter** extension (install it from the Extensions view, like the Python one) and the
Jupyter package (`pip install jupyter`); it's optional, and writing and running whole
scripts is enough to start.

## Markdown

Some files in this course are **Markdown** (`.md`) rather than Python — for example,
README files and the resume you'll write in Assignment 1. Markdown is a simple way to
format text using plain-text symbols:

- `#` at the start of a line makes a heading (`##` a smaller heading, and so on).
- `*text*` makes *italic* text; `**text**` makes **bold**.
- `- item` on its own line makes a bulleted list.
- `[link text](https://example.com)` makes a link.
- `![alt text](image.png)` inserts an image, where the **alt text** in the square brackets
  is what a screen reader reads in place of the image — always write it.

Markdown is plain text, so it is fully screen-reader-friendly. *For the full syntax, see
the [Markdown Guide](https://www.markdownguide.org/basic-syntax).*

## Python packages

For the first part of the course, the built-in Python plus a few packages you install with
`pip` (in the terminal, e.g. `pip install numpy`) is all you need. Later chapters use a
larger scientific stack (NumPy, Pandas, Xarray); when you get there you'll either install
those locally or use the LEAP hub, which already has them. You don't need any of that yet.

## The LEAP hub (for later)

The course's large climate datasets live in the cloud, on the **LEAP JupyterHub**. You
won't need it for the first chapters, which run fine on your own computer. When you do need
it, you'll connect to it *from this same VS Code* using a remote connection, so your
accessible script workflow stays exactly the same — only the data and computing move to the
cloud. The steps are on the [setup page](accessible_setup.md).

## In-class practice

About 15 minutes; ask if you get stuck.

1. **Open VS Code** and open a terminal (`` Ctrl+` ``).
2. **Write a script.** Create `hello.py`, put `print("Hello, world!")` in it, and save.
3. **Run it.** In the terminal, run `python hello.py`, then press `Alt+F2` to confirm the
   output reads `Hello, world!`.
4. **Make a Markdown file.** Create `notes.md`, add a top-level heading (`# My notes`) and
   a short sentence, and save. (You can open the Markdown preview from the Command Palette,
   `Ctrl+Shift+P`, if you'd like to check the formatting.)
5. **Edit and re-run.** Change `hello.py` to also print your name on a second line, save,
   and run it again — this write–save–run loop is the rhythm of the whole course.
