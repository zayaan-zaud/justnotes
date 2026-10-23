---
tags: [sql, postgresql, aggregates, query-features]
source: [PostgreSQL 18 docs — Table Expressions]
---
# GROUPING SETS, ROLLUP, and CUBE

All three compute multiple levels of aggregation **in one query pass** instead of `UNION ALL`-ing several separately-grouped `SELECT`s together — the SQL-native way to produce a rollup report (by month, by month+division, and a grand total) without hand-writing each level and unioning them.

## The three forms
- **`GROUPING SETS ((a,b), (a), ())`** — full control: name exactly which column combinations you want aggregated. `()` means the grand total (no columns).
- **`ROLLUP (a, b, c)`** — shorthand for the hierarchical prefixes: `(a,b,c)`, `(a,b)`, `(a)`, `()`. Fits data with a natural drill-down hierarchy (region → division → total).
- **`CUBE (a, b, c)`** — shorthand for **every** subset (the full power set): all 2ⁿ combinations. Fits genuine multidimensional "slice any way" reporting, at the cost of `2ⁿ` result groups.

```sql
SELECT month, division, SUM(amount)
FROM movements
GROUP BY ROLLUP (month, division);
-- one query, three levels: (month, division), (month) subtotal, () grand total
```

## Telling a real NULL from a subtotal NULL
A rolled-up row has `NULL` in the columns it didn't group by — but a real data `NULL` in that same column looks identical. `GROUPING(col)` disambiguates: returns `1` if `col` was **not** part of that row's grouping set (i.e. this is a subtotal), `0` if it genuinely was grouped (so a `NULL` there, if any, is real data). Always include `GROUPING()` columns in a rollup report if the grouped column can itself be legitimately `NULL` — otherwise a subtotal row and a "division unknown" row are indistinguishable.

## When to use
- Any reconciliation or reporting query that currently computes a monthly total, a monthly-by-division breakdown, and a grand total as three separate queries `UNION ALL`'d together — `ROLLUP` replaces all three with one `GROUP BY`, computed in a single pass over the data rather than three.
- `ROLLUP` for hierarchical drill-down (the common case); `GROUPING SETS` when the levels you want don't form a clean prefix hierarchy; `CUBE` only when you genuinely need every cross-combination, since it grows combinatorially and most reporting needs don't actually require all of them.

## Related
[[FILTER Clause on Aggregates]] · [[Periodic Snapshot Fact Tables]]
