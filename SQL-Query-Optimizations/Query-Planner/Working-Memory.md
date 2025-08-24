# What is work_mem in PostgreSQL?
- [work_mem](https://gelovolro.medium.com/what-is-work-mem-and-what-is-this-parameter-used-for-in-postgresql-1d6ea7fd7cdd) is an important configuration parameter in PostgreSQL, that defines the limit, i.e. the maximum amount of memory (if not enough, it’s going to spill and use the hard disk) allocated for executing operations such as:

# Operations
- Sorting, when performing operations like ORDER BY, DISTINCT, GROUP BY.
- JOINs usage (with hashing to build in-memory hash-tables, for example for the hash join).
- Set operations like UNION, INTERSECT and EXCEPT.

# Related terminologies in other dbs

|                 | Description                                                                                       |
|-----------------|---------------------------------------------------------------------------------------------------|
| MySQL / MariaDB | sort_buffer_size (per-thread sort memory), join_buffer_size, tmp_table_size / max_heap_table_size |
| SQL Server      | Memory grants are managed by the query optimizer; you don’t tune a direct work_mem equivalent.    |
| Oracle          | Uses PGA_AGGREGATE_TARGET or PGA_AGGREGATE_LIMIT to control memory for sorts/hashes.              |

# Important Points
- The execution plan will show how much memory has used for each sorting operation.