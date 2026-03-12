# git Workshop

> *"An idiot admires complexity, a genius admires simplicity"* – Terry Davis

A hands-on workshop covering the fundamentals of git and GitHub. This guide walks through everything from initializing your first repository to collaborating with a team using pull requests. It is designed as a companion to the lecture; read through it, run the commands, and build your understanding by doing.

**Pre-requisites:** A GitHub account, a terminal, and a willingness to break things.

This guide assumes comfortable knowledge of basic shell commands. You should also have [added your SSH key to GitHub](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/) before starting.

---

## Contents

1. [What is git?](#what-is-git)
2. [Installing git](#installing-git)
3. [Repositories](#repositories)
4. [Staging, Committing, and Logging](#staging-committing-and-logging)
5. [Light Plumbing — How git Stores Data](#light-plumbing--how-git-stores-data)
6. [Config](#config)
7. [SSH Key Setup with GitHub](#ssh-key-setup-with-github)
8. [Branches](#branches)
9. [Merge](#merge)
10. [Rebase](#rebase)
11. [Reset](#reset)
12. [Remotes](#remotes)
13. [GitHub](#github)
14. [Pull Requests](#pull-requests)
15. [Gitignore](#gitignore)
16. [Part 2 — Interactive Session](#part-2--interactive-session)

---

## What is git?

Git is a **Version Control System (VCS)** with roughly 95% adoption among developers. It lets you keep a history of code changes by author, revert mistakes, collaborate with others, and maintain backups of your work. Every saved snapshot in git is called a **commit**,a set of changes tied to an author and a date.

Git was created in 2005 by Linus Torvalds (creator of the Linux kernel) after license disputes with BitKeeper. He built the first version in just 5 days. It is currently maintained by Junio Hamano.

**Important:** GitHub ≠ git. Git is the tool. GitHub is a commercial hosting service that uses git under the hood. We interact with git through the command line (CLI).

---

## Installing git

Verify your installation:
```bash
git version
```

### Linux (Debian/Ubuntu) or WSL
```bash
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository ppa:git-core/ppa
sudo apt update
sudo apt install git
```

### macOS
```bash
brew update
brew install git
```

Use recent versions of git. Older versions may lack features covered in this workshop.

### Reading the manual

Git has excellent built-in documentation. You can access it any time with `man git`. Some useful navigation shortcuts for the pager:

| Key | Action |
|-----|--------|
| `q` | Quit |
| `j` / `k` | Move one line down / up |
| `d` / `u` | Half page down / up |
| `/<term>` | Search for "term" |
| `n` / `N` | Next / previous search result |
| `g` / `G` | Top / bottom of document |

---

## Repositories

A **repository (repo)** is a directory that contains your project files plus a special `.git` folder where git stores all of its internal tracking and versioning data.

### Creating a repo
```bash
mkdir research
cd research
git init
```

That's it. You now have a git repository. The `.git` directory inside is where all the magic lives — commits, branches, tags, and every object git needs.

### The three states

Every file in your working directory is in one of three states:

1. **Untracked** — git doesn't know about it yet
2. **Staged** — marked for inclusion in the next commit
3. **Committed** — safely stored in the repo history

Check where things stand at any time:
```bash
git status
```

---

## Staging, Committing, and Logging

### Staging

Before a change can be committed, it must be **staged** (added to the index):
```bash
git add <filename>              # stage a specific file
git restore --staged <filename> # unstage a file
git rm --cached <filename>      # remove from staging and stop tracking
```

### Committing

A **commit** is a snapshot of the repo at a point in time. Each commit records who made it, when, and what changed.
```bash
git commit -m "your message here"  # commit with an inline message
git commit --amend                 # modify the most recent commit
```

Write clear, descriptive commit messages. A common convention is to prefix with a category, e.g. `"A: add contents.md"` or `"fix: resolve null pointer in parser"`.

### Logging

View the commit history:
```bash
git log                          # full log with pager (arrow keys to scroll, q to exit)
git --no-pager log -n 1          # show only the latest commit, no pager
```

Each entry in the log shows a **commit hash** — a unique SHA-1 identifier. You can refer to any commit by its first 7 characters.

### Putting it together
```bash
echo "# Research Project" > contents.md
git status                           # untracked
git add contents.md                  # staged
git status                           # ready to commit
git commit -m "A: add contents.md"   # committed
git log                              # inspect your history
```

This staging → committing → logging cycle is roughly **50% of your daily git workflow**. The other 40% is collaboration with remotes, and the remaining 10% is fixing things and advanced usage.

---

## Light Plumbing — How git Stores Data

Git uses a collection of data structures internally (DAGs, Merkle trees, hash maps, and more), but you don't need to understand all of them. Here's the simplified version.

### Hashes

Every commit gets a unique hash derived from its content, message, author info, timestamp, and parent commit hash. These are SHA-1 hashes and are effectively unique (though collisions are theoretically possible).

```bash
git log                      # find a commit hash
git --no-pager log -n 1      # just the latest
```

### Objects, Trees, and Blobs

All data in your project is stored inside `.git/objects`. There are three key object types:

- **commit** — a snapshot pointing to a tree, with metadata (author, message, parent)
- **tree** — represents a directory, listing blobs and other trees
- **blob** — represents a file's contents

You can inspect any object with git's plumbing command:
```bash
git cat-file -p <hash>   # pretty-print the contents of an object
```

### How storage works

Git stores **entire snapshots** per commit, not just diffs. But it's smart about it — it uses compression and file packing, and if a file hasn't changed between commits, it stores only one copy. You can verify this by inspecting blob hashes across commits; unchanged files will have the same hash.

### Inside `.git`

| Path | Purpose |
|------|---------|
| `HEAD` | Pointer to the current branch/commit |
| `index` | Snapshot of the staging area |
| `objects/` | All versioned data (commits, trees, blobs) |
| `refs/heads/` | Branch tip references |
| `config` | Local repository settings |
| `hooks/` | Scripts that run on events (e.g. pre-commit) |
| `logs/` | History of ref updates |
| `COMMIT_EDITMSG` | Last commit message |
| `description` | Repo description (used by some tools) |

---

## Config

Git stores author information with every commit, so it needs to know who you are.

### Setting your identity
```bash
git config set --global user.name "Your Name"
git config set --global user.email "your.email@example.com"
```

### Viewing and managing config
```bash
git config list --local              # list local config
git config list --global             # list global config
cat ~/.gitconfig                     # view the global config file directly
git config get <section>.<key>       # get a single value
git config unset <section>.<key>     # remove a value
git config remove-section <section>  # remove an entire section
```

### Config hierarchy

Git config has four levels, each overriding the one above:

1. **system** (`/etc/gitconfig`) — all users on the machine
2. **global** (`~/.gitconfig`) — all repos for the current user
3. **local** (`.git/config`) — the specific repo
4. **worktree** (`.git/config.worktree`) — a specific worktree

The lowest (most specific) level wins when there's a conflict.

---

## SSH Key Setup with GitHub

SSH keys let you authenticate with GitHub securely without entering credentials every time.

### Generate a key
```bash
ssh-keygen -t ed25519 -C "your.email@example.com"
```
Press Enter for the default location. Set a passphrase (recommended).

### Add the key to GitHub
```bash
cat ~/.ssh/id_ed25519.pub   # copy this output
```
Go to **GitHub → Settings → SSH and GPG Keys** and paste it as both an Authentication and Signing key.

### Recommended global settings
```bash
git config set --global user.signingkey ~/.ssh/id_ed25519.pub
git config set --global gpg.format ssh
git config set --global commit.gpgsign true
git config set --global init.defaultbranch main
git config set --global core.editor <your-editor>   # e.g. vim, nano, code
git config set --global pull.rebase true
```

If you're on WSL/Linux and get repeated passphrase prompts, you may need to configure `ssh-agent`. See [GitHub's SSH agent guide](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/working-with-ssh-key-passphrases) for details.

---

## Branches

A **branch** is just a named pointer to a specific commit. Branches are cheap — creating one doesn't copy any files.

### Viewing branches
```bash
git branch         # local branches
git branch -r      # remote branches
git branch -a      # all branches
```

### Creating and switching
```bash
git branch <name>              # create a new branch
git switch <name>              # switch to an existing branch
git switch -c <name>           # create AND switch in one step
git switch -c <name> <hash>    # branch from a specific commit
```

Prefer `git switch` over `git checkout` for branch operations. `switch` is safer and more explicit about intent.

### Default branch naming

Git defaults to `master`, GitHub defaults to `main`. Rename if needed:
```bash
git branch -m <oldname> <newname>
```

### Committing on a branch

When you commit on a branch, only that branch moves forward. Other branches are unaffected.
```bash
git switch -c add_classics
echo "title,director,year" > classics.csv
git add classics.csv
git commit -m "D: added classics.csv"
git log
```

### How branches are stored

Branch tips are stored as simple text files in `.git/refs/heads/`, each containing a single commit hash. A "ref" is a pointer to a commit — all branches are refs, but not all refs are branches (tags are refs too).

```bash
git --no-pager log --decorate=full --oneline   # see full ref names
find .git/refs/heads -type f -exec cat {} \;   # peek at what's stored
```

---

## Merge

When you're done working on a branch, you typically merge it back into `main`.

### How merging works

When two branches have **diverged** (both have commits the other doesn't), git finds a common ancestor (the merge base), replays changes from both branches, and records the result in a new **merge commit** with two parents.

```bash
git switch main                              # switch to the branch you're merging INTO
git merge <feature-branch>                   # merge the feature branch in
git log --oneline --decorate --graph --parents  # visualize the result
```

### Fast-forward merge

If the base branch has **not** diverged (no new commits since the feature branched off), git simply moves the pointer forward. No merge commit is created.

### Merge conflicts

When both branches modified the same part of the same file, git can't auto-resolve. You'll see conflict markers in the file:
```
<<<<<<< HEAD
your changes
=======
their changes
>>>>>>> feature-branch
```

Edit the file to resolve the conflict, remove the markers, then:
```bash
git add <resolved-file>
git merge --continue
```

If you just want to accept all changes from one side:
```bash
git checkout --ours <file>     # keep the current branch's version
git checkout --theirs <file>   # keep the incoming branch's version
```

### Merge workflow example
```bash
# 1. Update main with a new commit
git switch main
# (edit contents.md)
git add contents.md
git commit -m "E: update contents"

# 2. Merge your feature branch
git merge add_classics -m "F: merge add_classics"
git log --oneline --graph --all

# 3. Clean up
git branch -d add_classics

# 4. Try a fast-forward merge
git switch -c update_titles
# (edit titles.md)
git add titles.md
git commit -m "G: update titles"
git switch main
git merge update_titles          # fast-forward, no merge commit
git branch -d update_titles
```

---

## Rebase

Rebase is the most debated topic in git. Many developers misuse or avoid it because they don't understand it. Used correctly, it keeps your history clean and linear.

### What rebase does

Instead of creating a merge commit, rebase **replays** your branch's commits on top of another branch:

1. Identifies the latest commit on the target branch (e.g. `main`)
2. Replays each of your branch's commits onto that new base
3. Updates your branch pointer to the end of the replayed commits

```bash
git switch <your-branch>
git rebase main
```

The result: a linear history that looks as if you branched off the latest `main`, even if `main` had new commits since you branched.

### Rebase vs. Merge

| | Merge | Rebase |
|---|---|---|
| **History** | Preserves true branching history | Creates linear history |
| **Merge commits** | Creates them | Avoids them |
| **Readability** | Cluttered on busy projects | Clean, easy to follow |
| **Safety** | Safe on any branch | **Never rebase public/shared branches** |

### The golden rule

**Never rebase a branch that other people have checked out.** If someone else is working on the same branch, rewriting its history will cause headaches. Only rebase your own local branches.

### Rebase workflow example
```bash
# 1. Create a branch from an older commit
git switch -c update_dune <commit-hash>   # branch off an earlier point

# 2. Add some commits
# (edit quotes/dune.md)
git add quotes/dune.md
git commit -m "H: update dune quotes"
# (edit again)
git add quotes/dune.md
git commit -m "I: more dune quotes"

# 3. Rebase onto main
git rebase main
git log --oneline --graph --all    # observe the clean linear history
```

---

## Reset

Reset is how you undo things in git. It moves the branch pointer (and `HEAD`) backwards.

### `--soft` — undo commit, keep changes staged
```bash
git reset --soft <commit-hash>
```
Your files are untouched. The changes from the undone commits remain staged, ready to be re-committed differently.

### `--mixed` (default) — undo commit and unstage
```bash
git reset <commit-hash>
```
Your files are untouched, but changes are unstaged. You'll need to `git add` again before committing.

### `--hard` — undo everything
```bash
git reset --hard <commit-hash>
```
**Dangerous.** This discards all changes — committed, staged, and in your working directory. If you delete a file and then `reset --hard` past the commit that added it, that file is gone.

### Quick reference

| Mode | Moves HEAD | Unstages | Removes file changes |
|------|-----------|----------|---------------------|
| `--soft` | ✓ | ✗ | ✗ |
| `--mixed` | ✓ | ✓ | ✗ |
| `--hard` | ✓ | ✓ | ✓ |

---

## Remotes

A **remote** is a copy of the repository hosted elsewhere (typically on GitHub). The convention is to call the primary remote `origin`.

### Adding a remote
```bash
git remote add origin <url>
git remote get-url origin          # verify
```

### Fetch — download remote data
```bash
git fetch origin                   # download objects and refs, doesn't change your files
```

After fetching, remote branches are available as `origin/<branch>` but aren't merged into your local branches yet.

### Merge remote into local
```bash
git merge origin/main              # merge fetched data into your current branch
```

### Push — upload local changes
```bash
git push origin main                              # push main to remote
git push origin <local>:<remote>                  # push to a differently named remote branch
git push origin :<remote-branch>                  # delete a remote branch
```

### Pull — fetch + merge in one step
```bash
git pull origin main               # equivalent to fetch + merge
git pull origin main --rebase      # fetch + rebase instead of merge
git pull origin main --no-rebase   # explicitly merge (if your default is rebase)
```

### Clone — copy a remote repo locally
```bash
git clone git@github.com:<username>/<repo>.git
```

---

## GitHub

GitHub is the most popular git hosting service (alternatives include GitLab, Codeberg, Gitea, and self-hosting).

It provides cloud backup, collaboration tools, and a public portfolio for your work. GitHub also has a CLI (`gh`) which is quite useful.

### Creating a repo on GitHub

1. Create a new repository on GitHub (leave it blank)
2. In your local repo, add it as a remote:
```bash
git remote add origin git@github.com:<username>/research.git
git ls-remote       # verify the connection
```
3. Push your work:
```bash
git push origin main
```

### Useful listing commands
```bash
git ls-remote                 # list remote refs
git ls-files                  # list files in the index
git ls-tree <tree-hash>       # list contents of a tree object
git remote show origin        # detailed remote info
```

---

## Pull Requests

If everyone pushes directly to `main`, you'll end up with a broken codebase. **Pull requests (PRs)** are GitHub's mechanism for proposing, reviewing, and merging changes.

### Workflow

1. Create a feature branch and make your changes:
```bash
git switch -c add_classics
# (make changes)
git add .
git commit -m "L: add classic films"
```

2. Push the branch to GitHub:
```bash
git push origin add_classics
```

3. On GitHub: **Pull Requests → New Pull Request**
4. Set base branch to `main`, compare branch to `add_classics`
5. Create the pull request — describe your changes
6. A teammate reviews and approves
7. Merge the PR on GitHub
8. Clean up:
```bash
# Delete the remote branch on GitHub (or use the GitHub UI)
git switch main
git pull origin main                # get the merged changes
git branch -d add_classics          # delete local branch
```

---

## Gitignore

A `.gitignore` file tells git which files and directories to ignore. You should always commit your `.gitignore`.

### What to ignore

1. **Generated files** — compiled code, build output, `.html` from pandoc
2. **Dependencies** — `node_modules/`, `venv/`, packages
3. **Environment-specific files** — editor configs, OS files
4. **Sensitive data** — passwords, API keys, `.env` files

### Syntax

```gitignore
# Ignore a directory anywhere in the tree
node_modules

# Ignore by extension
*.txt

# Rooted pattern — only matches at .gitignore level
/main.c

# Negation — keep a specific file that would otherwise be ignored
!keep.txt

# Ignore directory contents except one file
temp/*
!temp/instructions.md
```

### Nested `.gitignore`

You can place `.gitignore` files in subdirectories. Patterns in deeper `.gitignore` files only apply to that subtree.

### Workflow example
```bash
# Create some files we don't want tracked
mkdir secure
echo "root: 12345" > secure/passwords.txt
echo "# Lab Group" > labgroup.md

# Create .gitignore
touch .gitignore
# (add patterns: secure/, *.html, etc.)

git add .gitignore labgroup.md
git commit -m "M: add gitignore and lab group"
```

### Bonus: Pandoc

If you have Markdown files in your project, you can use [Pandoc](https://pandoc.org/) to convert them to HTML (or many other formats). This is a nice way to preview docs:

```bash
# Install
sudo apt install pandoc    # Linux/WSL
brew install pandoc        # macOS

# Convert
pandoc labgroup.md -o labgroup.html

# Open
xdg-open labgroup.html    # Linux
open labgroup.html         # macOS
explorer.exe labgroup.html # WSL
```

Since the generated `.html` is a build artifact, add `*.html` to your `.gitignore`.

---

## Part 2 — Interactive Session

*Coming soon — this section will contain the hands-on collaborative exercise we'll work through together.*

Head over to the **[Playground](playground.html)** to start practicing. There are challenges, merge conflicts, and a sandbox to break things in.

---

## Quick Reference

| Command | What it does |
|---------|-------------|
| `git init` | Initialize a new repo |
| `git status` | Show working tree status |
| `git add <file>` | Stage changes |
| `git commit -m "msg"` | Commit staged changes |
| `git log --oneline --graph --all` | Visualize commit history |
| `git branch <name>` | Create a branch |
| `git switch <name>` | Switch branches |
| `git switch -c <name>` | Create and switch |
| `git merge <branch>` | Merge a branch into current |
| `git rebase <branch>` | Rebase current onto branch |
| `git reset --soft HEAD~` | Undo last commit, keep staged |
| `git reset --hard HEAD~` | Undo last commit, discard all |
| `git remote add origin <url>` | Add a remote |
| `git fetch origin` | Download remote data |
| `git pull origin main` | Fetch and merge |
| `git push origin main` | Push to remote |
| `git clone <url>` | Clone a remote repo |
| `git cat-file -p <hash>` | Inspect an object |

---

*Workshop by Hans-Kristian Erik Östlund — March 2026*
