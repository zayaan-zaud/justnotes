---
tags: [sql, postgresql, dml, query-features]
source: [PostgreSQL 18 docs — INSERT]
---
# INSERT ... ON CONFLICT (Upsert)

`INSERT ... ON CONFLICT (...) DO UPDATE / DO NOTHING` inserts a row, or atomically updates the existing conflicting row instead — one statement, one round-trip, no race condition between "check if it exists" and "insert or update it."

## Syntax
```sql
INSERT INTO distributors (did, dname)
    VALUES (5, 'Gizmo Transglobal')
    ON CONFLICT (did) DO UPDATE
    SET dname = EXCLUDED.dname
    WHERE distributors.zipcode <> '21201';
```
- **Conflict target**: `ON CONFLICT (columns)` must name a **unique index or constraint** — Postgres infers which one matches. Required for `DO UPDATE`, optional for `DO NOTHING`. Can also target a partial unique index with `ON CONFLICT (col) WHERE predicate`, or name a constraint directly with `ON CONFLICT ON CONSTRAINT name`.
- **`EXCLUDED`** — a pseudo-table holding the row that *would* have been inserted, so `DO UPDATE SET x = EXCLUDED.x` means "overwrite with the new value," while referencing the target table's own alias (`distributors.zipcode` above) reaches the *existing* row.
- **`DO UPDATE ... WHERE`** — further restricts which conflicting rows actually get updated (a conflicting row that fails the `WHERE` is left untouched, not upserted).

## Vs. this project's delete+insert incremental pattern
A dbt `incremental_strategy='delete+insert'` model does the same logical job — replace rows matching a key — but as two separate statements (`DELETE` then `INSERT`), not one atomic operation. `ON CONFLICT` trades that two-step shape for a single atomic upsert, at the cost of needing a real unique index/constraint as the conflict target up front (delete+insert only needs the `unique_key` config, no DB-level constraint required). Worth knowing as the native alternative even where delete+insert stays the right tool — e.g. delete+insert naturally handles "this batch replaces everything for this key," which `ON CONFLICT` doesn't do as cleanly when a batch needs to *remove* rows that no longer exist in the source, not just add/update ones that do.

## When to use
- True upserts where each incoming row maps to at most one target row by a real unique key, and you don't need to delete rows absent from the new batch — the sweet spot `ON CONFLICT` is built for.
- Not a fit for "replace this key's rows with a new set that might have fewer rows than before" (e.g. a claim's revisions being fully re-supplied) — that's what delete+insert is actually for, since `ON CONFLICT` has no notion of "delete what's no longer present."

## Related
[[CREATE INDEX (PostgreSQL)]]
