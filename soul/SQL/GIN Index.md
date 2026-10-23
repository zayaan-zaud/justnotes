---
tags: [sql, postgresql, indexing]
source: [PostgreSQL 18 docs — Index Types]
---
# GIN Index

"Generalized Inverted Index" — built for columns that hold **multiple values per row** (arrays, jsonb, full-text search vectors), where the useful query is "does this row contain X" rather than "how does this row compare to that row."

## Mechanics
Unlike a B-tree (one index entry per row), GIN creates a separate index entry for **each component value** inside a row — every array element, every jsonb key/value — and maps each component back to the rows containing it. This is what makes `@>` (contains), `<@` (contained by), and `&&` (overlap) queries fast: the index is already organized by individual value, not by whole-row comparison.

## When to use
- Array columns queried with containment (`tags @> ARRAY['x']`) or overlap (`tags && ARRAY[...]`).
- `jsonb` columns — see [[JSONB Indexing (GIN)]] for the `jsonb_ops` vs `jsonb_path_ops` operator-class choice, which matters a lot for GIN specifically.
- Full-text search (`tsvector` columns) — GIN is generally preferred over [[GiST Index|GiST]] here: slower to build and update, but faster to query, which fits a read-heavy search workload.
- Larger on disk than a B-tree covering the same data, and slower to update — a genuine trade-off, not a strictly-better index type. Don't reach for GIN on scalar columns that a plain B-tree already handles fine.

## Related
[[JSONB Indexing (GIN)]] · [[Array Types (PostgreSQL)]] · [[GiST Index]] · [[B-Tree Index (Default)]]
