# Git Cheatsheet

Day-to-day Git, grouped by what you're trying to do. Commands run in your repo's root unless noted. Beginner basics up top, recovery and surgery toward the bottom.

## Config

Set the identity stamped on every commit (do this once, globally):

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

Make `main` the default branch name for new repos:

```bash
git config --global init.defaultBranch main
```

Set your editor for commit messages and rebases:

```bash
git config --global core.editor "code --wait"
```

Useful aliases that save keystrokes all day:

```bash
git config --global alias.st status
git config --global alias.lg "log --oneline --graph --decorate"
```

See where a setting comes from (which file set it):

```bash
git config --show-origin user.email
```

## Staging and committing

See what's changed and what's staged:

```bash
git status
```

Stage specific files (only these go into the next commit):

```bash
git add main.py routes.py
```

Stage everything that's tracked or new under the current path:

```bash
git add .
```

Interactively stage only some hunks of a file:

```bash
git add -p
```

Commit staged changes with a message:

```bash
git commit -m "Add JWT auth to /api/login"
```

Stage all tracked changes and commit in one step (skips new untracked files):

```bash
git commit -am "Fix off-by-one in pagination"
```

Add to the previous commit without changing its message:

```bash
git commit --amend --no-edit
```

Unstage a file but keep your edits:

```bash
git restore --staged main.py
```

## Branches

List local branches (the current one is starred):

```bash
git branch
```

Create a new branch and switch to it:

```bash
git switch -c feature/rate-limiter
```

Switch to an existing branch:

```bash
git switch main
```

Rename the branch you're on:

```bash
git branch -m better-name
```

Delete a merged branch (use `-D` to force-delete an unmerged one):

```bash
git branch -d feature/rate-limiter
```

Create a local branch tracking a remote one:

```bash
git switch -c fix/login origin/fix/login
```

## Merging vs rebasing

Merge another branch into your current one (keeps both histories, adds a merge commit):

```bash
git merge feature/rate-limiter
```

Merge with no fast-forward so the merge commit always shows up:

```bash
git merge --no-ff feature/rate-limiter
```

Rebase your branch onto the latest main (replays your commits on top, linear history):

```bash
git switch feature/rate-limiter
git rebase main
```

Rebase to clean up your own commits before opening a PR (squash, reword, reorder):

```bash
git rebase -i HEAD~3
```

Rule of thumb: rebase your own local work to keep history clean; merge shared branches so you don't rewrite history other people have pulled.

Abort a rebase that's gone sideways and return to where you started:

```bash
git rebase --abort
```

## Remotes (fetch / pull / push)

List your remotes and their URLs:

```bash
git remote -v
```

Add a remote named `origin`:

```bash
git remote add origin git@github.com:you/repo.git
```

Download remote changes without touching your working branch:

```bash
git fetch origin
```

Fetch and integrate the remote's changes into your branch:

```bash
git pull
```

Pull but rebase your local commits on top instead of merging:

```bash
git pull --rebase
```

Push your branch and set it to track the remote one (first push):

```bash
git push -u origin feature/rate-limiter
```

Push after rewriting history (rebase/amend), but refuse if someone else pushed first:

```bash
git push --force-with-lease
```

## Inspecting history

Compact, graphed log of recent commits:

```bash
git log --oneline --graph --decorate -20
```

Show commits that touched one file:

```bash
git log --oneline -- main.py
```

See unstaged changes:

```bash
git diff
```

See what's staged and about to be committed:

```bash
git diff --staged
```

Compare your branch against main:

```bash
git diff main...HEAD
```

Show a single commit's full change:

```bash
git show a1b2c3d
```

See who last changed each line of a file and in which commit:

```bash
git blame main.py
```

Search history for when a string was added or removed:

```bash
git log -S "SECRET_KEY" --oneline
```

## Undoing things

Discard uncommitted changes to a file (cannot be undone):

```bash
git restore main.py
```

Move the branch pointer back but keep changes staged:

```bash
git reset --soft HEAD~1
```

Move back and unstage changes, keeping them in your working tree (the safe default):

```bash
git reset HEAD~1
```

Throw away the last commit AND its changes entirely:

```bash
git reset --hard HEAD~1
```

Undo a commit by making a new commit that reverses it (safe on shared branches):

```bash
git revert a1b2c3d
```

Recover a commit you thought you lost (reflog tracks every HEAD move):

```bash
git reflog
git reset --hard HEAD@{2}
```

## Stashing

Shelve your uncommitted changes to switch tasks:

```bash
git stash
```

Stash including untracked files:

```bash
git stash -u
```

List stashes:

```bash
git stash list
```

Reapply the latest stash and remove it from the stack:

```bash
git stash pop
```

Apply a specific stash without dropping it:

```bash
git stash apply stash@{1}
```

## Tags

Create an annotated tag (carries a message and author, the right choice for releases):

```bash
git tag -a v1.2.0 -m "Release 1.2.0"
```

List tags:

```bash
git tag
```

Push a tag to the remote (tags don't push with a normal `git push`):

```bash
git push origin v1.2.0
```

Push all tags at once:

```bash
git push --tags
```

Delete a tag locally and on the remote:

```bash
git tag -d v1.2.0
git push origin :refs/tags/v1.2.0
```

## Resolving merge conflicts

See which files are conflicted:

```bash
git status
```

Open the file and look for the conflict markers, then edit to the final version:

```text
<<<<<<< HEAD
your version
=======
their version
>>>>>>> feature/rate-limiter
```

Mark a conflict resolved after editing:

```bash
git add main.py
```

Finish the merge once all conflicts are staged:

```bash
git commit
```

Keep one whole side without hand-editing (theirs is the branch being merged in):

```bash
git checkout --theirs main.py
git checkout --ours main.py
```

Bail out and go back to before the merge:

```bash
git merge --abort
```

## Cherry-pick

Apply a single commit from another branch onto your current one:

```bash
git cherry-pick a1b2c3d
```

Cherry-pick a range of commits (exclusive of the first):

```bash
git cherry-pick a1b2c3d..f4e5d6c
```

Cherry-pick but stage without committing, so you can tweak first:

```bash
git cherry-pick -n a1b2c3d
```

## Bisect

Find the commit that introduced a bug by binary search. Start it, mark a known-bad and known-good commit:

```bash
git bisect start
git bisect bad
git bisect good v1.1.0
```

For each commit Git checks out, test and tell it the result:

```bash
git bisect good   # bug not present here
git bisect bad    # bug is present here
```

Automate the search with a script that exits non-zero on the bug:

```bash
git bisect run ./test.sh
```

End the session and return to where you were:

```bash
git bisect reset
```

## Practice it live

Reading about Git is not the same as untangling a real merge conflict or running a bisect against a failing test. On [HeyDevJob](https://heydevjob.com) you fix real broken repos in live cloud workspaces from your browser - amend a bad commit, scrub a secret from history, bisect down to the breaking change. Free on the junior tier, no card, no setup.
