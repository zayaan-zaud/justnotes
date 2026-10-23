---
tags: [sql, postgresql, indexing, performance]
source: [PostgreSQL 18 docs — Multicolumn Indexes]
---
# Multicolumn Index Column Order

For a B-tree multicolumn index, **the order columns are declared in the index is not interchangeable with any other order** — it determines exactly which query shapes can actually narrow the index scan, as opposed to just filtering within it after the fact.

## The rule
> Equality constraints on leading (leftmost) columns, plus one inequality/range constraint on the next column after that, are what actually narrow the portion of the index scanned. Columns after that point are still checked *using* the index (so no heap trip is needed to check them), but they don't reduce how much of the index gets scanned.

For an index on `(major, minor)`:
| Query | What happens |
|---|---|
| `major = 5 AND minor = 3` | Both narrow the scan — best case |
| `major = 5 AND minor > 10` | `major =` narrows to that range, `minor >` further narrows within it |
| `major > 5 AND minor = 3` | Only `major`'s inequality narrows anything; `minor = 3` is checked but doesn't reduce the scanned range |
| `minor = 3` (alone) | Leading column `major` isn't constrained at all — the index is far less useful, closer to a full scan |

**GIN and BRIN don't have this rule at all** — column order is irrelevant for both; every column contributes equally regardless of position. This asymmetry is worth knowing so a B-tree-shaped intuition about "put the most selective column first" doesn't get carried over to a GIN/BRIN index where it simply doesn't apply.

## When to use
- Order multicolumn B-tree index columns to match the most common query's equality-filtered columns first, range/inequality-filtered columns next, and anything only ever used for output (not filtering) last, or move it to an `INCLUDE` clause instead — see [[Index-Only Scans and INCLUDE]].
- More than ~3 columns in a single index is rarely worth it unless a query pattern is genuinely stable and stylized — beyond that, reconsider whether it's actually one query's shape driving the design or several different ones that would be better served by separate indexes.
- Postgres 18 added a "skip scan" optimization letting some queries that skip the leading column still use the index reasonably efficiently when that leading column has few distinct values — worth checking `EXPLAIN` before assuming a query that doesn't constrain the leading column gets zero benefit from the index.

## Related
[[B-Tree Index (Default)]] · [[CREATE INDEX (PostgreSQL)]] · [[Partial Index]] · [[EXPLAIN and EXPLAIN ANALYZE]]
