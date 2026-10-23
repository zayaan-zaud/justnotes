---
tags: [sql, postgresql, indexing]
source: [PostgreSQL 18 docs — Index Types]
---
# SP-GiST Index

"Space-Partitioned GiST" — like [[GiST Index|GiST]], a pluggable framework, but built for **non-balanced, partitioned** structures (quadtrees, k-d trees, radix trees/tries) rather than GiST's balanced-tree approach.

## When to use
- Data that naturally partitions into a hierarchy of disjoint, unbalanced regions — geographic quadtrees, IP address ranges (radix tree/trie lookups), phone-number-prefix-style hierarchical keys.
- Like GiST, supports nearest-neighbor distance ordering.
- A narrower, more specialized tool than GiST — reach for it specifically when the data's natural structure is a partitioned/non-balanced tree, not as a general default over GiST.

## Related
[[GiST Index]] · [[B-Tree Index (Default)]]
