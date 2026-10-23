---
tags: [sql, postgresql, concurrency, performance]
source: [PostgreSQL 18 docs — Routine Vacuuming]
---
# VACUUM and Table Bloat

The direct consequence of [[MVCC (Multiversion Concurrency Control)|MVCC]]: `UPDATE`/`DELETE` leave old row versions in place rather than removing them, so something has to reclaim that space or the table grows forever relative to its actual logical row count. That something is `VACUUM`.

## What bloat actually is
Dead row versions (superseded by an `UPDATE`, or removed by a `DELETE`) that no longer-active transaction can see, but that haven't been reclaimed yet. A table that's logically 10 rows but has been updated a million times without vacuuming can physically occupy space for all million versions — pure overhead on every sequential scan, and wasted space the OS never gets back until reclaimed.

## Plain VACUUM vs VACUUM FULL
- **Plain `VACUUM`** — marks dead space as reusable *within the table* for future inserts, but doesn't shrink the file or return space to the OS. Takes a light lock (`SHARE UPDATE EXCLUSIVE`) that doesn't block normal reads/writes. This is the routine, low-cost operation meant to run constantly (via [[Autovacuum]]).
- **`VACUUM FULL`** — actually rewrites the table to compact it and return space to the OS. Takes an `ACCESS EXCLUSIVE` lock that blocks *everything* on the table, and needs roughly double the table's disk space temporarily. PostgreSQL's own guidance: avoid it except as an emergency measure — the goal is to prevent bloat with routine plain `VACUUM`, not to periodically nuke it with `FULL`.

## When to use
- Don't reach for `VACUUM FULL` as a routine maintenance step — its exclusive lock on a live table is a much bigger operational cost than the bloat it fixes, in almost all cases. Let [[Autovacuum]] handle plain `VACUUM` continuously instead.
- A table growing in disk size faster than its row count would suggest, with query plans getting slower over time despite no schema change, is the signature of unmanaged bloat — check `last_autovacuum`/`last_vacuum` in `pg_stat_user_tables` before assuming the query itself is the problem.
- `VACUUM` is also how PostgreSQL prevents **transaction ID wraparound**, a separate and more severe problem than bloat — see the wraparound section of [[Autovacuum]].

## Related
[[MVCC (Multiversion Concurrency Control)]] · [[Autovacuum]] · [[Index-Only Scans and INCLUDE]] · [[ANALYZE and Table Statistics]]
