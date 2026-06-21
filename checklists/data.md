[Home](../README.md) › **Data Checklist**

# Data Engineer Job-Readiness Checklist

`Checklist` · `8 stages` · `Tick what you can prove`

The concrete skill list that gets you hired as a data engineer - not topics to "have heard of," but things you can do in a workspace under pressure. It follows the [Data Engineer Roadmap](../roadmaps/data.md) from junior SQL to senior warehouse design. Tick an item only if you could do it right now on a real broken system, not just describe it. Anything you can't tick is a ticket to go ship on [HeyDevJob](https://heydevjob.com/data).

## 🧮 Stage 1 - SQL Foundations

- [ ] Write correct multi-table joins and reason about which join type keeps or drops rows
- [ ] Identify the grain of a table and confirm a query hasn't changed it
- [ ] Diagnose a doubled aggregate as a one-to-many fan-out join
- [ ] Fix a fan-out by narrowing the join or pre-aggregating the many-side in a subquery
- [ ] Use `WHERE` vs `HAVING` correctly (filter rows before vs groups after)
- [ ] Write `GROUP BY` aggregations and know when an aggregate must go in `HAVING`
- [ ] Rank rows per group with `ROW_NUMBER` / `RANK` over `PARTITION BY`
- [ ] Compute running totals and rolling windows with window functions
- [ ] Find and remove duplicate rows using a window function over the natural key
- [ ] Choose `UNION` vs `UNION ALL` deliberately for correctness and speed

## 🔁 Stage 2 - ETL Reliability

- [ ] Make a load idempotent with `INSERT ... ON CONFLICT` (UPSERT) on a natural key
- [ ] Explain why a non-idempotent append doubles rows on retry
- [ ] Skip and log malformed rows instead of crashing the whole load
- [ ] Quarantine rejected rows with a reason and surface a reject count
- [ ] Validate structural rules at ingestion and business rules after transformation
- [ ] Make a pipeline safe to re-run from the start after a mid-run failure
- [ ] Add retries with backoff around steps that touch flaky sources

## 📈 Stage 3 - Incremental Loading

- [ ] Track a watermark and load only `WHERE updated_at > watermark`
- [ ] Explain why incremental loading beats truncate-and-reload (no query blackout)
- [ ] Handle late-arriving and out-of-order data against a timestamp watermark
- [ ] Pick `>` vs `>=` at the watermark boundary and pair it with an idempotent write

## 🗂️ Stage 4 - File Formats & the Lakehouse

- [ ] Convert CSV to Parquet with compression
- [ ] Explain how columnar storage skips unread columns
- [ ] Lay out data with Hive-style partitioning (`dt=YYYY-MM-DD/`)
- [ ] Write a query that triggers partition pruning
- [ ] Avoid the small-files problem from over-fine partitioning

## ⚡ Stage 5 - Performance

- [ ] Replace row-by-row inserts with a bulk path (`COPY` / batched commits)
- [ ] Explain why single-row insert-and-commit is the worst-case load pattern
- [ ] Migrate a pandas transform to Polars' lazy, multi-core API for a real speedup
- [ ] Read an `EXPLAIN ANALYZE` plan and spot a seq scan that should be an index
- [ ] Add the right index for a new filter column and refresh stale statistics
- [ ] Diagnose a slow query to its actual cause instead of guessing

## 🛠️ Stage 6 - Orchestration

- [ ] Wrap an ETL script in an Airflow DAG with task dependencies
- [ ] Configure schedules, retries, and SLAs on a DAG
- [ ] Make tasks idempotent and keyed on the execution date so backfills are safe
- [ ] Run a backfill across a historical date range without double-counting
- [ ] Explain what Airflow gives you over cron plus a script

## 🧱 Stage 7 - Analytics Engineering (dbt)

- [ ] Structure a dbt project with the staging-to-marts pattern
- [ ] Declare raw tables in `sources.yml` and reference them with `{{ source() }}`
- [ ] Build models on top of each other with `{{ ref() }}` for automatic lineage
- [ ] Choose materializations (view for staging, table for marts) and justify each
- [ ] Add generic tests (`not_null`, `unique`, `relationships`) and run them in `dbt build`
- [ ] Write a dbt incremental model with a `unique_key` and `is_incremental()` filter

## 🏛️ Stage 8 - Senior: Modeling & Streaming

- [ ] Design a Kimball star schema: one fact table, conformed dimensions
- [ ] Use surrogate keys and explain why they beat natural keys in dimensions
- [ ] Implement a slowly changing dimension (Type 2) with valid-from/valid-to history
- [ ] Back-fill a large column in bounded, resumable batches without locking the table
- [ ] Verify a backfill against expected values before and during the run
- [ ] Build an idempotent (exactly-once) Kafka consumer that dedupes on an event key
- [ ] Commit Kafka offsets only after the downstream write succeeds
- [ ] Reason about late data, ordering, and replay in a streaming pipeline

> [!IMPORTANT]
> **Can't tick it? Prove it.**
> Every unchecked box is a real broken system waiting on [HeyDevJob](https://heydevjob.com/data) - a pipeline, query, or model you fix in a live cloud workspace from your browser. The junior tier is free, no card, no setup. Each ticket you ship lands on a portfolio hiring managers can open and click.
>
> **Start your portfolio →** [heydevjob.com/data](https://heydevjob.com/data)

---

**Explore Data** · [📍 Roadmap](../roadmaps/data.md) · [🛠️ Projects](../projects/data/README.md) · [💬 Interview](../interview/data.md) · [✅ Checklist](data.md)
