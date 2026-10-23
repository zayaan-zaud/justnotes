---
tags: [sql, postgresql, window-functions]
source: [PostgreSQL 18 docs — Window Functions]
---
# LAG and LEAD

`LAG(value, offset, default)` and `LEAD(value, offset, default)` read a value from a row *offset* positions before/after the current row, within the same partition — the direct SQL expression of "what was this same entity's value last time" without a self-join.

## Syntax
```sql
LAG(balance, 1) OVER (PARTITION BY claim_key ORDER BY effective_date)
```
Returns the `balance` from the previous row (by `ORDER BY`) in the same `claim_key` partition. `offset` defaults to 1; `default` (returned when there's no such row, e.g. the first row in a partition) defaults to `NULL`. `LEAD` is the mirror image, reading forward instead of back. Both always respect NULLs in the underlying data as real values (Postgres doesn't support the standard's `IGNORE NULLS` option) — a `NULL` in the target column is returned as-is, not skipped over to find the next non-null row.

## The recurring pattern: deriving a movement from a running balance
Many sources expose a **running balance** per revision (e.g. a reserve's current total after each update) rather than the movement itself. `LAG()` recovers the movement:
```sql
balance - LAG(balance) OVER (PARTITION BY key ORDER BY effective_date) AS movement
```
This is the general form of a pattern that shows up constantly in claim/ledger reconciliation: a source column that *looks* additive (safe to `SUM()` across revisions) but is actually a balance, where summing it directly double-counts every prior revision's position. `LAG()`-derived movement is the fix — and the tell that you need it is discovering that summing a column across a key's history produces a wildly inflated total relative to any external total it should tie to.

## When to use
- Any "compare this row to the previous/next row for the same entity" computation: period-over-period change, detecting a value that changed since last time, computing a duration between consecutive events.
- Get `ORDER BY` right inside the `OVER` clause — `LAG` reads "previous by this ordering," so ordering on the wrong column (a mostly-`NULL` date column instead of a fully-populated one, for instance) silently picks an arbitrary "previous" row rather than raising an error.
- The first row in each partition has no predecessor — decide deliberately whether the resulting `NULL` (or explicit `default`) should be treated as "zero movement" or "unknown, don't include this in a sum," since those have different correct handling and a bare `COALESCE(..., 0)` isn't always right (a claim's first-ever revision isn't a zero movement, it's the movement, and its balance IS the true first-period value).

## Related
[[Window Functions (Overview)]] · [[ROW_NUMBER, RANK, and DENSE_RANK]] · [[Late Arriving Facts]]
