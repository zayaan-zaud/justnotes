---
tags: [tooling, git, python, uv, dependencies]
source: [rin project, 2026-10]
---
# A Dependency Change and Its Lockfile Belong in the Same Commit

When code starts to import a new package, three things must reach the repository together: the code, the dependency declaration (`pyproject.toml`), and the resolved lockfile (`uv.lock`). Commit the code without the declaration and a fresh clone fails on `import`. Commit the declaration without the lock and the lock is out of date, so two machines install different versions.

## Example
A new page used `marimo`. The one-line addition to `pyproject.toml` and the 383-line `uv.lock` change were sitting uncommitted in the working tree, left over from earlier. Committing only the new code would have broken a fresh checkout, so both files went in the same commit as the code that needed them. An unrelated edit in `settings.py` stayed out.

## Gotchas
- Before committing code that adds an import, run `git diff pyproject.toml` and ask whether the new package is in it. If the dependency edit is uncommitted, it belongs to this commit.
- A lockfile diff is large and mechanical; check that it only adds the package and its transitive dependencies, not unrelated version bumps.
- The reverse also holds: if a `pyproject.toml` change is not yours to ship, leave the code that depends on it out as well.

## Related
[[Git Usage MOC]] · [[uv (Python Package Manager)]] · [[Stage Explicit Paths - git add -A --dry-run Shows What a Sweep Would Take]]
