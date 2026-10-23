---
tags: [performance, derived, adamson]
source: [Adamson ch.14]
---
# Pivoted Derived Tables

A derived table that **restructures the orientation** of an existing star to fit a reporting or integration need:
- **Rows → columns:** turn a type-valued dimension into a set of columns (e.g., one column per month, or per measure type) for spreadsheet-style output.
- **Columns → rows:** normalize repeating fact columns into a taller, narrower structure (often to feed tools that expect a "measure type" dimension — Kimball's *measure type dimension*).

Pivoting trades flexibility for a fixed, presentation-friendly shape; keep the atomic star as the source of truth.

## Related
[[Derived Schemas]] · [[Numeric Values - Attribute or Fact]]
