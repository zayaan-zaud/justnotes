---
tags: [sql, postgresql, query-features]
source: [PostgreSQL 18 docs — WITH Queries]
---
# Recursive CTEs

`WITH RECURSIVE name AS (base_case UNION [ALL] recursive_case)` is how SQL expresses traversal of a hierarchy or graph — a tree of parts, an org chart, a chain of claim revisions — without knowing the depth in advance. It's evaluated iteratively, not via true recursion, even though the syntax looks self-referential.

## Syntax and evaluation
```sql
WITH RECURSIVE t(n) AS (
    VALUES (1)                          -- base case (non-recursive term)
    UNION ALL
    SELECT n+1 FROM t WHERE n < 100     -- recursive term, references t
)
SELECT sum(n) FROM t;
```
Algorithm: evaluate the base case → put its rows in a *working table* → repeatedly evaluate the recursive term against the current working table, add new rows to the output, replace the working table with just the newly-produced rows → stop the moment a pass produces zero new rows. `UNION` (not `UNION ALL`) additionally discards duplicate rows at each step, which also protects against infinite loops on cyclic data that `UNION ALL` would spin on forever.

## A hierarchy example
```sql
WITH RECURSIVE included_parts(sub_part, part, quantity) AS (
    SELECT sub_part, part, quantity FROM parts WHERE part = 'our_product'
    UNION ALL
    SELECT p.sub_part, p.part, p.quantity * pr.quantity
    FROM included_parts pr, parts p
    WHERE p.part = pr.sub_part
)
SELECT sub_part, SUM(quantity) FROM included_parts GROUP BY sub_part;
```
Each pass expands one more level of the bill-of-materials tree; the recursive term stops contributing new rows once every leaf part has been reached.

## When to use
- Any genuinely hierarchical/recursive structure with unknown depth: org charts, category trees, a chain of document revisions each referencing its predecessor, dependency graphs.
- **Not a fit** for "give me the latest state of an entity given its append-only revision log" — that's a [[DISTINCT ON (PostgreSQL)]] or `ROW_NUMBER() OVER (PARTITION BY key ORDER BY version DESC)` problem (flat, one pass), not a recursive one. Recursive CTEs are for traversing *relationships between rows* (parent→child), not for picking the newest of several rows that share a key.
- On cyclic data (a graph with a cycle, not a true tree), `UNION ALL` will loop forever — use `UNION` to break cycles via duplicate elimination, or add explicit cycle detection (tracking visited nodes in an array column).

## Related
[[Common Table Expressions (CTEs)]] · [[Recursive Hierarchies]] · [[Ragged Hierarchies and Bridge Tables]]
