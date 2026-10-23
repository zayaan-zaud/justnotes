---
tags: [tooling, git, commits]
source: [rin project, 2026-10]
---
# One Logical Change per Commit, With the Evidence in the Body

A commit should be one change a reader can understand, revert or cherry-pick on its own. The message should say what changed and what was observed, not only what was typed. For a data project the history is the incident record: the commit body is where the numbers that justified a change live.

## Shape
- **Subject:** short, imperative, says the effect ("Marts are posted-only: filter unposted rows from the register-based facts").
- **Body:** the reason and the evidence. Row counts removed, tie rates before and after, what was explicitly not covered. "Removed 291 and 650 unposted rows", "ties on 30 of 32 months, two do not" are worth more than "fix filters".
- **Trailers** your tooling requires (co-author lines) go last.
- **Scope:** if the working tree holds two independent changes, make two commits. When they touch the same file, split by hunk (see [[Split One File Across Two Commits - Hand-Edit a Patch and git apply --cached]]).

## Example
A model rewrite and a filter change were done in one session. They became two commits: one retiring a workbook loader and deriving a table from another source, one making register facts posted-only. Each body carried its before/after counts, so either could be reverted without touching the other.

## Gotchas
- Do not put speculation in a commit message. If a cause is unexplained, say "cause not established" rather than naming a likely one.
- Write the message after the checks ran, so the numbers in it are the verified ones.
- A commit that changes the shape of data should also say what must be rebuilt downstream (an image, a materialised table).

## Related
[[Git Usage MOC]] · [[Stage Explicit Paths - git add -A --dry-run Shows What a Sweep Would Take]] · [[Solo Repository Workflow - Commit to main, Fetch First, Rebase Never Force]]
