---
tags: [etl, data-quality, kimball]
source: [Kimball ch.2 & 19]
---
# Error Event Schema

A special-purpose dimensional model **inside the ETL system** that records **data-quality errors** as they are detected. Each screen/quality check that fails emits an **error event** row.

## Shape
- A fact table with **one row per error event** (a [[Factless Fact Tables|factless]]/event-tracking pattern), dimensioned by the check, the table/column, severity, batch, and date.
- Enables trending and governance of data quality over time and feeds the **[[Audit Dimensions|audit dimension]]**.

## Related
[[Audit Dimensions]] · [[ETL 34 Subsystems]] · [[Factless Fact Tables]]
