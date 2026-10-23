---
tags: [fact-table, grain, snapshot]
source: [Kimball ch.2 & 4, Adamson ch.11]
---
# Periodic Snapshot Fact Tables

**Grain = one row per entity per regular time period** (e.g., one row per account per month, one row per product per store per day). Captures the **cumulative state / performance** of a process over a span, sampled at consistent intervals.

## Characteristics
- **Predictable, dense** table: rows are inserted every period whether or not activity occurred (unlike sparse transaction tables).
- Facts commonly include **[[Additive, Semi-Additive, and Non-Additive Facts|semi-additive]]** measures — **balances and levels** (inventory on hand, account balance) that sum across all dimensions **except time** (average over time instead).
- Ideal for trending, coverage, and "how much was there" questions.

## When to use
- Measuring **status/level** where transactions are too granular or don't capture standing state (inventory levels, account balances, subscriptions).
- Complements a [[Transaction Fact Tables|transaction fact table]] on the same process — the two answer different questions.

## Related
[[Fact Tables]] · [[Grain]] · [[Transaction Fact Tables]] · [[Accumulating Snapshot Fact Tables]] · [[Additive, Semi-Additive, and Non-Additive Facts]]
