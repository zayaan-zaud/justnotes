---
tags: [orchestration, scheduling, airflow]
source: [rin project docs, 2026-08]
---
# Catchup Replays Missed Schedules, Not Failed Runs

A scheduler's "catchup" / "backfill missed schedules" feature (Airflow's `catchup=True`, and equivalents elsewhere) recovers only intervals for which **no run was ever created** — typically because the scheduler process itself was down. It does **not** retry an interval that a run *was* created for and that run ended in failure. From the scheduler's point of view, a failed run is a handled interval, not a missing one, and it will never be revisited automatically.

## Consequence
If the actual cause of a missed load is a downstream dependency being unreachable (a VPN tunnel down, an API timeout) rather than the scheduler being offline, every scheduled attempt during the outage creates a real, failed run — and catchup does nothing for any of them once the dependency comes back. Combined with `retries: 0` (or no per-run retry configured), a missed day becomes **permanent** until a human — or a dedicated recovery task that explicitly re-checks recent days for gaps — intervenes.

## When to use
Whenever diagnosing "why didn't a missed day get picked up automatically" — first check whether the interval actually has a DagRun/job row at all (never created → catchup should have caught it, investigate why it didn't) versus has one that's marked failed (catchup will never touch it; that needs retries or a dedicated gap-scan/recovery task).

## Related
[[Idempotent-Skip as the Only Recovery Mechanism]] · [[Orchestrator Status Is Not Ground Truth]]
