---
tags: [fact-table, snapshot, advanced, time]
source: [Kimball ch.2 & 5-6, Adamson ch.11]
---
# Lag and Duration Facts

**Lag** (or **duration**) facts measure the **elapsed time between milestones** of a process — the natural companion of [[Accumulating Snapshot Fact Tables]], which carry multiple milestone dates.

## Design
- Compute and **store** each lag as a numeric fact (e.g., order-to-ship days, ship-to-deliver days) rather than forcing users to subtract date keys at query time.
- Multiple lags can be stored per row, one per pair of milestones, so cycle-time analysis is simple and additive/averageable.

## Related
[[Accumulating Snapshot Fact Tables]] · [[Calendar Date Dimension]] · [[Role-Playing Dimensions]]
