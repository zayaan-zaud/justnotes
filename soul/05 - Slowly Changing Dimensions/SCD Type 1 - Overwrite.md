---
tags: [dimension, scd]
source: [Kimball ch.2 & 5, Adamson ch.3]
---
# SCD Type 1 — Overwrite

When an attribute changes, **overwrite** the old value with the new one. The dimension always reflects the **current** state; **history is lost**.

## Characteristics
- Simplest technique; one row per entity.
- All existing fact rows are **retroactively** associated with the new value (as-is reporting).
- **Warning:** overwriting can **invalidate [[Aggregate Fact Tables|aggregates]]** built on that attribute — the ETL aggregate manager must be notified to rebuild.

## When to use
- Corrections of errors, or attributes where no one cares about history ("we only ever want the latest").

## Related
[[Slowly Changing Dimensions]] · [[SCD Type 2 - Add New Row]] · [[SCD Type 3 - Add New Attribute]] · [[Aggregate Fact Tables]]
