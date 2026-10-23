---
tags: [sql, postgresql, query-features, performance]
source: [PostgreSQL 18 docs — WITH Queries]
---
# CTE Materialization (MATERIALIZED vs NOT MATERIALIZED)

Before Postgres 12, every CTE was an **optimization fence** — computed once, in isolation, with no filter/join conditions from the outer query pushed into it. Postgres 12+ changed the default: a CTE referenced **once**, with no side effects, is now **inlined** into the surrounding query and optimized jointly with it, the same as a subquery would be. This is a real behavior change that silently affects query plans written assuming the old always-a-fence behavior.

## The current default rules
- **Referenced once, no side effects (no volatile functions), not recursive** → inlined by default. The outer query's `WHERE`/`JOIN` conditions can push down into it, and indexes on the underlying tables get used.
- **Referenced more than once** → materialized by default (computed once, reused) — because inlining would mean recomputing it from scratch at every reference point.
- **Recursive CTEs** → always materialized; required for correctness, not just performance.
- **Contains a volatile function** → always materialized, so the function runs once per row of the CTE rather than once per *reference* — otherwise a `NOT MATERIALIZED` volatile CTE referenced twice would silently evaluate the function twice per row.

## Forcing it explicitly
```sql
WITH w AS MATERIALIZED (SELECT * FROM big_table)
SELECT * FROM w w1 JOIN w w2 ON w1.key = w2.ref WHERE w2.key = 123;
```
Force materialization when you *want* the fence — e.g. the CTE is expensive and you deliberately don't want the outer `WHERE` re-executing it per reference, or it wraps a volatile/expensive function you want evaluated exactly once.

```sql
WITH w AS NOT MATERIALIZED (SELECT * FROM big_table)
SELECT * FROM w w1 JOIN w w2 ON w1.key = w2.ref WHERE w2.key = 123;
```
Force inlining even on a multiply-referenced CTE when each reference only needs a small slice — inlining lets `WHERE w2.key = 123` push down to an index scan on `big_table`, instead of first materializing the whole thing and filtering afterward.

## When to use
- Trust the default in most cases — it's a real optimization over the pre-12 always-a-fence behavior.
- Force `MATERIALIZED` when a CTE wraps an expensive/volatile computation referenced multiple times and you need it evaluated exactly once, not once per reference.
- Force `NOT MATERIALIZED` when a CTE is referenced multiple times but each reference filters down to a small slice — inlining lets those filters reach the base tables' indexes instead of scanning a fully-materialized intermediate.
- If a query plan looks wrong for a CTE-heavy query written before Postgres 12 knowledge, check whether it's assuming the old fence-by-default behavior — the fix might just be adding `MATERIALIZED` back where the old behavior was actually load-bearing.

## Related
[[Common Table Expressions (CTEs)]] · [[EXPLAIN and EXPLAIN ANALYZE]]
