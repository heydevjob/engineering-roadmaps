# Implement Cursor Pagination for a 'Load More' Feed

**Role:** Fullstack Engineer · **Level:** Mid · **Type:** Build · **Skills:** pagination, cursor, Flask, React, API design

> The "Load More" button keeps showing the same first ten items. The endpoint takes a `cursor` parameter and ignores it - so every page is page one. You're making the feed actually advance.

## The scenario

A feed has a "Load More" button, but the `GET /api/items` endpoint ignores the `cursor` query param the client sends and always returns from the top. You implement cursor pagination: read `cursor` (an item id) and `limit`, return the next `limit` items with id greater than the cursor, and include `next_cursor` (or `null` when exhausted).

## The code

The endpoint ignoring the cursor (`app.py`):

```python
@app.get("/api/items")
def items():
    cursor = int(request.args.get("cursor", 0))
    limit = int(request.args.get("limit", 10))
    # BUG: ignores `cursor` entirely - always returns the first page
    page = ITEMS[:limit]
    next_cursor = page[-1]["id"] if page else None
    return jsonify(items=page, next_cursor=next_cursor)
```

The symptom:

```text
"Load More" keeps showing items 1-10 instead of advancing to 11-20, 21-30
```

## How you'll approach it

1. **Spot the ignored param.** `cursor` is read but never used - the slice `ITEMS[:limit]` always starts at the top.
2. **Page on the cursor.** Return the next `limit` items whose `id > cursor`, so each request continues where the last left off.
3. **Return the next cursor.** Set `next_cursor` to the last returned id, or `null` when there are no more items.
4. **Verify.** "Load More" advances 1-10 → 11-20 → 21-30 with no repeats or skips, and stops cleanly at the end.

## What you'll learn

- Cursor (keyset) pagination: returning items after a stable id
- Designing the `cursor` / `limit` / `next_cursor` API contract
- Why offset pagination drifts when rows are inserted or deleted
- Wiring the cursor through to a "Load More" UI

## What it proves

You can build pagination that stays correct as data changes - the kind every real feed needs - across the API and the UI.

> Resume-ready: *Implemented cursor-based pagination for a Load More feed, advancing stably through the dataset without duplicates or skips.*

## On the roadmap

Part of the [Fullstack Engineer Roadmap](../../roadmaps/fullstack.md) - **Stage 6: Data & Persistence** → Pagination.

---

**Build it for real.** This is ticket **FS-210** on HeyDevJob - the real stuck-on-page-1 feed above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Implement Cursor Pagination on HeyDevJob →](https://heydevjob.com/fullstack)
