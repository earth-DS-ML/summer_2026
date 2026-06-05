# Python Functions and Classes

:::{admonition} Following along with a screen reader
:class: important
Work through the examples as `.py` scripts in VS Code — see [Setting up an accessible workflow](../computing_env/accessible_setup.md) and [Working with Python scripts](../computing_env/working_with_scripts.md). Run a script in the Git Bash terminal with `python yourfile.py`, and press `Alt+F2` to read the output.
:::

For longer and more complex tasks, it is important to organize your code into reusable elements. For example, if you find yourself cutting and pasting the same or similar lines of code over and over, you probably need to define a _function_ to encapsulate that code and make it reusable. An important principle in programming is **DRY**: "don't repeat yourself". Repetition is tedious and opens you up to errors. Strive for elegance and simplicity in your programs.

:::{admonition} Seeing a result in a script vs. a notebook
:class: note
The standard version of this lesson is a notebook, where typing an expression on its own line (for example `say_hello()`) automatically displays its value. A `.py` script does **not** do that — it only shows what you explicitly `print(...)`. So throughout this page, wherever the original notebook would just show a value, we wrap it in `print(...)` so the result actually appears in your terminal. Build each example into a `.py` file, run it with `python yourfile.py`, and press `Alt+F2` to read the output.
:::

## Functions

A **function** is a named block of code that takes some inputs (called **arguments**), does something, and usually gives back a result (called the **return value**). Once you've defined a function, you can call it as many times as you like with different inputs, instead of copy-pasting the same code (the DRY principle from above).

Defining a function uses the `def` keyword; calling it uses the function name followed by parentheses with the arguments inside.

```python
# define a function
def say_hello():
    """Return the word hello."""
    return 'Hello'
```

Functions are themselves **objects** in Python, just like numbers, strings, and lists. You can check the type of the function object:

```python
print(type(say_hello))
```

This prints:

```
<class 'function'>
```

Notice we wrote `say_hello` here **without** parentheses. The bare name refers to the function object itself; it does **not** run the function. To actually *call* the function — to run its code and get the return value — you add parentheses:

```python
print(say_hello())
```

This prints:

```
Hello
```

In a notebook you can also type `say_hello?` to read a function's documentation (its docstring). In a script, the equivalent is the built-in `help` function:

```python
help(say_hello)
```

This prints a short help page that includes the docstring, `Return the word hello.`

You can assign the result of a call to a variable, just like any other value:

```python
# assign the result to something
res = say_hello()
print(res)
```

This prints:

```
Hello
```

Functions get more useful when they take **arguments** — values passed in that the function can use:

```python
# take some arguments
def say_hello_to(name):
    """Return a greeting to `name`"""
    return 'Hello ' + name
```

Calling it with a string works as intended:

```python
print(say_hello_to('World'))
```

This prints:

```
Hello World
```

If we call this with a non-string argument like an integer, Python can't concatenate it to `'Hello '` and raises a `TypeError`:

```python
print(say_hello_to(10))
```

This raises an error whose last line reads:

```
TypeError: can only concatenate str (not "int") to str
```

We can make the function more robust by redefining it to convert the argument to a string first:

```python
# redefine the function
def say_hello_to(name):
    """Return a greeting to `name`"""
    return 'Hello ' + str(name)

print(say_hello_to(10))
```

This prints:

```
Hello 10
```

Wrapping the argument in `str(...)` converts whatever the caller passes into a string before concatenation, so the function now works regardless of the input type.

### Keyword arguments and default values

Arguments can have **default values**, making them **keyword arguments**. The caller can leave them out (and get the default) or specify them by name:

```python
# take an optional keyword argument
def say_hello_or_hola(name, spanish=False):
    """Say hello in multiple languages."""
    if spanish:
        greeting = 'Hola '
    else:
        greeting = 'Hello '
    return greeting + name

print(say_hello_or_hola('Ryan'))
print(say_hello_or_hola('Juan', spanish=True))
```

This prints:

```
Hello Ryan
Hola Juan
```

The first call leaves `spanish` out, so it uses the default `False` and greets in English. The second call passes `spanish=True` by name, switching the greeting to Spanish.

### A flexible number of arguments

A function can also accept a flexible number of arguments using `*args` — the leading `*` tells Python to collect any extra positional arguments into a tuple:

```python
# flexible number of arguments
def say_hello_to_everyone(*args):
    return ['hello ' + str(a) for a in args]

print(say_hello_to_everyone('Ryan', 'Juan', 'Xiaomeng'))
```

This prints:

```
['hello Ryan', 'hello Juan', 'hello Xiaomeng']
```

