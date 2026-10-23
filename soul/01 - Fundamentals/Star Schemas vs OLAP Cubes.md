---
tags: [fundamentals, olap, star-schema]
source: [Kimball ch.1-2, Adamson ch.3]
---
# Star Schemas vs OLAP Cubes

The same dimensional model can be physically deployed two ways. Both express [[Fact Tables|facts]] surrounded by [[Dimension Tables|dimensions]]; they differ in storage engine.

## Star schema (ROLAP)
- Lives in a **relational database** as a [[Star Schema]].
- Scales to the largest data volumes; the natural home for **atomic** [[Grain|grain]] data.
- Queried with SQL; more flexible, more portable, better for very large/detailed data.

## OLAP cube (MOLAP)
- A **multidimensional database** structure.
- Strong analytic capability: fast, complex calculations, drilling, and rich functions built in.
- Often **outperforms** relational for queries — but has practical **size limits** and vendor lock-in.

## Kimball guidance
- Build the atomic-grain star **first** as the foundation of record; populate cubes **from** the atomic relational data.
- Cubes are excellent for aggregated/summary delivery and interactive analysis.
- [[Slowly Changing Dimensions]] (esp. Type 2) can be harder to manage in some cube engines — verify before committing.

## Related
[[Star Schema]] · [[Aggregate Fact Tables]] · [[Fact Tables]] · [[Dimension Tables]]
