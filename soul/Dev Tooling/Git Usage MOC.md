---
tags: [tooling, git, moc]
source: [rin project, 2026-10]
---
# Git Usage MOC

Map of the git notes: how work gets from the working tree into history, how branches are kept tidy, and what can and cannot be undone. Written from a solo data-engineering repo; the mechanisms apply to any repo, and the choices (commit straight to `main`) are for repos with one author and no review step.

## Daily flow
- [[Solo Repository Workflow - Commit to main, Fetch First, Rebase Never Force]]: the default stance: `main` only, fetch first, rebase on divergence, push when asked.
- [[Stage Explicit Paths - git add -A --dry-run Shows What a Sweep Would Take]]: look before you sweep; stage by path.
- [[Split One File Across Two Commits - Hand-Edit a Patch and git apply --cached]]: non-interactive hunk splitting.
- [[A Dependency Change and Its Lockfile Belong in the Same Commit]]: the commit that keeps a fresh clone working.
- [[One Logical Change per Commit, With the Evidence in the Body]]: what a good message holds.

## Branch hygiene
- [[Git Branch Deletion - -d Refuses Unmerged Work, -D Does Not]]
- [[A Rebased Branch Looks Unmerged - git cherry Compares Patches Not Commits]]
- [[Delete a Remote Branch Only After Its Commits Are in main]]

## Safety
- [[What Git Can Undo and What It Cannot]]
- [[Open-Sourcing a Private Repo - Gitignore the Deployment, Publish the Code]]: what to untrack, move to config and verify before a repo goes public.

## Cleanup checklist (the whole pass in order)
1. `git fetch --prune origin`, then `git status -sb`.
2. List local branches with their state: `git rev-list --count main..<b>` and `git merge-base --is-ancestor <b> main`.
3. For each not-merged branch: `git cherry main <b>`; zero `+` means nothing would be lost.
4. Delete merged ones with `-d`; ones with zero `+` with `-D`; write down every tip SHA printed.
5. Decide the rest with the owner: they hold commits nothing else holds.
6. Push `main`, then `git push origin --delete` for merged remote branches, then `git branch -a` to confirm only `main` is left.
7. Check `git stash list`; stashes are not branches and survive this pass.

## Related
[[Paired Config Flags That Silently Fall Back]] · [[uv (Python Package Manager)]]
