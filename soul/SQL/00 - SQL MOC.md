---
tags: [moc, index, sql, postgresql]
source: [PostgreSQL 18 docs + rin project conversations]
---
# SQL — Master MOC

Home base for the SQL topic. Mostly PostgreSQL 18-specific (the version actually run in the `rin` project), distilled from the official docs plus real debugging sessions. Where a note is general SQL rather than Postgres-specific, it says so.

## 1. Data Types
[[Numeric Types (PostgreSQL)]] · [[JSON and JSONB]] · [[JSONB Indexing (GIN)]] · [[Date-Time Types (PostgreSQL)]] · [[Array Types (PostgreSQL)]] · [[Character Types (PostgreSQL) - text vs varchar vs char]] · [[Enum Types (PostgreSQL)]] · [[A Bare Date Sorts Below Its Own Same-Day Timestamp When Stored as Text]]

## 2. DDL / Schema
[[ALTER TABLE]] · [[DROP CASCADE Silently Destroys Dependent Views]]

## 3. Query Features & Clauses
[[FILTER Clause on Aggregates]] · [[DISTINCT ON (PostgreSQL)]] · [[GROUPING SETS, ROLLUP, and CUBE]] · [[LATERAL Joins]] · [[INSERT ... ON CONFLICT (Upsert)]] · [[Merging Multiple Sources with FULL JOIN + COALESCE]] · [[STRICT Functions Short-Circuit on Any NULL Argument]]

## 4. CTEs & Window Functions
[[Common Table Expressions (CTEs)]] · [[Recursive CTEs]] · [[CTE Materialization (MATERIALIZED vs NOT MATERIALIZED)]] · [[Window Functions (Overview)]] · [[ROW_NUMBER, RANK, and DENSE_RANK]] · [[LAG and LEAD]] · [[FIRST_VALUE, LAST_VALUE, and NTH_VALUE]] · [[Window Function Frame Clause (ROWS, RANGE, GROUPS)]]

## 5. Indexing & Performance
[[CREATE INDEX (PostgreSQL)]] · [[B-Tree Index (Default)]] · [[Hash Index]] · [[GiST Index]] · [[SP-GiST Index]] · [[GIN Index]] · [[BRIN Index]] · [[Partial Index]] · [[Index-Only Scans and INCLUDE]] · [[Multicolumn Index Column Order]] · [[Anti-Join Performance (LEFT JOIN + IS NULL vs NOT EXISTS)]]

## 6. Query Planning & EXPLAIN
[[EXPLAIN and EXPLAIN ANALYZE]] · [[Sequential Scan vs Index Scan vs Bitmap Scan]] · [[Nested Loop, Hash, and Merge Joins]] · [[ANALYZE and Table Statistics]]

## 7. Concurrency & MVCC
[[MVCC (Multiversion Concurrency Control)]] · [[Transaction Isolation Levels (PostgreSQL)]] · [[VACUUM and Table Bloat]] · [[Autovacuum]] · [[Row-Level Locking]] · [[Deadlocks (PostgreSQL)]]

---
### The essential mental model
> Every table lives under **[[MVCC (Multiversion Concurrency Control)|MVCC]]**: readers and writers never block each other, at the cost of needing **[[VACUUM and Table Bloat|VACUUM]]**/**[[Autovacuum|autovacuum]]** to clean up. Every query is answered by picking a **scan strategy** ([[Sequential Scan vs Index Scan vs Bitmap Scan|seq/index/bitmap]]) per table and a **join strategy** ([[Nested Loop, Hash, and Merge Joins|nested loop/hash/merge]]) to combine them, chosen by the planner from **[[ANALYZE and Table Statistics|statistics]]** — read via **[[EXPLAIN and EXPLAIN ANALYZE|EXPLAIN]]**, never guessed. Complex queries are built from **[[Common Table Expressions (CTEs)|CTEs]]** and **[[Window Functions (Overview)|window functions]]**, and multi-source reconciliation is almost always a careful **[[Merging Multiple Sources with FULL JOIN + COALESCE|FULL JOIN + COALESCE]]**.
