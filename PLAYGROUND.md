# git Playground

> A safe space to break things, fix them, and learn git by doing.

This page is yours to mess around with. The whole point is to practice — make changes, commit them, push them, open PRs, cause merge conflicts on purpose. Nothing here is precious.

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

Add yourself to the participants list below, commit, push, and open a pull request.

### Participants

| Name | Favourite MATLAB Function | Fun Fact |
|------|--------------------------|----------|
| Erik | `fft` | Made this workshop |
| *Your name here* | *???* | *???* |

---

## Challenge 2: Edit the Same File

Find a partner. Both of you edit the paragraph below on separate branches, then try to merge. Welcome to your first merge conflict.

**The paragraph:** MATLAB is a programming language. It is used for things. Some people have opinions about it.

---

## Challenge 3: The Rebase Gauntlet

1. Branch off `main`
2. Add a new file called `your-name.md` with a short bio
3. Make 3 small commits (one sentence each)
4. Meanwhile, someone else will push a commit to `main`
5. Rebase your branch onto the updated `main`
6. Push and open a PR

---

## Challenge 4: Undo Your Mistakes

Practice the three flavours of reset:
```bash
# Make a commit you regret
echo "oops" > mistake.txt
git add mistake.txt
git commit -m "this was a bad idea"

# Try each reset mode — observe what changes
git reset --soft HEAD~     # undo commit, changes stay staged
git commit -m "bad idea round 2"
git reset HEAD~            # undo commit, changes unstaged
git add . && git commit -m "bad idea round 3"
git reset --hard HEAD~     # nuclear option — everything gone
```

---

## Challenge 5: Pandoc Workflow

1. Edit this file (`PLAYGROUND.md`) — add anything you want below
2. Convert it locally:
```bash
pandoc PLAYGROUND.md --standalone --css=style.css -o playground.html
open playground.html  # or xdg-open / explorer.exe
```
3. Check that `playground.html` is ignored by `.gitignore`
4. Commit your markdown changes, push, and watch GitHub Actions deploy it automatically

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

*Remember: you can always `git reset --hard HEAD~` your way out of trouble. And if even that fails, `git reflog` has your back.*
