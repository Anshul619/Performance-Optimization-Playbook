# Query Planning

# How to do query planning?

````
EXPLAIN ANALYSE <SQL-Query>
````

# Deciding Parameters

|                | Description                                                       |
|----------------|-------------------------------------------------------------------|
| Statistics     | Statistics (from ANALYZE) about row counts and data distribution. |
| Cost Estimates | Cost estimates (CPU, I/O, memory)                                 |

# Case Studies

| Case Study                                                          | Remarks                                              |
|---------------------------------------------------------------------|------------------------------------------------------|
| [Finding Orders for a Customer](../Case-Studies/CustomerOrders.md)  | Simple Case study with sequential scan vs indexes    |
| [Finding all customer orders](../Case-Studies/AllCustomerOrders.md) | Optimization using indexes and pre-aggregation helps |
