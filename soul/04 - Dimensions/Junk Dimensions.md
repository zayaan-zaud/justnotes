---
tags: [dimension, advanced]
source: [Kimball ch.2 & 6, Adamson ch.6]
---
# Junk Dimensions

A **junk dimension** collects a grab-bag of **low-cardinality flags, indicators, and text codes** that don't belong to any existing dimension, packaging them into a single small dimension rather than scattering many tiny FKs across the fact table.

## Problem it solves
Transactions often carry miscellaneous booleans and codes (payment type, order-status flags, "gift wrap?", "web/phone/store"). Modeling each as its own dimension bloats the fact table into a [[Centipede Fact Tables|centipede]]; leaving them in the fact table as text pollutes it.

## Construction
- Combine the attributes into one dimension whose rows are the **distinct combinations** that actually occur (or the full Cartesian product if small).
- One surrogate FK in the fact table replaces many flag columns.
- Keep it small — if cardinality explodes, split into multiple junk dimensions.

## Related
[[Centipede Fact Tables]] · [[Degenerate Dimensions]] · [[Flags and Indicators as Textual Attributes]] · [[Dimension Tables]]
