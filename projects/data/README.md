[Home](../../README.md) › **Data Projects**

# Data Engineer Projects

`13 projects` · `Debug · Build · Harden · Optimize · Design` · `Junior → Senior`

Real pipelines, queries, and models to build and fix - each one proves a specific data-engineering skill and produces something you can put on a portfolio. Data engineering is about moving data correctly, fast, and reliably, so these run from a doubled SQL report to an exactly-once Kafka pipeline. Every project is grounded in a real [HeyDevJob](https://heydevjob.com/data) ticket: the actual code, the real failure, and a live cloud workspace where you build or fix it.

> [!NOTE]
> **Project types**
> 🔨 **Build** - create the pipeline or model from nothing · 🔍 **Debug** - find and fix wrong data · ⚡ **Optimize** - make it fast · 🛡️ **Harden** - make it reliable and replayable · 📐 **Design** - architect the warehouse

Pairs with the [Data Engineer Roadmap](../../roadmaps/data.md): the roadmap tells you what to learn, these are where you prove it.

## 🟢 Junior projects

Start here. Get SQL right, make loads reliable, and learn the lakehouse formats.

| Project | Ticket | Type | Skills |
|---------|--------|------|--------|
| [Debug a Broken SQL Revenue Aggregation](debug-a-broken-sql-aggregation.md) | `DE-108` | 🔍 Debug | SQL, joins, aggregation |
| [Rank Rows Per Group With Window Functions](rank-rows-with-window-functions.md) | `DE-115` | 🔨 Build | SQL, window functions, ranking |
| [Make an ETL Pipeline Idempotent](make-an-etl-pipeline-idempotent.md) | `DE-101` | 🛡️ Harden | ETL, SQL, idempotency |
| [Make a CSV Importer Resilient to Bad Data](make-a-csv-importer-resilient.md) | `DE-104` | 🛡️ Harden | CSV, data cleaning, error handling |
| [Convert CSV to Parquet for a Lakehouse](convert-csv-to-parquet.md) | `DE-113` | 🔨 Build | Parquet, pyarrow, partitioning |
| [Implement Incremental Loading in an ETL](implement-incremental-loading.md) | `DE-110` | 🔨 Build | ETL, incremental load, watermarks |

## 🟡 Mid projects

Performance, orchestration, and analytics engineering - production-grade pipelines.

| Project | Ticket | Type | Skills |
|---------|--------|------|--------|
| [Optimize Batch Insert Throughput (COPY)](optimize-batch-insert-with-copy.md) | `DE-203` | ⚡ Optimize | PostgreSQL, COPY, performance |
| [Schedule a Pipeline as an Airflow DAG](schedule-a-pipeline-with-airflow.md) | `DE-205` | 🔨 Build | Airflow, DAGs, orchestration |
| [Build a Staging-to-Marts dbt Project](build-a-dbt-staging-to-marts-project.md) | `DE-206` | 🔨 Build | dbt, analytics engineering, SQL |
| [Migrate a pandas Pipeline to Polars](migrate-pandas-to-polars.md) | `DE-208` | ⚡ Optimize | Polars, lazy evaluation, performance |

## 🔴 Senior projects

Dimensional modeling, safe backfills, and exactly-once streaming.

| Project | Ticket | Type | Skills |
|---------|--------|------|--------|
| [Build a Kimball Star Schema](build-a-kimball-star-schema.md) | `DE-302` | 📐 Design | dimensional modeling, Kimball, dbt |
| [Backfill a Column Safely (Batched + Verified)](backfill-a-column-safely.md) | `DE-304` | 🔨 Build / 📐 Design | backfill, batching, Postgres |
| [Build an Exactly-Once Kafka Pipeline](build-an-exactly-once-kafka-pipeline.md) | `DE-308` | 📐 Design | Kafka, streaming, exactly-once |

> [!IMPORTANT]
> **Build it for real**
> Reading about idempotency isn't the same as running an ETL twice and watching the row count double, then fixing it so it can't. Every project here is a live ticket on [HeyDevJob](https://heydevjob.com/data) - a real pipeline in a cloud workspace, no setup. The junior tier is free, no card. Ship it, and it lands on a portfolio hiring managers can open and click.
>
> **Start the Data Engineer projects on HeyDevJob →** [heydevjob.com/data](https://heydevjob.com/data)

---

**Explore Data** · [📍 Roadmap](../../roadmaps/data.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/data.md) · [✅ Checklist](../../checklists/data.md)
