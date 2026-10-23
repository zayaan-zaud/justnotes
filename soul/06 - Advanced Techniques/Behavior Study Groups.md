---
tags: [dimension, advanced, behavior, analytics]
source: [Kimball ch.2 & 8]
---
# Behavior Study Groups

A technique for **complex cohort analysis**. When defining a group of customers by intricate behavior (e.g., "bought X, then churned within 90 days") is too expensive to express in a single query, **capture the resulting set as a list of [[Natural, Durable, and Supernatural Keys|durable keys]]** — a **study group** table.

## Use
- The study group (just durable keys) is then **joined back** to any fact table to analyze that cohort's behavior across processes and time — without re-running the expensive selection.
- Groups can be combined with set operations (union/intersect/except) to build new cohorts.

## Related
[[Aggregated Facts as Dimension Attributes]] · [[Natural, Durable, and Supernatural Keys]] · [[Set Operation Derived Tables (Adamson)]]
