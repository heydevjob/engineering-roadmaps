# Backfill a Column Safely (Batched + Verified)

**Role:** Data Engineer · **Level:** Senior · **Type:** Build/Design · **Skills:** backfill, batching, Postgres, system design

> You need to populate `amount_cents` from `amount_usd` across millions of rows. Do it in one statement and you lock the table for minutes. Get the conversion off by 100 and you owe someone a refund. You're building the batched, verified, resumable backfill.

## The scenario

A money migration needs `amount_cents` (BIGINT) populated from `amount_usd * 100` across the `transactions` table. You write a backfill that's batched (500 rows at a time), committed per batch (no long lock), verified (every row satisfies `abs(cents/100 - usd) <= 0.005`), resumable (picks up via `WHERE amount_cents IS NULL`), and reports the final count.

## The code

The batched, verified pattern you build:

```python
CHUNK, TOL = 500, 0.005
while True:
    cur.execute("SELECT id, amount_usd FROM transactions "
                "WHERE amount_cents IS NULL ORDER BY id LIMIT %s", (CHUNK,))
    batch = cur.fetchall()
    if not batch:
        break
    for row_id, amount_usd in batch:
        cents = int(round(float(amount_usd) * 100))
        if abs(cents / 100.0 - float(amount_usd)) > TOL:   # verify before write
            conn.rollback(); raise RuntimeError(f"drift at id={row_id}")
        cur.execute("UPDATE transactions SET amount_cents=%s WHERE id=%s", (cents, row_id))
    conn.commit()        # commit each batch -> no long lock
```

(Build-from-scratch: you author the full backfill script.)

## How you'll approach it

1. **Batch it.** Process 500 rows per loop, never one giant `UPDATE` that locks the table.
2. **Commit per batch.** Each batch commits independently, so readers and writers aren't blocked for long.
3. **Verify before writing.** Check `abs(cents/100 - usd) <= 0.005` per row - a money off-by-100 means a wrong refund, so catch it before it commits.
4. **Make it resumable.** Select only rows where `amount_cents IS NULL`, so a kill/restart picks up where it left off. Print the total at the end.

## What you'll learn

- The batched-backfill pattern that avoids long table locks
- Per-batch commits for non-blocking progress
- Verifying each row before committing (critical for money)
- Resumability via a NULL-column predicate

## What it proves

You can run a high-stakes production backfill safely - batched, verified, and resumable - the judgment that separates a senior data engineer from a production incident.

> Resume-ready: *Built a batched, verified, resumable backfill to populate a money column across millions of rows with per-batch commits and zero long-lived locks.*

## On the roadmap

Part of the [Data Engineer Roadmap](../../roadmaps/data.md) - **Stage 8: Senior - Modeling & Streaming** → Safe backfills.

---

**Build it for real.** This is ticket **DE-304** on HeyDevJob - the real money backfill above, in a cloud workspace you build from your browser. Each fix lands on a portfolio you can show. [Backfill a Column Safely on HeyDevJob →](https://heydevjob.com/data)
