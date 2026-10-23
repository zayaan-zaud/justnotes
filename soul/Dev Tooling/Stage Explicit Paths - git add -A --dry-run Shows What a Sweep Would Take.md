---
tags: [tooling, git, staging, gitignore]
source: [rin project, 2026-10]
---
# Stage Explicit Paths - git add -A --dry-run Shows What a Sweep Would Take

`git add -A` stages everything: every modified file, every untracked file the ignore rules do not hide. That is rarely what a single commit should contain. Two habits keep a commit to what you meant: look first with a dry run, and stage by path.

## The habits
- **`git add -A --dry-run`** prints `add 'path'` for everything a real sweep would take, and changes nothing. Read it before any broad `git add`.
- **Stage explicit paths** for the actual commit: `git add path/one path/two`. Then `git diff --cached --stat` to confirm, then commit.
- **`.gitignore` is load-bearing.** A cleanup of the ignore file once exposed over a gigabyte of data files to a sweep. The dry run is how you notice before the commit instead of after.

## Example
At the end of a session the tree held the finished work plus the owner's own uncommitted edit to `settings.py`, a scratch notebook, its layout JSON and a cache folder. A dry run listed all of them; the commits named their files one by one, and the leftovers stayed uncommitted. The cache folder (`__marimo__/`) then got a one-line ignore rule in its own commit so it stopped appearing at all.

## Gotchas
- Tool caches and editor session folders (`__marimo__/`, `.ruff_cache/`, `__pycache__/`) are not "untracked work"; ignore them rather than dismissing them each time.
- Unrelated edits you are not ready to commit are not noise to hide: leave them modified, mention them, and do not revert them.
- Pair a dependency edit with its lockfile (see [[A Dependency Change and Its Lockfile Belong in the Same Commit]]).

## Related
[[Git Usage MOC]] · [[Split One File Across Two Commits - Hand-Edit a Patch and git apply --cached]] · [[Solo Repository Workflow - Commit to main, Fetch First, Rebase Never Force]]
