# Reading Python Out Loud

:::{admonition} What this page is for
:class: important
Code is full of punctuation — `=`, `:`, `()`, `_`, `*` — that a screen reader either reads
with an unfamiliar name or, at its default settings, skips entirely. This page is a
**reference**: it names the symbols you'll meet, says what each one *means* in Python, and
shows how Python code is normally written so it reads as smoothly as possible. Skim it once
before the coding lessons, then come back whenever a line of code sounds like noise.

It assumes [NVDA](https://www.nvaccess.org/) on Windows, but the ideas carry over to other
screen readers.
:::

## A note on symbol verbosity

Most screen readers have a setting for **how much punctuation they speak**. At the default
level, many symbols are silent — fine for prose, but in code every symbol matters, so you
may want to turn it up while reading or writing code.

In NVDA, **`NVDA+P`** cycles the *symbol level* between **None → Some → Most → All**. Setting
it to **Most** or **All** while you work in code means you'll actually hear the colons,
underscores, and asterisks that change what a line does.

:::{admonition} Please confirm what you hear
:class: note
The symbol names below (for example, reading `_` as "underscore" or `#` as "number") are
NVDA's common defaults, but the exact wording can depend on your NVDA version and settings.
If something speaks differently for you, trust your setup — and let us know, so we can fix
this page.
:::

## Indentation is part of the code

Before the symbols, the most important "invisible" thing: **Python uses indentation —
leading spaces at the start of a line — to group code into blocks.** The body of an `if`, a
`for` loop, or a function is indented underneath it. Two lines with the same indentation
belong to the same block; a change in indentation starts or ends a block. Getting it wrong
is a real error (`IndentationError`), not just a style issue.

Because indentation is whitespace, it's easy to miss by ear. Two things help:

- **VS Code announces indentation** as you move through a file, and the editor keeps it
  consistent for you (press `Tab` to indent; it inserts spaces).
- **NVDA can report indentation** — there's a setting for whether it speaks the number of
  spaces, plays a tone, or stays silent. If you can't tell how deeply a line is indented,
  turning this on makes the block structure audible.

The convention this course uses is **4 spaces per level** of indentation.

## Symbols you'll meet, and what they mean

These are grouped by what they do. Each entry gives the symbol, the name you'll most likely
hear, and its job in Python.

### Assignment and comparison

- **`=` (equals)** — *assignment*: give a name a value. `temp = 21.5` means "let `temp` be
  21.5". It does **not** mean "is equal to".
- **`==` (equals equals)** — *comparison*: asks whether two things are equal, and answers
  `True` or `False`. The doubling is what distinguishes "are these equal?" (`==`) from
  "make this equal to" (`=`).
- **`!=` (exclamation equals)** — "not equal to".
- **`<`, `>`, `<=`, `>=`** — less than, greater than, less than or equal to, greater than or
  equal to.

### Grouping and containers

- **`( )` (parentheses / "round brackets")** — calling a function — `print(x)` — and
  grouping math — `(a + b) * c`. A pair with commas inside also makes a *tuple*.
- **`[ ]` (square brackets)** — a *list*, like `[10, 20, 30]`, and *indexing* into one,
  like `readings[0]` for its first item.
- **`{ }` (curly braces)** — a *dictionary* of key–value pairs, like `{"unit": "C"}` (and
  *sets*).

### Punctuation inside code

- **`:` (colon)** — ends the line that *opens a block* (after `if`, `for`, `while`, `def`,
  `class`), with the block's body indented below it. It also appears in *slices*
  (`readings[1:3]`) and between a dictionary's keys and values.
- **`,` (comma)** — separates items: arguments to a function, elements of a list.
- **`.` (dot)** — reaches *inside* something to a piece that belongs to it:
  `df.mean()` is the `mean` method of `df`; `np.pi` is `pi` inside `np`.
- **`#` (number sign / "hash")** — starts a *comment*: everything after it on the line is a
  note for humans and is ignored by Python.

### Strings and quotes

- **`'` and `"` (single / double quote)** — wrap *text* (a "string"): `"degrees C"`. The two
  are interchangeable; just match the one you open with.
- **`"""` (triple quote)** — wraps text that spans *several lines*, and is also how a
  function's documentation (its "docstring") is written.

### Math

- **`+`, `-`, `*`, `/` (plus, minus, star/asterisk, slash)** — add, subtract, multiply,
  divide.
- **`//` (slash slash)** — *floor division*: divide and throw away the remainder, so
  `7 // 2` is `3`.
- **`%` (percent)** — *modulo*: the remainder after division, so `7 % 2` is `1`.
- **`**` (star star)** — *exponent*: `2 ** 3` is 2 to the power 3, which is `8`. (Watch for
  this one — two stars is "to the power of", not "times times".)

### Names and the underscore

- **`_` (underscore)** — the main way to put a gap in a name, since names can't contain
  spaces: `sea_surface_temp`. You'll hear it read literally as "underscore" between words.
- **`__` (underscore underscore, "dunder")** — a *double* underscore. Python uses it for
  special names like `__init__` (spoken "dunder init"). You won't write many, but you'll see
  them.

### Logic

- **`and`, `or`, `not`** — these are *words*, not symbols — easy to read, and the usual way
  to combine conditions.
- **`&`, `|` (ampersand, vertical bar / "pipe")** — element-by-element "and"/"or", which
  you'll meet later when filtering arrays and tables.

## How Python code is normally written (naming conventions)

Beyond individual symbols, Python has shared habits for *naming* things. Following them
makes code predictable — and, read aloud, far easier to follow.

- **`snake_case`** — variables and functions: lowercase words joined by underscores, like
  `sea_surface_temp` or `load_data`. This is the most common style you'll write.
- **`CapWords`** (also called PascalCase) — classes: each word capitalized, no underscores,
  like `DataFrame`.
- **`UPPER_CASE`** — constants, values meant never to change, like `EARTH_RADIUS`.
- **A single leading underscore** (`_helper`) signals "internal — not meant to be used from
  outside". A name that is *just* `_` is a common way to say "I have to name this, but I'm
  ignoring it".

A small, screen-reader-specific payoff: consistent names are easier to *hear*. Because a
screen reader speaks every underscore, a tidy `snake_case` name reads as a predictable
rhythm of words, whereas a jumbled name (`SeaSurfacetemp_2`) is a mouthful that's hard to
hold in your head.

## A few lines, read closely

Putting it together. **This code:**

**Code:**

```python
sea_surface_temp = 21.5
print(type(sea_surface_temp))
```

**End of code.**

reads as "sea underscore surface underscore temp **equals** twenty-one point five", then
"print, **open paren**, type, **open paren**, sea underscore surface underscore temp, **close
paren**, **close paren**". **This prints:**

```
<class 'float'>
```

**End of output.**

— telling you the value is a `float` (a decimal number).

**This code** indexes a list:

**Code:**

```python
readings = [10, 20, 30]
print(readings[0])
```

**End of code.**

"readings **equals open square bracket** ten **comma** twenty **comma** thirty **close square
bracket**", then we ask for `readings[0]` — item **zero**, because Python counts from 0.
**This prints:**

```
10
```

**End of output.**

## Using this page

You don't need to memorize this. Keep it open in a tab and jump back to it the first few
times a symbol trips you up — within a week or two the names become second nature. When you
reach a symbol that isn't here, that's worth telling us about, so we can add it.
