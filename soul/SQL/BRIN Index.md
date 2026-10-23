---
tags: [sql, postgresql, indexing, performance]
source: [PostgreSQL 18 docs — Index Types]
---
# BRIN Index

"Block Range Index" — instead of one entry per row, BRIN stores a **summary (min/max) per range of physical table pages**. Trades precision for size: it can only say "the value you want might be in this block range," not exactly which row, but the index itself is tiny — orders of magnitude smaller than a B-tree over the same column.

## The condition that makes it work
BRIN only pays off when the column is **naturally correlated with physical row order** — the classic case is a timestamp column that's always increasing because rows are inserted in time order (a ledger, an event log, an append-only fact table). If a table's rows for a given value range are scattered across the whole table physically, BRIN degrades to scanning almost every block anyway, losing its entire advantage.

## When to use
- Very large, naturally-ordered append-only tables — time-series data, transaction logs, immutable fact tables loaded in date order — where a full B-tree would be large and mostly redundant with the physical row order that already exists.
- Wrong choice the moment rows get reordered relative to the indexed column (e.g. `UPDATE`s that change the indexed value, or inserts that aren't roughly chronological) — check that the correlation assumption actually holds for the table before choosing BRIN over a B-tree, rather than assuming "big table + date column" is automatically a BRIN candidate.
- The trade is genuinely: BRIN for a huge win in index size at the cost of scan precision, vs a B-tree for exact row-level pointers at a much larger on-disk footprint.

## Related
[[B-Tree Index (Default)]] · [[Accumulating Snapshot Fact Tables]] · [[Transaction Fact Tables]]
