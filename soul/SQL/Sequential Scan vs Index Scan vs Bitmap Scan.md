---
tags: [sql, postgresql, performance, query-planning]
source: [PostgreSQL 18 docs — Using EXPLAIN]
---
# Sequential Scan vs Index Scan vs Bitmap Scan

The planner has more than a binary "use the index or don't" choice — there are four distinct table-access strategies, and each one is the right choice for a different combination of table size and predicate selectivity.

## The four
- **Seq Scan** — reads every row in physical order. Correct choice (not a fallback failure) when there's no usable index, the predicate isn't selective enough to beat reading sequentially, or the table's small enough that index overhead isn't worth it.
- **Index Scan** — walks the index, then fetches each matching row from the heap individually. Wins when very few rows match (highly selective predicate) or when the query's `ORDER BY` matches the index's order, avoiding a separate sort.
- **Index Only Scan** — answers entirely from the index, no heap visit at all — see [[Index-Only Scans and INCLUDE]] for the visibility-map condition that makes this actually happen rather than silently falling back.
- **Bitmap Heap Scan (with a Bitmap Index Scan feeding it)** — a middle ground: first builds an in-memory bitmap of *which pages* contain matching rows from the index, then visits the heap by physical page order rather than one row at a time. Wins for a moderately selective predicate (hundreds to thousands of rows, not a handful and not most of the table) — cheaper than a plain Index Scan's row-at-a-time random access, but still avoids reading the whole table. Multiple indexed conditions combine here via `BitmapAnd`/`BitmapOr` — Postgres can intersect or union two different indexes' bitmaps before ever touching the heap.

## When to use
- Don't read a `Seq Scan` in a plan as automatically wrong — check the row-count estimate and table size first. A `Seq Scan` on a query returning most of a small table is correct, not a missing-index bug.
- A plan choosing `Seq Scan` over an available index on a *large* table with a *selective* predicate is the real red flag — that's when to suspect stale statistics ([[ANALYZE and Table Statistics]]) rather than assume the index needs rebuilding.
- Bitmap scans showing up where an Index Scan was expected usually means the predicate matches more rows than a pure index scan handles efficiently — not a problem to fix, just the planner correctly picking the middle-selectivity tool.

## Related
[[EXPLAIN and EXPLAIN ANALYZE]] · [[B-Tree Index (Default)]] · [[Index-Only Scans and INCLUDE]] · [[ANALYZE and Table Statistics]]
