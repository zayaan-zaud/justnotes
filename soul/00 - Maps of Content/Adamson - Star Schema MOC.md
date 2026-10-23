---
tags: [moc, index, adamson]
source: [Adamson, Star Schema: The Complete Reference]
---
 Adamson — *Star Schema: The Complete Reference* MOC

Adamson is **architecture-neutral** (his techniques apply whether you follow [[Kimball DW-BI Architecture|Kimball]] or [[Inmon Corporate Information Factory|Inmon]]) and goes especially deep on **dimension design, bridges, fact-table types, and performance/derived schemas**. Map by part:

 Part I — Fundamentals
- **Ch.1 Analytic Databases & Dimensional Design:** [[Dimensional Modeling]] · [[Star Schema]] · [[Fact Tables]] · [[Dimension Tables]]
- **Ch.2 Data Warehouse Architectures:** [[Inmon Corporate Information Factory]] · [[Kimball DW-BI Architecture]] · [[Independent Data Marts]]
- **Ch.3 Stars and Cubes:** [[Dimension Surrogate Keys]] · [[Grain]] · [[Additive, Semi-Additive, and Non-Additive Facts]] · [[Star Schemas vs OLAP Cubes]]

 Part II — Multiple Stars
- **Ch.4 A Fact Table for Each Process:** [[Business Process (Measurement Event)]] · [[Value Chain]]
- **Ch.5 Conformed Dimensions:** [[Conformed Dimensions]] · [[Shrunken (Conformed) Dimensions]] · [[Drilling Across]] · [[Enterprise Data Warehouse Bus Matrix]]

 Part III — Dimension Design
- **Ch.6 More on Dimension Tables:** [[Degenerate Dimensions]] · [[Junk Dimensions]] · [[Role-Playing Dimensions]] · [[Calendar Date Dimension]] · [[Behavior Study Groups]]
- **Ch.7 Hierarchies and Snowflakes:** [[Dimension Hierarchies]] · [[Snowflaked Dimensions]] · [[Outrigger Dimensions]]
- **Ch.8 More Slow Change Techniques:** [[Slowly Changing Dimensions]] · [[SCD Type 3 - Add New Attribute]] · [[SCD Type 6 - Type 1 Attributes on Type 2 Dimension]]
- **Ch.9 Multi-Valued Dimensions and Bridges:** [[Bridge Tables (Multivalued Dimensions)]]
- **Ch.10 Recursive Hierarchies and Bridges:** [[Recursive Hierarchies]] · [[Ragged Hierarchies and Bridge Tables]]

 Part IV — Fact Table Design
- **Ch.11 Transactions, Snapshots & Accumulating Snapshots:** [[Transaction Fact Tables]] · [[Periodic Snapshot Fact Tables]] · [[Accumulating Snapshot Fact Tables]] · [[Lag and Duration Facts]]
- **Ch.12 Factless Fact Tables:** [[Factless Fact Tables]]
- **Ch.13 Type-Specific Stars:** [[Type-Specific Stars (Adamson)]] · [[Supertype and Subtype Schemas]] · [[Header-Line Fact Tables]]

 Part V — Performance
- **Ch.14 Derived Schemas:** [[Derived Schemas]] · [[Consolidated Fact Tables]] · [[Pivoted Derived Tables (Adamson)]] · [[Sliced Derived Tables (Adamson)]] · [[Set Operation Derived Tables (Adamson)]]
- **Ch.15 Aggregates:** [[Aggregate Fact Tables]] · [[Aggregate Design and Navigation]] · [[Shrunken (Conformed) Dimensions]]

 Part VI — Tools & Documentation
- **Ch.16 Design and Business Intelligence** · **Ch.17 Design and ETL** · **Ch.18 How to Design and Document a Dimensional Model:** [[Design Documentation (Adamson)]] · [[Dimensional Modeling Process and Tasks]]

 Related
[[Dimensional Modeling MOC]] · [[Kimball - Data Warehouse Toolkit MOC]]
