---
tags: [fact-table, currency, advanced]
source: [Kimball ch.2 & 6]
---
# Multiple Currency Facts

For transactions occurring in different currencies, store each monetary fact **twice**:
- in the **local/transaction currency** (the amount as it actually occurred), and
- in a single **standardized currency** (e.g., corporate USD/EUR) using the exchange rate at transaction time.

Add a **currency dimension** identifying the local currency of the row. This lets local users see native amounts while the enterprise rolls everything up in one comparable currency — additively.

## Related
[[Conformed Facts]] · [[Multiple Units of Measure Facts]] · [[Additive, Semi-Additive, and Non-Additive Facts]]
