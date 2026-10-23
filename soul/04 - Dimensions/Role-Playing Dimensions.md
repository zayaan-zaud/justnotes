---
tags: [dimension, advanced]
source: [Kimball ch.2 & 6, Adamson ch.6]
---
# Role-Playing Dimensions

A single physical dimension referenced **multiple times** in the same fact table, each reference playing a **different logical role**. The classic case: the [[Calendar Date Dimension]] used as *order date*, *ship date*, and *delivery date* on the same row.

## Implementation
- Keep **one** physical dimension table.
- Expose it through **multiple views (or aliases)**, one per role, each with **role-specific column names** ("Order Date", "Ship Date") so queries and BI tools stay unambiguous.
- The fact table holds a **separate FK** for each role.

## Common in
[[Accumulating Snapshot Fact Tables]] (many milestone dates), and anywhere the same entity appears in several capacities (e.g., a *ship-from* and *ship-to* facility from one location dimension).

## Related
[[Calendar Date Dimension]] · [[Accumulating Snapshot Fact Tables]] · [[Dimension Tables]]
