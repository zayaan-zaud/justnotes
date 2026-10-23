---
tags: [fact-table, grain, transaction]
source: [Kimball ch.2-3, Adamson ch.11]
---
# Transaction Fact Tables

The most fundamental and common fact table type. **Grain = one row per measurement event** at a point in space and time (a line on a sales ticket, a single payment, one shipment line).

## Characteristics
- **Grain:** one row per transaction (or transaction line). The row exists **only if the event happened** — transaction fact tables are **sparse**.
- **Facts:** typically fully **[[Additive, Semi-Additive, and Non-Additive Facts|additive]]** (quantity, amount).
- **Dimensions:** rich — you can attach every piece of context valid at the instant of the transaction (date, time, product, customer, store, promotion, cashier...).
- Often carries a **[[Degenerate Dimensions|degenerate dimension]]** (e.g., transaction/ticket number).

## Strengths & limits
- Most **flexible and dimensional** grain; the foundation for almost everything else.
- Answers "what happened" superbly, but **poor for measuring "state"** — you can't easily ask "how many were on hand?" or "what's the current pipeline status?" from raw transactions. For those, use [[Periodic Snapshot Fact Tables|periodic]] or [[Accumulating Snapshot Fact Tables|accumulating snapshots]].

## Related
[[Fact Tables]] · [[Grain]] · [[Periodic Snapshot Fact Tables]] · [[Accumulating Snapshot Fact Tables]] · [[Degenerate Dimensions]] · [[BRIN Index]]
