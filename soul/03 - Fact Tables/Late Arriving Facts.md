---
tags: [fact-table, etl, advanced]
source: [Kimball ch.2 & 19]
---
# Late Arriving Facts

A **late arriving fact** is a transaction that reaches the warehouse long after the event actually occurred. The ETL challenge: you must attach the dimension keys that were **in effect on the event date**, not today's keys.

## Handling
- With **[[SCD Type 2 - Add New Row|Type 2]]** dimensions, look up the surrogate key whose effective/expiration span **contains the event date** (not the current row).
- Recompute/patch any dependent [[Aggregate Fact Tables|aggregates]] and [[Accumulating Snapshot Fact Tables|snapshots]].

## Related
[[Late Arriving Dimensions]] · [[SCD Type 2 - Add New Row]] · [[ETL 34 Subsystems]] · [[Timespan Tracking in Fact Tables]] · [[LAG and LEAD]]
