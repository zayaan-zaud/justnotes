---
tags: [sql, postgresql, data-types]
source: [PostgreSQL 18 docs — Arrays]
---
# Array Types (PostgreSQL)

Any PostgreSQL type can be turned into an array column (`integer[]`, `text[]`), and arrays are genuinely ordered, duplicate-preserving collections — not a stand-in for a proper child table when the values need their own attributes or need to be queried/joined individually.

## Mechanics
- **1-based indexing**: `arr[1]` is the first element, not `arr[0]`. Out-of-bounds subscripts return `NULL` silently (not an error); out-of-bounds slices return an empty array (not `NULL`) — these two failure modes are different and both easy to miss.
- Declared sizes (`integer[4]`) are **not enforced** — pure documentation. All arrays of the same element type are interchangeable regardless of declared dimensions.
- `unnest(arr)` expands an array into one row per element — the standard way to turn array data back into relational rows for joining or grouping.
- `array_agg(col)` is the reverse: collapse grouped rows into a single array per group.

## Searching
- `x = ANY(arr)` — true if `x` matches any element.
- `x = ALL(arr)` — true only if `x` matches every element.
- `arr1 && arr2` — overlap: true if the two arrays share any element.
- `arr @> ARRAY[x]` — containment, same operator family as [[JSON and JSONB]]'s containment check.

## When to use
- Good fit for a small, unordered-but-fixed set of tags/codes attached to a row where you'll never need to query "give me all rows containing tag X across the whole table" at scale, and never need per-element attributes.
- Bad fit the moment you need to join on individual elements, filter/aggregate per element efficiently at scale, or attach more data to each element — that's a normalized child table, not an array column. A batch voucher string like `'HQB/26/05/005417,HIB/26/05/07798'` that needs per-voucher joining is exactly the shape where `regexp_split_to_array()` + `unnest()` (or a [[LATERAL Joins|LATERAL]] join) turns a delimited text blob into proper rows rather than leaving it as opaque text.

## Related
[[JSON and JSONB]] · [[LATERAL Joins]]
