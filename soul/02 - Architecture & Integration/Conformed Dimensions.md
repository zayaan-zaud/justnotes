---
tags: [dimension, integration, conformed, bus]
source: [Kimball ch.2 & 4, Adamson ch.5]
---
# Conformed Dimensions

**Conformed dimensions** are the cornerstone of enterprise integration. A dimension is *conformed* when it means the **same thing** — same key domain, same attributes, same values, same labels — everywhere it is used across multiple fact tables/processes.

## The rule (Adamson)
Two dimension tables conform if they are **identical**, OR if one is a **[[Shrunken (Conformed) Dimensions|perfect subset]]** of the other — same column definitions and same values for the columns they share (a subset of rows and/or columns). This subset case is what makes conformance work across different [[Grain|grains]].

## Why they matter
- They are the **shared backbone** of the [[Enterprise Data Warehouse Bus Architecture|bus architecture]].
- They enable **[[Drilling Across]]** — querying multiple fact tables and combining results on common dimension labels. Without conformance, results can't be lined up.
- They guarantee consistent labels, groupings, and filters enterprise-wide ("customer" means one thing).

## Types of conformance
- **Identical** conformed dimension shared verbatim by many facts.
- **[[Shrunken (Conformed) Dimensions|Shrunken/rolled-up]]** conformed dimension: a subset at a coarser grain (e.g., *Month* conforms to *Date*; *Brand* conforms to *Product*), used by aggregate or higher-grain facts.

## Governance
Conformance is an organizational commitment, not just a technical one — it requires agreement on definitions across departments, captured in the [[Enterprise Data Warehouse Bus Matrix|bus matrix]].

## Related
[[Shrunken (Conformed) Dimensions]] · [[Conformed Facts]] · [[Drilling Across]] · [[Enterprise Data Warehouse Bus Architecture]] · [[Enterprise Data Warehouse Bus Matrix]]
