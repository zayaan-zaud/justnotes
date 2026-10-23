---
tags: [sql, postgresql, indexing, performance]
source: [PostgreSQL 18 docs — Index Types]
---
# B-Tree Index (Default)

The index PostgreSQL builds when no method is specified — general-purpose, sortable-data-only, and the right default for the overwhelming majority of indexes on ordinary columns (keys, dates, numbers, text).

## What it supports
Equality and full ordering: `=`, `<`, `<=`, `>=`, `>`, plus everything derived from those — `BETWEEN`, `IN`, `IS NULL`/`IS NOT NULL`, and prefix pattern matching (`LIKE 'foo%'`, but **not** `LIKE '%foo'` — a leading wildcard can't use a sorted structure). Because it maintains sort order, a B-tree index can also satisfy an `ORDER BY` on the indexed column(s) without a separate sort step.

## When to use
- The default choice for any column that's filtered with equality/range conditions or sorted on — primary/foreign keys, dates, amounts, status codes.
- Not the right tool for: multi-valued columns needing "does this contain X" queries (arrays, jsonb — see [[GIN Index]]), geometric/nearest-neighbor queries (see [[GiST Index]]), or extremely large naturally-ordered tables where a tiny summary index beats an exact one (see [[BRIN Index]]).
- `LIKE`/regex matching that isn't anchored to the start of the string can't use a B-tree at all — that needs a different approach (trigram indexing via `pg_trgm`, or a [[GIN Index]]/[[GiST Index]] built for text search).

## Related
[[CREATE INDEX (PostgreSQL)]] · [[GIN Index]] · [[GiST Index]] · [[BRIN Index]] · [[Hash Index]] · [[Multicolumn Index Column Order]] · [[Index-Only Scans and INCLUDE]] · [[Partial Index]]
