---
tags: [sql, postgresql, performance, query-planning, joins]
source: [PostgreSQL 18 docs — Using EXPLAIN]
---
# Nested Loop, Hash, and Merge Joins

Postgres picks between three fundamentally different join algorithms based on the two inputs' sizes, whether a useful index exists, and whether the inputs are already sorted — and the choice matters enormously for join performance at scale, independent of whether the join's *logic* (the `ON` condition) is correct.

## The three
- **Nested Loop** — for each row of the outer input, scan the inner input for matches. Cheapest when the outer side is small and the inner side has a good index on the join key, since each outer row triggers a cheap indexed lookup rather than a full inner scan. Degrades badly if the outer side is large *and* the inner side has no usable index — that's an all-pairs scan.
- **Hash Join** — build an in-memory hash table from the smaller input, then stream the larger input through it, probing the hash table per row. Good default when both sides are moderately-to-large and there's no index to exploit — O(1) amortized lookup per probe row instead of a full inner scan each time.
- **Merge Join** — requires both inputs sorted (or already ordered via an index) on the join key; walks both simultaneously like a zipper. Wins when both sides are already sorted for another reason (e.g. both scanned via an index on the join column) — avoids both the hash-table memory cost and per-row lookup cost.

## Reading which one fired, and why
`EXPLAIN` names the node (`Hash Join`, `Merge Join`, `Nested Loop`) and, for `Hash Join`, shows a nested `Hash` node with bucket/batch/memory stats — worth checking `Batches: 1` vs more; multiple batches means the hash table didn't fit in `work_mem` and spilled to disk, which is much slower.

## When to use
- Don't hand-pick a join strategy by default — the planner's choice is usually right given accurate statistics. Force one off temporarily (`SET enable_hashjoin = off;`) only as a *diagnostic* to see what the planner would do otherwise, to isolate whether a slow query is a join-strategy problem or something else (e.g. stale stats feeding a bad row-count estimate into the choice).
- A `Nested Loop` over a large outer result with no index on the inner side is the classic accidental-cross-product-shaped slow query — if you see one where a `Hash Join` would obviously be cheaper, that's usually a sign the planner's row-count *estimate* for the outer side was wrong (too low), not that Nested Loop is inherently bad — see [[ANALYZE and Table Statistics]].

## Related
[[EXPLAIN and EXPLAIN ANALYZE]] · [[ANALYZE and Table Statistics]] · [[Merging Multiple Sources with FULL JOIN + COALESCE]]
