---
tags: [dimension, hierarchy, bridge, advanced, adamson]
source: [Adamson ch.10, Kimball ch.2 & 7]
---
# Recursive Hierarchies

A **recursive hierarchy** is a parent-child relationship a dimension has **with itself** — an employee reports to a manager (also an employee); a part contains sub-parts; a company owns subsidiaries. Depth is **variable and unbounded**, so it can't be flattened into fixed positional columns.

## Modeling options
1. **Self-referencing key** in the dimension (each row stores its parent's key). Faithful to the source but hard/slow to query for multi-level rollups in SQL.
2. **[[Ragged Hierarchies and Bridge Tables|Hierarchy bridge table]]** (Adamson's recommended approach): one row per **ancestor→descendant** pair with **depth** and **top/bottom flags**. Lets a fact roll up or drill from any node to any node in a single join, regardless of depth.
3. **Pathstring** attribute for simpler cases.

## Adamson's cautions (ch.10)
- Handle **shared sub-trees** and **allocation** to avoid double counting when a node appears under multiple parents.
- Bridge maintenance is non-trivial when the tree is **reparented** — the entire affected sub-tree's rows must be regenerated.

## Related
[[Ragged Hierarchies and Bridge Tables]] · [[Dimension Hierarchies]] · [[Bridge Tables (Multivalued Dimensions)]]
