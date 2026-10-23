---
tags: [dimension, bridge, many-to-many, advanced]
source: [Kimball ch.2 & 8-10, Adamson ch.9]
---
# Bridge Tables (Multivalued Dimensions)

Sometimes a single fact row legitimately relates to **many values** of a dimension — a hospital charge tied to **multiple diagnoses**, a bank account with **multiple customers**, a product with **multiple features**. A plain FK can't express this many-to-many relationship. The solution is a **bridge table**.

## Structure
- Group the multiple values into a **group** (e.g., a "diagnosis group"), give the group a key, and place that **group key** in the fact table (or in the dimension).
- The **bridge table** has one row per (group, member) pair, so a group of N values yields N rows.
- Optionally store a **weighting/allocation factor** on each bridge row so measures can be correctly **allocated** across members and still sum to 100% (avoiding double counting).

## Trade-off
- **Weighted** bridges give correct additive rollups but require the allocation factor.
- **Unweighted** ("impact") queries can double-count — acceptable for "how many charges *involved* diagnosis X" style questions if users understand it.

## Time-varying bridges
When the group membership itself changes over time, add effective/expiration dates to the bridge (Kimball: *time-varying multivalued bridge tables*).

## Related
[[Ragged Hierarchies and Bridge Tables]] · [[Recursive Hierarchies]] · [[Fact Tables]] · [[Dimension Tables]]
