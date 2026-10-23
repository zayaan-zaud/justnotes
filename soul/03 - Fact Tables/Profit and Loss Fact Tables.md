---
tags: [fact-table, profitability, allocation]
source: [Kimball ch.6-7]
---
# Profit and Loss (P&L) Fact Tables

A line-grain fact table that builds up a full **profit-and-loss statement** for each atomic transaction line: gross revenue, then a sequence of **[[Allocated Facts|allocated]]** cost and discount components, culminating in a contribution/profit measure.

## Shape
Columns march down the income statement as additive facts:
`gross revenue − discounts − allowances − cost of goods − freight − ... = contribution margin`.

## Value & caution
- Enables profitability analysis by **any dimension** (customer, product, region, channel).
- Only as trustworthy as its **allocation rules** — many components are allocated from higher levels, so results are directional unless the business owns and blesses the allocations.

## Related
[[Allocated Facts]] · [[Header-Line Fact Tables]] · [[Additive, Semi-Additive, and Non-Additive Facts]]
