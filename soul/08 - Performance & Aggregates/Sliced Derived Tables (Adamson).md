---
tags: [performance, derived, adamson]
source: [Adamson ch.14]
---
# Sliced Derived Tables

A **slice** is a derived table containing a **subset (partition) of a base star's rows** — for example, one region's or one subsidiary's data extracted for local distribution, performance, or security.

## Notes
- Because a slice keeps the **same grain and [[Conformed Dimensions|conformed dimensions]]** as the base, its numbers reconcile with the whole.
- Useful for **distributing** data to remote sites and for **row-level security/partitioning**.
- The complement of slicing is **merging** slices back / [[Set Operation Derived Tables (Adamson)|set operations]].

## Related
[[Derived Schemas]] · [[Conformed Dimensions]] · [[Set Operation Derived Tables (Adamson)]]
