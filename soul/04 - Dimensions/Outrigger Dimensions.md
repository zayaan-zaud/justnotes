---
tags: [dimension, snowflake, advanced]
source: [Kimball ch.2 & 6]
---
# Outrigger Dimensions

An **outrigger** is a dimension table that is referenced by **another dimension table** (a dimension with a foreign key to a second dimension). It is a **permitted, limited** form of [[Snowflaked Dimensions|snowflaking]].

## Legitimate uses
- A **date outrigger**: e.g., the customer dimension has a "date first purchased" attribute that references a physical date dimension so those dates get full calendar attributes.
- A cluster of attributes that is genuinely shared/reused and changes on its own cadence.

## Cautions
- Use **sparingly** — overuse reproduces the usability problems of snowflaking.
- Outriggers appear in **[[SCD Type 5 - Mini-Dimension + Type 1 Outrigger|Type 5 SCDs]]**, where a mini-dimension is attached to the base dimension as a Type 1 outrigger.

## Related
[[Snowflaked Dimensions]] · [[SCD Type 5 - Mini-Dimension + Type 1 Outrigger]] · [[Calendar Date Dimension]]
