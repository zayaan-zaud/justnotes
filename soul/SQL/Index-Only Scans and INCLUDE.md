---
tags: [sql, postgresql, indexing, performance]
source: [PostgreSQL 18 docs — Index-Only Scans]
---
# Index-Only Scans and INCLUDE

An **index-only scan** answers a query straight from the index, never touching the table's heap at all — but this only works, and only helps, under specific conditions that are easy to assume away.

## The two requirements
1. **Index type support** — B-tree always supports it; GiST/SP-GiST support it for some operator classes; GIN never does (it stores only partial/decomposed data, not full values, so it can't reconstruct a row's actual column values).
2. **Every column the query touches must be in the index** — either as a key column or via `INCLUDE`.

```sql
CREATE INDEX ON tab(x) INCLUDE (y);
SELECT y FROM tab WHERE x = 'key';   -- can be index-only
```
`INCLUDE` columns are stored in the index but not used for searching/ordering — they exist purely so a query selecting them doesn't need to visit the heap. On a unique index, uniqueness applies only to the key columns, not the included ones.

## The visibility-map catch — this is the part that actually determines whether it happens
MVCC visibility information lives in the heap, not the index, so even an index-only scan needs to confirm each candidate row is visible to the current transaction. Postgres avoids a heap trip for this via the **visibility map** — a tiny per-table structure marking which heap pages are "all-visible" (every row on that page is old enough to be visible to every current/future transaction). If the visibility map bit is set, the scan trusts it and skips the heap; if not, it falls back to checking the heap anyway, defeating the whole point.

**Consequence**: index-only scans only pay off on tables where a large fraction of pages are all-visible — which means **slowly-changing or append-mostly tables**. A table under constant `UPDATE`/`DELETE` churn keeps generating pages that aren't yet marked all-visible, so index-only scans silently degrade toward ordinary index scans regardless of how well the index itself is built. Autovacuum is what sets visibility-map bits for stable pages — if autovacuum isn't keeping up (or isn't running at all on a table, e.g. due to a bulk-load path that doesn't trigger it), index-only scans quietly stop paying off even with a perfectly designed covering index.

## When to use
- Add `INCLUDE` columns when a query's `SELECT` list needs a column that isn't part of the natural search key, on a table that's read-heavy and doesn't churn much.
- Before assuming a covering index is delivering an index-only scan, actually check `EXPLAIN` output for "Index Only Scan" vs plain "Index Scan" — the latter means the visibility-map fallback is firing, and the fix is usually about autovacuum health on that table, not the index definition.

## Related
[[CREATE INDEX (PostgreSQL)]] · [[B-Tree Index (Default)]] · [[Autovacuum]] · [[EXPLAIN and EXPLAIN ANALYZE]]
