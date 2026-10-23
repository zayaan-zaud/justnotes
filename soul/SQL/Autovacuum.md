---
tags: [sql, postgresql, concurrency, performance]
source: [PostgreSQL 18 docs — Routine Vacuuming]
---
# Autovacuum

The background process that runs [[VACUUM and Table Bloat|VACUUM]] (and `ANALYZE`) automatically so dead-row cleanup doesn't depend on someone remembering to run it manually. Runs continuously but reactively — it decides *when* to touch a table based on thresholds, not on a fixed schedule, which is exactly where it can quietly fail to keep up.

## When it triggers
Per table, once enough rows have changed: roughly `autovacuum_vacuum_threshold` (default 50) `+ autovacuum_vacuum_scale_factor` (default 10%) `× estimated row count`, capped by `autovacuum_vacuum_max_threshold`. A separate insert-based threshold (Postgres 13+) can trigger a vacuum pass on insert-heavy, append-only tables even with no deletes, so newly-inserted rows get frozen (see wraparound, below) promptly rather than accumulating unfrozen indefinitely.

## The four ways it silently falls behind
- **A long-running transaction** holds back the "xmin horizon" — the oldest snapshot still active — which prevents `VACUUM` from reclaiming *any* dead row anywhere in the database that's newer than that transaction's start, not just rows the transaction itself touches. An idle-in-transaction session left open for hours is a database-wide vacuuming problem, not a local one.
- **Write volume exceeds capacity** — `autovacuum_max_workers` (default 3) is a hard concurrency cap; if more tables need vacuuming at once than there are workers, some wait. High-churn tables may need a lower `autovacuum_vacuum_scale_factor` specifically (vacuum more eagerly, sooner) rather than relying on the database-wide default.
- **Autovacuum disabled on a specific table** (`WITH (autovacuum_enabled = off)`) — an explicit opt-out that silently means manual `VACUUM` is now required forever on that table, easy to forget once set.
- **Conflicting locks** — commands taking the same `SHARE UPDATE EXCLUSIVE` lock autovacuum needs (manual `ANALYZE`, `REINDEX`, some `ALTER TABLE` forms) can repeatedly interrupt an in-progress autovacuum on a busy table.

## Transaction ID wraparound — the problem autovacuum ALSO exists to prevent
Transaction IDs are a 32-bit counter compared with wraparound arithmetic; without intervention, a row's `xmin` can eventually look like it's "in the future" once ~2 billion transactions pass, making it silently invisible. `VACUUM` prevents this by **freezing** old-enough rows (marking them as permanently in the past, immune to the comparison). `autovacuum_freeze_max_age` (default 200 million transactions) forces a vacuum pass specifically for freezing, independent of the dead-row thresholds above. Ignored long enough, Postgres first warns, then in the critical range **refuses new transaction IDs entirely** — a full outage, not a performance degradation.

## When to check on it
- After any bulk-load path that inserts via a route autovacuum's normal triggers might not immediately catch (e.g. a non-standard bulk-copy mechanism) — verify `last_autovacuum`/`last_analyze` in `pg_stat_user_tables` rather than assuming it ran.
- If query plans on a table degrade over time with no schema change, check autovacuum's actual activity on that table before assuming it's a statistics or index problem — the underlying cause is very often that autovacuum simply hasn't kept up.

## Related
[[VACUUM and Table Bloat]] · [[MVCC (Multiversion Concurrency Control)]] · [[ANALYZE and Table Statistics]] · [[Index-Only Scans and INCLUDE]]
