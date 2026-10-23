---
tags: [dimension, hierarchy, bridge, advanced]
source: [Kimball ch.2 & 7, Adamson ch.10]
---
# Ragged Hierarchies and Hierarchy Bridge Tables

**Ragged / variable-depth hierarchies** (organization charts, bill-of-materials, account rollups) have no fixed number of levels, so they can't be flattened into positional columns. The general solution is a **hierarchy bridge table**.

## Hierarchy bridge (map) table
A separate table with one row for **every ancestor→descendant relationship** in the tree, including each node to itself. Typical columns:
- parent (ancestor) key, child (descendant) key,
- **depth from parent**, flags for **top node** and **bottom (leaf) node**.

Joining the fact → node → bridge lets you roll up or drill from **any** level to any level, at any depth. Adamson (ch.10) covers this as the recursive-hierarchy bridge in depth, including handling shared sub-trees and impacts on aggregation.

## Alternative: pathstring
Encode each node's full path as a text attribute (`/US/West/CA/`) and use `LIKE` wildcards. Simpler to build; weaker for reparenting and for level-aware analysis.

## Related
[[Dimension Hierarchies]] · [[Recursive Hierarchies]] · [[Bridge Tables (Multivalued Dimensions)]] · [[Snowflaked Dimensions]]
