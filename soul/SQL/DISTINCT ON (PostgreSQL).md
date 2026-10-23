---
tags: [sql, postgresql, query-features]
source: [PostgreSQL 18 docs — SELECT]
---
# DISTINCT ON (PostgreSQL)

`SELECT DISTINCT ON (expr, ...)` keeps exactly one row per group of rows sharing the same value(s) of `expr` — a PostgreSQL-specific way to pick "one representative row per key" without an aggregate function or a window-function subquery.

## Syntax and the rule that makes it deterministic
```sql
SELECT DISTINCT ON (location) location, time, report
FROM weather_reports
ORDER BY location, time DESC;
```
Which row survives per group is **whichever sorts first under `ORDER BY`** — and `ORDER BY` must start with the same expression(s) as `DISTINCT ON`. Without a matching `ORDER BY`, "which row is first" is unspecified and the result is non-deterministic (it can silently change between runs). The `DISTINCT ON` columns set the grouping; the rest of `ORDER BY` sets which row wins within each group.

## Vs. plain DISTINCT and vs. GROUP BY
- **Plain `DISTINCT`** requires every selected column to match for two rows to collapse — it can't return "one row per key, plus other columns that vary."
- **`GROUP BY`** requires every non-grouped column to go through an aggregate function — it can tell you the max/count/latest *value* of a column but can't return the *whole row* that value came from without extra work (a self-join or a window function).
- **`DISTINCT ON`** does exactly that in one clause: return the whole row, picked deterministically, one per key.

## When to use
- The canonical "latest row per key" pattern — a claim's current revision, a customer's most recent order, a sensor's latest reading. This is the exact shape of "dedupe to the newest `_pull_date` per business key" that recurs constantly in incremental/dedup ETL logic.
- Prefer this over `ROW_NUMBER() OVER (PARTITION BY key ORDER BY ...) ... WHERE rn = 1` when you don't need the row number itself for anything else — `DISTINCT ON` is shorter and reads its intent more directly. Reach for the `ROW_NUMBER()` form instead when you need to keep more than the top row per group (e.g. top 3), since `DISTINCT ON` can only ever return one row per key.
- Get the `ORDER BY` direction right the first time — `DISTINCT ON (key) ... ORDER BY key, updated_at DESC` keeps the newest row; drop the `DESC` by mistake and it silently keeps the oldest instead, with no error to catch it.

## Related
[[FILTER Clause on Aggregates]]
