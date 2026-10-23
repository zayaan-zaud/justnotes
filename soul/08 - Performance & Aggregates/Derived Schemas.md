---
tags: [performance, derived, adamson, moc]
source: [Adamson ch.14]
---
# Derived Schemas

A **derived schema** is a new star (or table) built **from an existing star** to serve a specific need — usability, performance, or a particular analytic pattern — without re-sourcing from operational systems. Adamson (ch.14) treats these as first-class design tools.

## Main kinds
- **[[Consolidated Fact Tables|Merged/consolidated fact tables]]** — combine facts from multiple stars at a common grain for cross-process analysis (e.g., actual vs. plan).
- **[[Pivoted Derived Tables (Adamson)|Pivoted tables]]** — restructure rows↔columns (normalize repeating columns into rows, or pivot type-values into columns) to fit a reporting need.
- **[[Sliced Derived Tables (Adamson)|Sliced tables]]** — a partition/subset of a star (e.g., a single region's data) for distribution or performance.
- **[[Set Operation Derived Tables (Adamson)|Set-operation tables]]** — built via union/intersect/minus of stars (e.g., a "customers who did A but not B" table).
- **[[Aggregate Fact Tables|Aggregate tables]]** — the most important derived schema; see the dedicated note.

## Principle
Derived schemas are **built from the atomic star**, keep **[[Conformed Dimensions|conformance]]**, and are managed as dependents that must be refreshed when the base changes.

## Related
[[Aggregate Fact Tables]] · [[Consolidated Fact Tables]] · [[Pivoted Derived Tables (Adamson)]] · [[Sliced Derived Tables (Adamson)]] · [[Set Operation Derived Tables (Adamson)]]
