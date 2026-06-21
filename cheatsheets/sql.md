# SQL Cheatsheet

Day-to-day SQL grouped by task, Postgres-flavored. Basics up top, query tuning and DDL toward the bottom. Examples assume an `orders` table with `id, customer, total, status, created_at`.

## SELECT, WHERE, ORDER, LIMIT

Select specific columns from a table:

```sql
SELECT id, customer, total FROM orders;
```

Filter rows with a condition:

```sql
SELECT * FROM orders WHERE status = 'completed';
```

Combine conditions and match a set of values:

```sql
SELECT * FROM orders
WHERE status = 'completed'
  AND total > 100
  AND customer IN ('acme', 'globex');
```

Pattern-match text (`ILIKE` is case-insensitive in Postgres):

```sql
SELECT * FROM orders WHERE customer ILIKE 'acme%';
```

Sort results, newest first, and cap the row count:

```sql
SELECT * FROM orders ORDER BY created_at DESC LIMIT 50;
```

Page through results by skipping rows (fine for small offsets):

```sql
SELECT * FROM orders ORDER BY created_at DESC LIMIT 50 OFFSET 100;
```

Filter on a time window:

```sql
SELECT * FROM orders WHERE created_at > NOW() - INTERVAL '7 days';
```

## Joins

Inner join keeps only rows that match in both tables:

```sql
SELECT o.id, c.name
FROM orders o
JOIN customers c ON c.id = o.customer_id;
```

Left join keeps every left row, NULLs where the right has no match:

```sql
SELECT o.id, c.name
FROM orders o
LEFT JOIN customers c ON c.id = o.customer_id;
```

Find left rows with no match (anti-join) by filtering for NULL:

```sql
SELECT o.id
FROM orders o
LEFT JOIN shipments s ON s.order_id = o.id
WHERE s.order_id IS NULL;
```

Self-join a table to itself (e.g. employees to their manager):

```sql
SELECT e.name, m.name AS manager
FROM employees e
JOIN employees m ON m.id = e.manager_id;
```

## Aggregation, GROUP BY, HAVING

Count, sum, and average across all rows:

```sql
SELECT COUNT(*), SUM(total), AVG(total) FROM orders;
```

Aggregate per group:

```sql
SELECT status, COUNT(*), SUM(total)
FROM orders
GROUP BY status;
```

Filter on an aggregate (HAVING runs after grouping; WHERE runs before):

```sql
SELECT customer, SUM(total) AS spent
FROM orders
GROUP BY customer
HAVING SUM(total) > 1000;
```

Count distinct values:

```sql
SELECT COUNT(DISTINCT customer) FROM orders;
```

Aggregate strings into one row per group:

```sql
SELECT customer, STRING_AGG(status, ', ') FROM orders GROUP BY customer;
```

## Subqueries and CTEs

Filter against a subquery's result set:

```sql
SELECT * FROM orders
WHERE customer_id IN (SELECT id FROM customers WHERE tier = 'pro');
```

Correlated subquery referencing the outer row:

```sql
SELECT o.* FROM orders o
WHERE o.total > (SELECT AVG(total) FROM orders WHERE customer_id = o.customer_id);
```

Use a CTE to name a subquery and read top-down:

```sql
WITH recent AS (
  SELECT * FROM orders WHERE created_at > NOW() - INTERVAL '30 days'
)
SELECT status, COUNT(*) FROM recent GROUP BY status;
```

Chain multiple CTEs in one statement:

```sql
WITH paid AS (
  SELECT * FROM orders WHERE status = 'completed'
),
per_customer AS (
  SELECT customer, SUM(total) AS spent FROM paid GROUP BY customer
)
SELECT * FROM per_customer WHERE spent > 500;
```

## Window functions

Rank rows within a partition without collapsing them:

```sql
SELECT customer, total,
       ROW_NUMBER() OVER (PARTITION BY customer ORDER BY total DESC) AS rn
FROM orders;
```

Get the latest order per customer (filter on the window in an outer query):

```sql
SELECT * FROM (
  SELECT *, ROW_NUMBER() OVER (PARTITION BY customer ORDER BY created_at DESC) AS rn
  FROM orders
) t
WHERE rn = 1;
```

Running total ordered by time:

```sql
SELECT created_at, total,
       SUM(total) OVER (ORDER BY created_at) AS running_total
FROM orders;
```

Compare a row to the previous one with LAG:

```sql
SELECT created_at, total,
       total - LAG(total) OVER (ORDER BY created_at) AS delta
FROM orders;
```

`RANK` leaves gaps after ties; `DENSE_RANK` does not:

```sql
SELECT customer, total,
       DENSE_RANK() OVER (ORDER BY total DESC) AS rnk
FROM orders;
```

## INSERT, UPDATE, DELETE

Insert a row and return its generated id:

```sql
INSERT INTO orders (customer, total, status)
VALUES ('acme', 250, 'pending')
RETURNING id;
```

Insert multiple rows at once:

