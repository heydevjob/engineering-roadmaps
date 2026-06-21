# Build a Kimball Star Schema

**Role:** Data Engineer · **Level:** Senior · **Type:** Design · **Skills:** dimensional modeling, Kimball, star schema, dbt

> Every dashboard joins six raw tables, so every dashboard is slow. You're modeling the orders mart as a star schema - one fact table, conformed dimensions, surrogate keys - so reporting joins become deterministic and fast.

## The scenario

Reporting is slow because dashboards join six raw tables. You build a Kimball star schema in dbt: `dim_customers`, `dim_products`, `dim_date`, and `fct_orders` at order-line grain - surrogate keys, foreign keys to each dimension, and tested relationships. All surrogate keys get unique+not_null tests; all FKs get relationships tests.

## The code

The fact-table pattern you build toward (`fct_orders.sql`):

```sql
SELECT
    md5(ol.id::text)                        AS order_sk,
    md5(ol.customer_id::text)               AS customer_sk,
    md5(ol.product_id::text)                AS product_sk,
    to_char(ol.order_date, 'YYYYMMDD')::int AS date_sk,
    ol.quantity,
    ol.quantity * p.price                   AS gross_amount,
    ol.discount                             AS discount_amount,
    ol.quantity * p.price - ol.discount     AS net_amount
FROM {{ source('raw', 'order_lines') }} ol
JOIN {{ source('raw', 'products') }} p ON p.id = ol.product_id
```

(Build-from-scratch: you author the fact table and three dimensions.)

## How you'll approach it

1. **Pick the grain.** Decide the fact grain (one row per order line) - everything else follows from it.
2. **Build dimensions.** `dim_customers`, `dim_products`, and a generated `dim_date` (one row per day across the year), each with a surrogate key.
3. **Build the fact.** `fct_orders` carries the measures (quantity, gross/discount/net amount) and FKs (surrogate keys) to each dimension.
4. **Test it.** Unique+not_null on every surrogate key, relationships tests on every FK, then `dbt build` runs the lineage and tests green.

## What you'll learn

- Dimensional modeling: fact tables, dimensions, and grain
- Surrogate keys and why they decouple the warehouse from source ids
- Conformed dimensions and a generated date dimension
- Enforcing integrity with unique/not_null/relationships tests

## What it proves

You can design the textbook reporting layout - a Kimball star schema with tested integrity - that makes dashboards fast and joins deterministic.

> Resume-ready: *Designed a Kimball star schema in dbt - fact table at order-line grain, conformed dimensions, surrogate keys, and tested relationships - to accelerate reporting.*

## On the roadmap

Part of the [Data Engineer Roadmap](../../roadmaps/data.md) - **Stage 8: Senior - Modeling & Streaming** → Dimensional modeling.

---

**Build it for real.** This is ticket **DE-302** on HeyDevJob - the real modeling task above, in a cloud workspace you build from your browser. Each model you ship lands on a portfolio you can show. [Build a Kimball Star Schema on HeyDevJob →](https://heydevjob.com/data)
