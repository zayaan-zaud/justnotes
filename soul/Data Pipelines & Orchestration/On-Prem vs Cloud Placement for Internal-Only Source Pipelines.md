---
tags: [data-engineering, architecture, infrastructure]
source: [rin project docs, 2026-08]
---
# On-Prem vs Cloud Placement for Internal-Only Source Pipelines

For a pipeline whose entire job is pulling from a source system reachable only inside a private network, moving the *compute* to the cloud doesn't eliminate that network dependency — it relocates it into a permanent site-to-site VPN tunnel that becomes standing infrastructure someone has to keep operating, every run, indefinitely. That's strictly more moving parts around the one thing the system actually needs to do reliably, not fewer.

## The framework
- If nothing downstream needs to reach the pipeline from outside the private network, keeping the whole pipeline co-located with the source (on-prem, or a VM inside the same network) is the simpler and more reliable shape — no tunnel to build, secure, monitor, or have fail silently overnight.
- The recommendation reverses only when there's a *separate* reason to expose output to remote consumers (a BI tool, external analysts, a public dashboard). In that case the right shape is **hybrid**: extraction and raw ingestion stay on-prem next to the source, and only the already-modelled output layer replicates outward to wherever it needs to be consumed.
- Don't default to "the cloud is the more scalable/professional choice" without asking what's actually driving the requirement — for an internal-only source, the cloud version usually adds a dependency (the tunnel) without removing one.

## When to use
Any pipeline design decision where every source system lives inside a private network and the "should we containerize/move this to the cloud" question comes up. Applies well beyond insurance data warehousing — any B2B/enterprise integration pulling from an internal-only ERP, reporting server, or database.

## Related
[[Containerization Checklist & Task-Execution Privilege Trade-offs]]
