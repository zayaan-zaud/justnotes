---
tags: [containerization, docker, deployment, drift]
source: [claude-conversation 2026-08-27, rin project]
---
# Baked, Mounted, or Ephemeral

Every path inside a container image has exactly one of three lifecycles, and the answer decides what a rebuild does to it. Leaving the answer undocumented is how "works locally, stale in production" bugs get in — and each of the three fails **silently, in a different direction**, so you cannot recognise them as one family from the symptoms.

- **Baked** — `COPY`d into the image. A rebuild is the ONLY way to update it, and an edit on disk changes nothing until then.
- **Mounted** — a named volume or bind mount over the path. It SHADOWS whatever the image holds, so a rebuild never refreshes it, however many times you run one.
- **Ephemeral** — written into the container's writable layer at runtime. A rebuild DESTROYS it, and it must be cheap to regenerate or it becomes a startup dependency you did not plan.

## The three failure modes
| lifecycle | what goes wrong | how it looks |
| --- | --- | --- |
| Baked | you edit on disk and forget to rebuild | production runs old code; often the error is downstream and unrelated-looking (a type mismatch, a missing column) — but see below, it can also be no error at all |
| Mounted | you rebuild and expect the new file to arrive | the file is simply absent inside the container; rebuilding again does not help |
| Ephemeral | you rebuild and the cached thing is gone | a fetch/download at startup, often several tasks racing for it at once |

None of these announce themselves. The mounted case is the nastiest, because the correct instinct — rebuild the image — actively confirms the wrong theory.

## The baked case can be completely silent
The worst version of the baked failure produces **no error anywhere**. Old code that is still internally consistent does not crash — it just does the old thing, successfully, and every status light stays green. A scheduled job rebuilt a warehouse from a three-day-old image: the build and its entire test suite passed, and the drift was noticed only because a human recognised that figures had moved backwards.

An unrelated-looking downstream error is therefore the *lucky* outcome — it is the version that gets caught. Plan for the version that doesn't:

- **Stamp provenance into the image at build time** (source digest, revision, build timestamp) and have the job log it on every run. This is attribution, not detection, and it turns "when did this start?" from archaeology into one line.
- **Compare that stamp against the source before deploying.** Detection has to happen where the source lives: an image built from a tree it cannot see can never answer "am I current?" itself.
- **Key the comparison on a digest of what actually enters the image, not a commit id.** The build copies a working tree, not a commit, so a clean id overstates a dirty build — and a docs-only commit invalidating a good image produces false alarms, which is how a guard gets routed around with an override flag.
- Choose the stamp's key carefully: see [[An Absence Check Needs a Namespace Only You Write To]], because the obvious standard label is one a base image already sets.

This is the deployment-layer instance of [[Orchestrator Status Is Not Ground Truth]]: a green run is a claim about the run, never about which code ran.

## What to do
- **Write the table down in the README, per path, before the second one bites.** It costs a few lines and it is the only artifact that makes the three recognisable as one question.
- Prefer **baked** for anything that must move in lockstep with the code.
- Use **mounted** only for state that must outlive the container (a database's data dir, generated output, uploaded files), and remember it needs its own delivery mechanism (`docker cp`, an init step) when its contents change.
- Keep **ephemeral** for things that are genuinely cheap to regenerate; if a rebuild makes several workers race to re-download the same dependency, bake it instead.
- Anything a compose file declares `external: true` is a fourth case: the tool will never create it, so pair it with an idempotent `create` chained into `up`.

## When to use
Standing up any containerized service; auditing a "why is production running old code" report; deciding where a new config/reference file should live. Pairs with test 1 of the [[Containerization Checklist & Task-Execution Privilege Trade-offs|containerization checklist]] (the image is self-sufficient) — this note is the per-path version of that question.

## Related
[[Containerization Checklist & Task-Execution Privilege Trade-offs]] · [[Docker Named Volumes (Ownership & Mount-Point Gotchas)]] · [[Paired Config Flags That Silently Fall Back]] · [[Orchestrator Status Is Not Ground Truth]] · [[An Absence Check Needs a Namespace Only You Write To]] · [[Expand-Contract for Backward-Compatible Schema Migrations]]
