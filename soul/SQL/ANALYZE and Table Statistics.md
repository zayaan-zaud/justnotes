---
tags: [sql, postgresql, performance, query-planning]
source: [PostgreSQL 18 docs — Planner Statistics]
---
# ANALYZE and Table Statistics

Every planner decision — scan type, join strategy, join order — rests on **row-count estimates**, and those estimates come entirely from statistics `ANALYZE` collected, not from the live table. Stale statistics don't cause errors; they cause confidently wrong plans that look fine until you check `EXPLAIN ANALYZE`'s actual-vs-estimated gap.

## What `ANALYZE` actually collects
Stored in `pg_statistic` (readable via the `pg_stats` view): approximate row count and page count (`reltuples`/`relpages` in `pg_class`), a most-common-values list per column (for equality selectivity), a histogram (for range/inequality selectivity), and a correlation coefficient (how well a column's value order matches physical row order — this is what a [[BRIN Index]] depends on being high). Postgres 14+ can also collect **extended/multivariate statistics** (`CREATE STATISTICS`) tracking functional dependencies and combined-column distinct counts — needed because the planner otherwise assumes columns are independent, and two correlated columns filtered together (e.g. `city = 'X' AND zip = '12345'`) get their selectivities wrongly multiplied together as if independent, producing a massive underestimate.

## Why staleness breaks plans, concretely
- Table grew 1000x since the last `ANALYZE` (e.g. a bulk load) → planner still estimates the old row count → picks an index scan appropriate for a small table, when a sequential/bitmap scan would actually be faster now.
- Row-count estimates for two joined tables are backwards (stats say A is small and B is large, but a recent load reversed that) → planner picks the wrong side as the outer loop or hash-build side → a join that should be fast runs like the tables are the wrong size.

## Autovacuum vs manual `ANALYZE`
Autovacuum triggers an automatic `ANALYZE` once enough rows have changed — but it's a background, eventually-consistent process, not immediate. Run `ANALYZE` **manually and immediately** after: a bulk `COPY`/load, a large `DELETE`/`UPDATE`, or before any query where plan quality actually matters and can't wait for autovacuum's next pass. This matters especially for warehouse-style bulk-insert pipelines, where a large load can finish and be queried again within seconds — well before autovacuum would have gotten to it.

## `default_statistics_target`
Controls how many entries go into the MCV list / histogram per column (default 100). Higher (`ALTER TABLE t ALTER COLUMN c SET STATISTICS 500`) gives better estimates for columns with skewed/irregular distributions, at the cost of slower `ANALYZE` and slightly slower planning. Leave uniform columns (IDs, sequential timestamps) at the default; raise it selectively on columns with real skew that the planner keeps misjudging.

## When to use
- Any time a plan looks wrong and the table's had a recent bulk change, check `pg_stat_user_tables.last_analyze` before assuming the index or join strategy itself is the problem — stale statistics is a more common root cause than either.
- If `EXPLAIN ANALYZE` shows a large gap between estimated and actual `rows` on a specific node, that's the concrete signal to `ANALYZE` that table (or raise its statistics target) rather than restructure the query.

## Related
[[EXPLAIN and EXPLAIN ANALYZE]] · [[BRIN Index]] · [[Autovacuum]] · [[Sequential Scan vs Index Scan vs Bitmap Scan]]
