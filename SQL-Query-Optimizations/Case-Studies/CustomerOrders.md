# Case Study: Finding Orders for a Customer

# Schema

```
CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    amount NUMERIC
);

-- Index on customer_id
CREATE INDEX idx_orders_customer ON orders(customer_id);

-- Index on order_date
CREATE INDEX idx_orders_date ON orders(order_date);
```

# Query

````
EXPLAIN ANALYZE
SELECT *
FROM orders
WHERE customer_id = 42
AND order_date BETWEEN '2025-01-01' AND '2025-01-31';
````

# Sample data
Let’s imagine orders has 10 million rows, and:
- Customer 42 has ~5,000 orders.
- January 2025 has ~200,000 orders.

# Step 1: Possible Plans


| Plan                             | Working Set                                                                                                                                | Description                                                                                 |
|----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| Sequential Scan (read all rows)  | Too expensive (5K*200K = 10M rows)                                                                                                         | Reads the whole table.<br/>- Cheap if the table is small or filters are not very selective. |
| Index Scan on customer_id        | still too many rows (e.g. 5K from customer)                                                                                                | Quickly finds all orders for customer 42, then filters by date.                             |
| Index Scan on order_date         | still too many rows (e.g. 200K from date)                                                                                                  | Quickly finds all January 2025 orders, then filters by customer.                            |
| Bitmap Index Scan (both indexes) | [Bitmap Index](https://github.com/Anshul619/Database-Internals/blob/main/Bitmap-Index-Scan.md) AND → narrows down to ~300 rows efficiently | Uses both indexes, combines results, then fetches matching rows.                            |

# Step 2: Example Execution Plan (EXPLAIN ANALYZE)
- PostgreSQL might pick 4th plan (most efficient)

````shell
Bitmap Heap Scan on orders  (cost=...)
    Recheck Cond: ((customer_id = 42) AND (order_date >= '2025-01-01'::date AND order_date <= '2025-01-31'::date))
    ->  BitmapAnd  (cost=...)
      ->  Bitmap Index Scan on idx_orders_customer  (cost=...)
          Index Cond: (customer_id = 42)
      ->  Bitmap Index Scan on idx_orders_date  (cost=...)
          Index Cond: ((order_date >= '2025-01-01'::date) AND (order_date <= '2025-01-31'::date))
````

# Step 5: Lessons Learned

|                                               | Description                                                                                                |
|-----------------------------------------------|------------------------------------------------------------------------------------------------------------|
| Indexes change the plan                       | Without indexes, Postgres would do a sequential scan.                                                      |
| Statistics matter                             | If stats are outdated, the planner might make the wrong choice.                                            |
| Planner adapts                                | If customer 42 had millions of orders, the planner might pick a different index or even a sequential scan. |
| [EXPLAIN ANALYZE](../Query-Planner/Readme.md) is key | Always check the actual plan + runtime.                                                                    |