---
tags: [sql, performance, postgresql]
source: [rin project docs, 2026-08]
---
# Anti-Join Performance (LEFT JOIN + IS NULL vs NOT EXISTS)

Finding rows in table A that have no matching row in table B ("anti-join") can be written as a correlated `NOT EXISTS` subquery or as a `LEFT JOIN ... WHERE b.key IS NULL`. On some planners and data shapes these are not equally cheap: a correlated `NOT EXISTS` against a large table can execute as a per-row subquery scan, while the `LEFT JOIN` form lets the planner use a single hash or merge join against a deduplicated set.

## Measured example
A correlated `NOT EXISTS` against a ~400k-row table didn't finish in 46 minutes. The equivalent `LEFT JOIN (SELECT DISTINCT ...) ON ... WHERE b.key IS NULL` returned in 0.48 seconds — roughly five orders of magnitude faster — and the gap widened further on a 3.6M-row table.

```sql
-- can be slow: correlated subquery, may not hash-join well
SELECT a.*
FROM a
WHERE NOT EXISTS (SELECT 1 FROM b WHERE b.key = a.key);

-- often much faster: plain join the planner can hash
SELECT a.*
FROM a
LEFT JOIN (SELECT DISTINCT key FROM b) b ON b.key = a.key
WHERE b.key IS NULL;
```

## When to use / gotchas
- Always check `EXPLAIN ANALYZE` on both forms for a large anti-join before assuming either is faster — the gap is planner- and data-shape-dependent, not a universal law, but it's large and common enough to be worth trying the rewrite whenever a `NOT EXISTS`/`NOT IN` anti-join against a large table is slow.
- `NOT IN` has an additional correctness trap (a NULL in the subquery result makes the whole condition unmatchable) that neither `NOT EXISTS` nor `LEFT JOIN`/`IS NULL` share — prefer one of those two over `NOT IN` regardless of performance.

## Related
[[CREATE INDEX (PostgreSQL)]] · [[Two-Stage Reconciliation (Existence Before Amounts)]]
