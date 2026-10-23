---
tags: [architecture, anti-pattern]
source: [Kimball ch.1, Adamson ch.2]
---
# Independent (Stand-Alone) Data Marts

An architecture — really an **anti-pattern** — in which individual departments build their own analytic databases from source data **without coordination**.

## Why it happens
Each department has its own funding, urgency, and vendor; a single-department mart is quick to deliver and satisfies the immediate need.

## Why it fails at scale
- **Inconsistent, non-[[Conformed Dimensions|conformed]] dimensions** — every mart defines "customer" or "product" differently. Numbers don't reconcile across departments.
- **Redundant ETL** — the same source data is extracted and processed many times.
- **No enterprise view** — you cannot [[Drilling Across|drill across]] processes because keys and labels don't match.
- Becomes a set of "stovepipes" that actively resist integration.

## The fix
Adopt the **[[Enterprise Data Warehouse Bus Architecture]]**: plan a shared set of [[Conformed Dimensions]] up front (the [[Enterprise Data Warehouse Bus Matrix|bus matrix]]) so independently built marts still integrate. You can still deliver one process at a time — just against conformed dimensions.

## Related
[[Enterprise Data Warehouse Bus Architecture]] · [[Conformed Dimensions]] · [[Kimball DW-BI Architecture]]
