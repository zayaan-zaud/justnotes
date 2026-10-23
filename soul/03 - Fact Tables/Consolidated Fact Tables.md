---
tags: [fact-table, integration, performance]
source: [Kimball ch.2 & 7, Adamson ch.14]
---
# Consolidated Fact Tables

A fact table that **combines facts from multiple business processes** into a single table at a **common grain**, when those processes are routinely analyzed together.

## Example
Actual **sales** and sales **forecast** are two separate processes/fact tables. Analysts constantly compare them, which normally requires [[Drilling Across|drilling across]]. A **consolidated** table pre-joins them at a shared grain (e.g., product-brand by month by region) so variance queries are fast and simple.

## Trade-offs
- **Pro:** convenience and performance for cross-process analysis; avoids repetitive multipass drill-across.
- **Con:** the source processes usually have **different natural grains**, so consolidation forces them to a common (often summarized) grain and adds ETL complexity/dependency. Build them only for genuinely common combined analyses, and **after** the underlying single-process fact tables exist.

## Requirement
The facts being combined must be **[[Conformed Facts|conformed]]** and expressed at compatible grains via **[[Conformed Dimensions|conformed dimensions]]**.

## Related
[[Drilling Across]] · [[Conformed Facts]] · [[Aggregate Fact Tables]] · [[Value Chain]] · [[Derived Schemas]] · [[Merging Multiple Sources with FULL JOIN + COALESCE]]
