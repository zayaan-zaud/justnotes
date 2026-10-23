---
tags: [grain, fundamentals, design-process]
source: [Kimball ch.2-3, Adamson ch.3]
---
# Grain

The **grain** is the precise definition of **what a single fact table row represents**. Declaring the grain is Step 2 of the [[Four-Step Dimensional Design Process]] and is the single most important decision in a dimensional design.

## Why it's critical
- The grain **fixes the meaning** of every row, which in turn determines which [[Dimension Tables|dimensions]] and [[Fact Tables|facts]] are possible. A dimension or fact is only admissible if it is true at the declared grain.
- **Never mix grains** in one fact table. Mixing forces users (and BI tools) into error-prone, conditional aggregation.

## Prefer the lowest (atomic) grain
Kimball's strong recommendation: build fact tables at the **most atomic (finest) grain** the source data supports.
- Atomic data is the most **dimensional** — it can be sliced by the maximum number of attributes.
- It is the most **flexible** — it answers questions you haven't thought of yet and can be rolled up any way.
- Higher-grain **[[Aggregate Fact Tables|aggregates]]** are performance add-ons layered *on top of* atomic data, never a replacement for it.

## Two ways to state grain
1. **In business terms** — "one row per line item on a retail sales ticket." (Preferred; most robust.)
2. **In terms of dimensions** — implied by the combination of foreign keys. Riskier because it can drift.

## Related
[[Four-Step Dimensional Design Process]] · [[Fact Tables]] · [[Transaction Fact Tables]] · [[Periodic Snapshot Fact Tables]] · [[Accumulating Snapshot Fact Tables]] · [[Aggregate Fact Tables]] · [[Merging Multiple Sources with FULL JOIN + COALESCE]]
