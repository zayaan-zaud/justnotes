---
tags: [sql, postgresql, query-features]
source: [PostgreSQL 18 docs — WITH Queries]
---
# Common Table Expressions (CTEs)

`WITH name AS (...)` defines a named, temporary result set scoped to a single statement — the standard way to break a complex query into readable, named steps instead of nesting subqueries or repeating the same logic inline.

## Syntax
```sql
WITH regional_sales AS (
    SELECT region, SUM(amount) AS total_sales
    FROM orders GROUP BY region
), top_regions AS (
    SELECT region FROM regional_sales
    WHERE total_sales > (SELECT SUM(total_sales)/10 FROM regional_sales)
)
SELECT region, product, SUM(amount)
FROM orders WHERE region IN (SELECT region FROM top_regions)
GROUP BY region, product;
```
Later CTEs can reference earlier ones (but not vice versa, except for [[Recursive CTEs]]). Each can be a `SELECT`, `INSERT`, `UPDATE`, `DELETE`, or `MERGE`.

## What CTEs are actually for
Readability and staged reasoning — naming an intermediate result so the final query reads as a pipeline of named steps, matching how the problem is actually being reasoned about (e.g. "first the per-source movements, then the per-claim rollup, then the final comparison"). They are **not** automatically a performance boundary in modern Postgres — see [[CTE Materialization (MATERIALIZED vs NOT MATERIALIZED)]] for the planner's actual behavior, which changed significantly in Postgres 12.

## When to use
- Any query complex enough that inlining the logic as nested subqueries would hurt readability — which is most multi-source reconciliation queries.
- Prefer one CTE per distinct *grain* or *concept* (one CTE = one aggregation level or one source's shape), not one CTE per arbitrary transformation step — matches the vault's own atomic-note discipline: each CTE should answer "what does one row of this represent" as cleanly as a fact table's own [[Grain]] statement does.

## Related
[[Recursive CTEs]] · [[CTE Materialization (MATERIALIZED vs NOT MATERIALIZED)]] · [[Merging Multiple Sources with FULL JOIN + COALESCE]] · [[Grain]]
