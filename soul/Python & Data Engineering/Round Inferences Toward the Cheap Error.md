---
tags: [data-engineering, data-quality, migration, defaults]
source: [claude-conversation, 2026-08-25]
---
# Round Inferences Toward the Cheap Error

When you have to reconstruct metadata that was never recorded, the two ways of being wrong almost never cost the same. Pick the direction whose error is cheap and recoverable — not the direction most likely to be exactly right.

## The asymmetry
Accuracy is the wrong objective when one error is reversible and the other is not. A reconstruction that is right 95% of the time but fails destructively is worse than one that is right 60% of the time and fails into wasted space.

Worked example — backfilling the date range a historical data load covered, where only the range's END was ever stored:
- **Overstate the range** → the load looks wider than it was → a later pass refuses to supersede it → some redundant rows survive. Costs disk. Reversible.
- **Understate the range** → the load looks narrower than it was → a later pass believes it re-read everything and deletes rows it never fetched. **Unrecoverable.**

So the rule for the unknown case is the deliberately generous envelope, even though it is usually too wide.

## Model unknown as unknown
Do not fill a gap with a plausible default — a guessed value is indistinguishable from a recorded one the moment it is written, and every later reader inherits the guess as fact. Leave it NULL and give NULL its own branch. "Unknown extent" must mean *refuse the destructive action*, never *assume the convenient extent*.

The corollary for destructive operations generally: **absence of evidence is not permission.** Deletion, GC, cache invalidation and retention sweeps should all require positive proof that the thing is redundant, not merely the failure to prove it is needed.

## When to use
Backfilling a metadata column added after the fact; choosing defaults for config a caller omitted; inferring types or schemas from samples; any retention, compaction or cleanup decision. Ask which way the mistake falls before asking how often it happens.

A useful tell that you have this backwards: the conservative branch feels wasteful. Wasteful is the point — it is what you are buying instead of an unrecoverable delete.

## Related
[[A Constraint Is Only as Strong as Its Key]] · [[Vacuous Checks (Why a Passing Reconciliation Can Prove Nothing)]] · [[Idempotent-Skip as the Only Recovery Mechanism]]
