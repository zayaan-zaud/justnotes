---
tags: [orchestration, airflow, architecture]
source: [rin project docs, 2026-08]
---
# A Triggered Job's Passed-In Value Is Inert Until the Receiver Reads It

When one job computes a value (which date to process, which batch id) and triggers a downstream job passing that value along (Airflow's `conf` on `TriggerDagRunOperator`, or the equivalent in any trigger/message-passing orchestration), the receiving job must **actively read** that passed-in value ahead of its own default/fallback computation — otherwise the whole point of centralizing the computation is silently defeated.

## The bug shape
A parent computes "today's processing day" once and triggers several children with `conf={"day": ...}`. If a child's own task still computes its day from its own default logic first (e.g. its own schedule's data-interval, or wall-clock "now") and only *would* fall back to `conf` if written to, but the code was never actually wired to check `conf` at all, then a manual/recovery trigger that intentionally passes a historical day gets silently ignored — the child recomputes "today" instead, and the trigger becomes a harmless no-op that looks like it worked (it ran, it succeeded, it just processed the wrong day).

## When to use
Auditing any fan-out where a coordinating job passes a computed value downstream — verify the receiver's code path actually branches on the passed-in value *before* falling through to its own default, not just that the parent sends it. This is easy to get half-built: the sending side is added and tested, the receiving side's check is assumed to already be there because it "would make sense" to have it.

## A stronger fix than patching the check
If every child needs the same parent-computed value and nothing else about the split serves a purpose, consider collapsing the parent/child structure into a single job that computes the value once as an ordinary internal argument — this removes the trigger/conf channel (and its "did the receiver actually read it" failure mode) entirely, rather than just adding a check to keep it working. See [[A New, Renamed, or Consolidated Scheduled Job Starts Paused]] for another failure mode this same parent/child shape introduces.

## Related
[[A New, Renamed, or Consolidated Scheduled Job Starts Paused]] · [[Orchestrator Status Is Not Ground Truth]]
