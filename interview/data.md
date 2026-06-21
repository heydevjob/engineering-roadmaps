[Home](../README.md) › **Data Interview**

# Data Engineer Interview Questions

`Interview prep` · `Click a question to reveal the answer`

The questions below are the ones that actually decide data engineering interviews - the SQL traps, the ETL reliability gotchas, the modeling judgment calls. They follow the [Data Engineer Roadmap](../roadmaps/data.md) from junior SQL up to senior warehouse design. Each answer is what a hiring manager wants to hear: correct, tight, and grounded in real pipelines. Where a question maps to a HeyDevJob ticket, there's a "Practice it live" link - go fix the actual thing in a cloud workspace.

## 🧮 SQL Foundations

<details>
<summary><b>Why does a one-to-many join inflate a `SUM`?</b></summary>

<br>

A join multiplies rows before the aggregate runs. If you join `orders` to a `tags` table where each order has many tags, every order row is duplicated once per tag, so the `SUM` counts each amount multiple times. The tell is totals that are an exact multiple (2x, 3x) of the correct figure. You fix it by removing the unneeded join or pre-aggregating the many-side in a subquery so the grain stays one row per order.

🛠️ *Practice it live:* [Debug a Broken SQL Revenue Aggregation](https://heydevjob.com/data)

</details>

<details>
<summary><b>What is the "grain" of a table and why does it matter?</b></summary>

<br>

The grain is what one row represents - one order, one order-line, one customer-day. Every join and aggregate has to respect it. Most reporting bugs are grain bugs: you joined to a finer-grained table and silently changed what a row means before summing. The discipline is to count rows at each stage of a query and confirm the grain hasn't changed.

</details>

<details>
<summary><b>When do you use `WHERE` vs `HAVING`?</b></summary>

<br>

`WHERE` filters rows before grouping; `HAVING` filters groups after aggregation. You can't reference an aggregate like `SUM(amount) > 100` in `WHERE` because the aggregate doesn't exist yet - that belongs in `HAVING`. Put non-aggregate filters in `WHERE` so fewer rows reach the `GROUP BY` and the query stays fast.

</details>

<details>
<summary><b>Explain `LEFT JOIN` vs `INNER JOIN` and a common bug.</b></summary>

<br>

`INNER JOIN` keeps only matched rows; `LEFT JOIN` keeps all left rows and nulls the right side when there's no match. The classic bug is adding a `WHERE right_table.col = x` filter on a `LEFT JOIN`, which drops the unmatched nulls and silently turns it back into an inner join. If you need the filter but want to keep unmatched rows, move the condition into the `ON` clause.

</details>

<details>
<summary><b>What do window functions do that `GROUP BY` can't?</b></summary>

<br>

Window functions compute across a set of rows while keeping every row in the output, where `GROUP BY` collapses rows. `ROW_NUMBER() OVER (PARTITION BY customer ORDER BY created_at DESC)` ranks each customer's orders so you can grab the latest per group in one pass. They solve "top N per group," running totals, and rolling averages declaratively, without a self-join.

🛠️ *Practice it live:* [Rank Rows Per Group With Window Functions](https://heydevjob.com/data)

</details>

<details>
<summary><b>How do you find and delete duplicate rows in SQL?</b></summary>

<br>

Use `ROW_NUMBER() OVER (PARTITION BY <natural key> ORDER BY id)` to number copies within each key, then keep `rn = 1` and delete the rest. The natural key is whatever should be unique (email, order number), not the surrogate `id`. Investigating why the duplicates exist - a non-idempotent load, usually - matters more than the cleanup query.

</details>

<details>
<summary><b>What's the difference between `UNION` and `UNION ALL`?</b></summary>

<br>

`UNION` removes duplicate rows; `UNION ALL` keeps everything. `UNION` is more expensive because it has to sort or hash to dedupe. If you know the inputs are disjoint, use `UNION ALL` for a free speedup and to avoid accidentally collapsing legitimately repeated rows.

</details>

## 🔁 ETL Reliability

<details>
<summary><b>What does idempotency mean for an ETL load, and how do you achieve it?</b></summary>

<br>

An idempotent load produces the same result whether it runs once or five times - critical because retries are inevitable. You achieve it with an UPSERT (`INSERT ... ON CONFLICT (key) DO UPDATE`) keyed on a natural unique key, or a delete-then-insert for the affected partition. A non-idempotent append doubles rows on every retry, and that bad data compounds downstream.

🛠️ *Practice it live:* [Make an ETL Pipeline Idempotent](https://heydevjob.com/data)

</details>

<details>
<summary><b>How should a pipeline handle malformed input rows?</b></summary>

<br>

Skip and log the bad row, then keep processing - don't let one anomaly crash the whole load. Validate per-row, write rejects to a dead-letter table or quarantine file with the reason, and surface a count so the bad data is visible instead of silently dropped. A rigid importer that dies on the first bad row loses all the good data and diagnoses nothing.

🛠️ *Practice it live:* [Make a CSV Importer Resilient to Bad Data](https://heydevjob.com/data)

</details>

<details>
<summary><b>Where do you put validation - at ingestion or after transformation?</b></summary>

<br>

Both, at different jobs. Validate structural things (types, required columns, parseable dates) at ingestion so garbage never enters the warehouse. Validate business rules (revenue is non-negative, every order has a valid customer) after transformation, since those depend on joined and modeled data. Failing fast at the right layer keeps the blast radius small.

</details>

<details>
<summary><b>A nightly job failed at 2am. How do you make the pipeline survive that?</b></summary>

<br>

Make every step retryable and idempotent so an automatic retry can't corrupt data, set retries with backoff in the orchestrator, and alert on failure rather than relying on someone noticing. The job should be safe to re-run from the start, which means no append-only side effects and a checkpoint or watermark it can resume from.

</details>

## 📈 Incremental Loading

<details>
<summary><b>What is a watermark and why is incremental loading better than truncate-and-reload?</b></summary>

<br>

A watermark is the high-water value (max `updated_at` or an incrementing id) from the last successful run; the next run loads only `WHERE updated_at > watermark`. Truncate-and-reload empties the target table first, creating a query blackout window and re-processing all history every night. Incremental loading keeps the table queryable the whole time and scales with new data, not total data.

🛠️ *Practice it live:* [Implement Incremental Loading in an ETL](https://heydevjob.com/data)

</details>

<details>
<summary><b>What can go wrong with a timestamp-based watermark?</b></summary>

<br>

Late-arriving and out-of-order data can land with a timestamp earlier than your watermark and get skipped. Boundary rows at exactly the watermark value can be missed or double-loaded depending on whether you use `>` or `>=` - which is why the load must be idempotent. Clock skew between source systems makes wall-clock watermarks unreliable; a monotonic source sequence is safer when available.

</details>

## 🗂️ File Formats & the Lakehouse

<details>
<summary><b>Why convert CSV to Parquet, and what does columnar storage buy you?</b></summary>

<br>

Parquet is columnar and compressed, so a query that touches three columns reads only those columns instead of every byte of every row. Compression and column encoding shrink storage and I/O dramatically, and the embedded schema means no type guessing. CSV is row-oriented, uncompressed, and untyped - fine for interchange, slow and expensive as a query target.

🛠️ *Practice it live:* [Convert CSV to Parquet for a Lakehouse](https://heydevjob.com/data)

</details>

<details>
<summary><b>What is partition pruning and how does Hive-style partitioning enable it?</b></summary>

<br>

Partitioning lays out files in directories like `dt=2026-06-01/`, so a query with `WHERE dt = '2026-06-01'` reads only that directory and skips the rest - that's pruning. It turns a full scan into a targeted one. The trade-off is granularity: partition too finely (by hour, or by a high-cardinality column) and you get millions of tiny files that hurt more than they help.

</details>

## ⚡ Performance

<details>
<summary><b>Why are row-by-row inserts slow, and what do you replace them with?</b></summary>

<br>

Each single-row `INSERT` with its own commit is a network round-trip plus a transaction, so loading a million rows means a million round-trips. Replace it with a bulk path - Postgres `COPY`, a multi-row insert, or batched commits - which moves the bottleneck from per-row overhead to the database's optimized bulk loader. This is routinely a 10-100x throughput win for the identical result.

🛠️ *Practice it live:* [Optimize Batch Insert Throughput (COPY)](https://heydevjob.com/data)

</details>

<details>
<summary><b>When would you reach for Polars over pandas?</b></summary>

<br>

For ETL-sized transforms, Polars is typically 5-30x faster because it's multi-core, has a lazy query optimizer that fuses operations, and uses Arrow memory. pandas is single-threaded and eager, so it materializes every intermediate. Polars' lazy API also lets it push filters down and skip work entirely. For the same DataFrame logic you get a large speedup with no infrastructure change.

🛠️ *Practice it live:* [Migrate a pandas Pipeline to Polars](https://heydevjob.com/data)

</details>

<details>
<summary><b>A query that was fast is now slow. How do you diagnose it?</b></summary>

<br>

Run `EXPLAIN ANALYZE` and look for a sequential scan where you expected an index, a join that exploded the row count, or a bad row estimate from stale statistics. Common causes are a missing index on a new filter column, data growth past a tipping point, or a fan-out join. Fix the actual cause - add the index, narrow the join, or `ANALYZE` to refresh stats - rather than blindly adding hints.

</details>

## 🛠️ Orchestration

<details>
<summary><b>Why use Airflow instead of cron plus a Python script?</b></summary>

<br>

cron gives you a schedule and nothing else. Airflow adds retries with backoff, task-level dependency management, backfills over historical date ranges, observability into which task failed and why, and SLAs. When a multi-step pipeline fails at step three, Airflow re-runs from there; cron just silently doesn't produce data.

🛠️ *Practice it live:* [Schedule a Pipeline as an Airflow DAG](https://heydevjob.com/data)

</details>

<details>
<summary><b>What does it mean for an Airflow task to be idempotent, and why does it matter for backfills?</b></summary>

<br>

A backfill re-runs the DAG across many past execution dates, so each task must produce correct output for its date no matter how many times it runs. Tasks should key their writes on the execution date (the logical interval), not `now()`, and overwrite the target partition rather than append. Non-idempotent tasks turn a backfill into a duplication or double-counting incident.

</details>

## 🧱 Analytics Engineering (dbt)

<details>
<summary><b>Explain the dbt staging-to-marts pattern.</b></summary>

<br>

Staging models clean and rename raw source columns one-to-one, materialized as views - they're the typed, consistent interface to raw data. Marts join and aggregate staging models into the tables the business actually queries, materialized as tables for speed. The separation keeps cleaning logic in one place and business logic in another, with lineage connecting them.

🛠️ *Practice it live:* [Build a Staging-to-Marts dbt Project](https://heydevjob.com/data)

</details>

<details>
<summary><b>What's the difference between a dbt source and a ref, and why use them?</b></summary>

<br>

`{{ source('raw', 'orders') }}` points at a raw table dbt didn't build; `{{ ref('stg_orders') }}` points at another dbt model. Using them instead of hardcoded table names lets dbt build the dependency graph, run models in the right order, and render lineage. They also make environment promotion automatic - the same code resolves to dev or prod schemas via the profile.

</details>

<details>
<summary><b>What kinds of tests does dbt give you and what do they catch?</b></summary>

<br>

Built-in generic tests (`not_null`, `unique`, `accepted_values`, `relationships`) catch the everyday data-quality failures - a null `customer_id`, a duplicated key, an orphaned foreign key. Singular tests are custom SQL that returns failing rows for bespoke business rules. Running them in `dbt build` means a broken assumption fails the run instead of quietly shipping wrong numbers to a dashboard.

</details>

<details>
<summary><b>How do incremental models work in dbt?</b></summary>

<br>

An incremental model only transforms new or changed rows on each run instead of rebuilding the whole table, using an `is_incremental()` filter against the existing table (typically `WHERE updated_at > (SELECT max(updated_at) FROM {{ this }})`). You declare a `unique_key` so dbt can merge rather than append. It's the dbt expression of watermark-based incremental loading, used when full rebuilds get too expensive.

</details>

## 🏛️ Senior: Modeling & Streaming

<details>
<summary><b>What is a Kimball star schema and why is it still the default for warehouses?</b></summary>

<br>

A star schema has one central fact table (the events, like order line items) surrounded by dimension tables (customer, product, date) joined on surrogate keys. It makes dashboards fast because joins are simple and predictable, and it keeps business attributes in conformed dimensions reused across facts. It's been the dominant analytics design since the 90s because it maps cleanly to how people ask business questions.

🛠️ *Practice it live:* [Build a Kimball Star Schema](https://heydevjob.com/data)

</details>

<details>
<summary><b>Why use surrogate keys instead of natural keys in a dimension?</b></summary>

<br>

A surrogate key is a meaningless integer the warehouse generates; a natural key is the source system's id. Surrogates decouple the warehouse from source-system changes, make joins fast and uniform, and are what slowly-changing dimensions need to represent the same entity at different points in time. The natural (business) key still lives on the row as an attribute for lineage.

</details>

<details>
<summary><b>How do you back-fill a new column on a large table safely?</b></summary>

<br>

Update in bounded batches by primary-key range so you never lock the whole table, commit each batch, and make the job resumable from the last completed range. Verify a sample of updated rows against the expected value before and during the run. For a money column especially, an off-by-100 means a wrong refund, so the verification step is what separates a senior backfill from an incident.

🛠️ *Practice it live:* [Backfill a Column Safely (Batched + Verified)](https://heydevjob.com/data)

</details>

<details>
<summary><b>Kafka delivers at-least-once by default. How do you get exactly-once processing?</b></summary>

<br>

At-least-once means retries deliver duplicates, so the consumer has to dedupe. The standard pattern is an idempotent consumer: derive a unique key per event and track processed keys (or UPSERT on that key) so a re-delivered event is a no-op. Combined with committing offsets only after the write succeeds, this gives effectively-once semantics without depending on the producer being perfect.

🛠️ *Practice it live:* [Build an Exactly-Once Kafka Pipeline](https://heydevjob.com/data)

</details>

<details>
<summary><b>How do you handle a slowly changing dimension (SCD Type 2)?</b></summary>

<br>

Type 2 keeps history by adding a new row each time a tracked attribute changes, with `valid_from` / `valid_to` timestamps and an `is_current` flag, all under a new surrogate key. Facts join to the dimension version that was current at the event time, so a report reflects what the attribute was then, not now. Type 1 just overwrites and loses history - the choice depends on whether the business needs point-in-time accuracy.

</details>

> [!IMPORTANT]
> **Build it for real**
> Reading answers gets you through the screen. Shipping fixes gets you the offer. Every "Practice it live" link above is a real broken pipeline, query, or model you fix in a live cloud workspace from your browser - then it lands on a portfolio hiring managers can open and click. The junior tier is free, no card, no setup.
>
> **Start your portfolio →** [heydevjob.com/data](https://heydevjob.com/data)

---

**Explore Data** · [📍 Roadmap](../roadmaps/data.md) · [🛠️ Projects](../projects/data/README.md) · [💬 Interview](data.md) · [✅ Checklist](../checklists/data.md)
