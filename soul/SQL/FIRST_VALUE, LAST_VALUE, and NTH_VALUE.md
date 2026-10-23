---
tags: [sql, postgresql, window-functions]
source: [PostgreSQL 18 docs — Window Functions]
---
# FIRST_VALUE, LAST_VALUE, and NTH_VALUE

These three read a value from a specific position **within the current window frame** — and `LAST_VALUE` in particular is a well-documented trap, because it interacts with the [[Window Function Frame Clause (ROWS, RANGE, GROUPS)|default frame]] in a way that surprises almost everyone the first time.

## The trap
```sql
LAST_VALUE(price) OVER (PARTITION BY product ORDER BY date)
```
This looks like it should return the most recent price per product. It doesn't — with the default frame (`RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`), the frame for every row ends at *that row's own position* (plus ties), not at the end of the partition. So `LAST_VALUE` returns **the current row's own value** on every row, not the partition's actual last value — the PostgreSQL docs themselves flag this as "likely to give unhelpful results" with the default frame.

## The fix
Widen the frame explicitly to cover the whole partition:
```sql
LAST_VALUE(price) OVER (
    PARTITION BY product ORDER BY date
    ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
)
```
`FIRST_VALUE` doesn't have this problem in the same way, since the default frame's start already is the partition start — but stating the frame explicitly for both is cheap insurance against relying on a default that differs by function.

## NTH_VALUE
`NTH_VALUE(value, n)` returns the value at the n-th row of the frame (1-indexed), `NULL` if the frame has fewer than n rows. PostgreSQL only supports counting from the frame's start (`FROM FIRST`) — the standard's `FROM LAST` isn't implemented; get "nth from the end" by reversing the `ORDER BY` in the window spec instead.

## When to use
- Never reach for `LAST_VALUE` without first deciding the frame explicitly — treat the default as wrong for this function until proven otherwise, rather than assuming it does what the name suggests.
- For "the current value of a claim/account as of this row, given its whole history so far," `FIRST_VALUE`/`LAST_VALUE` over an explicit frame is an alternative to a self-join or a [[LAG and LEAD|LAG]]-based approach — pick whichever reads more clearly for the specific case; they're computing the same information different ways.

## Related
[[Window Function Frame Clause (ROWS, RANGE, GROUPS)]] · [[LAG and LEAD]] · [[Window Functions (Overview)]]
