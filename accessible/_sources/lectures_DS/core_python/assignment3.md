# Assignment 3: Core Python

**At-home assignment — worth 10 points.** When you're done, push your work to your week folder and post a link to that **folder** on the matching Courseworks assignment.

:::{admonition} Doing this assignment with a screen reader
:class: important
Write your solutions as `.py` scripts in VS Code — see [Setting up an accessible workflow](../computing_env/accessible_setup.md) and [Working with Python scripts](../computing_env/working_with_scripts.md). Run a script in the Git Bash terminal with `python yourfile.py`, and press `Ctrl+Up` to read the output.
:::

:::{admonition} Learning goals
:class: tip
This assignment exercises the core Python skills from this section:

- Create and index lists and dictionaries
- Iterate through lists, tuples, and dictionaries
- Define functions with positional and keyword arguments
:::

:::{admonition} Working through this assignment
:class: tip
The standard version of this assignment is a Jupyter notebook with an empty code cell under each numbered task. In the accessible workflow you'll do the same tasks in a Python **script** instead.

Create a script called `assignment3.py` in VS Code. For each numbered task, write the code that solves it, and use `print(...)` to show the result so you can hear it. It helps to label each part with a comment, for example `# Part I, task 1`. As you work, save with `Ctrl+S`, run with `python assignment3.py` in the Git Bash terminal, and press `Ctrl+Up` to read the output.

When you're done, follow the **Submission instructions** section at the bottom of the page (you can jump to it by heading).
:::

## Part I: Lists and Loops

In this part you'll exercise lists and flow control by manually creating data structures.

:::{warning}
Pluto is [not a planet anymore](https://www.loc.gov/everyday-mysteries/item/why-is-pluto-no-longer-a-planet/).
:::

### 1) Create a list with the names of every planet in the solar system (in order)

### 2) Have Python tell you how many planets there are by examining your list

### 3) Use slicing to display the first four planets (the rocky planets)

### 4) Iterate through your planets and print the planet name only if it has an `s` at the end

## Part II: Dictionaries

### 1) Now create a dictionary that maps each planet name to its mass

You can use values from this [NASA planetary fact sheet](https://nssdc.gsfc.nasa.gov/planetary/factsheet/). You can use whatever units you want, but be consistent.

### 2) Use your dictionary to look up Earth's mass

### 3) Loop through the data and create a list of planets whose mass is greater than 100 × 10²⁴ kg

That mass threshold is one hundred times ten to the twenty-fourth kilograms:

$$100 \times 10^{24}\ \text{kg}$$

Display your result.

### 4) Now add Pluto to your dictionary

## Part III: Functions

### 1) Write a function to convert temperature from Kelvin to Celsius

…and another to convert from Celsius to Kelvin.

### 2) Write a function to convert temperature to Fahrenheit

Include an optional keyword argument to specify whether the input is in Celsius or Kelvin. Call your previously defined functions if necessary.

### 3) Check that the outputs are sensible

…by trying a few examples.

### 4) Now write a function that converts _from_ Fahrenheit

Use a keyword argument to specify whether you want the output in Celsius or Kelvin.

### 5) Write a function that takes two arguments (feet and inches) and returns height in meters

Verify it gives sensible answers.

### 6) Write a function that takes one argument (height in meters) and returns two values (feet and inches)

(Consult the [tutorial on numbers](https://docs.python.org/3/tutorial/introduction.html#numbers) if you're stuck on how to implement this.)

### 7) Verify that the "round trip" conversion from and back to meters is consistent

Check for 3 different values of height in meters.

## Submission instructions

When you're done, save your completed script as `assignment3.py` inside a new `week3/` folder in your private `clmt5405-assignments` GitHub repo (the one you set up in Assignment 1). Then push the commit from the Git Bash terminal:

The following is Python code:

~~~bash
git add week3/assignment3.py
git commit -m "Submit assignment 3"
git push
~~~

End of code.

Due Sunday night.
