---
tags: [docker, containerization, tooling]
source: [rin project docs, 2026-08]
---
# Docker Named Volumes (Ownership & Mount-Point Gotchas)

Two non-obvious failure modes when combining named Docker volumes with an application image, both worth checking for before debugging further downstream.

## Root ownership on first mount
A freshly-created named volume gets **root ownership** the first time it's mounted, regardless of the image directory's own `--chown` in the Dockerfile. A non-root process baked into the image then fails its first write into that volume with a `PermissionError` — even though the same directory inside the image (before the volume was mounted over it) was owned correctly. Fix with a one-off init step/service that `chown`s the volume to the right user before anything else touches it.

## A directory a tool needs to `rmtree()` can't be its own mount point
If a build/dependency tool fully deletes-and-recreates a directory as part of its normal operation (e.g. a package manager refreshing its cache directory), that directory cannot be a named-volume mount point — a mount point can't be removed, so the tool fails with something like `Device or resource busy`. Leave that directory in the container's ephemeral layer instead (cheap to regenerate) and reserve named volumes for directories that are only ever written *into*, not replaced wholesale.

## When to use
Any Dockerized app combining named volumes with a non-root user, or with tools that regenerate their working directories from scratch (dependency managers, package caches, build artifact directories).

## Related
[[Idempotent-Skip as the Only Recovery Mechanism]] · [[Containerization Checklist & Task-Execution Privilege Trade-offs]] · [[Docker CLI (oh-my-zsh plugin)]]
