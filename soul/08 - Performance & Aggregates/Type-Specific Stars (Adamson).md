---
tags: [fact-table, heterogeneous, adamson]
source: [Adamson ch.13]
---
# Type-Specific Stars

Adamson ch.13 covers designs for situations where **one process spans multiple types** of things with differing attributes/measures — the counterpart to Kimball's **[[Supertype and Subtype Schemas]]**.

## Approaches
- **Core + custom stars:** a **core** star with attributes/facts common to all types (enterprise view) plus **type-specific ("custom") stars** carrying each type's extra attributes and facts (detailed view).
- Also addresses **[[Header-Line Fact Tables|header/line]]** transaction structures and mixing transaction types in one process.

## Principle
Avoid one giant, mostly-null table; split by type while keeping a conformed core for cross-type reporting.

## Related
[[Supertype and Subtype Schemas]] · [[Header-Line Fact Tables]] · [[Factless Fact Tables]]
