---
tags: [tooling, git, staging]
source: [rin project, 2026-10]
---
# Split One File Across Two Commits - Hand-Edit a Patch and git apply --cached

When a single file holds edits that belong to two different commits, the usual tool is `git add -p`. It is interactive, so it does not work in a script, a CI step or an agent that cannot answer prompts. The non-interactive equivalent is: write the file's diff to a patch, keep only the hunks for this commit, and apply that patch to the index.

## Recipe
1. `git diff -U2 FILE > /tmp/file.patch` (small context keeps hunks separate).
2. `grep -n '^@@' /tmp/file.patch` lists the hunks. The first four-ish lines are the header (`diff --git`, `index`, `---`, `+++`).
3. Build a new patch: the header lines plus only the hunks wanted.
   `{ sed -n 1,4p file.patch; sed -n '18,$p' file.patch; } > wanted.patch`
4. `git apply --cached --recount wanted.patch` puts only those hunks in the index; the working file is untouched. `--recount` recomputes the line counts in the `@@` headers, which you broke by deleting hunks.
5. `git diff --cached --stat` to confirm what is staged, then commit. The remaining hunks are still unstaged for the next commit.

## Example
One document held a rule added for change A and a line added for change B. Applying the second hunk alone gave `CLAUDE.md | 3 ++-` in the first commit, and the other hunk went into the second commit with its own files.

## Gotchas
- Keep hunks intact. If two edits sit in one hunk, split it with `-U0` or fall back to editing the file in two passes (revert one edit, commit, restore it).
- Verify with `--stat` before committing; a hunk applied against the wrong header produces an error, but a wrong choice of hunk does not.
- Works the same for `git restore --staged`-style undo: `git apply --cached -R patch` removes exactly those hunks from the index.

## Related
[[Git Usage MOC]] · [[One Logical Change per Commit, With the Evidence in the Body]] · [[Stage Explicit Paths - git add -A --dry-run Shows What a Sweep Would Take]]
