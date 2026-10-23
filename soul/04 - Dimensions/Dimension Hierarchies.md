---
tags: [dimension, hierarchy]
source: [Kimball ch.2-3 & 7, Adamson ch.7 & 10]
---
# Dimension Hierarchies

Most dimensions contain one or more **hierarchies** — many-to-one rollups among attributes (Day→Month→Quarter→Year; Product→Brand→Category; Store→District→Region). Hierarchies drive **drilling down/up**.

## Fixed-depth positional hierarchies
The common, well-behaved case: a known, fixed number of levels. **Store each level as its own attribute** in the flat dimension (a "Category", "Subcategory", "Brand", "Product" column). Simple, fast, and BI-friendly. Multiple parallel hierarchies (e.g., a merchandising vs. a reporting rollup) are just multiple sets of attributes in the same dimension.

## Variable-depth / ragged hierarchies
When depth varies (org charts, parts explosions), positional columns don't work. Options (Kimball ch.2 & 7):
- **Slightly ragged:** pad to a fixed maximum depth.
- **[[Ragged Hierarchies and Bridge Tables|Hierarchy bridge table]]:** a many-to-many map of every ancestor→descendant pair — fully general. See [[Ragged Hierarchies and Bridge Tables]].
- **Pathstring attribute:** encode the path as a string (e.g., `/A/B/C/`) and query with wildcards — simpler but less flexible.

## Related
[[Ragged Hierarchies and Bridge Tables]] · [[Snowflaked Dimensions]] · [[Shrunken (Conformed) Dimensions]] · [[Dimension Tables]] · [[Recursive Hierarchies]]
