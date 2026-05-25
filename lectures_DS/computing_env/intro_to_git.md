# Intro to Git

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

The first time you use git on a new machine (or a new LEAP or Colab session), tell git who you are. This name and email get attached to every snapshot you make.

:::{admonition} Try it
:class: tip
Open a terminal and run these three commands, replacing the placeholders with your own name and the email associated with your GitHub account:

~~~
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
~~~

The last line tells git to call the default branch `main` (matching GitHub's convention).
:::

:::{note}
On LEAP this setup persists across sessions, so you'll only need to do it once. On Colab the runtime is ephemeral — your config disappears with the session — so you'll need to re-run these commands each new Colab session.
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
If `git init` reports `master` as the initial branch name (instead of `main`), it means `init.defaultBranch` wasn't set in your session — common on Colab if you didn't run the `git config` commands above. Either re-run those config commands and rename the branch with `git branch -m main`, or just continue using `master` (the workflow is identical, but our examples below say `main`).
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
We'll create a file, stage it, and commit it — in three steps. **Run each command, then look at the output before moving on.**

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

(On LEAP you can also open `hello.md` in JupyterLab's text editor if you prefer.)

To check that the line was written, you can print the file's contents in the terminal with `more hello.md` (or `cat hello.md`).

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
Run `git log` in `git_practice`. You should see your two commits with their messages, timestamps, and author info (the name and email you set with `git config`). If the output opens in a pager, press `q` to quit.
:::

> *Reference:* `git diff` shows exactly which lines changed between commits, or between your working directory and the last commit. `git checkout <commit-hash> <filename>` restores a file to an earlier version. You won't need these often at first, but they exist when you want to dig into changes or roll back.

## Connecting to GitHub

**Git** is the version-control tool itself — it runs entirely on your local machine. **GitHub** is a separate service that hosts git repositories online and adds collaboration features (a web interface, issue tracking, code review). Git works fine without GitHub, but GitHub gives you a place to back up your work, share it, and collaborate. Other similar services exist (GitLab, Bitbucket); GitHub is the most common in research and open source.

So far your commits have lived only in the `git_practice` directory on this machine. A **remote** is a copy of your repository hosted elsewhere — typically on GitHub. Pushing sends your local commits to the remote, and pulling brings new commits from the remote down to your local copy. This is how you back up your work, share it, and collaborate.

Before you can push, you need to authenticate to GitHub. The authentication setup depends on your environment.

:::{note}
Both authentication methods below assume you already have a GitHub account — you would have needed one for the LEAP JupyterHub anyway. If you don't have one, sign up free at [github.com](https://github.com) (use an email you'll keep access to).
:::

### From the LEAP JupyterHub: `gh-scoped-creds`

[gh-scoped-creds](https://github.com/yuvipanda/gh-scoped-creds/) is preconfigured on the 2i2c-managed LEAP JupyterHub.

Open a terminal in JupyterHub, run `gh-scoped-creds`, and follow the prompts.

You should now be able to push to GitHub from the hub. These credentials will expire after 8 hours (or whenever your JupyterHub server stops), and you'll have to repeat these steps to fetch a fresh set of credentials. Once you authenticate, you'll be provided with a link to a [GitHub App](https://docs.github.com/en/developers/apps/getting-started-with-apps/about-apps) that you have to [install](https://docs.github.com/en/developers/apps/managing-github-apps/installing-github-apps) on the repositories you want to be able to push to from this particular JupyterHub. You only need to do this once per JupyterHub, and can revoke access any time. You can always provide access to your own personal repositories, but might need approval from admins of GitHub organizations if you want to push to repos in that organization.

### From Google Colab: Personal Access Tokens

```{warning}
The Colab auth steps below are a working draft. Walk through them in a real Colab session and confirm each step still works before relying on them — GitHub's settings UI changes occasionally, and so do Colab's defaults.
```

On Colab, the `gh-scoped-creds` flow isn't available. Instead, you'll generate a **Personal Access Token (PAT)** once on GitHub and use it as your password when git asks for one.

**One-time setup on GitHub:**

1. Go to [github.com/settings/tokens](https://github.com/settings/tokens) (*Settings → Developer settings → Personal access tokens → Tokens (classic)*).
2. Click *Generate new token (classic)*.
3. Give it a name like `colab-clmt5405`, set an expiration (e.g., 90 days), and check the `repo` scope.
4. Click *Generate token* and **copy the token immediately** — GitHub will only show it to you once.
5. Save it in a password manager or another safe place.

**Using it in Colab:**

After cloning your repo with the HTTPS URL, when you run `!git push origin main`, Colab will prompt:

~~~
Username for 'https://github.com': <your-github-username>
Password for 'https://...github.com': <paste your PAT>
~~~

Use your **GitHub username** as the username and **paste the PAT** as the password.

To avoid pasting on every push within the same Colab session, run once:

~~~
!git config --global credential.helper store
~~~

This caches credentials to a plaintext file on the Colab runtime, which is erased when the runtime dies — so you'll re-paste once per new Colab session.

```{warning}
**Never commit your PAT to a public repo.** Treat it like a password — if it appears in any committed file, GitHub will detect and revoke it.
```

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
- **Remote sync:** `git push`, `git pull`, `git clone` (with authentication via `gh-scoped-creds` on LEAP or PAT on Colab)

This is enough to put any project under version control and share it through GitHub. There's much more to git — branching, merging, rebasing, conflict resolution — but you'll learn those when you need them. For a fuller tutorial, see the [Software Carpentry Git Lesson](http://swcarpentry.github.io/git-novice/) this material is based on.

When you've finished the exercises, you can clean up:

- **Locally**: `cd ..` out of `git_practice`, then `rm -r git_practice`.
- **On GitHub**: go to your `git_practice` repo's page, click the **Settings** tab, scroll to the bottom of the General page to the "Danger Zone," click **Delete this repository**, and type the repo name to confirm.
