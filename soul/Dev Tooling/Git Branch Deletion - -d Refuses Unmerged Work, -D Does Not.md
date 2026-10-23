---
tags: [tooling, git, branches]
source: [rin project, 2026-10]
---
# Git Branch Deletion - -d Refuses Unmerged Work, -D Does Not

`git branch -d <name>` deletes a branch only if every commit on it is already reachable from where git compares it (its upstream if one is set, otherwise `HEAD`). If any commit would be left with no branch pointing at it, git refuses. `git branch -D <name>` skips that check and deletes regardless.

## Example
Clearing out old branches on a solo repo: ten branches were plain ancestors of `main`, so `-d` removed them with no questions. Six others showed commits "not in main" and `-d` refused. Checking with [[A Rebased Branch Looks Unmerged - git cherry Compares Patches Not Commits]], one was a rebased copy of work already in `main` and one was a stale duplicate, so `-D` was safe for those. The rest held commits `main` lacked and were kept until the owner called them outdated, and only then removed with `-D`.

## When to use / gotchas
- Default to `-d`. Treat a refusal as information: it is telling you the branch holds a commit nothing else holds. Reach for `-D` only after you have checked that the content is elsewhere, or the owner has said it is outdated.
- `git branch -d` prints the old tip (`was 84d3d7ee`). Write that SHA down before moving on: `git branch <name> <sha>` brings the branch back for as long as the commit still exists (see [[What Git Can Undo and What It Cannot]]).
- Run it from a branch that is not the one being deleted, and know which branch counts as "merged into": `-d` compares against the current `HEAD` when no upstream is set, so run it from `main`.
- This deletes the local pointer only. A branch that was pushed also exists on the remote; see [[Delete a Remote Branch Only After Its Commits Are in main]].

## Related
[[Git Usage MOC]] · [[A Rebased Branch Looks Unmerged - git cherry Compares Patches Not Commits]] · [[What Git Can Undo and What It Cannot]]
