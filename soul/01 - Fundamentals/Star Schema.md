---
tags: [dimensional-modeling, fundamentals, star-schema]
source: [Kimball ch.1, Adamson ch.1 & 3]
---
# Star Schema

A **star schema** is the physical realization of a [[Dimensional Modeling|dimensional model]] in a relational database: a single central **[[Fact Tables|fact table]]** surrounded by, and joined to, a set of **[[Dimension Tables|dimension tables]]**. Diagrammed, the fact table sits in the middle with dimensions radiating outward like the points of a star.

## Structure
- **Fact table (center):** holds the numeric [[Additive, Semi-Additive, and Non-Additive Facts|facts]] and a set of foreign keys, one per dimension. The FK set typically forms the fact table's grain/identity.
- **Dimension tables (points):** each has a single-column **[[Dimension Surrogate Keys|surrogate primary key]]** referenced by the fact table, plus many descriptive attributes.

The join relationships are simple and predictable: fact FK → dimension PK. This is deliberately a *denormalized* design — dimensions are wide and flat rather than snowflaked.

## Why it works
- **Symmetry** makes queries predictable for both users and the optimizer.
- **Browsing** — you explore/constrain within a dimension table ([[Dimension Tables|dimension browsing]]).
- **Querying facts** — you constrain dimensions, then aggregate facts grouped by dimension attributes.

## Star vs cube
A star lives in a relational DB; the same logical model can be deployed as an **OLAP cube**. See [[Star Schemas vs OLAP Cubes]].

## Related
[[Dimensional Modeling]] · [[Fact Tables]] · [[Dimension Tables]] · [[Grain]] · [[Dimension Surrogate Keys]] · [[Snowflaked Dimensions]]
