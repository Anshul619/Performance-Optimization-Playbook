# SQL Query Optimization Playbook
This document is a practical checklist for SQL query optimization.
- It focuses purely on query-level techniques (not infrastructure, caching layers, or sharding).

# Goal
The goal is to help developers:
- Write efficient queries from the start.
- Debug and fix slow queries systematically.
- Understand which optimizations matter most (prioritized: ⭐ High → ⚡ Medium → 🟢 Low).

> Tip: Always start with[ query planning (EXPLAIN / EXPLAIN ANALYZE)](Query-Planner/Readme.md) when investigating performance issues.
This shows how the database executes your query (index scan, sequential scan, join order, etc.), which often reveals the root cause of slowness.

# Checklist

| Title                                     | Priority | Remarks                                                                                                                                                                                    |
|-------------------------------------------|----------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Use Proper Indexing                       | ⭐ High   | Create appropriate single, composite, or covering [indexes](https://github.com/Anshul619/Database-Internals/blob/main/DataStructures/Index.md). <br/>- Avoid over-indexing (slows writes). |
| Avoid `SELECT *`                          | ⭐ High   | Fetch only needed columns to reduce I/O and improve query planner decisions.                                                                                                               |
| Optimize WHERE Clauses                    | ⭐ High   | Use index-friendly conditions. <br/>- Avoid functions on indexed columns.                                                                                                                  |
| Use Joins Efficiently                     | ⭐ High   | Ensure join keys are indexed. Avoid unnecessary outer joins or Cartesian products.                                                                                                         |
| Regular Vacuum / Analyze (Postgres)       | ⭐ High   | Prevent table/index bloat, update planner statistics.                                                                                                                                      |
| Optimize Subqueries                       | ⚡ Medium | Prefer JOINs or [CTEs](https://github.com/Anshul619/SQL-Query-Language/blob/main/CTE.md) over correlated subqueries when possible.                                                         |
| Use `EXISTS` Instead of `IN` (Subqueries) | ⚡ Medium | `EXISTS` is faster on large datasets; `IN` can work for small, static lists.                                                                                                               |
| Avoid Redundant Data Retrieval            | ⚡ Medium | Eliminate unnecessary columns, joins, or repeated calculations.                                                                                                                            |
| Use `UNION ALL` Instead of `UNION`        | ⚡ Medium | Avoid duplicate-elimination overhead when uniqueness is not required.                                                                                                                      |
| Avoid Unnecessary `DISTINCT`              | ⚡ Medium | Sometimes `GROUP BY` or window functions are better.                                                                                                                                       |
| Avoid Unnecessary Ordering/Grouping       | ⚡ Medium | Only sort/aggregate when required. <br/>- Indexes on sort columns can help.                                                                                                                |
| Break Down Complex Queries                | 🟢 Low   | Use stepwise queries, temporary tables, or [materialized views](https://github.com/Anshul619/SQL-Query-Language/blob/main/MatrializedView.md) for readability and speed.                   |
| Prevent JSON/JSONB Columns for Filters    | 🟢 Low   | Hard to index, slow for filtering. Normalize when possible.                                                                                                                                |
| Batch Inserts/Updates                     | 🟢 Low   | Send multiple rows in one statement instead of row-by-row.                                                                                                                                 |
| Covering Indexes                          | 🟢 Low   | Include extra columns in an index to satisfy a query without hitting the table.                                                                                                            |
| Use Partial/Filtered Indexes              | 🟢 Low   | Index subsets of data (e.g. only "active" rows).                                                                                                                                           |

[Read more](https://www.datacamp.com/blog/sql-query-optimization)