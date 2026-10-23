---
tags: [moc, index, data-pipelines, orchestration]
source: [claude-conversation, 2026-10-12]
---
# Data Pipelines & Orchestration — MOC

Home base for loading data safely and running it on a schedule: idempotent loads, keys and grain, orchestrator semantics, and how pipelines are packaged and deployed.

## 1. Idempotent Loads, Keys and Grain
[[Idempotent-Skip as the Only Recovery Mechanism]] · [[Duplicates Within One Load Are Real, Across Loads They Are Revisions]] · [[A Dedupe Key That Encodes an Assumption Deletes Data When the Assumption Is False]] · [[The Delete Key and the Grain Are Different Things]] · [[A Constraint Is Only as Strong as Its Key]]

## 2. Time, Watermarks and Periods
[[An Incremental Watermark Must Track When Data Arrived, Not the Period It Describes]] · [[Derive a Period From the Business Calendar, Not the Load Timestamp]]

## 3. Safe Change and Publishing
[[Write-Audit-Publish (WAP) Pattern]] · [[Expand-Contract for Backward-Compatible Schema Migrations]] · [[Slim CI - Diff Against a Known-Good State, Not a Full Rebuild]] · [[Inline Deployment Lookups at Compile Time So Generated SQL Stays Identical]]

## 4. Scheduler and Orchestrator Semantics
[[Catchup Replays Missed Schedules, Not Failed Runs]] · [[A New, Renamed, or Consolidated Scheduled Job Starts Paused]] · [[A Triggered Job's Passed-In Value Is Inert Until the Receiver Reads It]] · [[Orchestrator Status Is Not Ground Truth]]

## 5. Containers, Placement and Secrets
[[Baked, Mounted, or Ephemeral]] · [[Containerization Checklist & Task-Execution Privilege Trade-offs]] · [[Docker Named Volumes (Ownership & Mount-Point Gotchas)]] · [[An Unset Inter-Service URL Defaults to Localhost, Which Inside a Container Means Itself]] · [[On-Prem vs Cloud Placement for Internal-Only Source Pipelines]] · [[Secrets Belong in a Vault, Not Just Env Vars]]

## 6. Observability
[[The Five Pillars of Data Observability]]

## Related topics
[[00 - Python & Data Engineering MOC]] · [[00 - SQL MOC]] · [[00 - Dev Tooling MOC]]
