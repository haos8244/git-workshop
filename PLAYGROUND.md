# git Playground

> A safe space to break things, fix them, and learn git by doing.

This page is yours to mess around with. The whole point is to practice, make changes, commit them, push them, open PRs, cause merge conflicts on purpose. Nothing here is precious.

**Workshop page:** [Back to the main guide](index.html)

---

## Getting Started

Clone the repo and create your own branch:
```bash
git clone git@github.com:<username>/research.git
cd research
git switch -c your-name/playground
```

---

## Challenge 1: Your First Contribution

Add yourself to the participants list below, commit, push, and open a pull request. Make sure nobody else is editing this file at the same time, coordinate with the group so you each go one at a time.

### Participants

| Name | Favourite MATLAB Function | Fun Fact |
|------|--------------------------|----------|
| Erik | `fft` | Made this workshop |
| *Your name here* | *???* | *???* |

---

## Challenge 2: The Full Workflow
 
Practice the core loop from the lecture: staging, committing, logging, branching, and merging.
 
1. Create a new branch from `main`
2. Create a file called `bios/your-name.md` and write a short bio
3. Stage and commit
4. Make a second edit and commit
5. Switch back to `main` and merge (this will be a fast-forward)
6. Clean up

---

## Challenge 3: Practice Rebase
 
Experience the difference between merge and rebase on your own branches.
 
1. Make sure `main` is up to date, then branch off it
2. Add a file and make 3 small commits
3. Switch to `main`, add a commit there so the histories diverge
4. Now rebase your branch onto the updated `main`
5. Notice the linear history, no merge commit. Compare what `git log` looks like versus if you had used `git merge`

---

## Challenge 4: Undo Your Mistakes
 
Practice the three flavours of reset. Run `git status` and `git log` after each step to see what changed.
 
```bash
# Setup — make a commit you regret
echo "oops" > mistake.txt
git add mistake.txt
git commit -m "this was a bad idea"
 
# Soft reset — undo the commit, changes stay staged 
# Re-commit so we can try the next mode
git commit -m "bad idea round 2"
 
# Mixed reset (default) — undo commit AND unstage
# Re-commit one more time
git add mistake.txt
git commit -m "bad idea round 3"
 
# Hard reset — nuclear option, everything gone

---

## Challenge 5: Gitignore + Pandoc
 
1. Edit this file (`PLAYGROUND.md`) — add anything you want in the Free Space section below
2. Convert it locally with Pandoc:
```bash
pandoc PLAYGROUND.md --standalone --css=style.css -o playground.html
```
3. Open it in your browser:
```bash
xdg-open playground.html    # Linux
open playground.html         # macOS
explorer.exe playground.html # WSL
```
4. Check that `playground.html` is ignored by `.gitignore` — it should not show up in `git status`
5. Commit your markdown changes, push, and watch GitHub Actions deploy it automatically

---

## Super Challenge **BONUS**: Edit the Same File

Find a partner. Both of you edit the paragraph below on separate branches, then try to merge. Welcome to your first merge conflict.

**The paragraph:** MATLAB is a programming language. It is used for things. Some people have opinions about it.

---

## Free Space

Go wild below this line. Add notes, doodles, ASCII art, MATLAB snippets, whatever you want. This is your sandbox.

```matlab
% starter code — do something fun with it
x = linspace(0, 2*pi, 100);
y = sin(x);
plot(x, y);
title('Hello from the git workshop!');
```

---

*Remember: you can always `git reset --hard HEAD~` your way out of trouble. And if even that fails, try `git reflog` to see what it does and how it works.*
