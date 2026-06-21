# Build a Staging-to-Marts dbt Project

**Role:** Data Engineer · **Level:** Mid · **Type:** Build · **Skills:** dbt, analytics engineering, SQL modeling

> The team's reporting logic lives as 200-line CTEs copy-pasted across notebooks. You're moving it into dbt with the standard staging → marts pattern - so transformations are versioned, tested, and have lineage.

## The scenario

Reporting queries are duplicated across notebooks. You build a minimal dbt project with the staging → marts pattern: `stg_orders.sql` cleans the raw orders source, `customer_orders.sql` joins staging with customers and aggregates spend, a `sources.yml` declares the raw tables, and a `not_null` test guards `customer_id`. `dbt build` runs the lineage and tests.

## The code

The project scaffold (`dbt_project.yml`):

```yaml
# TODO: fill in the dbt project config.
# name: shop
# profile: shop
# model-paths: ["models"]
# models:
#   shop:
#     staging:  {+materialized: view}
#     marts:    {+materialized: table}
```

The starting point:

```text
reporting logic = 200-line CTEs duplicated across notebooks (no tests, no lineage)
```

## How you'll approach it

1. **Declare sources.** `sources.yml` names the raw tables so models reference `{{ source('raw', 'orders') }}` and dbt can build lineage.
2. **Build staging.** `stg_orders.sql` selects from the source and cleans/renames - materialized as a view.
3. **Build the mart.** `customer_orders.sql` joins staging orders with customers and aggregates total spend - materialized as a table.
4. **Test and build.** Add a `not_null` test on `customer_id`, then `dbt build` compiles, runs the models in dependency order, and runs the tests.

## What you'll learn

- The dbt staging → marts convention: staging cleans, marts model for business questions
- Declaring sources and referencing them with `{{ source() }}`
- Materializations (view vs table) and why each layer uses which
- Built-in tests (`not_null`) and automatic lineage

## What it proves

You can structure transformations the modern analytics-engineering way - tested, versioned, with lineage - instead of copy-pasted notebook SQL.

> Resume-ready: *Built a dbt project with the staging-to-marts pattern - sources, cleaned staging models, an aggregated mart, and data tests - replacing duplicated notebook SQL.*

## On the roadmap

Part of the [Data Engineer Roadmap](../../roadmaps/data.md) - **Stage 7: Analytics Engineering (dbt)** → Staging → marts with dbt.

---

**Build it for real.** This is ticket **DE-206** on HeyDevJob - the real dbt scaffold above, in a cloud workspace you build from your browser. Free on the junior tier, no card, no setup. [Build a Staging-to-Marts dbt Project on HeyDevJob →](https://heydevjob.com/data)
