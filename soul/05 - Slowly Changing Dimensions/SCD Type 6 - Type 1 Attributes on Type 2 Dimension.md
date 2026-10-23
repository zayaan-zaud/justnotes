---
tags: [dimension, scd, hybrid, advanced]
source: [Kimball ch.2 & 5]
---
# SCD Type 6 — Type 1 Attributes on a Type 2 Dimension

**Type 6 = 1 + 2 + 3 (all sum to 6).** A hybrid that delivers **both** historical (as-was) and current (as-is) values of the **same** attribute from **one** dimension.

## Mechanics
- Build the dimension as **[[SCD Type 2 - Add New Row|Type 2]]** (versioned rows preserve the value in effect at each event).
- **Additionally embed "current" Type 1 column(s)** for the same attribute on **every** row. Whenever the attribute changes, ETL **overwrites the current-value column on all rows sharing that entity's [[Natural, Durable, and Supernatural Keys|durable key]]**.

## Result
On any fact row you can filter/group by either:
- the **historical** value (the Type 2 column of the row that was current at event time), or
- the **current** value (the overwritten Type 1 column).

## Type 6 vs Type 7
Type 6 puts both perspectives **in one dimension row**; [[SCD Type 7 - Dual Type 1 and Type 2 Dimensions|Type 7]] exposes two **separate** dimension views over the same table.

## Related
[[SCD Type 2 - Add New Row]] · [[SCD Type 3 - Add New Attribute]] · [[SCD Type 7 - Dual Type 1 and Type 2 Dimensions]] · [[Natural, Durable, and Supernatural Keys]]
