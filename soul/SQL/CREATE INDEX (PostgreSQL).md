---
tags: [sql, ddl, postgresql, performance]
source: [PostgreSQL docs — CREATE INDEX]
---
# CREATE INDEX (PostgreSQL)

`CREATE INDEX` builds an index on one or more columns (or expressions) of a table, trading extra storage and slower writes for faster lookups — the same performance trade-off that motivates [[Aggregate Fact Tables]]-style pre-computation at the data-modeling layer.

## Syntax essentials
```sql
CREATE [UNIQUE] INDEX [CONCURRENTLY] [name] ON table_name [USING method]
  (column_name | (expression) [ASC|DESC] [NULLS FIRST|LAST], ...)
  [INCLUDE (column_name, ...)]
  [WHERE predicate];
```
- **Index methods:** `btree` (default, general-purpose), `hash`, `gist`, `spgist`, `gin`, `brin`.
- **Expression indexes:** you can index `upper(col)` instead of `col`, so `WHERE upper(col) = 'JIM'` can use the index. Functions used must be *immutable* (deterministic, no outside dependency).
- **`UNIQUE`:** enforces no duplicate values across the indexed column(s); errors on insert/update violations.
- **`CONCURRENTLY`:** builds the index without locking out writes — slower and non-transactional, but avoids blocking a live production table during the build.
- **`INCLUDE (...)`:** adds non-key "payload" columns to the index leaf pages. They can't be searched on but enable index-only scans (avoid visiting the table) for queries that select them.
- **`WHERE predicate`:** creates a *partial index* covering only a subset of rows (e.g. only unbilled orders) — smaller, faster, cheaper to maintain than indexing the whole table.

## When to use / gotchas
- Index columns that are frequently filtered, joined, or sorted on — not every column, since each index slows down inserts/updates and costs storage.
- Use a partial index (`WHERE`) when queries consistently target a small, well-defined slice of a large table.
- Use `INCLUDE` sparingly — it bloats the index and, on B-tree, disables deduplication.
- `CONCURRENTLY` is mandatory practice for adding indexes to tables under live production load.

## Related
[[ALTER TABLE]] · [[Anti-Join Performance (LEFT JOIN + IS NULL vs NOT EXISTS)]]
