---
tags: [moc, index, python, data-engineering]
source: [claude-conversation, 2026-10-12]
---
# Python & Data Engineering — MOC

Home base for the Python & Data Engineering topic: how to read messy source data, how to check that a load or reconciliation proves something, and how to reason about evidence. Mostly distilled from the `rin` warehouse project.

## 1. Reading and Shaping Source Data
[[Classify the Response Body, Never the Status Code]] · [[Dispatch an Ambiguous Format Parse on Shape, Not on Which Attempt Succeeds]] · [[Reshaping Print-Layout Exports (Flatten-to-Record Pattern)]] · [[Prefer a Recorded Value Over a Decoded One]] · [[Empirical Grain Discovery (Candidate-Key Cardinality Probing)]]

## 2. Reconciliation and Checks
[[Two-Stage Reconciliation (Existence Before Amounts)]] · [[Vacuous Checks (Why a Passing Reconciliation Can Prove Nothing)]] · [[Unreachable Tests (When a Stronger Guarantee Hides the One Below It)]] · [[An Absence Check Needs a Namespace Only You Write To]]

## 3. Reasoning About Evidence
[[Confounded Measurement Cascades]] · [[Round Inferences Toward the Cheap Error]] · [[A Median Is Not a Weight Until You Check the Spread]] · [[Elapsed-Time Effort Proxies Must Control for Task Switching]]

## 4. Code Design
[[Delegate Shared Logic Between Layers Instead of Hand-Syncing Copies]]

## Related topics
[[00 - Data Pipelines & Orchestration MOC]] · [[00 - SQL MOC]] · [[00 - Dev Tooling MOC]]
