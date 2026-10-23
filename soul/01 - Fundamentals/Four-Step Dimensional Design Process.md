---
tags: [design-process, fundamentals, kimball]
source: [Kimball ch.2-3]
---
# Four-Step Dimensional Design Process

Kimball's canonical recipe for designing any fact table. Applied in order, driven by business requirements *and* the realities of the source data.

## The four steps
1. **Select the business process.** Identify the operational [[Business Process (Measurement Event)|measurement event]] to model (e.g., taking an order, shipping, invoicing). Processes — not departments or reports — are the unit of design.
2. **Declare the [[Grain]].** State exactly what one fact row means. Commit to the **lowest atomic grain** available. Everything downstream depends on this.
3. **Identify the [[Dimension Tables|dimensions]].** Ask "how do business people describe the data resulting from this process?" Attach every descriptive context true at the grain (date, product, customer, store, etc.).
4. **Identify the [[Fact Tables|facts]].** Determine the numeric measurements the process produces. Each fact must be true to the declared grain; watch [[Additive, Semi-Additive, and Non-Additive Facts|additivity]].

## Notes
- Steps 3 and 4 are somewhat interchangeable/iterative, but grain must be settled before either.
- The process is preceded by gathering **business requirements** and studying **data realities**, typically in [[Collaborative Dimensional Modeling Workshops]].
- Adamson frames the same idea as: identify process → grain → facts → dimensions.

## Related
[[Grain]] · [[Business Process (Measurement Event)]] · [[Dimension Tables]] · [[Fact Tables]] · [[Collaborative Dimensional Modeling Workshops]] · [[Enterprise Data Warehouse Bus Matrix]]
