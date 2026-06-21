# Implement Incremental Loading in an ETL

**Role:** Data Engineer · **Level:** Junior · **Type:** Build · **Skills:** ETL, incremental load, watermarks, data pipelines

> The sync truncates the target and reloads everything from scratch every run. It's slow, it gets slower as the source grows, and worst of all the target is *empty* during reload - a query blackout. You're switching it to load only what changed.

## The scenario

A product-sync pipeline does a full load each run: truncate the target, copy everything from the source. As the source grows it gets slow, and the truncate creates a window where the target is empty and queries return nothing. You convert it to incremental loading with a watermark.

## The code

The truncate-and-reload (`sync_pipeline.py`):

```python
# PROBLEM: truncate wipes the target - queries return nothing during reload
cur.execute("TRUNCATE products_target;")

# PROBLEM: copies ALL rows every time, even if nothing changed
cur.execute("""
    INSERT INTO products_target (product_id, name, price, stock, synced_at)
    SELECT product_id, name, price, stock, NOW()
    FROM products_source;
""")
```

The symptom:

```text
during each run the target is briefly EMPTY -> a query blackout window
+ the full copy gets slower as the source grows
```

## How you'll approach it

1. **Track a watermark.** Store the timestamp (or max id) of the last successfully processed row.
2. **Load only the delta.** Select `WHERE updated_at > watermark` so each run handles just new/changed rows - never truncating.
3. **Upsert into the target.** Merge the delta in (so updates apply) and advance the watermark.
4. **Verify.** The target stays populated and queryable throughout, and each run touches only changed rows.

## What you'll learn

- Watermark-based incremental loading vs full truncate-and-reload
- Why truncation causes a query blackout window
- Selecting and upserting only the changed delta
- Keeping a target table always-on for readers

## What it proves

You can build an incremental pipeline that scales with data growth and never blacks out the target - a core data-engineering pattern for always-on systems.

> Resume-ready: *Converted a full truncate-and-reload pipeline to watermark-based incremental loading, eliminating the query-blackout window and scaling with data growth.*

## On the roadmap

Part of the [Data Engineer Roadmap](../../roadmaps/data.md) - **Stage 3: Incremental Loading** → Watermark-based incremental loads.

---

**Build it for real.** This is ticket **DE-110** on HeyDevJob - the real truncate-and-reload pipeline above, in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Implement Incremental Loading in an ETL on HeyDevJob →](https://heydevjob.com/data)
