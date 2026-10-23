---
tags: [tooling, git, branches, rebase]
source: [rin project, 2026-10]
---
# A Rebased Branch Looks Unmerged - git cherry Compares Patches Not Commits

Rebasing rewrites commits: same change, new commit IDs. So a branch whose work was rebased onto `main` and merged still shows commits that `main` does not contain by ID. `git branch --merged`, `git rev-list main..<branch>` and `git branch -d` all compare IDs and call it unmerged. `git cherry main <branch>` compares the patch each commit makes, so it sees through the rewrite.

## How to read it
`git cherry main <branch>` prints one line per commit on `<branch>` that `main` lacks by ID:
- `-` : an equivalent patch is already in `main` (rebased or cherry-picked). Nothing to lose.
- `+` : no equivalent in `main`. This commit is real, unmerged work.

Count them: `git cherry main <branch> | grep -c '^+'`. Zero means the branch can go.

## Example
`git rev-list --count main..architecture/deepen-ingestion-seams` said 17 commits ahead. `git cherry` said 0 with `+`, 17 with `-`: every one was already in `main` after a rebase, so deleting it lost nothing. A backup branch showed 37 commits ahead by ID, but only one carried a patch `main` lacked (a stale docs rewrite): the one real difference, found in seconds.

## Gotchas
- Squash merges defeat it in the other direction. If five commits were squashed into one on `main`, `git cherry` shows five `+` even though the content landed. Check the diff, not the count (`git diff main <branch> -- <path>` or look at the squash commit).
- Patch matching is exact. A commit that was edited during a rebase (conflict resolution, reworded diff) will show `+` although most of it landed. Read the `+` commits before calling them lost.
- It tells you about commits, not about files that were never committed.

## Related
[[Git Usage MOC]] · [[Git Branch Deletion - -d Refuses Unmerged Work, -D Does Not]] · [[Solo Repository Workflow - Commit to main, Fetch First, Rebase Never Force]]
