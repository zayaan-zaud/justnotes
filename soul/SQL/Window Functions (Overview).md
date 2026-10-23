---
tags: [sql, postgresql, window-functions, query-features]
source: [PostgreSQL 18 docs — Window Functions Tutorial]
---
# Window Functions (Overview)

A window function computes across a set of related rows **without collapsing them** — unlike `GROUP BY`, every input row survives in the output, each carrying its own view of an aggregate/rank/offset value computed over its partition. This is what lets a query show "this claim's amount, next to this claim's running total, next to this claim's rank within its division" all on the same row.

## The `OVER` clause
```sql
avg(salary) OVER (PARTITION BY depname)
row_number() OVER (PARTITION BY depname ORDER BY salary DESC)
```
`PARTITION BY` splits rows into independent groups (like `GROUP BY`, but without collapsing). `ORDER BY` inside `OVER` determines processing order within each partition — required for ranking/offset functions (`ROW_NUMBER`, `LAG`) to mean anything, and it also sets the default [[Window Function Frame Clause (ROWS, RANGE, GROUPS)|frame]].

## Vs. GROUP BY
| | `GROUP BY` | Window function |
|---|---|---|
| Row count out | One row per group | Same row count as input |
| Detail + aggregate together | No — pick one | Yes, both on the same row |
| Where usable | `SELECT`, `HAVING` | `SELECT`, `ORDER BY` only — **not** `WHERE`/`GROUP BY`/`HAVING`, since those clauses execute before window functions do |

Repeated identical `OVER (...)` specs can be named once with `WINDOW w AS (...)` and referenced as `OVER w` — avoids retyping the same partition/order spec across several function calls in one query.

## When to use
- Reach for a window function the moment you need a per-row value that depends on *other* rows in the same result set without losing the row-level detail — a claim's rank among its division's claims that month, a running balance, "how does this row compare to the previous one for the same key." A `GROUP BY` subquery joined back to the detail rows can express the same thing but is more verbose and usually slower.
- Every offset function ([[LAG and LEAD]]) and ranking function ([[ROW_NUMBER, RANK, and DENSE_RANK]]) needs `ORDER BY` inside `OVER` to be meaningful — without it, "which row comes before/after" or "what rank" is undefined.

## Related
[[ROW_NUMBER, RANK, and DENSE_RANK]] · [[LAG and LEAD]] · [[Window Function Frame Clause (ROWS, RANGE, GROUPS)]] · [[DISTINCT ON (PostgreSQL)]]
