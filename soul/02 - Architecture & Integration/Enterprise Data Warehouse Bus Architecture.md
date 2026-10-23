---
tags: [architecture, integration, bus]
source: [Kimball ch.2 & 4]
---
# Enterprise Data Warehouse Bus Architecture

Kimball's strategy for building an **integrated** enterprise warehouse **incrementally**, one [[Business Process (Measurement Event)|business process]] at a time, without a monolithic upfront build and without creating [[Independent Data Marts|stovepipes]].

## The metaphor
Like a computer's shared **bus**, a common backbone of **[[Conformed Dimensions]]** lets independently built fact tables plug in and interoperate. Any new process that reuses the conformed dimensions instantly integrates with everything already on the bus.

## How it works
1. Draft the enterprise **[[Enterprise Data Warehouse Bus Matrix|bus matrix]]**: rows = business processes, columns = conformed dimensions.
2. Design and standardize the **conformed dimensions** once.
3. Build fact tables process-by-process, each reusing the shared conformed dimensions.
4. Because dimensions conform, users can **[[Drilling Across|drill across]]** fact tables and combine results.

## Payoff
- Incremental delivery with **enterprise coherence** — agile *and* integrated.
- Architecture and priorities are visible and communicable on one page (the matrix).

## Related
[[Enterprise Data Warehouse Bus Matrix]] · [[Conformed Dimensions]] · [[Drilling Across]] · [[Value Chain]] · [[Independent Data Marts]]
