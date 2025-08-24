# Finding All Customer Orders

# Schema

````
CREATE TABLE orders (
   id INT,
   customer_id INT,
   amount NUMERIC
);

CREATE TABLE customers (
   id INT,
   name TEXT
);
````

# Sample Data Set
- orders = 10 million rows
- customers = 100,000 rows

# Query

````
SELECT c.name, SUM(o.amount) AS total_amount
FROM orders o
JOIN customers c ON o.customer_id = c.id
GROUP BY c.name
ORDER BY total_amount DESC;
````

# Execution Plan (Simplified)
````
GroupAggregate
-> Sort
   -> Hash Join (spilled to disk)
      -> Seq Scan on orders (10M rows)
      -> Hash on customers (100K rows)
````

# Problems
- Hash Join needs a large in-memory hash table.
- Since orders is too big, it doesn't fit in [working memory](../Query-Planner/Working-Memory.md), **spills to disk** & query becomes very slow.

# ✅ Optimization

## Add an Index to Orders
- Now the database can do an Index Nested Loop Join instead of building a massive hash table.

````
CREATE INDEX idx_orders_customer ON orders(customer_id);
````

## Pre-Aggregate Orders
- This reduces [working-set](../Query-Planner/Working-Sets.md) early (10M → 100K), so working memory is much lower.

````sql
SELECT c.name, o.total_amount
FROM (
   SELECT customer_id, SUM(amount) AS total_amount
   FROM orders
   GROUP BY customer_id
) o
JOIN customers c ON o.customer_id = c.id
ORDER BY o.total_amount DESC;
````

## Optimized Plan
````
Sort
-> Nested Loop
   -> HashAggregate on orders (100K rows)
   -> Seq Scan on customers (100K rows)
````

## Results
- ✅ Runs in seconds instead of minutes.
- ✅ Memory usage stays below limit.