```sql
INSERT INTO orders (customer, total, status) VALUES
  ('acme', 250, 'pending'),
  ('globex', 90, 'completed');
```

Update rows that match a condition (always include a WHERE):

```sql
UPDATE orders SET status = 'completed' WHERE id = 42;
```

Delete matching rows:

```sql
DELETE FROM orders WHERE status = 'cancelled';
```

Empty a whole table fast (no per-row scan, resets faster than DELETE):

```sql
TRUNCATE orders;
```

## Upserts (ON CONFLICT)

Insert, or update if a unique key already exists:

```sql
INSERT INTO inventory (sku, qty) VALUES ('A1', 5)
ON CONFLICT (sku) DO UPDATE SET qty = EXCLUDED.qty;
```

Insert, or do nothing on conflict (idempotent insert):

```sql
INSERT INTO seen_events (event_id) VALUES ('evt_123')
ON CONFLICT (event_id) DO NOTHING;
```

Increment on conflict instead of overwriting:

```sql
INSERT INTO counters (name, n) VALUES ('hits', 1)
ON CONFLICT (name) DO UPDATE SET n = counters.n + 1;
```

## Indexes

Create an index to speed up lookups on a column:

```sql
CREATE INDEX idx_orders_status ON orders (status);
```

Composite index when you filter and sort on multiple columns (column order matters):

```sql
CREATE INDEX idx_orders_status_created ON orders (status, created_at DESC);
```

Unique index to enforce no duplicates:

```sql
CREATE UNIQUE INDEX idx_orders_ext ON orders (external_id);
```

Partial index covering only the rows you query:

```sql
CREATE INDEX idx_orders_open ON orders (created_at) WHERE status = 'pending';
```

Build an index without locking writes on a busy table:

```sql
CREATE INDEX CONCURRENTLY idx_orders_customer ON orders (customer);
```

List indexes on a table (psql):

```sql
\d orders
```

## EXPLAIN, ANALYZE, and tuning

See the planner's chosen plan without running the query:

```sql
EXPLAIN SELECT * FROM orders WHERE status = 'completed';
```

Run the query and show real timings and row counts (the one you actually want):

```sql
EXPLAIN ANALYZE
SELECT * FROM orders WHERE status = 'completed'
ORDER BY created_at DESC LIMIT 50;
```

What to look for: a `Seq Scan` on a large table usually means a missing index; an `Index Scan` or `Index Only Scan` is the win. Watch for a big gap between estimated and actual rows (stale statistics). A `Sort` step that an index could satisfy is wasted work.

Refresh planner statistics after a big data change:

```sql
ANALYZE orders;
```

Verbose plan with buffer/cache detail for deeper tuning:

```sql
EXPLAIN (ANALYZE, BUFFERS) SELECT * FROM orders WHERE customer = 'acme';
```

Note: a function on an indexed column hides the index. Index the expression instead:

```sql
CREATE INDEX idx_orders_lower_customer ON orders (LOWER(customer));
```

## Transactions

Group statements so they all commit or all roll back:

```sql
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

Undo everything since BEGIN:

```sql
ROLLBACK;
```

Set a partial undo point inside a transaction:

```sql
SAVEPOINT before_risky;
-- ...
ROLLBACK TO before_risky;
```

Lock a row so concurrent transactions wait (prevents lost updates):

```sql
BEGIN;
SELECT total FROM orders WHERE id = 42 FOR UPDATE;
UPDATE orders SET total = total + 10 WHERE id = 42;
COMMIT;
```

## Schema and DDL

Create a table with constraints and a sensible default:

```sql
CREATE TABLE orders (
  id          BIGSERIAL PRIMARY KEY,
  customer    TEXT NOT NULL,
  total       NUMERIC(10,2) NOT NULL DEFAULT 0,
  status      TEXT NOT NULL DEFAULT 'pending',
  created_at  TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

Add a column (with a default, this is fast in modern Postgres):

```sql
ALTER TABLE orders ADD COLUMN shipped_at TIMESTAMPTZ;
```

Rename a column:

```sql
ALTER TABLE orders RENAME COLUMN customer TO customer_name;
```

Add a foreign key linking to another table:

```sql
ALTER TABLE orders
ADD CONSTRAINT fk_customer FOREIGN KEY (customer_id) REFERENCES customers (id);
```

Add a check constraint to enforce a rule:

```sql
ALTER TABLE orders ADD CONSTRAINT positive_total CHECK (total >= 0);
```

Drop a column or a whole table:

```sql
ALTER TABLE orders DROP COLUMN shipped_at;
DROP TABLE orders;
```

## Practice it live

Reading SQL is not the same as taking a query from 800ms to 50ms by reading the plan and adding the right index. On [HeyDevJob](https://heydevjob.com) you fix real broken databases in live cloud workspaces from your browser - diagnose a seq scan with EXPLAIN ANALYZE, kill an N+1, write the migration. Free on the junior tier, no card, no setup.
