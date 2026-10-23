---
tags: [fact-table, dimension, design-decision]
source: [Kimball ch.2 & 6]
---
# Numeric Values: Attribute or Fact?

A recurring design judgment: a given number can plausibly live as a **fact** (in the fact table) or as a **dimension attribute**. 

## Guideline
- If the value is **measured repeatedly and participates in calculations/aggregations** (summed, averaged), it's a **[[Fact Tables|fact]]**.
- If it's a **stable descriptor used for constraining, grouping, and labeling** (e.g., a standard list price, a size), it's a **dimension attribute**.
- Many values legitimately appear **in both places** — e.g., a list price as a slowly changing attribute of the product dimension *and* as the price fact captured on each sale.

Consider how users will query it: filter/group → attribute; aggregate → fact.

## Related
[[Fact Tables]] · [[Dimension Tables]] · [[Additive, Semi-Additive, and Non-Additive Facts]] · [[Dynamic Value Banding]]
