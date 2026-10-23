---
tags: [tooling, git, recovery, safety]
source: [rin project, 2026-10]
---
# What Git Can Undo and What It Cannot

Before deleting anything, ask what undoes it. Git is very forgiving about committed history and not forgiving at all about anything it never saw.

## Recoverable (for a while)
- **A deleted branch**: its tip commit still exists until git prunes unreachable objects. Note the SHA that `git branch -d/-D` prints; `git branch <name> <sha>` restores it. `git reflog` also lists recent tips if you lost the SHA.
- **A bad commit, reset or rebase**: the old tip is in the reflog; `git reset --hard <sha>` or a new branch on that SHA brings it back.
- **A deleted file that was committed**: `git restore --source=<commit> -- path`.
- **Time limit**: unreachable commits are garbage-collected eventually (reflog entries and unreachable objects expire after weeks to a few months by default). "Recoverable" means soon, not forever.

## Not recoverable
- **Untracked files** (never added). `rm` is final; there is no commit to restore from. Scratch notebooks, local notes, generated caches.
- **Uncommitted edits** to tracked files, once overwritten (`git checkout -- file`, `git restore`, `git reset --hard`).
- **A local-only branch's work after the objects are pruned**, and a force-pushed-over or deleted remote branch that nobody else has cloned.
- **Stashes dropped or lost with their repository state.** Note that a stash survives deleting the branch it was made on (it keeps the label "On <branch>"), so branch cleanup does not clean stashes.

## Practical rules
- Record the tip SHA of anything you delete, in the commit message, the notes, or the chat.
- Before deleting an untracked file, read it. Deleting a stale cache is fine; deleting someone's half-written notebook is not.
- Prefer moving over deleting for files outside git (an `_unused/` folder), since moving is undoable.

## Related
[[Git Usage MOC]] · [[Git Branch Deletion - -d Refuses Unmerged Work, -D Does Not]] · [[Delete a Remote Branch Only After Its Commits Are in main]]
