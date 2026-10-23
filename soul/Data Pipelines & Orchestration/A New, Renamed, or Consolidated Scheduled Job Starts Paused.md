---
tags: [orchestration, airflow, scheduling, deployment]
source: [rin project docs, 2026-08]
---
# A New, Renamed, or Consolidated Scheduled Job Starts Paused

Most schedulers default a brand-new scheduled job (a new DAG id, in Airflow) to **paused**, requiring an explicit unpause before it will ever fire. This is easy to remember for a genuinely new job and easy to forget for one that only *looks* unchanged to a human but is new from the scheduler's point of view — a rename, or consolidating several existing jobs into one differently-named job, both mint a new job identity and reset to that paused default, even though the jobs being replaced were unpaused and running fine.

## Why this is dangerous specifically
The result — a scheduling job sitting paused since the moment it was deployed — is silent. Nothing errors. No run is ever attempted, so there's no failure to alert on either; from a "did my deploy work" glance, an import-error-free, correctly-defined, perfectly paused job looks identical to one quietly not doing its job at all. It can sit that way for days, found only by someone manually asking "is this actually running."

## When to use
After *any* change to a scheduled job's identity — a rename, a split, or a consolidation of several jobs into one — explicitly check and confirm its paused/enabled state before considering the deploy complete. Don't rely on "it was unpaused before" carrying forward; check the state of the *new* identity specifically, every time.

## Related
[[A Triggered Job's Passed-In Value Is Inert Until the Receiver Reads It]] · [[Orchestrator Status Is Not Ground Truth]]
