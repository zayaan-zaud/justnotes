---
tags: [dimension, advanced, reporting]
source: [Kimball ch.2 & 10]
---
# Dynamic Value Banding

**Value banding** groups a continuous numeric fact into ranges/buckets ("$0–100", "$100–500", ...) for reporting. When the bands must be **flexible/ad hoc**, model them as a small **band-definition table** with `low` and `high` bounds and a band label, then join the fact to it with a **between (range) join**.

- **Static bands** can instead be a simple attribute in a [[Junk Dimensions|junk]] or banding dimension.
- **Dynamic bands** let analysts redefine boundaries without reprocessing facts.

Related to the decision in [[Numeric Values - Attribute or Fact]].

## Related
[[Numeric Values - Attribute or Fact]] · [[Junk Dimensions]] · [[Fact Tables]]
