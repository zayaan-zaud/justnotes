---
tags: [dimension, scd, mini-dimension, advanced]
source: [Kimball ch.2 & 5 & 10]
---
# SCD Type 4 — Add Mini-Dimension

For a large dimension containing a **handful of rapidly changing attributes**, splitting off those volatile attributes into a separate **mini-dimension** avoids Type 2's explosive row growth.

## Mechanics
- Remove the **fast-changing attributes** (often continuously changing ones like age band, income band, credit-score band — frequently **[[Dynamic Value Banding|banded]]** to limit cardinality) from the base dimension.
- Put them in a **mini-dimension** whose rows are the **distinct combinations** of those attribute values.
- The **fact table carries FKs to both** the base dimension and the mini-dimension. The fact table thus captures the profile in effect at the time of each event — history is preserved in the fact table, not by versioning the base dimension.

## Why
- Keeps the big base dimension stable while still tracking volatile attributes over time.
- Classic case: customer demographics (income, age, credit band) on a huge customer dimension.

## Related
[[Dynamic Value Banding]] · [[SCD Type 5 - Mini-Dimension + Type 1 Outrigger]] · [[SCD Type 2 - Add New Row]] · [[Junk Dimensions]]
