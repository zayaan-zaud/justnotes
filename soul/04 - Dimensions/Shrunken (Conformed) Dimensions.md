---
tags: [dimension, conformed, aggregate]
source: [Kimball ch.2 & 4, Adamson ch.5 & 15]
---
# Shrunken (Conformed) Dimensions

A **shrunken dimension** is a **[[Conformed Dimensions|conformed]]** dimension that is a **subset** — in rows and/or columns — of a base dimension, so the two still conform and can be combined.

## Two forms
1. **Rolled-up (fewer rows/coarser grain):** a higher level of a hierarchy — *Month* is a shrunken *Date*; *Brand* is a shrunken *Product*. These are exactly the dimensions used by **[[Aggregate Fact Tables]]** and higher-grain facts.
2. **Subset of columns/rows for a narrower process:** a dimension that keeps only the attributes/rows relevant to some fact tables while remaining a perfect subset of the master.

## Why it matters
Because a shrunken dimension is a *perfect subset*, an aggregate built on it returns **identical answers** to the atomic star for the attributes they share — the guarantee that makes aggregate navigation safe and [[Drilling Across|drill-across]] valid.

## Related
[[Conformed Dimensions]] · [[Aggregate Fact Tables]] · [[Drilling Across]] · [[Dimension Hierarchies]]
