---
tags: [ci-cd, dbt, data-engineering, testing]
source: [claude-conversation, 2026-08-31; industry pattern, dbt "Slim CI" / state:modified+defer]
---
# Slim CI - Diff Against a Known-Good State, Not a Full Rebuild

Running a full pipeline build-and-test on every change doesn't scale — the fix isn't a faster full run, it's testing only what actually changed, plus what depends on it, by diffing against a stored snapshot of the last known-good state rather than rebuilding everything from nothing.

## The mechanism (dbt's `state:modified+` / `--defer` as the concrete example)
1. A `manifest.json` (or equivalent build-state snapshot) from the last successful production build is kept as the reference.
2. A CI run compares the current change against that manifest and selects only the models that changed **plus everything downstream of them** (`state:modified+`) — not the whole DAG.
3. Unselected models are **deferred to** the reference build's already-materialized versions rather than rebuilt, so a PR that touches one model doesn't need to rebuild every unrelated upstream table to test it.

## Why this is the right shape for data pipelines specifically
A code-only CI/CD pipeline can often afford to run everything on every push because compute is comparatively cheap. A data pipeline's "everything" means re-running expensive transformations and re-fetching from source systems that may themselves be rate-limited or slow — full-rebuild CI here is a self-imposed reliability and cost problem, not just a slow feedback loop.

## When to use
Any transformation layer with an internal dependency graph (dbt, or equivalent) once the full build is slow enough that people start skipping CI or batching multiple changes together to avoid running it repeatedly — a strong sign the feedback loop itself is now degrading the practice it exists to protect.

## Gotchas
- This only stays safe if the reference state genuinely reflects production — a stale or drifted manifest means "unchanged" models get deferred to a state that's no longer actually live. A staging environment that has drifted from production makes deferring-to-staging actively misleading; deferring straight to production's own last-known-good state is more reliable when staging parity isn't maintained.
- Selecting `state:modified+` catches downstream consumers of a change but not upstream ones a change might implicitly depend on (e.g. a macro/seed edit that isn't itself a model) — check what the selection syntax actually covers before trusting it as exhaustive.
- Fast PR-level CI and a slower, more thorough main-branch build are different tools for different moments — a lite PR build against an isolated schema is for quick iteration, not a substitute for the fuller validation (larger data volumes, full refresh) that should still gate an actual production promotion.

## Related
[[Write-Audit-Publish (WAP) Pattern]] · [[Idempotent-Skip as the Only Recovery Mechanism]]
