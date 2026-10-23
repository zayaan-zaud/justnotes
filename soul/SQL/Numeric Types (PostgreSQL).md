---
tags: [sql, postgresql, data-types]
source: [PostgreSQL 18 docs — Numeric Types]
---
# Numeric Types (PostgreSQL)

PostgreSQL splits numbers into two families with a hard trade-off: `numeric`/`decimal` is exact but slow, `real`/`double precision` is fast but approximate (IEEE 754). `integer`/`bigint` are exact and fast but whole-number-only. Picking the wrong family is a correctness bug waiting to happen, not just a performance choice.

## The three families
- **Integers** — `smallint` (2B, ±32,767), `integer` (4B, ±2.1B, the default choice), `bigint` (8B, ±9.2×10¹⁸).
- **Exact arbitrary-precision** — `numeric(precision, scale)`: precision = total significant digits, scale = digits right of the decimal point. Addition/subtraction/multiplication are always exact. Very slow relative to integer/float — this is a deliberate trade for correctness, not an oversight.
- **Approximate floating-point** — `real` (4B, ≥6 digits), `double precision` (8B, ≥15 digits). IEEE 754: ties round to nearest even, comparisons can surprise (`0.1 + 0.2 ≠ 0.3`), and both support `Infinity`/`-Infinity`/`NaN`.

## Rounding differs by family
`numeric` rounds ties **away from zero** (`2.5 → 3`, `-2.5 → -3`). `real`/`double precision` round ties **to nearest even** (`2.5 → 2`, `-2.5 → -2`), standard IEEE 754 banker's rounding. Mixing the two families in the same calculation and expecting consistent rounding is a real bug source.

## When to use
- **Money and anything reconciled against another system**: `numeric` only. This is non-negotiable — floating point silently drifts on repeated arithmetic, and a reconciliation that's off by fractions of a cent from float rounding is indistinguishable from a real discrepancy until someone traces it.
- **Scientific/statistical aggregates where exactness doesn't matter**: `double precision` — much faster than `numeric` at scale.
- **Surrogate/degenerate keys, counts, row identifiers**: `integer`/`bigint`.
- Unconstrained `numeric` (no precision/scale) accepts anything up to the implementation limit and is the safest default when you're not sure what precision a source system uses — constrain it later once you know the real range.

## Related
[[CREATE INDEX (PostgreSQL)]] · [[EXPLAIN and EXPLAIN ANALYZE]]
