---
tags: [process, documentation, adamson]
source: [Adamson ch.16-18]
---
# Designing and Documenting a Dimensional Model (Adamson)

Adamson's closing chapters (16–18) cover how a dimensional design meets **BI tools** and **ETL**, and how to **document** the model so it is buildable and maintainable.

## Design & BI (ch.16)
- Configure BI tools around the star: define the **[[Dimension Hierarchies|drill paths]]**, [[Drilling Across|drill-across]] rules, and which [[Aggregate Fact Tables|aggregates]] the navigator may use.
- Guard against fact-to-fact joins and non-additive pitfalls in the semantic layer.

## Design & ETL (ch.17)
- Map **source → target**, specify **[[Slowly Changing Dimensions|SCD]]** behavior per attribute, surrogate-key assignment, and load order (dimensions before facts).

## Documentation (ch.18)
A complete design package captures: **grain** of each fact table, the **[[Enterprise Data Warehouse Bus Matrix|conformance/bus matrix]]**, every attribute and fact with definitions, **SCD type per attribute**, keys, and source mappings. This documentation *is* the contract between modeler, ETL, and BI.

## Related
[[Dimensional Modeling Process and Tasks]] · [[Enterprise Data Warehouse Bus Matrix]] · [[Slowly Changing Dimensions]] · [[Aggregate Design and Navigation]]
