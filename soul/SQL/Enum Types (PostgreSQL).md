---
tags: [sql, postgresql, data-types]
source: [PostgreSQL 18 docs — Enumerated Types]
---
# Enum Types (PostgreSQL)

`CREATE TYPE mood AS ENUM ('sad', 'ok', 'happy')` creates a genuinely ordered type — comparison operators, `MIN`/`MAX`, and `ORDER BY` all follow **declaration order**, not alphabetical order. This makes enums useful for anything with a natural progression (status pipelines, severity levels) where you'd otherwise need a lookup table plus a sort-order column just to get the ordering right.

## Mechanics
- Values sort by declaration order: `'sad' < 'ok' < 'happy'` in the example above, regardless of alphabetical order.
- Type-safe: two different enum types can't be compared directly, even with identical-looking labels — must cast both to `text` first.
- Stored as 4 bytes on disk; label mapping lives in the `pg_enum` system catalog.

## The real limitation: growing an enum is easy, shrinking it isn't
- **Adding a value** (`ALTER TYPE mood ADD VALUE 'ecstatic'`) is supported and cheap.
- **Removing or reordering a value is not directly supported** — there's no `DROP VALUE`. The only path is dropping and recreating the type (and everything depending on it), which is disruptive on a live schema.
- Labels are case-sensitive and whitespace-significant, max 63 bytes.

## When to use
- Good fit for a genuinely closed, rarely-changing set of states where the ordering itself is meaningful (workflow stages, priority levels) — the ordering is the main thing an enum buys you over a plain `text` + `CHECK` constraint.
- Poor fit for anything that might need a value removed later, or where the "closed set" is actually going to keep growing in ways you can't fully predict — a reference/lookup table is more flexible and just as enforceable via a foreign key, without the one-way-door problem on removing values.
- If you only need to *enforce* a fixed set of allowed values with no ordering requirement, a `CHECK (col IN (...))` constraint or a foreign key to a small lookup table is usually the lower-friction choice.

## Related
[[ALTER TABLE]]
