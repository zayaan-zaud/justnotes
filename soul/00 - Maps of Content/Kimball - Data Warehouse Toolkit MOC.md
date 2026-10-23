---
tags: [moc, index, kimball]
source: [Kimball, The Data Warehouse Toolkit, 3rd ed.]
---
# Kimball — *The Data Warehouse Toolkit* (3rd ed.) MOC

Chapter-by-chapter map into the concept notes. Kimball's book is organized as a **techniques reference (ch.1–2)** followed by **case-study chapters (ch.3–16)** that reteach the techniques in business contexts, then **lifecycle/ETL chapters (ch.17–21)**.

## Part 1 — Foundations
- **Ch.1 — DW/BI & Dimensional Modeling Primer:** [[Dimensional Modeling]] · [[Star Schema]] · [[Kimball DW-BI Architecture]] · [[Inmon Corporate Information Factory]] · [[Independent Data Marts]]
- **Ch.2 — Dimensional Modeling Techniques Overview:** the master catalog — see every note in [[Dimensional Modeling MOC]]. Key anchors: [[Four-Step Dimensional Design Process]] · [[Grain]] · [[Enterprise Data Warehouse Bus Matrix]] · [[Slowly Changing Dimensions]]

## Part 2 — Case studies (technique introductions)
- **Ch.3 Retail Sales:** [[Four-Step Dimensional Design Process]] · [[Transaction Fact Tables]] · [[Calendar Date Dimension]] · [[Degenerate Dimensions]] · [[Dimension Surrogate Keys]]
- **Ch.4 Inventory:** [[Periodic Snapshot Fact Tables]] · [[Additive, Semi-Additive, and Non-Additive Facts]] · [[Enterprise Data Warehouse Bus Architecture]] · [[Value Chain]]
- **Ch.5 Procurement:** [[Slowly Changing Dimensions]] (Types 0–7) · [[SCD Type 4 - Mini-Dimension]]
- **Ch.6 Order Management:** [[Accumulating Snapshot Fact Tables]] · [[Role-Playing Dimensions]] · [[Junk Dimensions]] · [[Header-Line Fact Tables]] · [[Allocated Facts]] · [[Multiple Currency Facts]]
- **Ch.7 Accounting:** [[Profit and Loss Fact Tables]] · [[Consolidated Fact Tables]] · [[Dimension Hierarchies]] · [[Ragged Hierarchies and Bridge Tables]] · [[Multipass SQL & Fact-to-Fact Joins]]
- **Ch.8 CRM:** [[Bridge Tables (Multivalued Dimensions)]] · [[Aggregated Facts as Dimension Attributes]] · [[Behavior Study Groups]] · [[Timespan Tracking in Fact Tables]]
- **Ch.9 HR Management:** [[Recursive Hierarchies]] · [[SCD Type 3 - Add New Attribute]]
- **Ch.10 Financial Services:** [[Supertype and Subtype Schemas]] · [[Dynamic Value Banding]] · [[SCD Type 4 - Mini-Dimension]]
- **Ch.11 Telecommunications** · **Ch.12 Transportation** · **Ch.13 Education** ([[Factless Fact Tables]]) · **Ch.14 Healthcare** ([[Bridge Tables (Multivalued Dimensions)]]) · **Ch.15 Electronic Commerce** · **Ch.16 Insurance** (capstone review of most techniques)

## Part 3 — Lifecycle & ETL
- **Ch.17 Lifecycle Overview:** [[Kimball DW-BI Lifecycle]]
- **Ch.18 Dimensional Modeling Process & Tasks:** [[Dimensional Modeling Process and Tasks]] · [[Collaborative Dimensional Modeling Workshops]]
- **Ch.19 ETL Subsystems & Techniques:** [[ETL 34 Subsystems]] · [[Audit Dimensions]] · [[Error Event Schema]] · [[Late Arriving Dimensions]]
- **Ch.20 ETL System Design Process:** [[ETL System Design Process]]
- **Ch.21 Big Data Analytics:** [[Big Data Analytics]]

## Related
[[Dimensional Modeling MOC]] · [[Adamson - Star Schema MOC]]
