# Welcome to Earth Data Science and Machine Learning Course Series

This is the online home for the first of the two summer 2026 courses:
- *Computing and Research Methods for Climate Data Science* (CLMT5405)
- *Machine Learning for Climate Science and Environmental Sustainability* — taught by Dr. Kara Lamb

## Logistics

- **Instructor:** Dr. Dhruv Balwada (dbalwada@ldeo.columbia.edu)
- **Teaching Assistant:** Progga Dutta (pd2816@columbia.edu)
- **Hours and location:** TuTh 9am–10:45am, 602 Northwest Corner
- **Office hours:** TBD

## Motivation and Scope

Computing has become an indispensable tool for nearly all Earth and Environmental Scientists and Practitioners, but it doesn't usually appear in our curriculums.
This material focuses on _data analysis_, a subset of computing in which the data already exist, e.g. from observations or from the output of a simulation, but have to be transformed into understanding.
There are many different ways to gain understanding, but most workflows often boil down to:

- read data files
- perform some analysis operations, from very simple (e.g. take the mean) to very complex (e.g. train a deep neural network)
- visualize the output in a plot

CLMT5405 doesn't attempt to teach deep learning; its goal is to teach the basic foundations of Earth and Environmental Data Science which are often overlooked.
The material is designed to be accessible for Earth Science graduate students in any discipline, with little to no prerequisites.

This material is intended to introduce students to modern computing software, programming tools and best practices that are broadly applicable to the analysis and visualization of Earth and Environmental data.
This includes an introduction to Unix, version control, and basic programming in the open-source Python language.
The bulk of the content is devoted to in-depth exploration of the numerical analysis and visualization packages which comprise the modern Scientific Python ecosystem, including Numpy, Scipy, Matplotlib, Pandas, Xarray, using real Earth and Environmental datasets.

## Learning Goals

After completing all of the material, students should have the ability to:

- Use unix commands to work with files and directories
- Navigate the JupyterHub Environment effectively
- Identify common geoscience data formats and the python packages which can load these data
- Perform basic exploratory data analysis on Earth and Environmental data, distinguishing between
  - _Tabular data_: rows and columns
  - _Gridded data_: multidimensional numerical arrays
- Use visualization to enhance interpretation of data, including maps and interactive visualizations
- Construct complete, well-structured programs in Python
- Practice reproducible research through version control

## Course structure

Each week, students complete some lecture material and one homework assignment. Class time is **mostly hands-on practice**: students read the lecture material and work through exercises in their own environment, while the instructor and TA roam to answer questions and provide guidance. The instructor opens each session with a brief overview, and gives additional overviews mid-session when the material calls for it. The balance is intentionally weighted toward practice — you'll spend more class time doing things than listening.

## Assignments and grading

Your grade in this course is based on:

- **60%** — final project
- **40%** — weekly assignments (in-class and at-home)

Most weeks have **two assignments, each worth 10 points**:

- **In-class assignment** — the **"Try it"** exercises built into that week's lecture notebooks. Work through them in your own copy of the notebook during class, then submit the completed notebook. Each section makes clear which in-class work needs to be submitted — look for the **In-class assignment** note on the section overview and at the top of each relevant lecture notebook. (A few early command-line lessons — the Unix and Git practice — have nothing to submit, so those weeks have only an at-home assignment.)
- **At-home assignment** — the week's homework page (the numbered "Assignment" pages in each section).

You'll keep all your work in the private GitHub repository you set up in [Assignment 1](./lectures_DS/computing_env/assignment1.md), with each week's assignment in its own folder (e.g. `week1/`, `week2/`). **To hand it in:** push your work to GitHub, then post a link to the relevant folder or notebook on that week's **Courseworks** assignment. (Each assignment page tells you whether to link the whole folder or a specific notebook.)

**Late policy:** all weekly assignments are due by the end of Sunday night of the following week — assignments not in by then receive a zero.

## Final project

A final project at the end of the course serves as a capstone, and counts for 60% of your grade. The [project requirements](./lectures_DS/project_require.md) page has the details.

You don't need to worry about the project right away — Assignment 1 already nudges you to jot down some early ideas. Starting in week 2 we'll begin discussing projects more formally, and firm up a timeline (proposal, milestones, presentation) as the term progresses.

## Using AI in This Course

AI tools like ChatGPT are now part of how people work with data. This course treats them as a tool you'll learn to use well — including where they help, where they don't, and what your own judgment still has to provide.

**What to use.** For this course, the recommended chat-based AI tool is [Google Gemini](https://www.cuit.columbia.edu/content/google-gemini), which Columbia provides free to students through CUIT. Other chat tools (ChatGPT, Claude.ai) are also fine if you prefer them. Avoid editor-integrated tools like Copilot, Cursor, or Claude Code — chat keeps the "ask → read → verify" loop visible, which matters while you're still learning the underlying skills.

**How to use it — Socratic mode.** Default to asking the AI to *teach* you, not to *do it for you*. At the start of a working session, prime your chat with a tutor prompt. Here is one to start with — feel free to adapt it as you learn what works:

```
You are acting as my Socratic Tutor for a graduate-level Climate Data Science
course. I am going to show you bugs or ask about Python/Xarray/Git.

Rules:
1. NEVER give me corrected code blocks or direct syntax fixes.
2. Explain the computational or data concept I am missing.
3. Ask me ONE targeted guiding question to help me find the solution myself.
```

The goal is to use AI to build understanding, not to paste solutions you can't explain.

**What AI is good at, and what it isn't.** Chat-based AI is genuinely useful for explaining error messages, suggesting matplotlib syntax, walking through an unfamiliar library API, or summarizing what a function does. It is less reliable for judging whether your scientific result is correct, picking the right analysis for *your* data, catching subtle bugs in numerical or coordinate-system code, or knowing what "looks right" for a specific geophysical field. Treat AI as a fast, broadly-read but inexperienced collaborator — useful for the syntax layer, not a substitute for your own scientific judgment.

**Your responsibilities.** You're responsible for understanding and being able to explain everything you submit. If you can't explain how a piece of code or analysis works, that's a signal to revisit the material — not to lean more heavily on AI.

## History and Origins of the course

Most of the content for CLMT5405 derives from previous versions of this course:

- [Ryan Abernathey's 2021 book](https://earth-env-data-science.github.io/) — the original incarnation.
- [Yutian Wu's 2024 repo](https://github.com/yutianwuldeo/GR6901) — a more recent update.
- [Project Pythia](https://foundations.projectpythia.org/) — a separate but excellent companion resource.

The [summer 2025 version of this course](https://earth-ds-ml.github.io/summer_2025/) is also available online for reference.


