[Home](../../README.md) › [Backend Projects](README.md) › **Switch to Cursor-Based Pagination**

# Switch to Cursor-Based Pagination

`Backend` · `🟢 Junior` · `🔨 Build` · `Ticket BE-116`

**Skills** — pagination · SQL · API design

> Page 1 of the events feed is fast. Page 500 makes Postgres scan and throw away 10,000 rows. And if new events arrive mid-scroll, users see duplicates. Offset pagination has hit its limits - you're rebuilding it on a cursor.

> [!NOTE]
> **The scenario**
> The `/api/events` endpoint uses offset pagination. Deep pages get slow (`OFFSET 10000` scans and discards 10,000 rows), and inserting new events between requests shifts the result set so users see duplicates or skip rows. You replace it with keyset (cursor) pagination - stable under inserts, fast at any depth.

## 🧩 The code

The offset implementation (`main.py`):

```python
@app.get("/api/events")
def list_events(page: int = 1, limit: int = DEFAULT_LIMIT):
    """Current implementation: offset pagination. Replace with cursor."""
    offset = (page - 1) * limit
    cur.execute(
        """
        SELECT id, event_type, payload, created_at
        FROM events
        ORDER BY created_at DESC, id DESC
        LIMIT %s OFFSET %s
        """,
        (limit, offset),
    )
```

The symptom:

```text
GET /api/events?page=500   # much slower than page=1 (scans 500*limit rows)
# new events inserted during paging -> duplicates / skipped rows
```

## 🛠️ How you'll approach it

1. **See why offset degrades.** `OFFSET n` still reads and discards `n` rows, so latency grows with depth - and a new row at the top shifts every page boundary.
2. **Page on a stable key.** Order by `(created_at, id)` and use the last row's values as the cursor instead of a page number.
3. **Encode the cursor.** Return a `next_cursor`; the next request continues `WHERE (created_at, id) < cursor` - O(1) regardless of depth.
4. **Verify stability.** Insert rows mid-scroll and confirm no duplicates and no skips.

## 🎓 What you'll learn

- Why offset pagination degrades and destabilizes under writes
- Keyset/cursor pagination: ordering on a stable key and encoding the cursor
- Designing the `limit` / `cursor` / `next_cursor` contract
- The index behind the sort key that keeps it fast

## 🏆 What it proves

You can build list endpoints that stay fast and correct at any scale - and you understand the offset-vs-cursor trade-off well enough to choose.

> [!TIP]
> **Resume-ready** — *Replaced offset pagination with cursor-based pagination on a high-volume feed, keeping latency flat at any depth and stable under concurrent inserts.*

## 🗺️ On the roadmap

Part of the [Backend Engineer Roadmap](../../roadmaps/backend.md) - **Stage 1: HTTP & API Fundamentals** → Pagination.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **BE-116** on HeyDevJob - the real offset-paginated endpoint above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup.
> [Switch to Cursor-Based Pagination on HeyDevJob →](https://heydevjob.com/backend)

**Explore Backend** · [📍 Roadmap](../../roadmaps/backend.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/backend.md) · [✅ Checklist](../../checklists/backend.md)

[◀ Prev: Add Server-Side Form Validation](add-server-side-validation.md) · [Next: Cache a Hot Read Path With Redis ▶](cache-a-hot-read-path-with-redis.md)
