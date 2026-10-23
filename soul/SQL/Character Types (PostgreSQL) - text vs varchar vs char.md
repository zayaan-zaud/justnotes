---
tags: [sql, postgresql, data-types]
source: [PostgreSQL 18 docs — Character Types]
---
# Character Types (PostgreSQL) — text vs varchar vs char

PostgreSQL's own documentation is explicit: there is **no performance difference** between `char(n)`, `varchar(n)`, and `text` beyond `char(n)`'s extra storage for padding and a few CPU cycles checking length limits. This is unlike most other databases, where `varchar`/`char` distinctions carry real performance implications — don't import that assumption into Postgres.

## The three types
- **`text`** — unlimited length, no padding. PostgreSQL's native string type; most built-in functions return `text`.
- **`varchar(n)` / `character varying(n)`** — unlimited-length storage, but rejects input over `n` characters with an error (unless explicitly cast, which truncates silently).
- **`char(n)` / `character(n)`** — physically **blank-padded** to exactly `n` characters. Trailing spaces are semantically insignificant in comparisons but *are* stored, costing extra space. Usually the **slowest** of the three because of that padding overhead.

## When to use
- **Default to `text` or unconstrained `varchar`.** PostgreSQL's own recommendation. There's no storage or speed penalty for not declaring a length.
- Only add a length constraint (`varchar(n)`) when it's a genuine business rule you want the database to enforce (e.g., a 2-character country code) — not out of a habit carried over from a database where length mattered for performance.
- Avoid `char(n)` entirely in new schema design — it exists mainly for SQL-standard/legacy compatibility, and its padding behavior is a common source of surprising `LIKE`/regex mismatches since `varchar`/`text` treat trailing whitespace as significant while `char(n)` doesn't.

## Related
[[ALTER TABLE]]
