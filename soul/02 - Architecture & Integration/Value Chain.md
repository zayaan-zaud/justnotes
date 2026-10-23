---
tags: [architecture, planning]
source: [Kimball ch.2 & 4-7]
---
# Value Chain

An organization's **value chain** is the natural end-to-end sequence of [[Business Process (Measurement Event)|business processes]] that create and deliver its products/services — e.g., for a retailer: *purchase orders → inventory → deliveries → store sales → returns*.

## Why it matters to modeling
- Each **link** in the chain is a candidate **[[Fact Tables|fact table]]** at its own atomic [[Grain|grain]].
- Links **share dimensions** (product, date, store...), which therefore must be **[[Conformed Dimensions|conformed]]**. The value chain is essentially the row structure of the **[[Enterprise Data Warehouse Bus Matrix|bus matrix]]**.
- Modeling the whole chain lets analysts follow performance across stages and [[Drilling Across|drill across]] them (e.g., compare what was ordered vs. received vs. sold).

## Consolidated views
Combining metrics from multiple value-chain steps into one table for convenience/performance yields a [[Consolidated Fact Tables|consolidated fact table]] (built carefully, since the steps have different natural grains).

## Related
[[Business Process (Measurement Event)]] · [[Enterprise Data Warehouse Bus Matrix]] · [[Conformed Dimensions]] · [[Consolidated Fact Tables]]
