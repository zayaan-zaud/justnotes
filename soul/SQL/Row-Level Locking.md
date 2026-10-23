---
tags: [sql, postgresql, concurrency]
source: [PostgreSQL 18 docs — Explicit Locking]
---
# Row-Level Locking

`SELECT ... FOR UPDATE/NO KEY UPDATE/SHARE/KEY SHARE` explicitly locks the selected rows against concurrent modification — the tool for "read this row, then act on it, without another transaction changing it out from under you" in between the read and the follow-up write.

## The four modes, weakest to strongest
- **`FOR KEY SHARE`** — weakest. Only blocks operations that would change the row's key columns (relevant to foreign-key checking). Allows almost everything else concurrently.
- **`FOR SHARE`** — blocks `UPDATE`/`DELETE`/`FOR UPDATE`/`FOR NO KEY UPDATE` on the row, but multiple transactions can hold `FOR SHARE` on the same row simultaneously (a genuine shared lock).
- **`FOR NO KEY UPDATE`** — blocks the same as `FOR UPDATE` except it still allows a concurrent `FOR KEY SHARE`. What plain `UPDATE` statements acquire automatically when they don't need `FOR UPDATE`'s full strength.
- **`FOR UPDATE`** — strongest. Blocks every other row lock mode on that row. Also raises an explicit serialization error under Repeatable Read/Serializable if the row changed since the transaction's snapshot was taken, rather than silently locking a stale version.

## Practical modifiers
- **`SKIP LOCKED`** — skip rows already locked by another transaction instead of waiting for them. The standard pattern for a work-queue table: several workers each `SELECT ... FOR UPDATE SKIP LOCKED LIMIT 1` to grab a different unclaimed row without contending on the same one.
- **`NOWAIT`** — fail immediately with an error instead of blocking if the row is already locked, rather than waiting indefinitely.

## When to use
- `FOR UPDATE` for "read then update this exact row, and nobody else should touch it in between" — the classic read-modify-write pattern that's otherwise a race condition under plain `SELECT` + `UPDATE`.
- `FOR SHARE`/`FOR KEY SHARE` when you need to prevent a row from being deleted/modified while you're using it, but don't need exclusive access yourself and want to allow other readers to do the same.
- `SKIP LOCKED` for queue-style processing where losing a contended row to another worker and moving on is the correct behavior, not an error condition.
- Locking more rows than necessary, or holding a lock longer than necessary, is the direct path to [[Deadlocks (PostgreSQL)|deadlocks]] and contention — lock only what the following statement actually needs to change.

## Related
[[Deadlocks (PostgreSQL)]] · [[Transaction Isolation Levels (PostgreSQL)]] · [[MVCC (Multiversion Concurrency Control)]] · [[INSERT ... ON CONFLICT (Upsert)]]
