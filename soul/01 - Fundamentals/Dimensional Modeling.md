---
tags: [dimensional-modeling, fundamentals, kimball, adamson]
source: [Kimball ch.1-2, Adamson ch.1]
---
# Dimensional Modeling

**Dimensional modeling** is a design discipline for structuring data so it is simultaneously **understandable to business users** and **fast to query**. It is the widely accepted approach for delivering data to analytic/BI systems, as opposed to the entity-relationship (3NF) modeling used for operational/transaction systems.

## Core idea
Every dimensional model separates the world into two kinds of information:
- **[[Fact Tables]]** — the numeric *measurements* of a business process (what happened, how much).
- **[[Dimension Tables]]** — the *descriptive context* around those measurements (who, what, where, when, why, how).

Facts and dimensions joined together form a **[[Star Schema]]** (or an OLAP cube).

## Why not just normalize?
Third-normal-form models minimize redundancy and are ideal for transaction integrity, but they scatter data across many tables. That is hostile to two goals dimensional modeling optimizes for:
1. **Simplicity / understandability** — business users can navigate a star intuitively.
2. **Query performance** — predictable, wide joins between one fact table and its dimensions.

## Design driven by business processes
Dimensional models are built around **[[Business Process (Measurement Event)|business processes]]** (measurement events like *orders*, *shipments*, *payments*), not departments or reports. This is what makes them resilient to changing business questions.

## Two goals (Kimball)
Kimball frames every design decision against two goals: **(1)** deliver data that's understandable to the business, and **(2)** deliver fast query performance. When the two conflict, understandability usually wins.

## Related
[[Star Schema]] · [[Grain]] · [[Four-Step Dimensional Design Process]] · [[Fact Tables]] · [[Dimension Tables]] · [[Star Schemas vs OLAP Cubes]]
