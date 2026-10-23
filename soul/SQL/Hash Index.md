---
tags: [sql, postgresql, indexing]
source: [PostgreSQL 18 docs — Index Types]
---
# Hash Index

Stores only a hash of the indexed value and supports exactly one operator: `=`. No ordering, no range queries, no sorting benefit.

## When to use
- Rarely the right choice. A [[B-Tree Index (Default)|B-tree]] supports everything a hash index does (equality) plus range queries and sort-order — for the same equality lookup, a B-tree is essentially never meaningfully worse and is strictly more flexible if query patterns ever change.
- The one narrow case where hash can win: a very large index on a column that's *only* ever equality-filtered, never ranged or sorted, where hash's smaller per-entry footprint matters at scale. Worth measuring against a B-tree before committing to it, not assuming.
- Default to B-tree unless you have a specific, measured reason not to.

## Related
[[B-Tree Index (Default)]] · [[CREATE INDEX (PostgreSQL)]]
