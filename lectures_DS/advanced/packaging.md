# Going Further with Computing

This course introduced just enough programming to get you working with data. But real research and professional computing draws on a much wider set of skills — and simply knowing these things exist, and roughly what they are for, makes them far easier to pick up when a project calls for them.

This page is a short, curated map of **coding and computing topics beyond this course** that are worth knowing about. None of it is required — think of it as signposts for where to go next as your own work grows.

## Where to go learn this

There are many good books, courses, and tutorials for this kind of material. One we particularly like is MIT's free [Missing Semester of Your CS Education](https://missing.csail.mit.edu/) — short, practical lectures on exactly the tools that usually get picked up informally rather than taught in a class. The [2026 edition](https://missing.csail.mit.edu/2026/) is current and well worth your time; each lecture is about an hour and stands on its own, and we link the relevant ones beside the topics below.

## Topics worth knowing about

A handful of the most useful next steps, each framed by what you already did in this course:

- **The command line, in depth** ([shell](https://missing.csail.mit.edu/2026/course-shell/), [command-line environment](https://missing.csail.mit.edu/2026/command-line-environment/)) — in the *Computing Environment* module you got around the shell with `pwd`, `ls`, and `cd`. That barely scratches the surface: with scripting and pipes you can automate repetitive work, chain tools together, and work fluently on the remote servers and HPC clusters where much climate data lives — often with no graphical interface at all.

- **Version control, beyond the basics** ([Version Control and Git](https://missing.csail.mit.edu/2026/version-control/)) — you used Git to `commit` and `push` your assignments to GitHub. Branches, merges, and pull requests are the next step: they let you try out changes without fear of losing work, and collaborate on shared code without overwriting each other.

- **Debugging and profiling** ([Debugging and Profiling](https://missing.csail.mit.edu/2026/debugging-profiling/)) — working in notebooks, you could poke at a problem interactively: rerun a cell, print a variable, read the traceback inline. That easy visibility fades once your code grows into functions and scripts, where you can no longer see what is happening *inside* a function from the outside. A **debugger** lets you pause a running program and step through its variables one line at a time, and a **profiler** measures which lines actually take the time — so when a long analysis is slow, you fix the part that matters instead of guessing.

- **Packaging and sharing code** ([Packaging and Shipping Code](https://missing.csail.mit.edu/2026/shipping-code/)) — in *Core Python* you turned a set of functions into a `.py` module (`gcdistance`) and imported it. Packaging is the next step up: wrapping modules into an installable package, so a one-off analysis becomes a tool you and your collaborators can reuse across projects and cite in a paper.

- **Testing and code quality** ([Code Quality](https://missing.csail.mit.edu/2026/code-quality/)) — as the notebooks in this course grew, you mostly checked your work by eye. Automated tests, linters, and continuous integration check it for you — giving you confidence that your results are reproducible (the software-side complement to the reproducibility ideas from the *Data* module) and that your code keeps working as it changes.

## Agentic coding

This one deserves a special mention, because it is changing how code gets written right now.

Throughout this course we asked you to use AI as a chat-based tutor — asking it to explain things and unstick you, not to write your code for you. That was deliberate: you only build real fluency by working through the problems yourself.

It is worth knowing where the field is heading, though. **Agentic coding tools** — such as [Claude Code](https://claude.com/claude-code), [Cursor](https://cursor.com/), and [GitHub Copilot](https://github.com/features/copilot) — go well beyond chat: you describe what you want in plain language, and the tool reads your whole project, writes and runs code, executes tests, and iterates on the results on its own. This is quickly becoming a standard part of how research and professional software gets written, and Missing Semester's [lecture on agentic coding](https://missing.csail.mit.edu/2026/agentic-coding/) is a good hands-on introduction.

The catch — and the reason we had you learn the fundamentals first — is that these tools are only as good as your ability to steer and check them. You still need to read the code, recognize when it is wrong or doing something you did not intend, and know what to ask for next. The skills from this course are exactly what let you use agentic tools as a force multiplier, rather than being led astray by them.
