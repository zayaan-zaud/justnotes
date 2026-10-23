---
tags: [sql, postgresql, concurrency]
source: [PostgreSQL 18 docs — Transaction Isolation]
---
# Transaction Isolation Levels (PostgreSQL)

Three isolation levels, each built on [[MVCC (Multiversion Concurrency Control)|MVCC]] snapshots rather than blocking locks — the choice is about *which snapshot* a transaction sees and what it does when a conflict is detected, not about whether readers/writers block each other (they don't, at any level).

## The three, and what each prevents
| Level | Prevents | Still allows | Needs retry logic |
|---|---|---|---|
| **Read Committed** (default) | Dirty reads | Non-repeatable reads, phantom reads, serialization anomalies | No |
| **Repeatable Read** | + non-repeatable reads, phantom reads (Postgres exceeds the SQL standard here) | Serialization anomalies | Yes |
| **Serializable** | Everything, including serialization anomalies | Nothing | Yes |

- **Read Committed** takes a fresh snapshot **per statement**, not per transaction — two `SELECT`s in the same transaction can see different data if something else committed in between. This is the default for a reason: highest concurrency, matches most application code's actual expectations (each statement sees "now").
- **Repeatable Read** takes one snapshot **at transaction start** and holds it for every statement in that transaction — internally consistent, but can produce a `could not serialize access due to concurrent update` error if the transaction tries to modify a row another transaction changed since the snapshot was taken. The application must catch this and **retry the whole transaction**, not just the failed statement.
- **Serializable** adds predicate locking on top of Repeatable Read's snapshot to detect read/write dependencies between concurrent transactions that would produce a result impossible in any serial (one-at-a-time) execution — even ones that don't touch the same rows. Also requires retry-on-failure handling (`SQLSTATE 40001`).

## When to use
- **Read Committed** for the overwhelming majority of application code — it's what "just works" without extra retry logic.
- **Repeatable Read** when a transaction needs multiple queries to agree with each other on one consistent view (e.g. computing several aggregates that must reflect the exact same point in time) — but only if the application is prepared to retry on serialization failure.
- **Serializable** only when correctness genuinely requires it — e.g. two concurrent transactions that could each individually look correct but together violate an invariant neither one alone would catch (the classic case: two transactions each checking "is the total under budget" and both inserting, each individually correct against what they saw, together violating the budget). It has real overhead and requires retry handling — don't reach for it by default.

## Related
[[MVCC (Multiversion Concurrency Control)]] · [[Row-Level Locking]] · [[Deadlocks (PostgreSQL)]]
