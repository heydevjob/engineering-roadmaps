# Fix the N+1 Query

**Role:** Backend Engineer · **Level:** Mid · **Type:** Debug/Optimize · **Skills:** SQL, query optimization, ORMs

> `/api/orders` fetches 100 orders, then fires one more query per order to look up its user. That's 101 queries and a 5-second response. The fix collapses it to 2.

## The scenario

The orders endpoint loads 100 orders, then loops and runs a separate query for each order's user - the classic N+1 problem, 101 queries total, 5 seconds under real data. You find it, confirm the query count, and collapse it into a single JOIN (or one batched `WHERE id IN (...)`).

## The code

The loop firing a query per order (`main.py`):

```python
# Query 1: Get all orders
cur.execute("SELECT id, user_id, product, quantity, total_price, created_at FROM orders")
orders = cur.fetchall()

results = []
for order in orders:
    order_id, user_id, product, quantity, total_price, created_at = order

    # BUG: Separate query for EACH order's user (N+1 problem)
    cur.execute("SELECT id, name, email FROM users WHERE id = %s", (user_id,))
    user = cur.fetchone()

    results.append({ "id": order_id, "user": { ... } })
```

The symptom:

```text
$ curl /api/orders        # ~5 seconds for 100 orders
# query log: 1 (orders) + 100 (one per order) = 101 queries
```

## How you'll approach it

1. **Spot the pattern.** A query inside a loop over a result set is N+1 - one parent query plus one child query per row.
2. **Confirm it.** The query log shows 101 queries; the cost is dominated by round-trips, not the queries themselves.
3. **Collapse it.** Replace the per-row lookup with a single `JOIN` between orders and users, or fetch all users in one `WHERE id IN (...)` and stitch in memory.
4. **Verify by count.** Re-run and confirm it's 2 queries and ~200ms, not 101 and 5s.

## What you'll learn

- Recognizing N+1 from query logs and request timing
- The usual source: a query inside a loop (or a lazy ORM relationship)
- Fixing it with a JOIN or a batched fetch
- Verifying by query count, not by feel

## What it proves

You can find the most common hidden performance killer in backend code and fix it - turning an endpoint that doesn't scale into one that does.

> Resume-ready: *Diagnosed an N+1 query (101 queries, 5s) and collapsed it to a single JOIN, cutting response time to ~200ms.*

## On the roadmap

Part of the [Backend Engineer Roadmap](../../roadmaps/backend.md) - **Stage 2: Databases & SQL** → The N+1 problem.

---

**Build it for real.** This is ticket **BE-203** on HeyDevJob - the real N+1 endpoint above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Fix the N+1 Query on HeyDevJob →](https://heydevjob.com/projects/n-plus-1-query-fix-portfolio-project)
