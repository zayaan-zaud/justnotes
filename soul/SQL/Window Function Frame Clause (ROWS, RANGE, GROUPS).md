---
tags: [sql, postgresql, window-functions, query-features]
source: [PostgreSQL 18 docs — Window Function Calls]
---
# Window Function Frame Clause (ROWS, RANGE, GROUPS)

Every window function operates over a **frame** — the subset of rows in its partition that are actually visible to it — and the frame clause is what defines that subset. The default frame is a common source of "why did `SUM() OVER (...)` give me a running total instead of the whole partition's total" confusion, so it's worth knowing explicitly rather than relying on the default and being surprised.

## The default frame, and why it surprises people
With `ORDER BY` present and no explicit frame, the default is:
```
RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
```
This means: every row from the start of the partition through the current row's last **peer** (rows tied on the `ORDER BY` value). Combined with an aggregate, this produces a **running total**, not a whole-partition total — which is usually *not* what someone reaching for `SUM(x) OVER (PARTITION BY key ORDER BY date)` actually wants. Get the whole-partition total instead with either no `ORDER BY`, or an explicit `ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING`.

## The three modes
- **`ROWS`** — frame bounds are counted in exact physical rows (`2 PRECEDING` = the 2 rows immediately before, by row position).
- **`GROUPS`** — frame bounds are counted in **peer groups** (sets of rows tied on `ORDER BY`), not individual rows — `1 PRECEDING` means the current peer group plus the one before it, however many rows that contains.
- **`RANGE`** — frame bounds are a **value offset** on a single `ORDER BY` column, not a row or group count — `RANGE BETWEEN INTERVAL '1 day' PRECEDING AND CURRENT ROW` means "all rows whose date is within 1 day of this row's date," regardless of how many rows that is. Requires exactly one `ORDER BY` column.

`ROWS` and `RANGE`/`GROUPS` diverge specifically when there are ties in `ORDER BY`: `ROWS BETWEEN 2 PRECEDING AND CURRENT ROW` always means exactly 3 physical rows; `RANGE`/`GROUPS` with the same current row can include a different row count depending on how many rows tie on the ordering value.

## Frame bounds and exclusion
Bounds: `UNBOUNDED PRECEDING`, `offset PRECEDING`, `CURRENT ROW`, `offset FOLLOWING`, `UNBOUNDED FOLLOWING`. `frame_start` can't be `UNBOUNDED FOLLOWING`, `frame_end` can't be `UNBOUNDED PRECEDING`, and `frame_end` can't sort before `frame_start`.

`EXCLUDE` further trims the frame after the bounds are set: `EXCLUDE CURRENT ROW` (drop just the current row), `EXCLUDE GROUP` (drop the current row and all its peers), `EXCLUDE TIES` (drop the peers but keep the current row itself), `EXCLUDE NO OTHERS` (the default — no exclusion).

## When to use
- Use `ROWS` for moving averages / trailing-N-rows calculations where "N rows" is the actual business meaning (a 3-period moving average).
- Use `RANGE` with an interval offset for genuinely time-based windows ("everything in the last 7 days") where the row count is incidental to the real question.
- Be deliberate about `ORDER BY` in a window spec used with an aggregate — if the goal is a whole-partition total (not a running one), say so explicitly rather than relying on remembering the default.

## Related
[[LAG and LEAD]] · [[ROW_NUMBER, RANK, and DENSE_RANK]] · [[Common Table Expressions (CTEs)]]
