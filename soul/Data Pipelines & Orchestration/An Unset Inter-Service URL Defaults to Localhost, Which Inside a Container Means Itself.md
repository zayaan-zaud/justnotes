---
tags: [orchestration, docker, networking, airflow]
source: [rin project docs, 2026-08]
---
# An Unset Inter-Service URL Defaults to Localhost, Which Inside a Container Means Itself

When an orchestrator splits its own execution across more than one process/container (a worker or task process that talks HTTP back to a separate API/coordinator process, rather than touching shared state directly), the config value pointing the worker at that API process is often optional with a `localhost` default. Leaving it unset doesn't fail loudly — the worker resolves `localhost` to **its own container**, not the sibling container actually running the API server, and every call fails with a connection-refused error that reads like the other service is down, not like a missing config value.

## When to use
Standing up any multi-process/multi-container orchestrator where task execution is decoupled from the scheduler/coordinator via an internal API (this is the shape Airflow 3's Task Execution API introduced, replacing tasks talking to the metadata DB directly) — always set the inter-service URL explicitly to the sibling container's service name, never rely on a default resolving correctly. A connection-refused error immediately after standing up a new orchestrator version is a config-omission smell before it's a networking-problem smell.

## Related
[[Baked, Mounted, or Ephemeral]] · [[Containerization Checklist & Task-Execution Privilege Trade-offs]] · [[Paired Config Flags That Silently Fall Back]]
