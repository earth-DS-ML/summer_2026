# Setting up an accessible workflow

*This page is written for students using a screen reader (it assumes
[NVDA](https://www.nvaccess.org/) on Windows, but the ideas apply to other screen
readers too). It replaces the in-browser terminal and notebook interface used elsewhere
in the course with a setup that a screen reader can actually read.*

## Why a different setup?

The course's default environment opens a **terminal and notebooks inside a web browser**
(JupyterLab on the LEAP hub, or Google Colab). Those in-browser tools are highly visual,
and large parts of them — especially the terminal — are not reliably read by a screen
reader.

The good news: you do **not** have to learn Python through that interface. A text-based
workflow built around **[Visual Studio Code (VS Code)](https://code.visualstudio.com/)**
is far more screen-reader-friendly, and it runs the exact same commands and Python the
course uses. This page sets that up once; the rest of the chapter then works inside it.

A quick note on the plan, so it isn't a surprise later:

- **For now** (this chapter and the first Python chapters), everything runs **on your own
  computer**. You don't need the cloud at all.
- **Later** (the data chapters, which use large cloud datasets), you connect this *same*
  VS Code to the course's LEAP hub. The interface stays identical; only the data and
  computing move to the cloud. That step is described at the end of this page.

## What to install

On your Windows machine, install these. A sighted helper or the course assistant can help
with this one-time install; after that, you work on your own.

1. **NVDA** — the screen reader, if it isn't already installed: <https://www.nvaccess.org/>
2. **Python** — from <https://www.python.org/downloads/>. During installation, check the
   box labelled **"Add python.exe to PATH"**.
3. **Visual Studio Code** — from <https://code.visualstudio.com/>.
4. **The Python extension for VS Code** — open VS Code, open the Extensions view
   (`Ctrl+Shift+X`), search for "Python" by Microsoft, and install it.
5. **Git** — from <https://git-scm.com/download/win>. You'll need this for the *Intro to
   Git* lesson; it also installs **Git Bash**, the terminal you'll set up just below.

(Later in the course you'll also install scientific packages such as NumPy and Pandas, or
just use the LEAP hub, which already has them. You don't need those yet.)

## Turning on VS Code's accessibility features

VS Code has strong, built-in support for screen readers. Turn it on **once**, explicitly, so
it stays on every time VS Code opens:

1. Press `Ctrl+Shift+P` to open the Command Palette.
2. Type **Open User Settings (JSON)** and press `Enter`.
3. Inside the curly braces, add this line: `"editor.accessibilitySupport": "on"`
4. Press `Ctrl+S` to save.

After this, the status bar shows **"Screen Reader Optimized"** every time VS Code opens.

:::{admonition} A note on these shortcuts
:class: note
The shortcuts on this page were tested on **Windows with NVDA** by the course assistant. A
few are still being confirmed and are marked *(being confirmed)* — if one doesn't behave as
described on your machine, that's useful to tell us. Also, keys written as `Fn+...` assume a
keyboard where the function keys need `Fn`; if yours sends `F1`–`F12` directly, drop the
`Fn`.
:::

Two commands are worth memorizing first:

- **Accessibility Help — `Fn+Alt+F1`.** Press this in any view to hear a summary of what you
  can do there and the relevant shortcuts. When in doubt, press it — it's the "what can I do
  here?" button.
- **Accessible View — `Alt+F2`.** Opens the current editor content — your Python script,
  error messages, hovers — as plain text you can read line by line. (The *terminal* has its
  own keys for this, covered in the terminal section below.)

**Audio cues** are normally on automatically with a screen reader attached (for example, a
sound when a line has an error); run **"Help: List Signal Sounds"** from the Command Palette
to hear the full list.

### Use Caps Lock as the NVDA key (recommended)

Several reading shortcuts below use the **NVDA modifier key**. By default that's `Insert`,
but `Caps Lock` is easier to reach, so it helps to enable it as a second NVDA key:

1. With NVDA running, press `Insert+N` to open the NVDA menu.
2. Go to **Preferences → Settings → Keyboard**.
3. Under **NVDA Modifier Keys**, check the box for **Caps Lock**.
4. Select **OK**.

Now both `Insert` and `Caps Lock` act as the NVDA key. The terminal-reading steps below
assume this.

> *NVDA tip:* VS Code's own guidance is to **stay in focus mode and use the hotkeys**,
> rather than switching NVDA to browse mode.

## The terminal — the key fix

This is the part that did not work in the browser. In VS Code it does — but the
screen-reader steps take a little one-time setup, so this section spells them out in detail.

### Make your terminal use Git Bash

On Windows, VS Code's terminal defaults to **PowerShell**, which uses different commands
from the Unix ones this course teaches (for example, `touch` and `ls -F` don't work in
PowerShell). Git for Windows — which you installed above — includes **Git Bash**, a
terminal that *does* understand them. Set it as your default once:

1. Open the Command Palette: `Ctrl+Shift+P`.
2. Type **Terminal: Select Default Profile** and press Enter.
3. Choose **Git Bash** from the list.

Now every terminal you open is a Git Bash terminal, and the commands in the lessons work
exactly as written. *(When you later connect to the LEAP hub, its terminal is Linux, where
these same commands also work — so nothing you learn here goes to waste.)*

### Open a terminal

1. Open a terminal: the **Terminal** menu → **New Terminal**, or press `` Ctrl+` ``. A
   terminal opens at the bottom of the window, and keyboard focus moves into it — so what
   you type goes straight to the terminal.
2. Type a command and press Enter as usual — for example, `pwd`. The result is printed on
   the next line.

### Read the output with the terminal's Accessible View

A sighted user just looks at the output. With a screen reader, you read it through the
terminal's **Accessible View**, which turns its contents into plain text you can move through
one line at a time. These keys were tested with NVDA on Windows:

1. Make sure focus is in the terminal. If you're not sure, press `` Ctrl+` `` to jump back
   into it.
2. Press **`Ctrl+Up`** to open the terminal's Accessible View.
3. Move through it with **`Caps Lock+Up`** and **`Caps Lock+Down`** — your screen reader
   speaks one line at a time, so you can go back up to re-read earlier output. (`Caps Lock`
   is the NVDA key you enabled above.)
4. Press **`Ctrl+Down`** to close the Accessible View and return to the terminal.

:::{admonition} If you forget these keys
:class: tip
Any time focus is in the terminal, press **`Fn+Alt+F1`** (Accessibility Help). VS Code reads
out the keys that work where you are — it's the "what can I do here?" button.
:::

### If you can't re-read earlier commands: turn on shell integration

Jumping *between* your previous commands in the Accessible View — pressing `Ctrl+Shift+O`
there to move from one command's output to another — relies on a VS Code feature called
**shell integration**. It usually turns on by itself, but with Git Bash it sometimes
doesn't, and then re-reading past output can fail. If that happens, switch it on by hand.
This is a **one-time** step, and a sighted helper or the course assistant can do it with you
once:

1. In the terminal, run this single command. It adds shell integration to Git Bash's
   startup file:

The following is a terminal command:

   ~~~
   echo '[[ "$TERM_PROGRAM" == "vscode" ]] && . "$(code --locate-shell-integration-path bash)"' >> ~/.bashrc
   ~~~

End of command.

2. Close the terminal (type `exit` and press Enter), then open a fresh one with `` Ctrl+` ``.

Now `Ctrl+Shift+O` inside the Accessible View lists your recent commands, so you can jump
straight to any one's output. *(To confirm it's active, a sighted helper can hover the mouse
over the terminal tab: VS Code shows the shell-integration quality as "Rich", "Basic", or
"None" — "None" means it isn't on yet.)*

Everything the course shows you "in a JupyterLab terminal" — `pwd`, `ls`, `cd`, and the
rest of the next lesson — you do **here**, in this Git Bash terminal.

## Running Python

Two screen-reader-friendly ways, in order of preference:

1. **Write a script and run it.** Make a file `lesson1.py`, type your code, save
   (`Ctrl+S`), then in the terminal run `python lesson1.py`. The code is linear, NVDA
   reads it line by line, and the output appears in the terminal (read it with `Ctrl+Up`).
   This is the main way to work.
2. **Run cells inside a script (optional, needs extra setup).** If you want to run code
   piece by piece, put `# %%` on its own line to mark a "cell" in a `.py` file; VS Code
   can then run each cell and show the result in a panel (read it with `Alt+F2`). This
   needs the **Jupyter** extension (install it from the Extensions view, like the Python
   one) and the Jupyter package (`pip install jupyter`). You don't need any of this to
   start — running whole scripts is enough.

For now, avoid doing your main work *inside* `.ipynb` notebook cells — that's the
cell-based, visual interface that is hardest to navigate.

## Later: connecting to the LEAP hub

When the course reaches the data chapters (which use large datasets stored in the cloud),
you'll connect this same VS Code to the **LEAP JupyterHub**, so you keep the accessible
interface but gain the hub's data and computing. The steps are here:

> **VS Code → LEAP hub:** <https://leap-stc.github.io/compute/vs_code_to_hub/>

That setup (a one-time access token and SSH key) is more involved, so plan to do it **with
the course assistant or Disability Services**, once, early. After it's connected, your VS
Code looks and behaves exactly as it does now — it just runs on the hub. (That page is
written mainly around opening notebooks; you can ignore those parts and simply use
**Terminal → New Terminal**, which now runs on the hub, for your script workflow.)

## Keystroke quick reference

*Tested on Windows with NVDA by the course assistant. Items marked **(being confirmed)** are
still being verified — `Fn+...` keys assume a keyboard where the function keys need `Fn`.*

**Accessibility & help**
- `Fn+Alt+F1` — Accessibility Help (what can I do here?)
- `Alt+F2` — Accessible View of the editor (read your script and code-check results)
- `Escape` — close the accessibility help / view

**Reading terminal output**
- `` Ctrl+` `` — open a terminal
- `Ctrl+Up` — open the terminal's Accessible View
- `Caps Lock+Up` / `Caps Lock+Down` — move up / down between lines in the Accessible View
- `Ctrl+Down` — close the terminal's Accessible View
- `Ctrl+Shift+O` — move between commands in the Accessible View *(being confirmed)*

**Moving around the window**
- `Ctrl+Shift+P` — Command Palette (run any command by name)
- `Fn+F6` / `Fn+Shift+F6` — move focus to the next / previous panel (editor, terminal, sidebar)
- `Ctrl+1` — return focus to the editor

**Working with files**
- `Ctrl+N` — new file
- `Ctrl+P` — quick-open a file by name
- `Ctrl+S` — save the current file
- `Ctrl+W` — close the current editor tab

**Errors and code suggestions** *(being confirmed)*
- `F8` / `Shift+F8` — go to the next / previous error or warning (NVDA announces it)

To switch a **Markdown** file from preview to editable text: `Ctrl+Shift+P` → **Reopen Editor
With Text Editor**.

You're set. Continue to **[Intro to Unix](intro_to_unix.md)**, and do each "Try it" in the
Git Bash terminal you just set up.
