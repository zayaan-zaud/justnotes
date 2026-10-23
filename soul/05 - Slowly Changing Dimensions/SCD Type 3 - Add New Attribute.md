---
tags: [dimension, scd, history]
source: [Kimball ch.2 & 5]
---
# SCD Type 3 — Add New Attribute

Preserve a **limited** amount of history by adding **new column(s)** rather than new rows. The row keeps both a **"current"** and a **"previous"** (or "original") value of the attribute side by side.

## Use for
- **Soft / alternate realities** — a planned or occasional relabeling where users want to analyze facts by **either** the old **or** the new grouping simultaneously (e.g., a sales-territory realignment: report under both the prior and new territory).

## Limits
- Captures only a **fixed number of prior versions** (usually one). Not for attributes that change frequently or need complete history — use [[SCD Type 2 - Add New Row|Type 2]] for that.
- Often combined with Type 2 on other attributes.

## Related
[[Slowly Changing Dimensions]] · [[SCD Type 2 - Add New Row]] · [[SCD Type 6 - Type 1 Attributes on Type 2 Dimension]]
