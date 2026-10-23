---
tags: [etl, process, kimball]
source: [Kimball ch.20]
---
# ETL System Design & Development Process

Kimball ch.20 turns the **[[ETL 34 Subsystems|34 subsystems]]** into a staged development plan.

## Outline
1. **Draw a high-level plan** — a simple schematic of sources → staging → targets before touching tools.
2. **Choose an ETL tool** (or hand-code) informed by the required subsystems.
3. **Develop default strategies** for recurring problems: extraction method per source, [[Slowly Changing Dimensions|SCD]] policy per attribute, error handling, and the surrogate-key approach.
4. **Build dimension ETL first** — dimensions (with surrogate keys and SCD logic) before facts, since facts depend on dimension keys.
5. **Build fact ETL** — surrogate-key pipeline/lookup, then load transaction/snapshot/accumulating tables; handle [[Late Arriving Facts|late-arriving data]].
6. **Automate & operationalize** — scheduling, monitoring, recovery (the "Managing" subsystems).

## Principle
Load **dimensions before facts**; the fact loader's surrogate-key lookup requires current dimension rows to exist.

## Related
[[ETL 34 Subsystems]] · [[Dimension Surrogate Keys]] · [[Late Arriving Dimensions]] · [[Kimball DW-BI Lifecycle]]
