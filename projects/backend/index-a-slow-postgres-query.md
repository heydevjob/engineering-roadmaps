[Home](../../README.md) › [Backend Projects](README.md) › **Index a Slow Postgres Query**

# Index a Slow Postgres Query

`Backend` · `🟢 Junior` · `⚡ Optimize` · `Ticket BE-115`

**Skills** — SQL · indexing · query planning

> `/api/orders/recent` times out under load. The query is fine - Postgres is just sequentially scanning 50,000 rows because the index it needs doesn't exist. Same SQL, 800ms or 50ms, depending on one index.

> [!NOTE]
> **The scenario**
> An endpoint filters orders by `status = 'completed'` and `created_at` within 7 days, sorted by `created_at DESC`. Postgres does a sequential scan of 50,000 rows instead of an index scan, so it's slow under load. You read the plan, add the right index, and confirm the planner uses it.

## 🧩 The code

The query that's scanning the table (`main.py`):

```python
@app.get("/api/orders/recent")
def recent_orders():
    """Return completed orders from the last 7 days, newest first."""
    cur.execute(
        """
        SELECT id, customer, total, status, created_at
        FROM orders
        WHERE status = 'completed'
          AND created_at > NOW() - INTERVAL '7 days'
        ORDER BY created_at DESC
        LIMIT 50
        """
    )
```

The symptom:

```text
$ curl /api/orders/recent          # ~800ms
EXPLAIN ANALYZE ...
-> Seq Scan on orders  (rows=50000)   # should be an Index Scan
```

## 🛠️ How you'll approach it

1. **Read the plan.** `EXPLAIN ANALYZE` shows `Seq Scan on orders` - Postgres is reading every row because nothing helps it filter or sort.
2. **Match the index to the query.** The filter is on `status` + `created_at` and the sort is `created_at DESC` - a composite index on `(status, created_at)` lets Postgres seek and return rows already ordered.
3. **Create it and re-check.** Add the index, re-run `EXPLAIN ANALYZE`, confirm it switched to an Index Scan.
4. **Confirm the win.** Latency drops from ~800ms to well under 50ms.

## 🎓 What you'll learn

- Reading `EXPLAIN ANALYZE` and spotting the sequential scan
- Choosing the index: column order, composite, covering the sort
- Why an index might still be ignored (order, selectivity, a function on the column)
- Proving the win from the plan and the timing, not by guessing

## 🏆 What it proves

You can take a query from hundreds of milliseconds to single digits by reading the plan and indexing correctly - the most direct database performance skill there is.

> [!TIP]
> **Resume-ready** — *Diagnosed a sequential scan via EXPLAIN ANALYZE and added a composite index on (status, created_at), cutting query time from ~800ms to under 50ms.*

## 🗺️ On the roadmap

Part of the [Backend Engineer Roadmap](../../roadmaps/backend.md) - **Stage 2: Databases & SQL** → Indexing & slow queries.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **BE-115** on HeyDevJob - the real seq-scanning query above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup.
> [Index a Slow Postgres Query on HeyDevJob →](https://heydevjob.com/backend)

**Explore Backend** · [📍 Roadmap](../../roadmaps/backend.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/backend.md) · [✅ Checklist](../../checklists/backend.md)

[◀ Prev: Cache a Hot Read Path With Redis](cache-a-hot-read-path-with-redis.md) · [Next: Honor an Idempotency-Key Header on POST ▶](honor-an-idempotency-key.md)
