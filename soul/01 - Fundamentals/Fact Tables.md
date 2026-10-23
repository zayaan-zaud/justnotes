---
tags: [fact-table, fundamentals]
source: [Kimball ch.1-2, Adamson ch.1 & 3]
---
# Fact Tables

The **fact table** stores the numeric **measurements** produced by a [[Business Process (Measurement Event)|business process]]. It is the largest table in a [[Star Schema]] (rows in the millions/billions) and the target of nearly every analytic query's aggregation.

## Anatomy
A fact table row contains:
- **Foreign keys** to every associated [[Dimension Tables|dimension]] (the context).
- **Facts** — the numeric measures themselves (quantity, amount, etc.).
- Optionally **[[Degenerate Dimensions|degenerate dimension]]** keys (e.g., an order number) that live in the fact table with no dimension of their own.

## Key principles
- **Grain first.** Every fact table must declare a single, clear **[[Grain]]** — the meaning of one row. Never mix grains.
- **Measurements should be [[Additive, Semi-Additive, and Non-Additive Facts|additive]]** wherever possible, since BI tools sum across millions of rows.
- **Sparsity:** fact tables only store rows for events that actually happened (except some [[Factless Fact Tables]] / coverage tables).
- **Narrow but deep:** few columns, enormous row counts. Keep textual/descriptive data out — that belongs in dimensions.

## The three fundamental grains
Almost every fact table is one of three types:
1. [[Transaction Fact Tables]] — one row per measurement event.
2. [[Periodic Snapshot Fact Tables]] — one row per entity per regular period.
3. [[Accumulating Snapshot Fact Tables]] — one row per pipeline instance, updated over its life.

## Related
[[Grain]] · [[Additive, Semi-Additive, and Non-Additive Facts]] · [[Dimension Tables]] · [[Factless Fact Tables]] · [[Conformed Facts]]
