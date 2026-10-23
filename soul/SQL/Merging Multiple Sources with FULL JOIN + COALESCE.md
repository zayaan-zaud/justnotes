---
tags: [sql, postgresql, joins, query-features, reconciliation]
source: [claude-conversation, 2026-08-16 — rin project reconciliation debugging session]
---
# Merging Multiple Sources with FULL JOIN + COALESCE

The general pattern for reconciling N independent sources that each may or may not have a row for a given key: `FULL JOIN` every source together on the shared key, then `COALESCE(a.key, b.key, c.key, ...)` to recover "whichever source actually had it" as a single merged key column. Powerful, but it fails in a small number of specific, recurring ways — worth knowing the failure modes as a set, not rediscovering each one by trial and error.

## The shape
```sql
FROM source_a a
FULL JOIN source_b b ON a.key = b.key AND a.period = b.period
FULL JOIN source_c c ON c.key = COALESCE(a.key, b.key) AND c.period = COALESCE(a.period, b.period)
```
Each successive join has to `COALESCE` across *every prior source*, not just the immediately preceding one — a row that only exists in `source_a` still needs `source_c` to find it by `a.key`, since `b.key` will be `NULL` for that row.

## Failure modes (each one a real, silent bug, not a syntax error)
- **Missing or wrong join predicate on a secondary key (e.g. period/date).** Joining on the business key alone lets a row match *any* period, not just its own — a value from one month silently "bleeds" into every other period that key appears in. The query still runs and returns plausible-looking numbers; nothing errors.
- **`COALESCE` on the wrong side of the join predicate.** `a.key = COALESCE(b.key, c.key)` is backwards — it coalesces two columns that are *both already know to not be the driving side*, rather than coalescing across the sources already joined. The correct form always coalesces the *already-joined* side, matched against the new table's own unqualified key.
- **`COALESCE(x, x)` — coalescing a column with itself.** A no-op that silently degrades the merge to matching on only one source instead of all prior ones. Easy to introduce when fixing the bug above under time pressure and grabbing the wrong second argument.
- **Fan-out from an unaggregated join side.** If any joined source isn't unique at the join key (e.g. a revision-log table with multiple rows per key per period), the `FULL JOIN` multiplies — one logical entity becomes N rows, and any `SUM()` downstream silently overcounts by exactly the duplication factor. Always verify (`GROUP BY key, period HAVING COUNT(*) > 1`) or pre-aggregate each source to its true grain *before* joining, not after — don't rely on "it's probably unique."
- **A `CASE`/flag expression that only checks some of the sources.** When classifying "which sources have this row" (e.g. `CASE WHEN a.key IS NULL AND b.key IS NULL THEN 'missing' ELSE 'present' END`), every branch needs to account for every source — a branch that only tests two of three sources will silently misclassify rows where only the untested source has data.

## When to use
- Reconciling N sources that should agree on the same business event (a ledger, a source report, a manual schedule) where you need to see rows even when only some sources have them — this is the shape a `FULL JOIN` chain is actually for, as opposed to an [[Anti-Join Performance (LEFT JOIN + IS NULL vs NOT EXISTS)|anti-join]], which only answers "is it missing," not "here are all four sources' values side by side."
- Before trusting any output from this pattern: verify each source's grain first (pre-aggregate if needed), verify every join predicate compares like-truncated keys on every dimension (not just the primary business key), and verify the classification `CASE` tests every source, not a subset.

## Related
[[Anti-Join Performance (LEFT JOIN + IS NULL vs NOT EXISTS)]] · [[Grain]] · [[Consolidated Fact Tables]]
