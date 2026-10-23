---
tags: [performance, derived, adamson]
source: [Adamson ch.14]
---
# Set-Operation Derived Tables

Derived tables built by applying **set operations** — **union, intersection, difference (minus)** — across stars or slices. Example: a table of "customers who bought in Q1 **but not** Q2" (a difference), or combining two regional slices (a union).

## Notes
- Powerful for **cohort/segment** construction; conceptually related to Kimball's **[[Behavior Study Groups]]**.
- Result must preserve **[[Conformed Dimensions|conformance]]** to remain analyzable alongside base stars.

## Related
[[Derived Schemas]] · [[Behavior Study Groups]] · [[Sliced Derived Tables (Adamson)]]
