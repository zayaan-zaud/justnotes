---
tags: [sql, postgresql, indexing, performance]
source: [PostgreSQL 18 docs — Partial Indexes]
---
# Partial Index

`CREATE INDEX ... ON table (col) WHERE predicate` indexes only the rows matching `predicate` — smaller, faster to scan, and cheaper to maintain on writes than indexing the whole table, because rows that will never be queried through this index never enter it.

## What it's for
- **Excluding a common value that queries never filter to.** If 95% of rows share one status and no query cares about finding *those*, indexing them is pure overhead — a partial index over just the interesting 5% is smaller and more selective.
- **Enforcing uniqueness only within a subset.** `CREATE UNIQUE INDEX ... ON tests (subject, target) WHERE success` enforces "only one *successful* result per subject/target," while allowing unlimited failed attempts — a constraint a plain unique index can't express.

## The rule that determines whether it actually gets used
The query's `WHERE` clause must **imply** the index's predicate for the planner to consider the index at all — and Postgres's implication check is syntactic/simple, not a general theorem prover. `x < 1` implying `x < 2` works; a parameterized `WHERE x < $1` never implies a fixed `WHERE x < 2` predicate, because the planner can't know the parameter's value at plan time. If a partial index silently isn't being used, check first whether the query's filter genuinely and recognizably implies the index's `WHERE` clause — a logically-equivalent but differently-*phrased* condition may not be recognized as implying it.

## When to use
- Don't reach for a partial index as a substitute for table partitioning — many non-overlapping partial indexes on the same column for different value ranges (`WHERE category = 1`, `WHERE category = 2`, ...) is an anti-pattern; a single multicolumn index (or real table partitioning for large tables) is the better tool there.
- Good fit for stable, known query patterns against a stable data distribution — re-evaluate if the data distribution shifts significantly, since the predicate's selectivity assumption can go stale.

## Related
[[B-Tree Index (Default)]] · [[CREATE INDEX (PostgreSQL)]] · [[Multicolumn Index Column Order]]
