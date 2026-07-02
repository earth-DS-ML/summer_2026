# Python Fundamentals

This material is mostly adapted from the [official python tutorial](https://docs.python.org/3/tutorial/), Copyright 2001-2024, Python Software Foundation. It is used here under the terms of the [Python License](https://docs.python.org/3/license.html).

:::{admonition} Following along with a screen reader
:class: important
Work through the examples as `.py` scripts in VS Code — see [Setting up an accessible workflow](../computing_env/accessible_setup.md) and [Working with Python scripts](../computing_env/working_with_scripts.md). Run a script in the Git Bash terminal with `python yourfile.py`, and press `Ctrl+Up` to read the output.
:::

:::{admonition} In-class assignment — 10 points
:class: note
The **"Try it"** exercises on this page are part of your in-class assignment for this section. Complete them as `.py` scripts, push them to your week folder, and post the link on the matching **Courseworks** assignment. (One 10-point assignment covers all the lecture material in this section.)
:::

## Invoking Python

There are three main ways to use Python.

1. By running a Python file, e.g. `python myscript.py`
2. Through an interactive console (the Python interpreter or the iPython shell)
3. In an interactive notebook (e.g. Jupyter)

The standard version of this course mostly uses Jupyter notebooks. In this accessible version you'll use the first approach throughout: you'll write the code into a `.py` file and run it with `python myscript.py`, then read the result with `Ctrl+Up`.

:::{admonition} A note on how output works
:class: note
In a Jupyter notebook, the value of the last line of a cell is shown automatically, so the course material often relies on a bare expression like `1/2` to display a result. In a script that does **not** happen — nothing is shown unless you `print()` it. So as you copy these examples into `.py` files, **wrap the thing you want to see in `print(...)`**. For example, where the lecture writes `1/2` on its own line, write `print(1/2)`. Each "This prints:" block below shows what you'll hear after running the code and pressing `Ctrl+Up`.
:::

## Basic Variables: Numbers and Strings

In Python you create a variable by assigning a value to a name with `=`. Every variable has a **type** — `int`, `str`, `float`, `bool`, etc. — which determines what operations you can do with it. This section covers declaring variables, checking their types, and how Python handles operations between types.

The following is Python code:

```python
# comments are anything that comes after the "#" symbol
a = 1       # assign 1 to variable a
b = "hello" # assign "hello" to variable b
```

End of code.

> *Reference:* Python has a small set of [reserved words](https://docs.python.org/3/reference/lexical_analysis.html#keywords) (`if`, `def`, `for`, `class`, etc.) that can't be used as variable names, and a long list of [built-in functions](https://docs.python.org/3/library/functions.html) (`print`, `len`, `type`, `range`, etc.) that are always available. You don't need to memorize either list — just be aware they exist.

How do we see our variables? With `print()`:

The following is Python code:

```python
print(a)
print(b)
print(a, b)
```

End of code.

The following is the expected output:

```
1
hello
1 hello
```

End of output.

In Python, every piece of data is an **object** — numbers, strings, lists, even functions are all objects. Each object has a **type** (also called its **class**) that determines what you can do with it. You can add numbers, for example, but adding a number to a string raises an error, as we'll see below.

You can check the type of any value with the built-in `type()` function:

The following is Python code:

```python
print(type(a))
print(type(b))
```

End of code.

The following is the expected output:

```
<class 'int'>
<class 'str'>
```

End of output.

*Shortcut (notebooks only):* in a Jupyter notebook, the value of the last line of a code cell is displayed without `print()` — so the original lecture writes just `type(b)` to see the type. In a script you need `print(type(b))`, which prints:

The following is the expected output:

```
<class 'str'>
```

End of output.

You can also test whether an object is a particular type:

The following is Python code:

```python
print(type(a) is int)
print(type(a) is str)
```

End of code.

The following is the expected output:

```
True
False
```

End of output.

Every object has its own **attributes** (data) and **methods** (functions you can call on it), accessed with the `variable.method` syntax. In a Jupyter notebook you can press `<tab>` after a `.` to see what's available (tab completion). In VS Code you get the same thing: after you type `b.` the editor pops up a completion list, and with NVDA running you can arrow through the suggestions to hear them.

Note the difference between *referring to* a method and *calling* it. This refers to the method without running it:

The following is Python code:

```python
print(b.capitalize)
```

End of code.

Because no parentheses follow `capitalize`, this does **not** run the method — it prints a description of the method object itself, something like `<built-in method capitalize of str object at ...>` (the part after `at` is a memory address that changes every run, so don't expect a fixed number there).

Adding the parentheses actually **calls** the method:

The following is Python code:

```python
# this calls the method
print(b.capitalize())
# there are lots of other methods
```

End of code.

The following is the expected output:

```
Hello
```

End of output.

Binary operations act differently on different types of objects:

The following is Python code:

```python
c = 'World'
print(b + c)
print(a + 2)
```

End of code.

The following is the expected output:

```
helloWorld
3
```

End of output.

So `+` joins (concatenates) two strings, but adds two numbers. What happens if you mix the two? The original lecture's next line is `print(a + b)` — adding an `int` to a `str` — and it deliberately fails:

The following is Python code:

```python
print(a + b)
```

End of code.

This raises an error. The last line of the traceback reads:

The following is the expected output:

```
TypeError: unsupported operand type(s) for +: 'int' and 'str'
```

End of output.

Reading errors is a normal part of programming. Run the script, press `Ctrl+Up`, and read the **last line** of the traceback first — it names the type of error (`TypeError`) and explains the problem (you can't `+` an `int` and a `str`).

:::{admonition} Try it
:class: tip
Create a file `variables.py` in VS Code. In it, declare your own variables of a few different types — a number, a string, and a boolean — and print each one's type with `type()`, e.g. `print(type(my_number))`. Then add a line that does a binary operation mixing types, like `print("a" + 1)`, save, and run it with `python variables.py`. Press `Ctrl+Up` and read the error you get.
:::

## Math

Basic arithmetic and boolean logic is part of the core Python library. Remember to wrap each result in `print(...)` so you can hear it.

Addition and subtraction:

The following is Python code:

```python
print(1 + 1 - 5)
```

End of code.

The following is the expected output:

```
-3
```

End of output.

Multiplication:

The following is Python code:

```python
print(5 * 10)
```

End of code.

The following is the expected output:

```
50
```

End of output.

Division:

The following is Python code:

```python
print(1 / 2)
```

End of code.

The following is the expected output:

```
0.5
```

End of output.

That result was automatically converted to a float. We can confirm:

The following is Python code:

```python
print(type(1 / 2))
```

End of code.

The following is the expected output:

```
<class 'float'>
```

End of output.

Exponentiation uses `**`:

The following is Python code:

```python
print(2 ** 4)
```

End of code.

The following is the expected output:

```
16
```

End of output.

Rounding:

The following is Python code:

```python
print(round(9 / 10))
```

End of code.

The following is the expected output:

```
1
```

End of output.

Python even has built-in support for complex numbers (where `j` is the imaginary unit):

The following is Python code:

```python
print((1 + 2j) / (3 - 4j))
```

End of code.

The following is the expected output:

```
(-0.2+0.4j)
```

End of output.

Now boolean logic. The operators are the words `and`, `or`, and `not`:

The following is Python code:

```python
print(True and True)
print(True and False)
print(True or True)
print((not True) or (not False))
```

End of code.

The following is the expected output:

```
True
False
True
True
```

End of output.

:::{admonition} Try it
:class: tip
Create `math.py`. Try a few arithmetic operations of your own — addition, multiplication, exponentiation — each inside `print(...)`. Confirm with `type()` that `1/2` is a float but `1+1` is an int, e.g. `print(type(1/2))` and `print(type(1+1))`. Then add a boolean expression: `print((True and False) or True)`. Predict the result before you run it. (It prints `True`.)
:::

## Conditionals

Conditionals let your program make decisions: do *this* if a condition is true, otherwise do *that*. Python uses `if`, `elif` (else-if), and `else` to express these branches. **Indentation matters** — Python uses it to group statements into blocks (unlike languages that use braces). The body of each branch is indented (by convention, four spaces).

The following is Python code:

```python
x = 100
if x > 0:
    print('Positive Number')
elif x < 0:
    print('Negative Number')
else:
    print('Zero!')
```

End of code.

With `x = 100`, this prints:

The following is the expected output:

```
Positive Number
```

End of output.

Indentation is **mandatory**, and blocks are closed by returning to a lower indentation level. You can nest one `if` inside another by indenting further:

The following is Python code:

```python
# indentation is MANDATORY
# blocks are closed by indentation level
if x > 0:
    print('Positive Number')
    if x >= 100:
        print('Huge number!')
```

End of code.

With `x` still equal to 100, this prints:

The following is the expected output:

```
Positive Number
Huge number!
```

End of output.

:::{admonition} Try it
:class: tip
Create `conditionals.py`. Set a variable to a number, then write an `if` / `elif` / `else` block that prints `"small"`, `"medium"`, or `"large"` depending on the value. Save and run it. Then change the number, save, and run again — repeat until you've seen all three branches fire. (This is the write–save–run loop from the scripts page.)
:::

## More Flow Control

Beyond conditionals, the main way to repeat work in Python is with **loops**. A `while` loop runs as long as a condition is true; a `for` loop iterates over a sequence of items (a list, a range, the keys of a dict, etc.). The `range()` function generates a sequence of numbers that's useful with `for`.

A `while` loop:

The following is Python code:

```python
count = 0
while count < 10:
    count += 1   # shorthand for: count = count + 1
print(count)
```

End of code.

This loop runs until `count` reaches 10, so it prints:

The following is the expected output:

```
10
```

End of output.

A `for` loop over a range of numbers:

The following is Python code:

```python
for i in range(5):
    print(i)
```

End of code.

The following is the expected output:

```
0
1
2
3
4
```

End of output.

**Important point:** in Python, we always count from 0! Notice that `range(5)` gives the numbers 0, 1, 2, 3, 4 — five numbers starting at 0, and it stops *before* 5.

> *Reference:* In a Jupyter notebook you can type a name followed by `?` (e.g. `range?`) to see the built-in help for it. In a script, use the `help()` function instead: add a line like `help(range)`, run the script, and read the help text with `Ctrl+Up`. Works for any function, type, or variable.

A `for` loop can iterate over the items of a list directly. Here `len(pet)` gives the number of characters in each string:

The following is Python code:

```python
for pet in ['dog', 'cat', 'fish']:
    print(pet, len(pet))
```

End of code.

The following is the expected output:

```
dog 3
cat 3
fish 4
```

End of output.

:::{admonition} Try it
:class: tip
Create `loops.py`. Write a `for` loop that prints the squares of the numbers 0 through 9, one per line (hint: loop over `range(10)` and `print(n**2)`). Then, below it, write a `while` loop that prints the same squares. Save and run, and check with `Ctrl+Up` that both loops produce the same ten lines.
:::

## Lists

A **list** is an ordered, mutable collection of items, written with square brackets like `['dog', 'cat', 'fish']`. You can add to a list, remove from it, sort it, and access items by position. Lists are the workhorse data structure of Python — used everywhere from holding a handful of items to storing thousands of records.

The following is Python code:

```python
l = ['dog', 'cat', 'fish']
print(type(l))
```

End of code.

The following is the expected output:

```
<class 'list'>
```

End of output.

Lists have lots of methods. For example, `.sort()` sorts the list in place (it changes the list itself rather than returning a new one):

The following is Python code:

```python
l = ['dog', 'cat', 'fish']
l.sort()
print(l)
```

End of code.

This prints (alphabetical order):

The following is the expected output:

```
['cat', 'dog', 'fish']
```

End of output.

We can convert a range into an actual list with `list()`:

The following is Python code:

```python
r = list(range(5))
print(r)
```

End of code.

The following is the expected output:

```
[0, 1, 2, 3, 4]
```

End of output.

The `.pop()` method removes and returns the **last** item of a list. An empty list is treated as "false" in a condition, so `while r:` keeps looping until the list is empty:

The following is Python code:

```python
r = list(range(5))
while r:
    p = r.pop()
    print('p:', p)
    print('r:', r)
```

End of code.

The following is the expected output:

```
p: 4
r: [0, 1, 2, 3]
p: 3
r: [0, 1, 2]
p: 2
r: [0, 1]
p: 1
r: [0]
p: 0
r: []
```

End of output.

> *Reference:* Lists have many built-in methods — `append`, `extend`, `insert`, `remove`, `pop`, `clear`, `index`, `count`, `sort`, `reverse`, `copy`. See the [Python docs on lists](https://docs.python.org/3/tutorial/datastructures.html#more-on-lists) for the full reference. The ones you'll use most in this course are `append`, `sort`, and `pop`.

You can join two lists with `+`:

The following is Python code:

```python
x = list(range(5))
y = list(range(10, 15))
z = x + y
print(z)
```

End of code.

The following is the expected output:

```
[0, 1, 2, 3, 4, 10, 11, 12, 13, 14]
```

End of output.

You access items from a list by **index** in square brackets. Index `0` is the first item, `-1` is the last. A **slice** like `[:3]` takes a range of items:

The following is Python code:

```python
print('first', z[0])
print('last', z[-1])
print('first 3', z[:3])
print('last 3', z[-3:])
print('middle, skipping every other item', z[5:10:2])
```

End of code.

The following is the expected output:

```
first 0
last 14
first 3 [0, 1, 2]
last 3 [12, 13, 14]
middle, skipping every other item [10, 12, 14]
```

End of output.

In the last example, `z[5:10:2]` means "from index 5 up to (but not including) index 10, taking every 2nd item." The three numbers in a slice are *start*, *stop*, and *step*.

**MEMORIZE THIS SYNTAX!** It is central to so much of Python and often proves confusing for users coming from other languages.

In terms of set notation, Python indexing is *left inclusive*, *right exclusive*. If you remember this, you will never go wrong. (That is, `z[5:10]` includes index 5 but not index 10.)

Because of that, asking for an index equal to the length of the list is off the end, and Python raises an error:

The following is Python code:

```python
N = len(z)
print(z[N])
```

End of code.

This raises an error whose last line reads:

The following is the expected output:

```
IndexError: list index out of range
```

End of output.

(The list has 10 items at indices 0 through 9, so index 10 — which is `len(z)` — does not exist.)

This index notation also applies to strings — a string behaves like a sequence of characters:

The following is Python code:

```python
name = 'Ryan Abernathey'
print(name[:4])
```

End of code.

The following is the expected output:

```
Ryan
```

End of output.

You can also test for the presence of an item in a list with `in`:

The following is Python code:

```python
print(5 in z)
```

End of code.

At this point `z` is `[0, 1, 2, 3, 4, 10, 11, 12, 13, 14]`, which does not contain the number 5, so this prints:

The following is the expected output:

```
False
```

End of output.

Lists are general-purpose containers — they can hold mixed types and grow or shrink. For numerical math on many values at once, you'll use NumPy arrays (covered later).

Because lists are **mutable**, you can replace an item by assigning to its index. Here we put a string into a list that otherwise held numbers:

The following is Python code:

```python
z[4] = 'fish'
print(z)
```

End of code.

The following is the expected output:

```
[0, 1, 2, 3, 'fish', 10, 11, 12, 13, 14]
```

End of output.

Python is full of tricks for iterating and working with lists.

A **list comprehension** builds a new list in a single, readable line. Read it as "n squared, for each n in range 5":

The following is Python code:

```python
squares = [n**2 for n in range(5)]
print(squares)
```

End of code.

The following is the expected output:

```
[0, 1, 4, 9, 16]
```

End of output.

The `zip()` function lets you iterate over two (or more) lists together, pairing up their items:

The following is Python code:

```python
for item1, item2 in zip(x, y):
    print('first:', item1, 'second:', item2)
```

End of code.

The following is the expected output:

```
first: 0 second: 10
first: 1 second: 11
first: 2 second: 12
first: 3 second: 13
first: 4 second: 14
```

End of output.

:::{admonition} Try it
:class: tip
Create `lists.py`. Make a list of your own (any topic) and try a few methods — `.append(...)`, `.sort()`, and `len(...)` — printing the list after each change. Then try slicing: `print(mylist[:2])`, `print(mylist[-1])`, `print(mylist[::2])`. Predict each result before you run the script, then check with `Ctrl+Up`.
:::

## Other Data Structures

We have the basic building blocks for programming now. Python has two more data structures we'll meet often: tuples and dictionaries.

## Tuples

Tuples are similar to lists, but they are *immutable* — they can't be extended or modified once created. Their main use is to pack together a small group of related values (often of different types) that travel as a unit, so other parts of your code can unpack them.

Tuples are created with parentheses, or just commas:

The following is Python code:

```python
a = ('Ryan', 33, True)
b = 'Takaya', 25, False
print(type(b))
```

End of code.

Even without parentheses, the commas make `b` a tuple, so this prints:

The following is the expected output:

```
<class 'tuple'>
```

End of output.

They can be indexed like lists (remember, counting from 0, so index 1 is the *second* element):

The following is Python code:

```python
print(a[1])  # not the first element!
```

End of code.

The following is the expected output:

```
33
```

End of output.

And they can be **unpacked** — assigning a tuple to several names at once spreads its values across those names in order:

The following is Python code:

```python
name, age, status = a
print(name, age, status)
```

End of code.

The following is the expected output:

```
Ryan 33 True
```

End of output.

:::{admonition} Try it
:class: tip
Create `tuples.py`. Make your own tuple with a few different types (e.g., a name string, an age int, and a boolean). Unpack it into three named variables and print each. Then add a line that tries to modify it, such as `t[0] = "new"`, save, and run. Read the error with `Ctrl+Up` — because tuples are immutable, you'll get a `TypeError: 'tuple' object does not support item assignment`.
:::

## Dictionaries

This is an extremely useful data structure. It maps **keys** to **values**.

Dictionaries are unordered (you look items up by key, not by position).

There are different ways to create dictionaries — curly braces with `key: value` pairs, or the `dict()` function with keyword arguments:

The following is Python code:

```python
d = {'name': 'Ryan', 'age': 33}
e = dict(name='Takaya', age=25)
print(e)
```

End of code.

The following is the expected output:

```
{'name': 'Takaya', 'age': 25}
```

End of output.

You access a value by putting its key in square brackets:

The following is Python code:

```python
print(d['name'])
```

End of code.

The following is the expected output:

```
Ryan
```

End of output.

Square brackets `[...]` are Python for "get item" in many different contexts (lists, strings, dicts, and more).

You can test for the presence of a key with `in`:

The following is Python code:

```python
print('age' in d)
print('height' in e)
```

End of code.

The following is the expected output:

```
True
False
```

End of output.

If you try to access a key that doesn't exist, Python raises an error:

The following is Python code:

```python
print(d['height'])
```

End of code.

This raises an error whose last line reads:

The following is the expected output:

```
KeyError: 'height'
```

End of output.

You add a new key simply by assigning to it. Here the value is a tuple:

The following is Python code:

```python
d['height'] = (5, 11)  # a tuple
print(d)
```

End of code.

The following is the expected output:

```
{'name': 'Ryan', 'age': 33, 'height': (5, 11)}
```

End of output.

Keys don't have to be strings — here we use a number as a key:

The following is Python code:

```python
d[99] = 'ninety nine'
print(d)
```

End of code.

The following is the expected output:

```
{'name': 'Ryan', 'age': 33, 'height': (5, 11), 99: 'ninety nine'}
```

End of output.

Iterating over a dictionary gives you its keys, which you can then use to look up each value:

The following is Python code:

```python
for k in d:
    print(k, d[k])
```

End of code.

The following is the expected output:

```
name Ryan
age 33
height (5, 11)
99 ninety nine
```

End of output.

A nicer way is to iterate over keys and values at once with `.items()`:

The following is Python code:

```python
for key, val in d.items():
    print(key, val)
```

End of code.

This prints the same thing:

The following is the expected output:

```
name Ryan
age 33
height (5, 11)
99 ninety nine
```

End of output.

:::{admonition} Try it
:class: tip
Create `dicts.py`. Build a dict mapping a few country names to their capitals. Look up one capital and print it. Then iterate through the dict with `.items()` and print each `key: value` pair. Finally add a line that looks up a country that's *not* in the dict, save, and run — read the `KeyError` with `Ctrl+Up`.
:::
