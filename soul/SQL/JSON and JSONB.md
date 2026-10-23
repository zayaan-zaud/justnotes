---
tags: [sql, postgresql, data-types, json]
source: [PostgreSQL 18 docs — JSON Types]
---
# JSON and JSONB

PostgreSQL has two JSON types that look interchangeable but store data completely differently: `json` keeps an exact text copy of the input (preserves whitespace, key order, duplicate keys), while `jsonb` decomposes it into a binary format (normalizes key order, drops whitespace, keeps only the last of any duplicate key). `jsonb` is the default choice for almost everything.

## Key differences
| | `json` | `jsonb` |
|---|---|---|
| Storage | exact text | decomposed binary |
| Input speed | fast (no parsing) | slower (parses on write) |
| Query speed | slow (reparses every read) | fast (pre-parsed) |
| Indexable | no | yes (GIN, btree, hash) |
| Key order / whitespace | preserved | not preserved |

Use `json` only when you genuinely need the exact original text back (audit trail of a raw payload) or depend on key order. Otherwise `jsonb` — it's faster to query and the only one that can be indexed.

## Core operators
- `->` / `->>` — get a field as `jsonb` / as `text`.
- `#>` / `#>>` — get a nested value by path array, as `jsonb` / as `text`.
- `@>` / `<@` — containment: does the left value contain the right (or vice versa)? Works on objects, arrays (order-insensitive, ignores duplicates), and nested structures.
- `?` / `?|` / `?&` — key/element existence at the **top level only** — `?` doesn't look inside nested objects, so `{"foo":{"bar":1}}::jsonb ? 'bar'` is `false`. Use `@>` for nested existence checks instead.
- `@?` / `@@` — SQL/JSON path match (`jsonpath` expressions like `$.tags[*] ? (@ == "qui")`).

## When to use
- Prefer `@>` (containment) over reaching into a specific path with `->` when checking "does this document have X somewhere" — it's both more flexible and the operator that GIN indexes actually accelerate.
- `?` existence checks are a trap on nested JSON — reach for `@>` the moment the key you're checking isn't guaranteed top-level.
- See [[JSONB Indexing (GIN)]] for how to make `@>`/`?`/`@?` queries actually use an index instead of scanning.

## Related
[[JSONB Indexing (GIN)]] · [[GIN Index]] · [[Array Types (PostgreSQL)]]
