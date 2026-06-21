# Debug a Broken SQL Revenue Aggregation

**Role:** Data Engineer · **Level:** Junior · **Type:** Debug · **Skills:** SQL, joins, aggregation, reporting

> The revenue report says Electronics did ~$600. Finance knows it was ~$300. The numbers are exactly double - the tell that a join is fanning out rows before the `SUM`. You're finding the extra join.

## The scenario

A daily revenue report shows category totals roughly double what they should be. The query joins and groups to produce per-category summaries, but a join to a many-rows table (products have multiple tags) inflates the row count before the `SUM`, so the totals inflate too. You find the fan-out and fix it.

## The code

The inflating query (`report.py`):

```python
query = """
    SELECT
        p.category,
        COUNT(oi.id) AS total_items,
        SUM(oi.quantity * oi.unit_price) AS total_revenue
    FROM order_items oi
    JOIN products p ON p.id = oi.product_id
    JOIN product_tags pt ON pt.product_id = p.id   # <- fan-out: many tags per product
    GROUP BY p.category
    ORDER BY total_revenue DESC;
"""
```

The symptom:

```text
Electronics total_revenue: ~$600   (should be ~$300)  # doubled
```

## How you'll approach it

1. **Suspect the join.** Doubled totals almost always mean a one-to-many join multiplied rows before the aggregate.
2. **Count at each stage.** The join to `product_tags` produces one row per (order_item, tag) - so every order item is counted once per tag.
3. **Remove the fan-out.** Drop the unnecessary `product_tags` join (it isn't needed for revenue), or aggregate tags in a subquery so the grain stays one row per order item.
4. **Verify.** Totals now match the known-correct figures.

## What you'll learn

- Why a one-to-many join inflates a `SUM`
- The debugging habit of counting rows at each pipeline stage
- Fixing fan-out by narrowing the join or pre-aggregating in a subquery
- That "GROUP BY gave the wrong number" is usually a grain problem

## What it proves

You can debug the single most common SQL reporting bug - a join that quietly doubles your numbers - which is core data-engineering correctness.

> Resume-ready: *Diagnosed a doubled revenue report to a one-to-many join inflating rows before aggregation and corrected the query grain.*

## On the roadmap

Part of the [Data Engineer Roadmap](../../roadmaps/data.md) - **Stage 1: SQL Foundations** → Joins & aggregation correctness.

---

**Build it for real.** This is ticket **DE-108** on HeyDevJob - the real doubled report above, in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Debug a Broken SQL Revenue Aggregation on HeyDevJob →](https://heydevjob.com/data)