:::{admonition} Try it
:class: tip
Create a file (for example `functions_practice.py`). Write a function `square(x)` that returns `x` squared, and call it with a few numbers, printing each result. Then extend it: define a function `power(x, n=2)` with `n` as an optional keyword argument so the user can compute other powers. Add `print(power(3))` and `print(power(3, n=3))`, save, and run it with `python functions_practice.py`. Press `Alt+F2` and verify that `power(3)` gives `9` and `power(3, n=3)` gives `27`.
:::

### Pure vs. Impure Functions

Functions that don't modify their arguments or produce any other side-effects are called [_pure_](https://en.wikipedia.org/wiki/Pure_function).

Functions that modify their arguments or cause other actions to occur are called _impure_.

Below is an impure function:

```python
def remove_last_from_list(input_list):
    input_list.pop()
```

Watch what happens when we call it twice on the same list:

```python
names = ['Ryan', 'Juan', 'Xiaomeng']
remove_last_from_list(names)
print(names)
remove_last_from_list(names)
print(names)
```

This prints:

```
['Ryan', 'Juan']
['Ryan']
```

Notice what happened: each call to `remove_last_from_list(names)` modified the **same `names` list outside the function**. After the first call it dropped to `['Ryan', 'Juan']`; after the second, to `['Ryan']`. The function had a **side effect** — calling it silently changed the input. That's what makes it impure.

Here's a **pure** version that does the same conceptual job (remove the last item) without modifying the original. It makes a copy of the input, modifies the copy, and returns it:

```python
def remove_last_from_list_pure(input_list):
    new_list = input_list.copy()
    new_list.pop()
    return new_list
```

Calling the pure version:

```python
names = ['Ryan', 'Juan', 'Xiaomeng']
new_names = remove_last_from_list_pure(names)
print(names)
print(new_names)
```

This prints:

```
['Ryan', 'Juan', 'Xiaomeng']
['Ryan', 'Juan']
```

Notice the difference: this time `names` is **unchanged** after the call — the function returned a new list (`new_names`) instead of mutating the input. Pure functions are usually safer and easier to reason about, because the inputs you pass in won't be silently altered somewhere down the line.

### Namespaces and scope

When you create a variable inside a function, it's **local** to that function — it doesn't affect variables of the same name outside. Functions can *read* variables from the outer (parent) scope, but assigning to a name inside a function creates a fresh local variable that shadows any outer one. This is called **scoping**, and it's a common source of confusion for new programmers.

The example below illustrates: `print_name_v2` changes `name` locally without affecting the outer `name`.

```python
name = 'Ryan'

def print_name():
    print(name)

def print_name_v2():
    name = 'Kerry'
    print(name)

print_name()
print_name_v2()
print(name)
```

This prints:

```
Ryan
Kerry
Ryan
```

Reading the three lines: `print_name()` reads the outer `name` and prints `Ryan`. `print_name_v2()` creates its own local `name` set to `Kerry` and prints that. The final `print(name)` shows the outer `name` is still `Ryan` — the assignment inside `print_name_v2` never touched it.

### A more complex function: Fibonacci Sequence

