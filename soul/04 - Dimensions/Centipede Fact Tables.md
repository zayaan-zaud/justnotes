---
tags: [fact-table, dimension, anti-pattern]
source: [Kimball ch.2 & 5]
---
# Centipede Fact Tables

A **centipede fact table** is an anti-pattern where the fact table sprouts **too many foreign keys** — often dozens — because the designer created a separate small dimension for every low-cardinality attribute (and/or snowflaked hierarchy levels into separate dimension FKs).

## Why it's bad
- Cluttered, hard to use, and slower (many joins).
- Usually a symptom of **over-normalizing dimensions** or failing to group related attributes.

## Fixes
- **Combine** correlated low-cardinality flags into a **[[Junk Dimensions|junk dimension]]**.
- **Collapse hierarchy levels** back into their parent dimension (don't snowflake — see [[Snowflaked Dimensions]]).
- Aim for a **handful (single digits to ~20)** of well-chosen dimensions per fact table, not scores.

## Related
[[Junk Dimensions]] · [[Snowflaked Dimensions]] · [[Dimension Tables]] · [[Advanced Fact Table Techniques]]
