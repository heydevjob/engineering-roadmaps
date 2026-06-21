[Home](../../README.md) › [Data Projects](README.md) › **Migrate a pandas Pipeline to Polars**

# Migrate a pandas Pipeline to Polars

`Data` · `🟡 Mid` · `⚡ Optimize` · `Ticket DE-208`

**Skills** — Polars · pandas · lazy evaluation · performance

> The daily revenue pipeline takes ~3 seconds on 200k rows in pandas. The exact same logic in Polars' lazy API runs ~5x faster - same output, multi-core, with a query planner fusing the steps. You're porting it.

> [!NOTE]
> **The scenario**
> A revenue-by-region pipeline in pandas (join → group → rolling average) takes ~3 seconds on 200k rows. You port it to Polars' lazy API: `scan_csv`, join on `customer_id`, group by (region, week), sum amount, add a 4-week rolling average per region, and `collect()`. It must be at least 2x faster with identical output.

## 🧩 The code

The pandas pipeline to port (`pipeline.py`):

```python
def run():
    orders = pd.read_csv("orders.csv", parse_dates=["created_at"])
    customers = pd.read_csv("customers.csv")
    joined = orders.merge(customers, left_on="customer_id", right_on="id", how="inner")
    joined["week"] = joined["created_at"].dt.isocalendar().week
    by_region_week = (joined.groupby(["region", "week"], as_index=False)["amount"].sum()
                      .rename(columns={"amount": "revenue"}).sort_values(["region", "week"]))
    by_region_week["revenue_4w_avg"] = (by_region_week.groupby("region")["revenue"]
                      .rolling(window=4, min_periods=1).mean().reset_index(0, drop=True))
    by_region_week.to_csv("revenue.csv", index=False)
```

The target:

```text
~3s in pandas -> 2x+ faster in Polars, identical output (region, week, revenue, revenue_4w_avg)
```

## 🛠️ How you'll approach it

1. **Go lazy.** Start from `pl.scan_csv()` so Polars builds a query plan instead of materializing each step.
2. **Translate the ops.** Join on `customer_id`, derive `week`, `group_by(["region","week"]).agg(sum)`, then a windowed 4-week rolling mean per region.
3. **Collect once.** `collect()` at the end lets the planner fuse operations and parallelize across cores.
4. **Verify.** Output matches pandas row-for-row, and it runs at least 2x faster (typically much more).

## 🎓 What you'll learn

- Polars' lazy API and why deferring execution enables optimization
- Translating pandas join/groupby/rolling to Polars expressions
- How the query planner fuses operations and uses all cores
- Verifying a performance port produces identical results

## 🏆 What it proves

You can speed up a real ETL pipeline several-fold by moving to a modern dataframe engine - a measurable, in-demand optimization skill.

> [!TIP]
> **Resume-ready** — *Ported a pandas join/groupby/rolling pipeline to Polars' lazy API, producing identical output ~5x faster via query optimization and multi-core execution.*

## 🗺️ On the roadmap

Part of the [Data Engineer Roadmap](../../roadmaps/data.md) - **Stage 5: Performance** → Faster dataframes.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **DE-208** on HeyDevJob - the real pandas pipeline above, in a cloud workspace you port from your browser. Free on the junior tier, no card, no setup.
> [Migrate a pandas Pipeline to Polars on HeyDevJob →](https://heydevjob.com/data)

**Explore Data** · [📍 Roadmap](../../roadmaps/data.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/data.md) · [✅ Checklist](../../checklists/data.md)

[◀ Prev: Build a Staging-to-Marts dbt Project](build-a-dbt-staging-to-marts-project.md) · [Next: Build a Kimball Star Schema ▶](build-a-kimball-star-schema.md)
