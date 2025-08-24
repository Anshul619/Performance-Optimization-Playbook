# Working Sets
- [Working Set](https://shekhargulati.com/2012/01/10/how-working-set-affects-mongodb-performance/) is the amount of data (including indexes) that will be in used by your query and if this data fits in RAM then the application performance will be great else it would degrade drastically.
- Concept of working set applies to all databases.

# Example
- Let's say big_table has **10 million rows** and [work_mem](Working-Memory.md) is set to **64MB**.
- The working set is those 10M rows that need sorting.
- If they don’t fit in 64MB, Postgres will spill temporary files to disk.
- The planner tries to predict this and might choose a slower plan if it thinks spilling is inevitable.

````
SELECT * 
FROM big_table
ORDER BY col1;
```