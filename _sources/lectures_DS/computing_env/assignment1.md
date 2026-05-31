# Assignment 1: Files, Git, and GitHub

**At-home assignment — worth 10 points.** When you're done, push your work to your week folder and post a link to that **folder** on the matching Courseworks assignment.

The main goal of this assignment is to set up the GitHub repository where you'll submit every assignment in this course (Part 1), and to submit your first piece of work to it (Part 2). Every weekly assignment will live in its own folder inside that repo.

:::{admonition} Learning goals
:class: tip
This assignment exercises the environment-setup skills from this section:

- Use Unix commands (`mkdir`, `cd`, `touch`) to create folders and files
- Initialize a local Git repository and make commits
- Connect a local repository to a GitHub remote and push
- Write basic Markdown (headings, lists, images, links)
- Practice the edit → add → commit → push loop
:::

## Prerequisites

Before starting, make sure you've worked through:

- [JupyterLab and Colab](./jupyterlab_and_colab.md) — you have a working environment.
- [Intro to Unix](./intro_to_unix.md) — you're comfortable with `mkdir`, `cd`, `touch`.
- [Intro to Git](./intro_to_git.md) — you've run the `git config` setup and authentication is working (`gh-scoped-creds` on LEAP, or a PAT on Colab).

## Part 1: Set up your assignments repository

Every assignment you submit in this course will live in one private GitHub repository. Set it up now.

1. **Create a local directory and initialize git:**

   ~~~
   mkdir clmt5405-assignments
   cd clmt5405-assignments
   git init
   ~~~

2. **Add a Readme and make a first commit:**

   ~~~
   echo "# My CLMT5405 assignments" > Readme.md
   git add Readme.md
   git commit -m "Initial commit"
   ~~~

3. **Create a private repository on GitHub:**
   - Go to [github.com/new](https://github.com/new).
   - Name it `clmt5405-assignments` (use exactly this name — don't vary the capitalization or punctuation).
   - Choose **Private**.
   - Don't initialize it with a README — leave it empty.

4. **Connect your local repo to GitHub and push:**

   ~~~
   git remote add origin https://github.com/<your-username>/clmt5405-assignments.git
   git push -u origin main
   ~~~

5. **Add the instructors as collaborators.** On GitHub, go to your repo → **Settings** → **Collaborators and teams** → **Add people**. Add:
   - `dhruvbalwada` (Dhruv, instructor)
   - `progga004` (Progga, TA)

   They'll receive invitations to accept so they can see and grade your work.

6. **Email us your name and username.** A GitHub invitation only tells us your username, not who you are. So we can match your username to you, send a short email to both [Dhruv](mailto:dbalwada@ldeo.columbia.edu) and [Progga](mailto:pd2816@columbia.edu) with your **full name** and your **GitHub username**.

That's it for Part 1 — your assignments repo is ready, and you'll push commits to it to submit future assignments.

## Part 2: Submit a "week 1" assignment to your repo

Every week, you'll submit your assignment as a folder inside your `clmt5405-assignments` repo. Set up the pattern now by putting a small markdown resume into a `week1/` folder.

1. **From inside your `clmt5405-assignments` repo, create the `week1` folder and an empty resume file:**

   ~~~
   mkdir week1
   cd week1
   touch resume.md
   ~~~

2. **Edit `resume.md`** to include:
   - A top-level heading with your name.
   - An image (a photo of you, or your spirit animal).
   - A secondary heading `## Education`.
   - A bulleted list of schools you attended, with each name hyperlinked to that institution's website.

   Use whatever editor works in your environment — JupyterLab's text editor on LEAP (you can open Markdown Preview alongside to see the rendering), or `nano` / GitHub's web UI on Colab.

3. **Commit and push:**

   ~~~
   cd ..
   git status
   git add week1/resume.md
   git commit -m "Add week 1 assignment"
   git push
   ~~~

4. **Verify on GitHub.** Refresh your `clmt5405-assignments` repo page. You should see a `week1/` folder; click into it to see your rendered `resume.md`.

5. **Practice the iterative loop.** Add a `## Project Interests` section to `week1/resume.md` — some potential ideas of climate-data projects you might want to work on for this course. These are just starting thoughts; you can absolutely change your mind or refine them as the term goes on. Commit and push:

   ~~~
   git add week1/resume.md
   git commit -m "Add Project Interests to week 1"
   git push
   ~~~

   Refresh the GitHub page and confirm the new section appears.

This is the pattern for every assignment in this course: make a week folder, add your work, commit, push.
