---
tags: [dimension, scd, moc]
source: [Kimball ch.2 & 5, Adamson ch.3 & 8]
---
# Slowly Changing Dimensions (SCD)

Dimension attributes change over time (a customer moves, a product is recategorized). **Slowly Changing Dimension** techniques define **how the warehouse responds** to a change in a source attribute — the central question being whether to **preserve history** or **overwrite it**. Kimball 3rd edition codifies **eight types, 0–7**.

## The two philosophies
- **As-is** reporting: facts should reflect **current** attribute values → overwrite ([[SCD Type 1 - Overwrite|Type 1]]).
- **As-was** reporting: facts should reflect the attribute value **in effect when the measurement happened** → preserve history ([[SCD Type 2 - Add New Row|Type 2]]).
Hybrids (5, 6, 7) deliver **both** views.

## The eight types
| Type | Name | Effect |
|---|---|---|
| [[SCD Type 0 - Retain Original\|0]] | Retain original | Value never changes (e.g., date of birth) |
| [[SCD Type 1 - Overwrite\|1]] | Overwrite | No history; current value only |
| [[SCD Type 2 - Add New Row\|2]] | Add new row | Full history via new versioned row + surrogate key |
| [[SCD Type 3 - Add New Attribute\|3]] | Add new attribute | Limited history in extra column(s) ("current"/"previous") |
| [[SCD Type 4 - Mini-Dimension\|4]] | Mini-dimension | Split fast-changing attributes into a separate small dimension |
| [[SCD Type 5 - Mini-Dimension + Type 1 Outrigger\|5]] | 4 + Type 1 outrigger | Type 4 plus current mini-dim reference in base dim |
| [[SCD Type 6 - Type 1 Attributes on Type 2 Dimension\|6]] | 1+2+3 hybrid | Type 2 rows plus current-value columns |
| [[SCD Type 7 - Dual Type 1 and Type 2 Dimensions\|7]] | Dual | Both a Type 1 (current) and Type 2 (historical) view of one dimension |

Mnemonic: **Types 5, 6, 7 all "add up"** to techniques that combine 1 with the historical types (4+1=5, 2+... =6, 1&2=7).

## Prerequisite
Types 2, 6, 7 depend on **[[Dimension Surrogate Keys|surrogate keys]]** and a **[[Natural, Durable, and Supernatural Keys|durable key]]**.

## Related
[[Dimension Surrogate Keys]] · [[Natural, Durable, and Supernatural Keys]] · [[Late Arriving Dimensions]]
