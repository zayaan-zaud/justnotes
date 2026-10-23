---
tags: [fact-table, advanced, collection]
source: [Kimball ch.2 & 6-7 & 12, Adamson ch.11-13]
---
# Advanced Fact Table Techniques

A reference collection of the specialized fact-table patterns from Kimball ch.2 (with worked examples across the case-study chapters). Each links to fuller notes where they exist.

## Structure & keys
- **Fact table surrogate key** — a single-column surrogate PK on the fact table itself (not a dimension FK). Useful for identifying/reprocessing individual rows, resuming interrupted loads, and updating specific rows. See [[Fact Table Surrogate Keys]].
- **[[Degenerate Dimensions]]** — operational identifiers (order #, ticket #) kept in the fact table with no dimension.
- **[[Centipede Fact Tables]]** — anti-pattern: too many (dozens of) normalized dimension FKs; fix with [[Junk Dimensions]] and by grouping correlated attributes.

## Allocation & money
- **[[Allocated Facts]]** — pushing header-level amounts (freight, discounts) down to the line grain via allocation rules; enables profit & loss (P&L) fact tables.
- **[[Header-Line Fact Tables]]** — modeling parent/child transaction documents; generally drop the header, model at the line grain, allocate header facts down.
- **[[Multiple Currency Facts]]** — store each amount twice (local + standard currency) plus a currency dimension.
- **[[Multiple Units of Measure Facts]]** — store facts once in a base unit plus conversion factors as facts.

## Time & derived measures
- **[[Lag and Duration Facts]]** — elapsed time between milestones (esp. [[Accumulating Snapshot Fact Tables]]).
- **Year-to-date facts** — generally compute in BI, don't store, to avoid ambiguity.
- **[[Timespan Tracking in Fact Tables]]** — effective/expiration timestamps on fact rows to reconstruct point-in-time state.
- **[[Numeric Values - Attribute or Fact]]** — deciding whether a number is a fact or a dimension attribute.

## Late-arriving data
- **[[Late Arriving Facts]]** — a transaction arrives long after its event date; must look up the dimension keys **in effect at the event date** (esp. with [[SCD Type 2 - Add New Row|Type 2]]).

## Query patterns
- **[[Multipass SQL & Fact-to-Fact Joins]]** — never join fact tables directly; aggregate separately then merge.

## Related
[[Fact Tables]] · [[Drilling Across]] · [[Accumulating Snapshot Fact Tables]]
