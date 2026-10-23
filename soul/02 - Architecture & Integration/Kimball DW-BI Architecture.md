---
tags: [architecture, kimball]
source: [Kimball ch.1]
---
# Kimball DW-BI Architecture

Kimball's reference architecture for a data warehouse / business intelligence system, made of four separable components. Famously illustrated by the **restaurant metaphor**.

## The four components
1. **Operational source systems** — the transaction systems that capture the business (OLTP). Outside the DW's control; the "farm" the raw ingredients come from.
2. **ETL system (back room / kitchen).** [[ETL 34 Subsystems|Extract, Transform, Load]]. Data is cleaned, conformed, and prepared here. Off-limits to end users, just like a restaurant kitchen.
3. **Presentation area (front room / dining room).** Where data is organized dimensionally as [[Star Schema|star schemas]] / cubes and made available for querying. **This is where the [[Enterprise Data Warehouse Bus Architecture|bus architecture]] and [[Conformed Dimensions]] live.** Must contain **atomic** [[Grain|detail]], not just summaries.
4. **BI applications** — the reports, dashboards, ad-hoc tools, and analytic apps users actually touch.

## Defining principles
- The presentation area is **dimensional**, built from **atomic** data, and organized by [[Business Process (Measurement Event)|business process]].
- Integration is achieved through the **[[Enterprise Data Warehouse Bus Architecture|bus architecture]]** with [[Conformed Dimensions]] — *not* through a centralized normalized store.

## Contrast
Differs from [[Inmon Corporate Information Factory]] (normalized enterprise warehouse feeding marts) and from [[Independent Data Marts]] (uncoordinated silos).

## Related
[[Inmon Corporate Information Factory]] · [[Independent Data Marts]] · [[Enterprise Data Warehouse Bus Architecture]] · [[Kimball DW-BI Lifecycle]] · [[ETL 34 Subsystems]]
