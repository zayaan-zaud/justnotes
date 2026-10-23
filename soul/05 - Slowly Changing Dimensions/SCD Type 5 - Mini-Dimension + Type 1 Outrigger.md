---
tags: [dimension, scd, mini-dimension, outrigger, advanced]
source: [Kimball ch.2 & 5]
---
# SCD Type 5 — Mini-Dimension + Type 1 Outrigger

**Type 5 = [[SCD Type 4 - Mini-Dimension|Type 4]] + a Type 1 reference (4 + 1 = 5).** It preserves historical profiles **and** lets you report by the entity's **current** profile without going through a fact table.

## Mechanics
- Keep the Type 4 **mini-dimension** and the fact-table FK to it (history preserved at event time).
- **Also embed a current mini-dimension FK inside the base dimension** as a **[[Outrigger Dimensions|Type 1 outrigger]]**. ETL **overwrites** this reference whenever the entity's current mini-dimension assignment changes.
- Logically presented as a single joined table, so users can filter/group by the **currently-assigned** profile attributes directly from the base dimension.

## Delivers
Historical facts by **as-was** profile (via fact FK) **and** by **current** profile (via the base-dimension outrigger).

## Related
[[SCD Type 4 - Mini-Dimension]] · [[Outrigger Dimensions]] · [[SCD Type 6 - Type 1 Attributes on Type 2 Dimension]]
