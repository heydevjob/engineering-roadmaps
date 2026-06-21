[Home](../../README.md) › [Data Projects](README.md) › **Optimize Batch Insert Throughput (COPY)**

# Optimize Batch Insert Throughput (COPY)

`Data` · `🟡 Mid` · `⚡ Optimize` · `Ticket DE-203`

**Skills** — PostgreSQL · batch processing · COPY · performance

> The import inserts one row at a time and commits after each. 10,000 rows takes hours. Each insert-and-commit is a separate round-trip to the database. You're switching to `COPY` and taking it from hours to seconds.

> [!NOTE]
> **The scenario**
> A data-import pipeline inserts records one at a time and commits after every row, so large CSVs take hours. You replace single-row inserts with a batched approach - ideally `COPY` from an in-memory buffer - to process 10,000 rows in under 5 seconds without losing data integrity.

## 🧩 The code

The row-by-row loop (`loader.py`):

```python
# BUG: single-row INSERT in a loop - extremely slow for 10000 rows
for row in reader:
    cur.execute(
        """INSERT INTO sensor_data (timestamp, sensor_id, temperature,
           humidity, pressure, location) VALUES (%s, %s, %s, %s, %s, %s)""",
        (row['timestamp'], row['sensor_id'], row['temperature'],
         row['humidity'], row['pressure'], row['location'])
    )
    conn.commit()   # BUG: committing after EVERY single row
    count += 1
```

The symptom:

```text
10,000 rows -> hours, because each INSERT + commit is a separate DB round-trip
```

## 🛠️ How you'll approach it

1. **See the cost.** The bottleneck is network round-trips: one per insert, plus a commit each time.
2. **Stop committing per row.** Commit once per batch (or once at the end), not after every insert.
3. **Use COPY.** Stream the rows into a `StringIO` buffer and `COPY` them in one bulk operation - the fastest load path Postgres has (or `execute_batch` if COPY doesn't fit).
4. **Verify.** 10,000 rows load in seconds, with the same row count and data as before.

## 🎓 What you'll learn

- Why single-row insert-and-commit is the worst-case load pattern
- Batching to move the bottleneck from round-trips to bulk throughput
- Using `COPY` with an in-memory buffer for maximum speed
- Preserving integrity while changing the load mechanism

## 🏆 What it proves

You can take a slow load from hours to seconds with the right bulk pattern - a direct, measurable data-engineering performance win.

> [!TIP]
> **Resume-ready** — *Replaced row-by-row inserts with batched COPY, cutting a 10,000-row load from hours to seconds while preserving data integrity.*

## 🗺️ On the roadmap

Part of the [Data Engineer Roadmap](../../roadmaps/data.md) - **Stage 5: Performance** → Batch loading.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **DE-203** on HeyDevJob - the real row-by-row loader above, in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup.
> [Optimize Batch Insert Throughput (COPY) on HeyDevJob →](https://heydevjob.com/data)

**Explore Data** · [📍 Roadmap](../../roadmaps/data.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/data.md) · [✅ Checklist](../../checklists/data.md)

[◀ Prev: Implement Incremental Loading in an ETL](implement-incremental-loading.md) · [Next: Schedule a Pipeline as an Airflow DAG ▶](schedule-a-pipeline-with-airflow.md)
