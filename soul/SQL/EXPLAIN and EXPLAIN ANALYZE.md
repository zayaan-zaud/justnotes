---
tags: [sql, postgresql, performance, query-planning]
source: [PostgreSQL 18 docs — Using EXPLAIN]
---
# EXPLAIN and EXPLAIN ANALYZE

`EXPLAIN` shows the planner's chosen strategy and its *estimated* costs without running the query. `EXPLAIN ANALYZE` actually **executes** the query and reports real timing and row counts alongside the estimates — the only way to see where an estimate and reality diverge, which is exactly where a bad plan comes from.

## The critical safety rule for write queries
`EXPLAIN ANALYZE` on an `INSERT`/`UPDATE`/`DELETE`/`MERGE` really performs the write — the output being "just a plan" doesn't mean the data change didn't happen. Always wrap it in a transaction you roll back:
```sql
BEGIN;
EXPLAIN ANALYZE UPDATE claims SET status = 'closed' WHERE claim_key = 'x';
ROLLBACK;
```

## Reading the output
- `(cost=startup..total rows=N width=W)` — **estimates**, not measurements. `startup` is the cost before the first row is available (matters for `LIMIT`); `total` assumes every row is consumed; `rows` is the planner's row-count estimate for that node; `width` is average row size in bytes.
- With `ANALYZE`: `actual time=X..Y rows=Z loops=N` — real numbers. `loops` matters: a node under a nested loop can execute many times, and the shown time is *per loop* — multiply by `loops` for the node's real total contribution.
- Large estimate-vs-actual gaps (e.g. estimated 100 rows, actual 100,000) are the single most useful diagnostic in the whole output — see [[ANALYZE and Table Statistics]] for why this happens and how to fix it.

## `BUFFERS`
`EXPLAIN (ANALYZE, BUFFERS)` adds cache-hit accounting per node: `shared hit` (served from cache, fast) vs `shared read` (had to hit disk, slow). A node with a high `read` count relative to `hit` on a query that's supposedly "hot" is a sign the working set doesn't fit in memory, not necessarily a bad plan.

## When to use
- Reach for plain `EXPLAIN` to sanity-check a query's shape before running it on a large table (does it show a `Seq Scan` where an index should apply?).
- Reach for `EXPLAIN ANALYZE` the moment estimated and actual behavior might diverge — after a bulk load, on a query against a table with skewed data, or any time a query is slower than the plan "looks like it should be."

## Related
[[Sequential Scan vs Index Scan vs Bitmap Scan]] · [[Nested Loop, Hash, and Merge Joins]] · [[ANALYZE and Table Statistics]]
