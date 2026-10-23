---
tags: [performance, aggregate, adamson]
source: [Adamson ch.15, Kimball ch.15 & 20]
---
# Aggregate Design and Navigation (Adamson)

Adamson ch.15 is the deep treatment of **[[Aggregate Fact Tables|aggregates]]** as a performance strategy — the "families of stars" idea.

## Rules of a good aggregate
- Each aggregate is a **rollup of a base star** using **[[Shrunken (Conformed) Dimensions|shrunken conformed dimensions]]** — so it returns **identical answers** for the attributes it shares with the base.
- Aggregates must include **only fully additive** facts at the summarized grain (beware [[Additive, Semi-Additive, and Non-Additive Facts|semi-/non-additive]] measures).
- Store aggregates in **separate tables** (or cube partitions), not mixed into the atomic fact table (no mixed [[Grain|grains]]).

## Aggregate navigation
- An **aggregate navigator** (BI or DB layer) transparently rewrites a user's query to the **smallest table that can answer it**, so aggregates stay **invisible** to users and no report hard-codes a specific table.
- Requires accurate **metadata** mapping base ↔ aggregate ↔ shrunken dimensions.

## Lifecycle
- Choose aggregates from **actual query patterns** (don't guess); a handful of well-chosen aggregates covers most load.
- **Maintenance:** aggregates must be rebuilt when base facts change or when a **[[SCD Type 1 - Overwrite|Type 1]]** overwrite alters a grouping attribute.

## Related
[[Aggregate Fact Tables]] · [[Shrunken (Conformed) Dimensions]] · [[Derived Schemas]] · [[Star Schemas vs OLAP Cubes]]
