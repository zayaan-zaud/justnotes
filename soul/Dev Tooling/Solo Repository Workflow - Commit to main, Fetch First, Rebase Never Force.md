---
tags: [tooling, git, workflow]
source: [rin project, 2026-10]
---
# Solo Repository Workflow - Commit to main, Fetch First, Rebase Never Force

On a private repo with one author and no review step, the lightest workflow that stays safe is: work on `main`, commit in small logical pieces, `git fetch` before you commit or push, and when local and remote have diverged, rebase your commits on top. Never force-push to fix a divergence.

## Why each part
- **Straight to `main`, no feature branches.** A branch only helps if something needs isolating or reviewing. With one author and no pull requests it is just a second place for work to go stale. Old branches pile up and later need a cleanup pass (see [[Git Branch Deletion - -d Refuses Unmerged Work, -D Does Not]]).
- **Fetch first.** Even a solo repo changes on the server side: a pull request opened or merged in the GitHub UI, an edit made from another machine, a bot. `git fetch` then `git status -sb` shows `ahead N`/`behind N` without touching your files. Do it before committing a sweeping change and before pushing.
- **Rebase, not force.** If the remote has a commit you lack, `git rebase origin/main` replays yours on top and the push is a plain fast-forward. A force-push rewrites the remote's history and can silently discard the commit you had not seen.
- **Push when asked.** A commit is local and undoable; a push is published and other clones may pick it up. Commit and push are separate decisions.

## Gotchas
- A free-tier private repo on GitHub has no branch protection (the API returns 403 for the setting), so nothing stops a bad force-push except your own habit.
- Leave unrelated working-tree edits uncommitted rather than sweeping them in; see [[Stage Explicit Paths - git add -A --dry-run Shows What a Sweep Would Take]].
- After a clean push, `git status -sb` should read `## main...origin/main` with no ahead/behind counts. That is the check, not the push output.

## Related
[[Git Usage MOC]] · [[One Logical Change per Commit, With the Evidence in the Body]] · [[A Rebased Branch Looks Unmerged - git cherry Compares Patches Not Commits]]
