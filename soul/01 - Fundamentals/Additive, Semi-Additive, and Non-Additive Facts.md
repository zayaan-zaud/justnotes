---
tags: [fact-table, fundamentals, additivity]
source: [Kimball ch.1-2, Adamson ch.3 & 11]
---
# Additive, Semi-Additive, and Non-Additive Facts

Because BI queries sum facts across millions of rows, the **additivity** of each measurement determines how it may legitimately be aggregated. Classifying facts by additivity is a core modeling task.

## Additive facts
Can be summed across **every** dimension, including [[Calendar Date Dimension|time]]. The ideal, most useful kind (e.g., *sales amount*, *quantity sold*). Design fact tables to hold additive measures wherever possible.

## Semi-additive facts
Can be summed across some dimensions but **not across time**. Classic example: **balances and levels** — account balance, inventory quantity on hand. Summing an inventory level across days is meaningless; instead you **average** over time (or take an ending value). Common in [[Periodic Snapshot Fact Tables]].

## Non-additive facts
Cannot be summed across **any** dimension. Chiefly **ratios and percentages** (e.g., unit price, gross margin %, temperature). 
**Rule of thumb:** store the fully additive *components* (numerator and denominator) as separate facts and compute the ratio **after** aggregation in the BI layer — never store and sum the ratio itself.

## Related
[[Fact Tables]] · [[Grain]] · [[Periodic Snapshot Fact Tables]] · [[Conformed Facts]] · [[Numeric Values - Attribute or Fact]]
