---
tags: [tooling, git, remotes, branches]
source: [rin project, 2026-10]
---
# Delete a Remote Branch Only After Its Commits Are in main

A remote branch lives in two places: on the server, and as a remote-tracking ref (`origin/<name>`) in every clone. Deleting it on the server is the part other people and other machines can feel, and your local reflog does not cover it.

## Commands
- `git fetch --prune origin` refreshes the remote-tracking refs and drops those whose branch no longer exists on the server. Run it first so you are looking at the truth.
- `git branch -r` lists what the server has.
- `git push origin --delete <name>` deletes it on the server.
- `git branch -a` afterwards should show only `main` plus `remotes/origin/main` (and the `HEAD` pointer).

## The check before you delete
Is every commit already in `main`? `git merge-base --is-ancestor origin/<name> main && echo merged`. If that is false, run `git cherry main origin/<name>` (see [[A Rebased Branch Looks Unmerged - git cherry Compares Patches Not Commits]]). A `+` line is work nothing else holds.

## Example
Three remote branches were fully merged and were deleted after `main` had been pushed, in that order. A fourth, a local-only branch, never reached the remote at all, so for it deletion removed the only copy (see [[What Git Can Undo and What It Cannot]]).

## Gotchas
- Push `main` first. Then "merged into main" is true on the server too, not just in your clone.
- A branch can be local-only. `git branch -vv` shows `[origin/...]` when it tracks one; no bracket means the work exists nowhere else.
- Hosting services sometimes keep a closed pull request's branch restorable in the UI; do not rely on it.

## Related
[[Git Usage MOC]] · [[Git Branch Deletion - -d Refuses Unmerged Work, -D Does Not]] · [[Solo Repository Workflow - Commit to main, Fetch First, Rebase Never Force]]
