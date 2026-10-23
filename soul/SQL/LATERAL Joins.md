---
tags: [sql, postgresql, joins, query-features]
source: [PostgreSQL 18 docs — Table Expressions]
---
# LATERAL Joins

A plain `FROM` item can't reference columns from another `FROM` item in the same query — each is evaluated independently. `LATERAL` lifts that restriction: a `LATERAL` subquery or set-returning function is evaluated **once per row** of whatever precedes it, with that row's columns in scope.

## Syntax
```sql
FROM table1, LATERAL (subquery referencing table1) alias
-- or
FROM table1 CROSS JOIN LATERAL function(table1.column) alias
-- or, for optional per-row results:
FROM table1 LEFT JOIN LATERAL function(table1.column) alias ON TRUE
```

Without `LATERAL`, `FROM foo, (SELECT * FROM bar WHERE bar.id = foo.bar_id) ss` is an error — `foo` isn't visible inside the subquery. Add `LATERAL` and it becomes valid: the subquery re-runs for every row of `foo`, with `foo.bar_id` bound each time.

## The general form of `UNNEST(regexp_split_to_array(...))`
`CROSS JOIN UNNEST(...)` is a special case of this same mechanism — a set-returning function call that needs the current row's column as input. `LATERAL` is the general-purpose version: it applies to any subquery or function, not just `unnest`. Recognizing them as the same underlying mechanism means reaching for a `LATERAL` subquery (not just a function call) whenever the per-row logic is more than a single set-returning function — e.g. "for each claim, the 3 most recent revisions" needs a `LATERAL (SELECT ... ORDER BY ... LIMIT 3) ON TRUE`, which a bare `unnest()` can't express.

## When to use
- Splitting a delimited text column into rows while keeping the parent row's other columns in scope (a batch voucher string like `'HQB/26/05/005417,HIB/26/05/07798'` needing per-voucher joins) — exactly the shape `regexp_split_to_array()` + `UNNEST()` already covers, but `LATERAL` generalizes it to any per-row derivation, not just array-splitting.
- "Top N per group" queries — `LEFT JOIN LATERAL (SELECT ... WHERE fk = outer.id ORDER BY ... LIMIT N) ON TRUE` — where [[DISTINCT ON (PostgreSQL)]] only gets you N=1.
- Calling a set-returning function with an argument that depends on the outer row — the function call itself requires `LATERAL` semantics even without the keyword in simple cases (Postgres implicitly treats a bare function-call `FROM` item as `LATERAL`), but subqueries need it spelled out explicitly.
- `LEFT JOIN LATERAL ... ON TRUE` preserves the outer row even when the lateral side returns nothing — the join-analogue of a `COALESCE` default, useful for "show me X even if it has no matching Y" without losing rows.

## Related
[[Array Types (PostgreSQL)]] · [[DISTINCT ON (PostgreSQL)]]
