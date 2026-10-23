---
tags: [etl, process, kimball, moc]
source: [Kimball ch.19-20]
---
# The 34 Subsystems of ETL

Kimball decomposes the ETL system into **34 subsystems** grouped into **four major areas**. The point: "ETL" is not one program but a large set of reusable services; naming them all forces you to design for the hard parts (change data capture, conforming, SCD management, error handling) instead of only the happy path.

## The four areas
1. **Extracting** (subsystems 1–3) — get raw data out of sources: **data profiling**, **change data capture (CDC)**, and the **extract system**.
2. **Cleaning & Conforming** (4–8) — the quality core: **data-cleansing system**, **error event schema**, **audit dimension** assembly, **deduplication**, and the **conforming system** that standardizes [[Conformed Dimensions|conformed dimensions]] and [[Conformed Facts|facts]].
3. **Delivering** (9–21) — prepare data for presentation: the **[[Slowly Changing Dimensions|SCD]] manager**, **surrogate key generator/pipeline**, **[[Bridge Tables (Multivalued Dimensions)|bridge]] & hierarchy managers**, **[[Late Arriving Dimensions|late-arriving data]] handler**, fact-table loaders (transaction/snapshot/accumulating), **[[Aggregate Fact Tables|aggregate]] builder**, OLAP cube builder, etc.
4. **Managing** (22–34) — operate it reliably: **job scheduler**, **backup/recovery**, **version control**, **lineage & dependency**, **problem escalation**, **compliance manager**, **metadata repository**, and more.

## Why it matters
- Most ETL effort is in **cleaning/conforming and delivering**, not extraction.
- Subsystems like the **surrogate key generator**, **SCD manager**, and **conforming system** directly implement the modeling techniques ([[Dimension Surrogate Keys]], SCD Types 1–7, conformance).

## Related
[[ETL System Design Process]] · [[Slowly Changing Dimensions]] · [[Conformed Dimensions]] · [[Late Arriving Dimensions]] · [[Error Event Schema]] · [[Audit Dimensions]]
