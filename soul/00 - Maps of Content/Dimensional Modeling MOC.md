---
tags: [moc, index, dimensional-modeling]
source: [Kimball 3rd ed. + Adamson Star Schema]
---
# Dimensional Modeling — Master MOC

Home base for this vault. Notes distilled from **Ralph Kimball, *The Data Warehouse Toolkit* (3rd ed.)** and **Christopher Adamson, *Star Schema: The Complete Reference***. Both teach the same core discipline; where they differ or complement, the notes say so.

Per-book maps: [[Kimball - Data Warehouse Toolkit MOC]] · [[Adamson - Star Schema MOC]]

## 1. Fundamentals
[[Dimensional Modeling]] · [[Star Schema]] · [[Fact Tables]] · [[Dimension Tables]] · [[Grain]] · [[Business Process (Measurement Event)]] · [[Four-Step Dimensional Design Process]] · [[Additive, Semi-Additive, and Non-Additive Facts]] · [[Star Schemas vs OLAP Cubes]]

## 2. Architecture & Integration
[[Kimball DW-BI Architecture]] · [[Inmon Corporate Information Factory]] · [[Independent Data Marts]] · [[Enterprise Data Warehouse Bus Architecture]] · [[Enterprise Data Warehouse Bus Matrix]] · [[Conformed Dimensions]] · [[Conformed Facts]] · [[Drilling Across]] · [[Value Chain]]

## 3. Fact Tables
**Three grains:** [[Transaction Fact Tables]] · [[Periodic Snapshot Fact Tables]] · [[Accumulating Snapshot Fact Tables]]
**Other types:** [[Factless Fact Tables]] · [[Aggregate Fact Tables]] · [[Consolidated Fact Tables]]
**Advanced:** [[Advanced Fact Table Techniques]] · [[Fact Table Surrogate Keys]] · [[Allocated Facts]] · [[Header-Line Fact Tables]] · [[Profit and Loss Fact Tables]] · [[Multiple Currency Facts]] · [[Multiple Units of Measure Facts]] · [[Lag and Duration Facts]] · [[Timespan Tracking in Fact Tables]] · [[Late Arriving Facts]] · [[Numeric Values - Attribute or Fact]] · [[Multipass SQL & Fact-to-Fact Joins]]

## 4. Dimensions
[[Dimension Surrogate Keys]] · [[Natural, Durable, and Supernatural Keys]] · [[Degenerate Dimensions]] · [[Junk Dimensions]] · [[Role-Playing Dimensions]] · [[Calendar Date Dimension]] · [[Snowflaked Dimensions]] · [[Outrigger Dimensions]] · [[Shrunken (Conformed) Dimensions]] · [[Dimension Hierarchies]] · [[Ragged Hierarchies and Bridge Tables]] · [[Bridge Tables (Multivalued Dimensions)]] · [[Flags and Indicators as Textual Attributes]] · [[Null Attributes in Dimensions]] · [[Centipede Fact Tables]] · [[Dynamic Value Banding]]

## 5. Slowly Changing Dimensions
[[Slowly Changing Dimensions]] — overview + the 8 types:
[[SCD Type 0 - Retain Original|Type 0]] · [[SCD Type 1 - Overwrite|Type 1]] · [[SCD Type 2 - Add New Row|Type 2]] · [[SCD Type 3 - Add New Attribute|Type 3]] · [[SCD Type 4 - Mini-Dimension|Type 4]] · [[SCD Type 5 - Mini-Dimension + Type 1 Outrigger|Type 5]] · [[SCD Type 6 - Type 1 Attributes on Type 2 Dimension|Type 6]] · [[SCD Type 7 - Dual Type 1 and Type 2 Dimensions|Type 7]]

## 6. Advanced Techniques
[[Recursive Hierarchies]] · [[Supertype and Subtype Schemas]] · [[Audit Dimensions]] · [[Late Arriving Dimensions]] · [[Aggregated Facts as Dimension Attributes]] · [[Behavior Study Groups]]

## 7. Process, Lifecycle & ETL
[[Kimball DW-BI Lifecycle]] · [[Dimensional Modeling Process and Tasks]] · [[Collaborative Dimensional Modeling Workshops]] · [[ETL 34 Subsystems]] · [[ETL System Design Process]] · [[Error Event Schema]] · [[Big Data Analytics]]

## 8. Performance & Aggregates
[[Derived Schemas]] · [[Aggregate Design and Navigation]] · [[Pivoted Derived Tables (Adamson)]] · [[Sliced Derived Tables (Adamson)]] · [[Set Operation Derived Tables (Adamson)]] · [[Type-Specific Stars (Adamson)]] · [[Design Documentation (Adamson)]]

---
### The essential mental model
> A **[[Business Process (Measurement Event)|business process]]** produces measurements → captured as **[[Fact Tables|facts]]** at a declared **[[Grain|grain]]** → surrounded by descriptive **[[Dimension Tables|dimensions]]** → forming a **[[Star Schema|star]]** → integrated enterprise-wide through **[[Conformed Dimensions|conformed dimensions]]** on the **[[Enterprise Data Warehouse Bus Architecture|bus]]**.