The [Fibonacci sequence](https://en.wikipedia.org/wiki/Fibonacci_sequence) is `1, 1, 2, 3, 5, 8, 13, …` — each number is the sum of the previous two. Here's a function that returns the first `n` Fibonacci numbers as a list. It uses list methods (`append`) and negative indexing (`[-1]`, `[-2]`) to build the sequence step by step.

```python
def fib(n):
    l = [1, 1]
    for i in range(n - 2):
        l.append(l[-1] + l[-2])
    return l

print(fib(10))
```

This prints:

```
[1, 1, 2, 3, 5, 8, 13, 21, 34, 55]
```

The function starts with the first two numbers, then loops `n - 2` more times, each time appending the sum of the last two items (`l[-1]` is the last item, `l[-2]` is the second-to-last).

:::{admonition} Try it
:class: tip
Create a file (for example `temperature.py`). Write a function that takes a temperature in Celsius and returns it in Fahrenheit (formula: `F = C * 9/5 + 32`). Then add an optional keyword argument so the user can choose Kelvin output instead (`K = C + 273.15`). Call your function with a few different inputs, printing each result. Save, run with `python temperature.py`, and read the output with `Alt+F2`.

(This problem will come back in Assignment 3.)
:::

## Classes

You've worked with many built-in object types — strings, lists, dictionaries, and later NumPy arrays and pandas DataFrames. Each has its own attributes and methods.

A **class** is a *template* for creating your own kind of object — like a custom version of `list` or `str`, tailored to whatever you're modeling. The class definition specifies what data each object carries (its **attributes**) and what it can do (its **methods**). Each object created from the class is called an **instance**; you can create as many instances as you want, each with its own values.

You might not write many classes early on, but you'll see them in almost every codebase you read, and as your projects grow you'll occasionally want to write your own to bundle related data and behavior together. The example below walks through a small `Hurricane` class so you can follow what's going on when you meet one in the wild.

### A class to represent a hurricane

Three things to notice in the code below:

- The `class Hurricane:` line **defines the class**. Everything indented under it is part of the class.
- `__init__` is a **special method** that runs automatically every time you make a new `Hurricane` — for example, when you write `h = Hurricane('florence')` in the next snippet. The arguments after `self` (here, just `name`) are the values you put inside those parentheses.
- `self` refers to the new object being built. The line `self.name = name` says: *store the `name` you passed in as an attribute on this object*, so later you can read it back as `h.name`.

```python
class Hurricane:

    def __init__(self, name):
        self.name = name
```

Now create an instance and print it:

```python
h = Hurricane('florence')
print(h)
```

This prints something like:

```
<__main__.Hurricane object at 0x102996d20>
```

Notice that printing `h` just shows the class name and a memory address (the long hexadecimal number after `at` will differ each time you run it) — not very useful. We'll fix this later by adding a `__repr__` method.

Our class only has a single attribute so far. We can read it back with `h.name`:

```python
print(h.name)
```

This prints:

```
florence
```

Let's grow the class. We'll add two more attributes (`category` and `lon`), uppercase the `name` as it's stored, and add **input validation** — a check that raises a `ValueError` if the longitude is outside the valid range.

```python
class Hurricane:

    def __init__(self, name, category, lon):
        self.name = name.upper()
        self.category = int(category)

        if lon > 180 or lon < -180:
            raise ValueError(f'Invalid lon {lon}')
        self.lon = lon
```

Create a valid instance and read its name back:

```python
h = Hurricane('florence', 4, -46)
print(h.name)
```

This prints:

```
FLORENCE
```

Notice the name came back as `'FLORENCE'`, not `'florence'` — that's because `__init__` ran `name.upper()` and stored the uppercased version as the attribute.

Now try creating one with an out-of-range longitude:

```python
h = Hurricane('ryan', 5, 300)
```

This raises an error whose last line reads:

```
ValueError: Invalid lon 300
```

The validation check inside `__init__` (`if lon > 180 or lon < -180: raise ValueError(...)`) caught the bad longitude and stopped construction with a `ValueError`. The instance was never created.

### Adding a method

Now let's add a custom method. A **method** is just a function defined inside a class; its first argument is always `self`, the instance it's called on.

```python
class Hurricane:

    def __init__(self, name, category, lon):
        self.name = name.upper()
        self.category = int(category)

        if lon > 180 or lon < -180:
            raise ValueError(f'Invalid lon {lon}')
        self.lon = lon

    def is_dangerous(self):
        return self.category > 1
```

Create an instance and call the method:

```python
f = Hurricane('florence', 4, -46)
print(f.is_dangerous())
```

This prints:

```
True
```

`f` was created with `category=4`, so `self.category > 1` is `True` and the method returns `True`.

### Magic / dunder methods

Methods whose names begin and end with double underscores (`__init__`, `__repr__`, etc.) — pronounced "dunder" for *double-underscore* — are special: Python calls them automatically in particular situations. We've already met `__init__`, which Python calls when you create an instance. Another useful one is `__repr__`, which Python calls when it needs to display the object (for example, when you `print` it). By default `__repr__` returns the ugly memory address we saw earlier; defining your own gives a nicer display.

```python
class Hurricane:

    def __init__(self, name, category, lon):
        self.name = name.upper()
        self.category = int(category)

        if lon > 180 or lon < -180:
            raise ValueError(f'Invalid lon {lon}')
        self.lon = lon

    def __repr__(self):
        return f"<Hurricane {self.name} (cat {self.category})>"

    def is_dangerous(self):
        return self.category > 1
```

Now print an instance:

```python
f = Hurricane('florence', 4, -46)
print(f)
```

This prints:

```
<Hurricane FLORENCE (cat 4)>
```

Now printing `f` shows the formatted string from our `__repr__` instead of the memory-address gibberish we saw earlier. That's the point of `__repr__`.

:::{admonition} Try it
:class: tip
Create a file (for example `my_class.py`) and define a minimal class of your own — pick a domain (e.g. a `Station` for a weather station, or a `Reading` for a measurement). Give it an `__init__` that takes 2–3 arguments and stores them as attributes. Create an instance, then `print` each of its attributes. Save, run with `python my_class.py`, and read the output with `Alt+F2`. The goal is to build enough familiarity that you can read class definitions confidently when you encounter them.
:::
