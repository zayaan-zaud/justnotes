---
tags: [fact-table, aggregate, performance]
source: [Kimball ch.2 & 15 & 20, Adamson ch.15]
---
# Aggregate Fact Tables

**Aggregate fact tables** are pre-summarized rollups of an atomic fact table, built purely for **query performance**. They store the same facts at a **coarser [[Grain|grain]]** (e.g., sales by product *brand* by *month* by *region* instead of by SKU by day by store).

## Key rules
- **Aggregates supplement, never replace, atomic data.** The atomic [[Fact Tables|fact table]] remains the foundation of record.
- Aggregate dimensions must be **[[Shrunken (Conformed) Dimensions|shrunken conformed dimensions]]** — perfect rolled-up subsets of the base dimensions (Month conforms to Date; Brand conforms to Product). This guarantees identical answers at either grain.
- Aggregates should be **invisible to users** — an **aggregate navigator** (in the BI/DB layer) transparently redirects queries to the smallest applicable table.

## Trade-offs
- Dramatic speedups (orders of magnitude) for common summary queries.
- Cost: extra ETL, storage, and administration; must be kept in sync with the base facts. Adamson's "families of stars" / [[Derived Schemas]] discuss managing them.
- OLAP cubes are effectively managed aggregate structures — see [[Star Schemas vs OLAP Cubes]].

## Related
[[Shrunken (Conformed) Dimensions]] · [[Grain]] · [[Derived Schemas]] · [[Star Schemas vs OLAP Cubes]] · [[Consolidated Fact Tables]]
