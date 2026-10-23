---
tags: [dimension, normalization, snowflake]
source: [Kimball ch.2 & 20, Adamson ch.7]
---
# Snowflaked Dimensions

A **snowflake** is a dimension that has been **normalized** — its hierarchical/redundant attributes split out into separate related sub-tables (e.g., product → brand → category as three joined tables) instead of one flat dimension.

## Kimball's position: generally avoid
- Snowflaking **complicates** the schema for users and BI tools and provides **negligible storage savings** (dimensions are tiny next to fact tables).
- It can **hurt query/browse performance** and undermines the simplicity that makes stars valuable.
- **Recommendation:** keep dimensions **flat and denormalized**. Redundancy in dimensions is a feature, not a bug.

## Acceptable relatives
- **[[Outrigger Dimensions]]** — a *permitted, limited* form: a dimension referencing another dimension for a well-defined cluster of attributes (e.g., a date outrigger inside a dimension). Use sparingly.
- Some ETL tools and OLAP engines snowflake internally; that's an implementation detail, not the user-facing model.

## Adamson
Adamson (ch.7) treats snowflakes more neutrally as a modeling option and details their mechanics, but agrees the flat star is the default for usability.

## Related
[[Outrigger Dimensions]] · [[Dimension Hierarchies]] · [[Dimension Tables]] · [[Star Schema]]
