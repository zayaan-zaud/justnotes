---
tags: [dimension, scd, hybrid, advanced]
source: [Kimball ch.2 & 5]
---
# SCD Type 7 — Dual Type 1 and Type 2 Dimensions

The final hybrid supporting **both as-was and as-is** reporting — by exposing the **same underlying [[SCD Type 2 - Add New Row|Type 2]] dimension through two lenses**.

## Mechanics
- The fact table carries **both** the **surrogate key** (which resolves to the **historical/Type 2** view) **and** the **durable key** (which resolves to a **current/Type 1** view).
- Reporting: 
  - join via the surrogate key → **as-was** (historical) attribute values;
  - join via the durable key to a current-view dimension (or a Type 1 view keyed on the durable key) → **as-is** (current) values.

## Type 7 vs Type 6
- **Type 6:** one dimension row holds both current and historical columns.
- **Type 7:** one physical table, **two separate dimension views/roles**; the fact table's dual keys pick which perspective a query uses.

## Related
[[SCD Type 2 - Add New Row]] · [[SCD Type 6 - Type 1 Attributes on Type 2 Dimension]] · [[Natural, Durable, and Supernatural Keys]] · [[Dimension Surrogate Keys]]
