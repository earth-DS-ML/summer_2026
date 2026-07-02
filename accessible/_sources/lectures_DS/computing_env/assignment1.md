# Assignment 1: Files, Git, and GitHub

**At-home assignment — worth 10 points.** When you're done, push your work to your week folder and post a link to that **folder** on the matching Courseworks assignment.

The main goal of this assignment is to set up the GitHub repository where you'll submit every assignment in this course (Part 1), and to submit your first piece of work to it (Part 2). Every weekly assignment will live in its own folder inside that repo.

:::{admonition} Doing this assignment with a screen reader
:class: important
Do all the steps below in your **VS Code** setup: the **Git Bash** terminal for the `git` and Unix commands (press `Ctrl+Up` to read output), and the VS Code editor for writing `resume.md`. See [Setting up an accessible workflow](./accessible_setup.md) if you need to.
:::

:::{admonition} Learning goals
:class: tip
This assignment exercises the environment-setup skills from this section:

- Use Unix commands (`mkdir`, `cd`, `touch`) to create folders and files
- Initialize a local Git repository and make commits
- Connect a local repository to a GitHub remote and push
- Write basic Markdown (headings, lists, images with alt text, links)
- Practice the edit → add → commit → push loop
:::

## Prerequisites

Before starting, make sure you've worked through:

- [Setting up an accessible workflow](./accessible_setup.md) and [Working with Python scripts](./working_with_scripts.md) — you have a working VS Code environment.
- [Intro to Unix](./intro_to_unix.md) — you're comfortable with `mkdir`, `cd`, `touch`.
- [Intro to Git](./intro_to_git.md) — you've run the `git config` setup and authentication is working (Git Credential Manager or `gh auth login` on your computer).

## Part 1: Set up your assignments repository

Every assignment you submit in this course will live in one private GitHub repository. Set it up now.

1. **Create a local directory and initialize git:**

The following is a terminal command:

   ~~~
   mkdir clmt5405-assignments
   cd clmt5405-assignments
   git init
   ~~~

End of command.

2. **Add a Readme and make a first commit:**

The following is a terminal command:

   ~~~
   echo "# My CLMT5405 assignments" > Readme.md
   git add Readme.md
   git commit -m "Initial commit"
   ~~~

End of command.

3. **Create a private repository on GitHub:**
   - Go to [github.com/new](https://github.com/new).
   - Name it `clmt5405-assignments` (use exactly this name — don't vary the capitalization or punctuation).
   - Choose **Private**.
   - Don't initialize it with a README — leave it empty.

4. **Connect your local repo to GitHub and push:**

The following is a terminal command:

   ~~~
   git remote add origin https://github.com/<your-username>/clmt5405-assignments.git
   git push -u origin main
   ~~~

End of command.

5. **Add the instructors as collaborators.** On GitHub, go to your repo → **Settings** → **Collaborators and teams** → **Add people**. Add:
   - `dhruvbalwada` (Dhruv, instructor)
   - `progga004` (Progga, TA)

   They'll receive invitations to accept so they can see and grade your work.

6. **Email us your name and username.** A GitHub invitation only tells us your username, not who you are. So we can match your username to you, send a short email to both [Dhruv](mailto:dbalwada@ldeo.columbia.edu) and [Progga](mailto:pd2816@columbia.edu) with your **full name** and your **GitHub username**.

That's it for Part 1 — your assignments repo is ready, and you'll push commits to it to submit future assignments.

## Part 2: Submit a "week 1" assignment to your repo

Every week, you'll submit your assignment as a folder inside your `clmt5405-assignments` repo. Set up the pattern now by putting a small markdown resume into a `week1/` folder.

1. **From inside your `clmt5405-assignments` repo, create the `week1` folder and an empty resume file:**

The following is a terminal command:

   ~~~
   mkdir week1
   cd week1
   touch resume.md
   ~~~

End of command.

2. **Edit `resume.md`** to include:
   - A top-level heading with your name.
   - An image (a photo of you, or your spirit animal) — and give it **descriptive alt text**, the short text a screen reader reads aloud in place of the image. In Markdown, the alt text goes in the square brackets: `![A golden retriever lying in tall grass](dog.jpg)`. Writing good alt text is itself one of the most important skills in this course, so take a moment to make yours genuinely descriptive.
   - A secondary heading `## Education`.
   - A bulleted list of schools you attended, with each name hyperlinked to that institution's website.

   Edit the file in the **VS Code editor** (open `resume.md`, make your changes, and save with `Ctrl+S`). VS Code can show a Markdown preview if you'd like, but the text source is what gets committed.

3. **Commit and push:**

The following is a terminal command:

   ~~~
   cd ..
   git status
   git add week1/resume.md
   git commit -m "Add week 1 assignment"
   git push
   ~~~

End of command.

4. **Verify on GitHub.** Refresh your `clmt5405-assignments` repo page. You should see a `week1/` folder; click into it to see your rendered `resume.md`.

5. **Practice the iterative loop.** Add a `## Project Interests` section to `week1/resume.md` — some potential ideas of climate-data projects you might want to work on for this course. These are just starting thoughts; you can absolutely change your mind or refine them as the term goes on. Commit and push:

The following is a terminal command:

   ~~~
   git add week1/resume.md
   git commit -m "Add Project Interests to week 1"
   git push
   ~~~

End of command.

   Refresh the GitHub page and confirm the new section appears.

This is the pattern for every assignment in this course: make a week folder, add your work, commit, push.
