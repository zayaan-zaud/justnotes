---
tags: [data-engineering, reconciliation, etl]
source: [rin project docs, 2026-08]
---
# Two-Stage Reconciliation (Existence Before Amounts)

When tying two systems that record the same economic event (e.g. a subledger and a general ledger), split the check into two stages instead of computing one combined variance: **Stage 1** — which rows exist on only one side; **Stage 2** — of the rows that exist on both sides, do the amounts match. Mixing the two makes any single variance number meaningless, since a missing row and an amount mismatch both surface as unexplained noise with no way to tell them apart.

## Key points
- Run existence-matching first (an anti-join each direction — see [[Anti-Join Performance (LEFT JOIN + IS NULL vs NOT EXISTS)]]) and resolve or explain every unmatched row before comparing amounts on the matched set.
- **Reconcile on amounts, never on line counts.** Two systems can tie exactly on money while disagreeing completely on row counts, because one books a single total line while the other splits the same total across N rows (e.g. one line per participating agent on a shared payment). Line-count parity is not evidence a join is right, and a line-count mismatch is not evidence it's wrong.
- **The mirrored-variance diagnostic:** a large matched-side variance in one direction, mirrored by an equal-and-opposite bucket elsewhere, is the signature of aggregating at the wrong grain — not a real finding. If it looks too symmetric to be random noise, check the grain before chasing the numbers.

## When to use
Any GL-vs-subledger tie-out, any two-source reconciliation, any join between systems that don't share a physical key.

## Related
[[Empirical Grain Discovery (Candidate-Key Cardinality Probing)]] · [[Anti-Join Performance (LEFT JOIN + IS NULL vs NOT EXISTS)]]
