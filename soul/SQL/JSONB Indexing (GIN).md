---
tags: [sql, postgresql, data-types, json, performance, indexing]
source: [PostgreSQL 18 docs — JSON Types]
---
# JSONB Indexing (GIN)

`jsonb` columns are indexed with GIN, and the operator class you pick changes both what queries can use the index and how big it is — `jsonb` is not "index it and you're done" the way a plain column is.

## The two operator classes
- **`jsonb_ops` (default)** — `CREATE INDEX ON t USING GIN (col)`. Indexes every key AND every value independently. Supports `@>`, `?`, `?|`, `?&`, `@?`, `@@`. Larger index, more flexible (can search for a bare key with no known value).
- **`jsonb_path_ops`** — `CREATE INDEX ON t USING GIN (col jsonb_path_ops)`. Indexes a single hash per full value-path (`foo.bar.baz` as one unit, not three). Supports only `@>`, `@?`, `@@` — **not** `?`/`?|`/`?&`. Smaller, faster, more selective for containment queries. Can't answer "does this key exist with any value" — only "does this exact path+value exist."

## Expression indexes for targeted access
If queries consistently hit one field (`WHERE doc -> 'tags' ? 'qui'`), index just that expression instead of the whole document: `CREATE INDEX ON t USING GIN ((doc -> 'tags'))`. Smaller and more selective than indexing the whole column for a workload that only ever touches one path.

## When to use which
- Default to `jsonb_path_ops` if your queries are all `@>`-style containment checks — smaller index, faster lookups, and that's most reconciliation/filter use cases ("does this document contain this key-value pair").
- Use `jsonb_ops` only if you also need bare key-existence queries (`?`/`?|`/`?&`) without knowing the value.
- Reach for a plain btree/hash index on the whole `jsonb` column only for equality checks on complete documents — rare.

## Related
[[JSON and JSONB]] · [[GIN Index]] · [[CREATE INDEX (PostgreSQL)]]
