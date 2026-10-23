---
tags: [sql, postgresql, window-functions]
source: [PostgreSQL 18 docs — Window Functions]
---
# ROW_NUMBER, RANK, and DENSE_RANK

Three ranking functions that agree when there are no ties and diverge in exactly how they treat them — picking the wrong one for tied `ORDER BY` values is a quiet, easy-to-miss bug rather than an error.

## The three, on the same tied data
For rows ordered `10, 10, 10, 20, 30`:
| | `ROW_NUMBER()` | `RANK()` | `DENSE_RANK()` |
|---|---|---|---|
| 10 | 1 | 1 | 1 |
| 10 | 2 | 1 | 1 |
| 10 | 3 | 1 | 1 |
| 20 | 4 | **4** | **2** |
| 30 | 5 | 5 | 3 |

- **`ROW_NUMBER()`** — always unique, 1-2-3-4-5, no matter how many ties. Two rows tied on every `ORDER BY` column still get different, essentially arbitrary numbers between them.
- **`RANK()`** — ties share a rank, and the **next value skips ahead** by the tie-group size (three 1's, then straight to 4).
- **`DENSE_RANK()`** — ties share a rank, and the next value is **just the next integer**, no skip (three 1's, then 2).

## When to use
- **`ROW_NUMBER()`** for "pick exactly one row per group" (`... WHERE rn = 1`, the classic latest-row-per-key dedupe pattern) — its guarantee of uniqueness even among ties is exactly what makes `WHERE rn = 1` deterministic, as opposed to `RANK() = 1` which could return multiple rows if the top value is tied.
- **`RANK()`** when the actual business meaning is competition-style ranking and skipped numbers are meaningful ("these three are tied for 1st, so the next distinct one really is 4th, not 2nd").
- **`DENSE_RANK()`** when you want a compact "which tier is this row in" without gaps — e.g. counting how many distinct price points exist below a given row, where ties should count as one tier, not consume multiple integers.
- The recurring trap: using `ROW_NUMBER()` when ties genuinely should be treated as equal (e.g. picking a truly non-deterministic "first" row among tied duplicates), or using `RANK()`/`DENSE_RANK()` for a `WHERE rank = 1` filter and getting back more than one row because the top value was tied — check whether ties are actually possible in the data before assuming `= 1` returns exactly one row.

## Related
[[Window Functions (Overview)]] · [[DISTINCT ON (PostgreSQL)]] · [[LAG and LEAD]]
