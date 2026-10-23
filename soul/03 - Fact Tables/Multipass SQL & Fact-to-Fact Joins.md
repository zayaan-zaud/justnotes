---
tags: [fact-table, query-technique, integration]
source: [Kimball ch.2 & 7]
---
# Multipass SQL & Avoiding Fact-to-Fact Joins

**Rule: never join two fact tables directly on their shared dimension keys.** Doing so multiplies rows through the many-to-many relationship they share via the dimension, producing inflated, wrong aggregates (fan-out / double counting).

## The correct pattern
Use **multipass SQL** (a.k.a. phased aggregation / [[Drilling Across|drill across]]):
1. Query each fact table **separately**, each grouping by the common conformed dimension attributes.
2. **Full-outer-join the summarized result sets** on those common attributes.
3. Combine the aligned, already-aggregated facts.

Because each fact table is reduced to one row per dimension-attribute combination *before* being joined, the fan-out is eliminated.

## Related
[[Drilling Across]] · [[Conformed Dimensions]] · [[Consolidated Fact Tables]]
