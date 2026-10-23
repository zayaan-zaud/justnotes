---
tags: [process, design-process, kimball]
source: [Kimball ch.18, Adamson ch.18]
---
# Dimensional Modeling Process and Tasks

The practical workflow for producing a dimensional design, sitting on the data track of the [[Kimball DW-BI Lifecycle]]. It operationalizes the [[Four-Step Dimensional Design Process]] as a team activity.

## Key tasks (Kimball ch.18)
1. **Prepare** — gather requirements, profile source data, assemble the modeling team, choose the business process from the **[[Enterprise Data Warehouse Bus Matrix|bus matrix]]**.
2. **High-level model** — a "bubble chart" / diagram of the fact table and its dimensions for the chosen process.
3. **Detailed design** — table-by-table, attribute-by-attribute and fact-by-fact specification; decide grain, keys, and **[[Slowly Changing Dimensions|SCD]]** types per attribute.
4. **Review & validate** — with business users, source experts, and DBAs.
5. **Finalize** — documentation and handoff to physical design/ETL.

## Design workshops
The work happens in **[[Collaborative Dimensional Modeling Workshops]]**, iterating with business and IT together rather than in isolation.

## Adamson's documentation approach
Adamson (ch.18) gives a parallel, tool-agnostic method for **designing and documenting** a model: capture grain, conformance (the bus matrix), attribute definitions, SCD behavior, and source-to-target mappings so the design is buildable and maintainable.

## Related
[[Four-Step Dimensional Design Process]] · [[Collaborative Dimensional Modeling Workshops]] · [[Kimball DW-BI Lifecycle]] · [[Enterprise Data Warehouse Bus Matrix]] · [[Design Documentation (Adamson)]]
