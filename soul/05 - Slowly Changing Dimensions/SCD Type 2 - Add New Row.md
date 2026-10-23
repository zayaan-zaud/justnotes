---
tags: [dimension, scd, history]
source: [Kimball ch.2 & 5, Adamson ch.3 & 8]
---
# SCD Type 2 — Add New Row

The **primary technique for preserving full history**. When a tracked attribute changes, **insert a new dimension row** for the entity with a **new [[Dimension Surrogate Keys|surrogate key]]**, leaving the old row intact.

## Mechanics
- Same [[Natural, Durable, and Supernatural Keys|natural/durable key]], **new surrogate key** per version.
- Fact rows join to the surrogate key that was **current when the measurement occurred** → each fact is permanently tied to the **as-was** attribute values ("perfect partitioning of history").
- Support columns on each row:
  - **row effective date** and **row expiration date** (validity span),
  - a **current-row flag** (e.g., `Y/N`),
  - optionally a **change reason**.

## Consequences
- The dimension grows with each change; entities have multiple rows.
- Requires the **durable key** to gather all versions of one entity.
- **[[Late Arriving Facts]]** and **[[Late Arriving Dimensions]]** need special handling to pick the correct historical row.

## Related
[[Dimension Surrogate Keys]] · [[Natural, Durable, and Supernatural Keys]] · [[SCD Type 1 - Overwrite]] · [[SCD Type 6 - Type 1 Attributes on Type 2 Dimension]] · [[Late Arriving Dimensions]]
