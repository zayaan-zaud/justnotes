---
tags: [data-engineering, orchestration, pipeline-design]
source: [rin project docs, 2026-08]
---
# Idempotent-Skip as the Only Recovery Mechanism

Instead of building a separate checkpoint/resume system for interrupted jobs, make "check if this unit of work is already done, skip if so" the normal-path behavior of every run — then interruption recovery is just "run it again," with no separate code path to build, test, or trust.

## How it works
A backfill (or any long batch job) checks an idempotent completion record (e.g. a run log keyed by unit of work) *before* doing the expensive part of each unit. A completed unit costs no real work on a re-run. Losing a network connection, closing the laptop, or Ctrl-C'ing the job — and then just re-running the same command — becomes the entire recovery procedure, because normal operation and interruption-recovery are literally the same code path.

## When to use
Any long-running or resumable batch/backfill job. This is a step beyond "have idempotent writes" (necessary but not sufficient) — the sharper design move is collapsing recovery into normal operation rather than maintaining a parallel resume/checkpoint mechanism that can itself have bugs and drift from the main path.

## Gotchas
- Requires the idempotency check to be cheap and reliable relative to the work it's guarding — if checking "is this done" is itself expensive or fragile, the recovery story degrades.
- Any operation that mutates state in a way the idempotency check can't see (e.g. purging before re-fetching) breaks the guarantee — purge-then-fetch turns a safe resume into a destructive retry if the fetch then fails partway.

## Related
[[Docker Named Volumes (Ownership & Mount-Point Gotchas)]] · [[Orchestrator Status Is Not Ground Truth]] · [[A Constraint Is Only as Strong as Its Key]] · [[Round Inferences Toward the Cheap Error]] · [[Write-Audit-Publish (WAP) Pattern]] · [[Slim CI - Diff Against a Known-Good State, Not a Full Rebuild]] · [[Duplicates Within One Load Are Real, Across Loads They Are Revisions]]
