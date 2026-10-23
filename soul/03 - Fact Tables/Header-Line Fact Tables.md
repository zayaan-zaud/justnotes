---
tags: [fact-table, advanced, transactions]
source: [Kimball ch.6, Adamson ch.13]
---
# Header/Line Fact Tables

Operational transactions often come as a **parent header** (one per order/invoice) with multiple **child lines** (one per product on the order). Naively modeling the header as its own fact table creates grain and join problems.

## Kimball guidance
- **Model at the line grain.** Discard the header "fact table"; carry header-level *context* as [[Degenerate Dimensions|degenerate dimensions]] (order number) and dimension FKs on each line row.
- **[[Allocated Facts|Allocate]]** header-level amounts (shipping, order discount) **down to the lines** so every measure lives at the single line grain.
- This avoids fact-to-fact header/line joins and keeps everything additive at one grain.

## Related
[[Allocated Facts]] · [[Degenerate Dimensions]] · [[Transaction Fact Tables]] · [[Grain]]
