# Schedule a Pipeline as an Airflow DAG

**Role:** Data Engineer · **Level:** Mid · **Type:** Build · **Skills:** Airflow, DAGs, orchestration, scheduling

> The daily ETL runs as `python3 etl.py` from cron - no retries, no backfills, no visibility. When it fails at 3am, nobody knows until the dashboard is stale. You're turning it into a proper Airflow DAG.

## The scenario

A daily ETL runs from cron with no orchestration. You wrap it in an Airflow DAG: `dag_id="daily_etl"`, daily schedule, `catchup=False`, three `PythonOperator` tasks (extract → transform → load) wired with dependencies, and `retries=2` with a retry delay.

## The code

The starter scaffold (`dags/daily_etl.py`):

```python
"""Daily ETL DAG - STARTER CODE.
Wrap these three functions into an Airflow DAG with extract >> transform >> load."""

def extract(): ...      # provided
def transform(): ...    # provided
def load(): ...         # provided

# TODO: define the DAG and three PythonOperator tasks here.
# dag_id="daily_etl", schedule="@daily", catchup=False, retries=2.
```

The starting point:

```text
ETL runs from cron: no retries, no backfill, no dependency visibility, no SLAs
```

## How you'll approach it

1. **Define the DAG.** `dag_id="daily_etl"`, `schedule="@daily"`, `catchup=False`, and default args with `retries=2` and a retry delay.
2. **Wrap each step.** A `PythonOperator` for each of extract, transform, load.
3. **Wire dependencies.** `extract >> transform >> load` so the order is explicit and the graph is visible.
4. **Verify.** `airflow dags test daily_etl <date>` runs the three tasks in order end to end.

## What you'll learn

- Defining an Airflow DAG: schedule, catchup, default args, retries
- Wrapping functions in `PythonOperator` tasks
- Expressing dependencies with `>>` and why explicit ordering matters
- What orchestration buys you over cron: retries, backfills, visibility, SLAs

## What it proves

You can turn an ad-hoc script into an orchestrated, retried, observable pipeline - the step that makes data work production-grade.

> Resume-ready: *Migrated a cron ETL to an Airflow DAG with extract/transform/load tasks, dependencies, and retries, gaining backfills and pipeline observability.*

## On the roadmap

Part of the [Data Engineer Roadmap](../../roadmaps/data.md) - **Stage 6: Orchestration** → Airflow DAGs.

---

**Build it for real.** This is ticket **DE-205** on HeyDevJob - the real cron ETL above, in a cloud workspace you build the DAG for from your browser. Free on the junior tier, no card, no setup. [Schedule a Pipeline as an Airflow DAG on HeyDevJob →](https://heydevjob.com/data)
