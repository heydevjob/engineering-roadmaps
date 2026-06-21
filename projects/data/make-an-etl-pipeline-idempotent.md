[Home](../../README.md) › [Data Projects](README.md) › **Make an ETL Pipeline Idempotent**

# Make an ETL Pipeline Idempotent

`Data` · `🟢 Junior` · `🛡️ Harden` · `Ticket DE-101`

**Skills** — ETL · SQL · idempotency · data pipelines

> Run the daily ingest twice on the same 100-row file and the table has 200 rows. A plain `INSERT` with no dedup means every retry doubles your data - and bad totals compound downstream. You're making the load replayable.

> [!NOTE]
> **The scenario**
> A daily order-ingestion pipeline doubles row counts on every run because it uses a plain `INSERT` with no duplicate check. Ops saw 200 rows after two runs of the same 100-row CSV. You make it idempotent so re-running produces the same result as running once.

## 🧩 The code

The plain insert (`load_orders.py`):

```python
cursor.execute("""
    INSERT INTO orders (
        order_id, customer_id, product_name,
        quantity, total_amount, order_date
    ) VALUES (%s, %s, %s, %s, %s, %s)
""", (row['order_id'], row['customer_id'], row['product_name'],
      int(row['quantity']), float(row['total_amount']), row['order_date']))
conn.commit()
```

The symptom:

```text
run the 100-row CSV twice -> 200 rows, duplicate order_id values
```

## 🛠️ How you'll approach it

1. **See why it duplicates.** A plain `INSERT` appends unconditionally, so a second run re-inserts every row.
2. **Switch to UPSERT.** Use `INSERT ... ON CONFLICT (order_id) DO UPDATE` (or `DO NOTHING`) so an existing `order_id` updates in place instead of duplicating.
3. **Ensure the constraint exists.** UPSERT needs a unique key on `order_id` to detect the conflict.
4. **Verify.** Running the same CSV twice now leaves exactly 100 rows.

## 🎓 What you'll learn

- Why ETL pipelines must be replayable (retries and reruns are inevitable)
- The UPSERT pattern (`INSERT ... ON CONFLICT`) for idempotent loads
- The unique constraint that makes conflict detection work
- How non-idempotent loads compound bad data downstream

## 🏆 What it proves

You can make a pipeline safe to re-run - the reliability property every production ETL needs, and the first thing a data team checks.

> [!TIP]
> **Resume-ready** — *Made an ETL load idempotent with INSERT ... ON CONFLICT UPSERT, so reruns and retries produce the same result instead of duplicating rows.*

## 🗺️ On the roadmap

Part of the [Data Engineer Roadmap](../../roadmaps/data.md) - **Stage 2: ETL Reliability** → Idempotency.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **DE-101** on HeyDevJob - the real duplicating pipeline above, in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup.
> [Make an ETL Pipeline Idempotent on HeyDevJob →](https://heydevjob.com/projects/idempotent-etl-pipeline-portfolio-project)

**Explore Data** · [📍 Roadmap](../../roadmaps/data.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/data.md) · [✅ Checklist](../../checklists/data.md)

[◀ Prev: Rank Rows Per Group With Window Functions](rank-rows-with-window-functions.md) · [Next: Make a CSV Importer Resilient to Bad Data ▶](make-a-csv-importer-resilient.md)
