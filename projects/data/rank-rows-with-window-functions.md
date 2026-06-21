[Home](../../README.md) › [Data Projects](README.md) › **Rank Rows Per Group With Window Functions**

# Rank Rows Per Group With SQL Window Functions

`Data` · `🟢 Junior` · `🔨 Build` · `Ticket DE-115`

**Skills** — SQL · window functions · ranking · analytics

> "Top 3 customers per region" sounds simple until you try it. The current report runs a slow correlated subquery per region. You're rewriting it as one window-function query - the technique every data interview probes after joins.

> [!NOTE]
> **The scenario**
> A "top 3 customers per region by total spend" report uses a slow correlated subquery per region. You rewrite it as a single query: compute each customer's total spend, rank within each region with a window function, and keep only the top 3 per region.

## 🧩 The code

The scaffold you build toward (`query.sql`):

```sql
-- Top 3 customers per region by total spend.
-- Output: region, customer_id, customer_name, total_spend, rank
-- Order by region, rank ASC.
-- TODO: rewrite using a window function (ROW_NUMBER OVER PARTITION BY region).

SELECT 'TODO' AS region;
```

The target:

```text
3 rows per region (9 total), ranked by total_spend DESC, in a single scan
```

## 🛠️ How you'll approach it

1. **Aggregate spend.** Sum each customer's spend (a `GROUP BY` or CTE).
2. **Rank within region.** `ROW_NUMBER() OVER (PARTITION BY region ORDER BY total_spend DESC)` numbers customers per region without collapsing rows.
3. **Filter to top 3.** Wrap it in a CTE/subquery and keep `rank <= 3` (you can't filter a window function in the same `WHERE`).
4. **Verify.** Exactly 3 rows per region, ordered by region then rank, in one pass.

## 🎓 What you'll learn

- Window functions: `ROW_NUMBER`/`RANK` over `PARTITION BY`
- Why you rank in one query and filter in an outer query
- Replacing slow correlated subqueries with a single declarative pass
- The pattern behind top-N-per-group, running totals, and rolling windows

## 🏆 What it proves

You can use window functions fluently - the most-asked SQL skill after joins, and the tool that turns awkward per-group problems into one clean query.

> [!TIP]
> **Resume-ready** — *Replaced a slow correlated-subquery report with a single window-function query (ROW_NUMBER over PARTITION BY) to return top-N customers per region.*

## 🗺️ On the roadmap

Part of the [Data Engineer Roadmap](../../roadmaps/data.md) - **Stage 1: SQL Foundations** → Window functions.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **DE-115** on HeyDevJob - the real top-N-per-group report above, in a cloud workspace you build from your browser. Free on the junior tier, no card, no setup.
> [Rank Rows Per Group With Window Functions on HeyDevJob →](https://heydevjob.com/projects/sql-window-functions-ranking-project)

**Explore Data** · [📍 Roadmap](../../roadmaps/data.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/data.md) · [✅ Checklist](../../checklists/data.md)

[◀ Prev: Debug a Broken SQL Revenue Aggregation](debug-a-broken-sql-aggregation.md) · [Next: Make an ETL Pipeline Idempotent ▶](make-an-etl-pipeline-idempotent.md)
