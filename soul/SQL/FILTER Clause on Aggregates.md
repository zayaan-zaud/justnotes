---
tags: [sql, postgresql, aggregates, query-features]
source: [PostgreSQL 18 docs — Aggregate Expressions]
---
# FILTER Clause on Aggregates

`aggregate_function(expr) FILTER (WHERE condition)` restricts which rows feed *that one aggregate*, letting several differently-scoped aggregates share a single `GROUP BY` pass instead of each needing its own `CASE` wrapper or its own query.

## Syntax
```sql
SELECT
    count(*) AS unfiltered,
    count(*) FILTER (WHERE i < 5) AS filtered
FROM generate_series(1,10) AS s(i);
```
Both aggregates scan the same grouped rows; `FILTER` just narrows the input to the second one. Equivalent to `count(CASE WHEN i < 5 THEN 1 END)`, but reads as intent ("count rows meeting this condition") rather than a workaround.

## When to use
- **Any time you're computing several conditional sums/counts in one `GROUP BY`** — e.g. a reconciliation rollup that needs `SUM(amount) FILTER (WHERE movement_type = 'provision_raise')` next to `SUM(amount) FILTER (WHERE movement_type = 'provision_release')` in the same row, rather than a `CASE WHEN movement_type = 'provision_raise' THEN amount END` inside `SUM()`. `FILTER` is the more direct expression of "this aggregate, scoped to this subset" — reach for it by default over a `CASE`-inside-aggregate whenever the condition is a simple `WHERE`-shaped filter rather than a value transformation.
- `NULL`-safe: rows failing the filter are excluded from that aggregate's input entirely (not counted as 0 or NULL contributing to a sum) — same semantics as a separate query filtered the same way, just computed in one pass.
- Works with ordered-set aggregates too, and with aggregate-based window functions — but not with pure window functions like `ROW_NUMBER()` that aren't aggregates.

## Related
[[GROUPING SETS, ROLLUP, and CUBE]] · [[DISTINCT ON (PostgreSQL)]]
