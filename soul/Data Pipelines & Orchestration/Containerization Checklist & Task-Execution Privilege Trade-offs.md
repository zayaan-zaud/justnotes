---
tags: [containerization, docker, orchestration, architecture]
source: [rin project docs, 2026-08]
---
# Containerization Checklist & Task-Execution Privilege Trade-offs

"Fully containerized" is a vague claim until pinned to testable properties, and one specific design choice inside containerized orchestration — how tasks reach Docker to launch their own containers — carries a real security trade-off that's easy to default into without weighing.

## Three tests for "actually containerized"
A setup can pass "it works" while failing all three:
1. **The image is self-sufficient.** It contains the application itself, not just a mount point into which a host checkout is bind-mounted at runtime.
2. **A cold clone reaches a working system.** `git clone` + `docker compose up` (or equivalent) produces a running system on a machine that has never seen the repo before — no manual host-side setup steps required first.
3. **Nothing load-bearing lives on only one workstation.** If the whole system's ability to run depends on files, credentials, or state that exist on exactly one person's laptop, it isn't actually containerized, whatever else is true.

## The Docker-socket privilege trade-off
When an orchestrator runs each task in its own container (rather than baking the task's code into the orchestrator's own image), it needs some way to reach a Docker daemon to launch those containers. The direct route — mounting `/var/run/docker.sock` into the orchestrator container — is a real privilege-escalation decision, not a formality: anything with access to that socket has root-equivalent control of the host. On a shared host, weigh this explicitly against alternatives: a socket-proxy exposing a restricted API subset, or a remote Docker daemon the orchestrator talks to over the network instead of a local socket.

## When to use
Auditing any "is this containerized" claim before relying on it for deployment/reliability planning; deciding how a task-per-container orchestrator (Airflow, Kestra, Jenkins, CI runners) should reach Docker.

## Related
[[On-Prem vs Cloud Placement for Internal-Only Source Pipelines]] · [[Docker Named Volumes (Ownership & Mount-Point Gotchas)]] · [[Baked, Mounted, or Ephemeral]] · [[Secrets Belong in a Vault, Not Just Env Vars]]
