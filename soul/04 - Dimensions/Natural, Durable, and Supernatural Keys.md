---
tags: [dimension, keys]
source: [Kimball ch.2 & 19]
---
# Natural, Durable, and Supernatural Keys

Three related identifiers that coexist with the [[Dimension Surrogate Keys|surrogate key]]:

- **Natural key** — the operational/business identifier from the source system (customer ID, SKU). It is kept as an **attribute** of the dimension, not as the primary key. Under [[SCD Type 2 - Add New Row|Type 2]], one natural key maps to **many** surrogate keys (one per version).
- **Durable (supernatural) key** — a warehouse-assigned key that **permanently and uniquely** identifies an entity across **all** its Type 2 versions and even across source-system changes. Persistent and unchanging, it lets you group every version of the same customer/product regardless of surrogate-key churn or source re-keying.
- The durable key is "supernatural" because, unlike the natural key, it is **immune to operational reuse or reformatting**.

## Why both
- **Surrogate key** = per-version primary key (join target).
- **Natural key** = lineage back to the source.
- **Durable key** = stable identity across versions (used to find "the current row for this entity," and to link mini-dimensions, etc.).

## Related
[[Dimension Surrogate Keys]] · [[SCD Type 2 - Add New Row]] · [[SCD Type 4 - Mini-Dimension]] · [[Late Arriving Dimensions]]
