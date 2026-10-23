---
tags: [sql, postgresql, concurrency]
source: [PostgreSQL 18 docs — MVCC Introduction]
---
# MVCC (Multiversion Concurrency Control)

PostgreSQL's concurrency model: every statement sees a **consistent snapshot** of the database as of some point in time, rather than being blocked by or blocking concurrent writers. This is the single fact that explains most of Postgres's other concurrency behavior — why readers never wait on writers, why `UPDATE`/`DELETE` don't actually erase anything immediately, and why [[VACUUM and Table Bloat|VACUUM]] has to exist at all.

## The core mechanism
An `UPDATE` doesn't overwrite a row in place — it writes a **new version** of the row and marks the old version as superseded (but doesn't delete it). A `DELETE` similarly just marks a row's current version as no-longer-valid rather than removing it immediately. Each transaction's snapshot determines which row versions it can see: old enough to exist, not yet marked superseded as of that snapshot. This means:
- **Reading never blocks writing, and writing never blocks reading** — a reader's snapshot doesn't care that a writer is concurrently creating a newer version, and a writer creating a new version doesn't need to wait for readers still using the old one.
- Multiple concurrent transactions can be looking at genuinely different versions of the same row simultaneously, each internally consistent.
- Old row versions accumulate as garbage the moment no active transaction's snapshot can see them anymore — reclaiming that space is [[VACUUM and Table Bloat|VACUUM]]'s job, not something that happens automatically at commit time.

## Vs. lock-based concurrency
A traditional lock-based system serializes access — a writer takes an exclusive lock that blocks readers until it commits. MVCC trades that blocking for extra storage (multiple row versions coexisting) and a cleanup process (VACUUM) — generally much better concurrency at the cost of needing that cleanup to actually run.

## When this matters practically
- Explains why a long-idle transaction is dangerous even if it's not touching much data — its snapshot pins the visibility horizon, preventing VACUUM from reclaiming anything newer, anywhere in the database, until it finishes.
- Explains why `SELECT COUNT(*)` can be surprisingly slow — there's no single global row count Postgres can just read, because "how many rows exist" depends on which snapshot is asking.

## Related
[[VACUUM and Table Bloat]] · [[Transaction Isolation Levels (PostgreSQL)]] · [[Autovacuum]] · [[Row-Level Locking]]
