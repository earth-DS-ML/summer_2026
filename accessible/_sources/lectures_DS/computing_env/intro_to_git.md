# Intro to Git

:::{admonition} Doing this lesson with a screen reader
:class: important
Run the commands below in the **Git Bash terminal** in VS Code (the one you set as your default in [Setting up an accessible workflow](accessible_setup.md)). After each command, press **`Alt+F2`** (Accessible View) to read the output. The `git config` step below also turns off git's scrolling pager, so commands like `git log` print their output straight into the terminal where your screen reader can read it.
:::

## Why version control?

[_Version control_](https://en.wikipedia.org/wiki/Version_control) is a way to keep track of changes to a set of files over time. A version control system takes **snapshots** of your project at points you choose, and lets you go back to any earlier snapshot — or compare two snapshots to see exactly what changed.

Without version control, projects tend to grow tails like this:

~~~
my_code.py
my_code_version2.py
my_code_version2B-RPA-edit.py
my_code_FINAL_VERSION.py
my_code_THIS_IS_ACTUALLY_THE_FINAL_VERSION.py
~~~

Version control replaces all of that with a single file you keep editing, while the history of every change is recorded behind the scenes. It also lets you share code with collaborators, make simultaneous edits, and merge changes in a controlled way.

The tool we'll use is called [Git](https://git-scm.com). Git is powerful and has a somewhat steep learning curve, but in this class we'll only use a small subset of it. That subset will get you very far.

> *Reference:* For a deeper tutorial, see [Version Control with Git](http://swcarpentry.github.io/git-novice/) on Software Carpentry.

## Setting up git

The first time you use git on a new machine, tell git who you are. This name and email get attached to every snapshot you make.

:::{admonition} Try it
:class: tip
Open a terminal and run these four commands, replacing the placeholders with your own name and the email associated with your GitHub account:

~~~
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
git config --global core.pager cat
~~~

The third line tells git to call the default branch `main` (matching GitHub's convention). The fourth line turns off git's **pager**: by default, commands like `git log` open their output in a scrolling viewer (`less`) that is awkward with a screen reader; `core.pager cat` makes git print the output straight into the terminal instead, so you can read it with `Alt+F2`.
:::

:::{note}
This configuration lives on the computer where you run it, so on your own machine you only need to do it once. (When you later connect to the LEAP hub through VS Code, you'll run the same `git config` commands once there too.)
:::

## Tracking changes in a local repo

The basic git workflow has three "places" where files live:

1. **Working directory** — your project files as they actually are right now.
2. **Staging area** — files you've marked as "ready to be committed" (`git add`).
3. **Repository** — the recorded history of committed snapshots (`git commit`).

You move files from working → staging with `git add`, and from staging → repository with `git commit`. Let's walk through the full cycle on a small practice repo.

### Initialize a repo — `git init`

`git init` turns a regular directory into a git repository. It creates a hidden `.git` folder where the history is stored.

:::{admonition} Try it
:class: tip
Create a fresh directory to play with, then initialize it as a git repo:

~~~
mkdir git_practice
cd git_practice
git init
~~~

You should see a message like `Initialized empty Git repository in /.../git_practice/.git/`.
:::

:::{note}
If `git init` reports `master` as the initial branch name (instead of `main`), it means `init.defaultBranch` wasn't set before you ran it. Either re-run the config command above and rename the branch with `git branch -m main`, or just continue using `master` (the workflow is identical, but our examples below say `main`).
:::

### Check the state — `git status`

`git status` tells you what's in your working directory, what's staged, and what's already committed. It's the command you'll run most often — whenever you're unsure where you stand.

:::{admonition} Try it
:class: tip
Run `git status` in `git_practice`. You should see "On branch main" and "No commits yet" — the repo exists but you haven't put anything in it yet.
:::

### Stage and commit a file — `git add` and `git commit`

`git add <filename>` stages a file for the next commit. `git commit -m "message"` records the staged files as a snapshot, with a short message describing the change.

:::{admonition} Try it
:class: tip
We'll create a file, stage it, and commit it — in three steps. **Run each command, then read the output with `Alt+F2` before moving on.**

**1. Create the file:**

~~~
touch hello.md
git status
~~~

You should see `hello.md` listed as **untracked** — git knows it's there but isn't tracking it yet.

**2. Stage it:**

~~~
git add hello.md
git status
~~~

Now `hello.md` shows up as a **staged change**, ready to be committed.

**3. Commit it:**

~~~
git commit -m "Add an empty hello file"
git status
~~~

The output should say "nothing to commit, working tree clean" — your snapshot is now part of the repo's history.
:::

### Make a change and commit again

The real value of git shows up once you have a history of commits, not just one.

:::{admonition} Try it
:class: tip
Add some content to `hello.md`. The quickest way that works in any environment is shell redirection — the `>` symbol sends a command's output into a file:

~~~
echo "# My first file" > hello.md
~~~

(You can also open `hello.md` in the VS Code editor and type into it if you prefer.)

To check that the line was written, print the file's contents in the terminal with `cat hello.md`, then read it with `Alt+F2`.

Then stage and commit:

~~~
git status
git add hello.md
git commit -m "Add a heading"
~~~

You now have two commits recording the file's history.
:::

### View the history — `git log`

`git log` shows the sequence of commits, newest first.

:::{admonition} Try it
:class: tip
Run `git log` in `git_practice`. You should see your two commits with their messages, timestamps, and author info (the name and email you set with `git config`). Because you turned off the pager, the output prints directly into the terminal — read it with `Alt+F2`.
:::

> *Reference:* `git diff` shows exactly which lines changed between commits, or between your working directory and the last commit. `git checkout <commit-hash> <filename>` restores a file to an earlier version. You won't need these often at first, but they exist when you want to dig into changes or roll back.

## Connecting to GitHub

**Git** is the version-control tool itself — it runs entirely on your local machine. **GitHub** is a separate service that hosts git repositories online and adds collaboration features (a web interface, issue tracking, code review). Git works fine without GitHub, but GitHub gives you a place to back up your work, share it, and collaborate. Other similar services exist (GitLab, Bitbucket); GitHub is the most common in research and open source.

So far your commits have lived only in the `git_practice` directory on this machine. A **remote** is a copy of your repository hosted elsewhere — typically on GitHub. Pushing sends your local commits to the remote, and pulling brings new commits from the remote down to your local copy. This is how you back up your work, share it, and collaborate.

Before you can push, you need to authenticate to GitHub.

:::{note}
The methods below assume you already have a GitHub account. If you don't have one, sign up free at [github.com](https://github.com) (use an email you'll keep access to).
:::

### Authenticating from your own computer

The first time you push from a new computer, you need to prove to GitHub that it's you. On Windows, the simplest way:

- **Git Credential Manager (built in).** Git for Windows installs this automatically. The first time you run `git push`, a browser window opens asking you to sign in to GitHub. Sign in once, and your credentials are saved for future pushes.
- **Alternative — the GitHub CLI.** If you'd rather stay in the terminal, install the [GitHub CLI](https://cli.github.com/) and run `gh auth login`, then follow the prompts (a short, linear set of questions plus a one-time code you paste into a GitHub web page). This is a screen-reader-friendly flow.

Either way, you only set this up once per computer.

### Authenticating on the LEAP hub (for later)

When you start using the LEAP hub through VS Code for the data chapters, GitHub authentication there uses a preconfigured tool called [gh-scoped-creds](https://github.com/jupyterhub/gh-scoped-creds): open a terminal on the hub, run `gh-scoped-creds`, and follow the prompts (it gives you a short code to enter at a GitHub web page). Those credentials expire after about 8 hours or when your hub server stops, so you re-run it as needed. You don't need this yet — it's here so you know where to look later.

## Pushing and pulling

Once authentication is set up, you can connect your local `git_practice` repo to a GitHub repository.

:::{admonition} Try it
:class: tip
1. On GitHub, [create a new empty repository](https://github.com/new) called `git_practice`. Don't add a README or any other files — leave it completely empty.
2. In your terminal, in the `git_practice` directory, add the GitHub repo as a remote (replace `your-username` with your GitHub username):

   ~~~
   git remote add origin https://github.com/your-username/git_practice.git
   ~~~

3. Push your commits to GitHub:

   ~~~
   git push -u origin main
   ~~~

   The `-u origin main` part tells git to remember this remote and branch as the default, so future `git push` and `git pull` work without arguments.

4. Refresh your GitHub repo page — you should see `hello.md` and your two commits.
:::

To bring changes from the remote back down (e.g., a collaborator pushed something, or you edited the file directly on GitHub), use `git pull`:

:::{admonition} Try it
:class: tip
On the GitHub website, open `hello.md`, edit it (add a new line), and commit the change through the web UI. Then back in your terminal, run:

~~~
git pull
~~~

Your local copy should now have the change you made on GitHub.
:::

> *Reference:* `git clone <repo-url>` copies an existing GitHub repository down to your local machine. You'll use this any time you want a local copy of a repo that already exists on GitHub.

## Summary

You've now used the basic git workflow:

- **One-time setup:** `git config`
- **Local tracking:** `git init`, `git status`, `git add`, `git commit`, `git log`
- **Remote sync:** `git push`, `git pull`, `git clone` (with authentication via Git Credential Manager or `gh auth login` on your computer, and `gh-scoped-creds` on the LEAP hub later)

This is enough to put any project under version control and share it through GitHub. There's much more to git — branching, merging, rebasing, conflict resolution — but you'll learn those when you need them. For a fuller tutorial, see the [Software Carpentry Git Lesson](http://swcarpentry.github.io/git-novice/) this material is based on.

When you've finished the exercises, you can clean up:

- **Locally**: `cd ..` out of `git_practice`, then `rm -r git_practice`.
- **On GitHub**: go to your `git_practice` repo's page, click the **Settings** tab, scroll to the bottom of the General page to the "Danger Zone," click **Delete this repository**, and type the repo name to confirm.
