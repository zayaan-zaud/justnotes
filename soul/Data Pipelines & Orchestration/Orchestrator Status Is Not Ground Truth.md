---
tags: [orchestration, airflow, distributed-systems]
source: [rin project docs, 2026-08]
---
# Orchestrator Status Is Not Ground Truth

A task marked FAILED (or SUCCESS) by an orchestrator is a claim about the orchestrator's own bookkeeping, not necessarily about whether the underlying work actually failed or succeeded — whenever a task's status is reported over a side channel (an API call, a heartbeat, an auth token) that's decoupled from the process actually doing the work.

## Example shape of the bug
Under a token/heartbeat-based execution model, a long-running task had its auth token to the orchestrator's API server expire mid-run. The orchestrator marked the task FAILED — but the actual subprocess it had launched kept running, orphaned, in the background, and finished its real work correctly and on time. The orchestrator's status was simply wrong.

## When to use
Whenever an orchestrator's task-status reporting is architecturally separate from the process it launched (common in systems using tokens, heartbeats, or polling rather than a direct process-exit-code check) — don't trust a FAILED/SUCCESS label alone for a long-running or resource-heavy task; check the actual side effect (output written, rows loaded, file produced) before re-running or alerting on it. Also worth checking whether the token/heartbeat expiry window is long enough for your longest expected task before it ever bites in production.

## Related
[[Idempotent-Skip as the Only Recovery Mechanism]] · [[Catchup Replays Missed Schedules, Not Failed Runs]] · [[An Absence Check Needs a Namespace Only You Write To]] · [[A New, Renamed, or Consolidated Scheduled Job Starts Paused]] · [[The Five Pillars of Data Observability]]
