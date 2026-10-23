---
tags: [big-data, architecture, kimball]
source: [Kimball ch.21]
---
# Big Data Analytics

Kimball's final chapter positions **big data** (Hadoop/NoSQL era) alongside — not against — dimensional modeling, offering best-practice recommendations across management, architecture, data modeling, and governance.

## Key stances
- **Dimensional thinking still applies.** Grain, conformed dimensions, durable keys, and the bus matrix remain the vocabulary for making big data analyzable and integrated.
- **Extend, don't abandon.** Big-data platforms handle volume, semi-structured data, and schema-on-read, but the presentation layer users consume should still be **dimensional and conformed**.
- **Governance and metadata** grow *more* important with scale and variety, not less.
- Recommends practices around **data highways**, **fact/dimension extraction from unstructured sources**, and integrating results back into the conformed [[Enterprise Data Warehouse Bus Architecture|bus]].

## Related
[[Enterprise Data Warehouse Bus Architecture]] · [[Conformed Dimensions]] · [[ETL 34 Subsystems]]
