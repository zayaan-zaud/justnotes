---
tags: [fundamentals, design-process]
source: [Kimball ch.2, Adamson ch.4]
---
# Business Process (Measurement Event)

A **business process** is a natural operational activity performed by an organization that **generates measurements** — an order, a shipment, an invoice, a payment, a customer call, an inventory snapshot. In dimensional modeling, processes are the **fundamental unit of design**: each process typically yields one fact table (at its atomic grain).

## Why model by process, not department
- Processes are **stable**; departments, reports, and org charts change constantly.
- Modeling by process produces data that many departments can share — the foundation of an [[Enterprise Data Warehouse Bus Architecture|enterprise bus]] rather than isolated [[Independent Data Marts|data marts]].
- Adamson: **"a fact table for each process"** — resist the temptation to cram multiple processes into one table.

## Characteristics
- Frequently expressed as **action verbs** (ordering, shipping, billing).
- Generates or captures **performance metrics** (the facts).
- Has a natural **[[Grain]]** (the event's atomic level).

## Value chain
Sequencing an organization's processes end to end reveals its **[[Value Chain]]** (e.g., purchasing → inventory → sales). Each link is a candidate fact table; shared dimensions across links are [[Conformed Dimensions]].

## Related
[[Four-Step Dimensional Design Process]] · [[Value Chain]] · [[Enterprise Data Warehouse Bus Matrix]] · [[Consolidated Fact Tables]]
