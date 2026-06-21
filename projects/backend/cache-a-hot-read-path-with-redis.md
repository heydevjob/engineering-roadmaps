# Cache a Hot Read Path With Redis

**Role:** Backend Engineer · **Level:** Junior · **Type:** Optimize · **Skills:** caching, Redis, invalidation

> `GET /api/products/{id}` runs a 300ms JOIN, and the product page hits it thousands of times in a row for data that barely changes. You're putting Redis in front - and, the hard part, keeping it fresh on writes.

## The scenario

A product-detail endpoint runs an expensive JOIN (300ms+) and gets hammered with identical requests. You add a cache-aside layer: check Redis first, fall back to Postgres on a miss, populate the cache with a 30s TTL - and invalidate the key on update so the next read after a write sees fresh data.

## The code

The uncached read and the update that must invalidate (`main.py`):

```python
@app.get("/api/products/{product_id}")
def get_product(product_id: int):
    """
    TODO: cache-aside via Redis.
    - Check `product:{product_id}` in Redis first; on hit, return cached=true
    - On miss, query Postgres, write to Redis with 30s TTL, return cached=false
    """
    data = _query_product(product_id)   # ~300ms JOIN every call
    if not data:
        raise HTTPException(status_code=404, detail="not found")
    data["cached"] = False
    return data

@app.put("/api/products/{product_id}")
def update_product(product_id: int, body: dict):
    """Updates the product, then TODO: invalidate the cache for this key."""
```

The symptom:

```text
$ curl /api/products/1   # ~300ms
$ curl /api/products/1   # still ~300ms - nothing is cached
```

## How you'll approach it

1. **Read-through on GET.** Check `product:{id}` in Redis; on hit return it (`cached=true`). On miss, query Postgres, store with a 30s TTL, return `cached=false`.
2. **Invalidate on PUT.** After an update, delete (or rewrite) the key so the next GET can't serve stale data - this is the part people forget.
3. **Mind the herd.** Note what happens when a hot key expires under load and how a TTL or refresh strategy softens it.
4. **Verify.** Second GET is sub-millisecond; a PUT followed by a GET returns the new value, not the old.

## What you'll learn

- The cache-aside pattern end to end: read-through, miss, populate
- Choosing a TTL and the staleness trade-off
- Invalidation on write - the genuinely hard part of caching
- Avoiding a thundering herd when a hot key expires

## What it proves

You can add caching that makes a system faster *without* serving wrong data - the part most engineers get wrong.

> Resume-ready: *Added a Redis cache-aside layer with write-invalidation to a hot read path, cutting a 300ms query to sub-millisecond without serving stale data.*

## On the roadmap

Part of the [Backend Engineer Roadmap](../../roadmaps/backend.md) - **Stage 5: Caching & Performance** → Cache-aside.

---

**Build it for real.** This is ticket **BE-114** on HeyDevJob - the real hot read path above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Cache a Hot Read Path With Redis on HeyDevJob →](https://heydevjob.com/backend)
