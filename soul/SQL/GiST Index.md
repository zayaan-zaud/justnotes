---
tags: [sql, postgresql, indexing]
source: [PostgreSQL 18 docs — Index Types]
---
# GiST Index

"Generalized Search Tree" — not one fixed algorithm but a pluggable framework other index strategies build on, most visibly for geometric data and nearest-neighbor search.

## What it supports
Bounding-box and containment/overlap operators (`<<`, `&&`, `@>`, `<@`) for geometric types, and crucially **nearest-neighbor ("KNN") search** via `ORDER BY geom <-> point '(x,y)' LIMIT n` — "find the 10 closest locations to this point," a query shape a B-tree fundamentally can't answer efficiently since proximity isn't a total order.

## When to use
- Geometric/spatial data and nearest-neighbor queries — the canonical use case.
- Full-text search (via the `tsvector`/GiST combination) is another common application, though [[GIN Index|GIN]] is generally the better choice for text search specifically (GIN is larger to build but faster to query for that workload; GiST is faster to build/update but slower to query).
- Not the default choice for ordinary scalar columns — reach for [[B-Tree Index (Default)|B-tree]] there.

## Related
[[SP-GiST Index]] · [[GIN Index]] · [[B-Tree Index (Default)]]
