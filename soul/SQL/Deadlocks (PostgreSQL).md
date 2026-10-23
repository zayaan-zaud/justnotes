---
tags: [sql, postgresql, concurrency]
source: [PostgreSQL 18 docs — Explicit Locking]
---
# Deadlocks (PostgreSQL)

Two transactions each hold a lock the other needs, and each is waiting on the other to release it — neither ever will, so PostgreSQL detects the cycle and forcibly aborts one of them to break it. This can happen with ordinary `UPDATE`/`DELETE` row locks, not just explicit `SELECT ... FOR UPDATE` — any two transactions touching the same rows in a different order can produce one.

## The shape of it
```sql
-- Transaction 1                          -- Transaction 2
UPDATE accounts SET ... WHERE id = 1;     UPDATE accounts SET ... WHERE id = 2;
UPDATE accounts SET ... WHERE id = 2;     UPDATE accounts SET ... WHERE id = 1;
-- T1 waits on T2's lock on row 2          -- T2 waits on T1's lock on row 1 — cycle
```
Each transaction individually looks correct — it's the *combination* of opposite acquisition order that creates the cycle.

## What Postgres does about it
Detects the cycle automatically and aborts one of the two transactions (which one is not predictable and shouldn't be relied on) with a deadlock error, letting the other proceed. The aborted transaction gets an error back and must be retried by the application — this isn't a database bug to fix, it's an expected outcome application code needs to handle.

## When to use / prevention
- **Acquire locks in a consistent order across every transaction that touches the same set of rows** — the single most effective prevention. If every transaction updates accounts in ascending ID order, the cyclic-wait shape above can't occur.
- Acquire the strongest lock a transaction will need **up front**, rather than escalating from a weaker to a stronger lock partway through — escalation under contention is a common deadlock source.
- Keep transactions short — the longer a lock is held, the more opportunity for another transaction to build up a conflicting wait.
- Don't try to eliminate every possible deadlock through code discipline alone — write retry logic for deadlock errors as a matter of course, the same way [[Transaction Isolation Levels (PostgreSQL)|Repeatable Read/Serializable]] transactions need retry logic for serialization failures. Both are the database telling the application "try again," not a sign of corruption.

## Related
[[Row-Level Locking]] · [[Transaction Isolation Levels (PostgreSQL)]] · [[MVCC (Multiversion Concurrency Control)]]
