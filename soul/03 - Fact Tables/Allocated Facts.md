---
tags: [fact-table, allocation, advanced, profitability]
source: [Kimball ch.2 & 6-7]
---
# Allocated Facts

Many valuable measures exist only at a **header** or aggregate level (freight cost, invoice discount, overhead) but are most useful at the **line-item grain** for profitability analysis. **Allocation** pushes those higher-level amounts **down** to the atomic grain using agreed business rules (by weight, by value, by quantity...).

## Why
- Enables **[[Header-Line Fact Tables|line-level]]** profit & loss: revenue − allocated costs = contribution at the most atomic level, sliceable by any dimension.
- Without allocation, you can't compute true product/customer profitability.

## Cautions
- Allocation rules are a **business/political decision** — get finance to own them; the modeler implements, doesn't invent, the rules.
- Do allocation in **ETL**, storing the already-allocated facts, so all users share one consistent version.

## Related
[[Header-Line Fact Tables]] · [[Profit and Loss Fact Tables]] · [[Fact Tables]]
