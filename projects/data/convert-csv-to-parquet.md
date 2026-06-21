# Convert CSV to Parquet for a Lakehouse

**Role:** Data Engineer · **Level:** Junior · **Type:** Build · **Skills:** Parquet, pyarrow, columnar formats, partitioning

> Storage costs on the data lake are climbing because everything's stored as raw CSV. You're converting the bronze layer to Parquet - columnar, compressed, partitioned by date - so it's half the size and queries prune to the partitions they need.

## The scenario

The data lake's CSV exports are bloating storage and slowing queries. You convert them to Parquet with compression and Hive-style partitioning by year/month (derived from an `event_ts` column). The output should be under 50% of the CSV size and support partition pruning.

## The code

The starter scaffold (`convert.py`):

```python
"""CSV -> Parquet converter - STARTER CODE.
Read events.csv and write Parquet partitioned by year/month under
./lake/events/. Use snappy compression."""
import pandas as pd

df = pd.read_csv("events.csv", parse_dates=["event_ts"])
print(f"loaded {len(df)} rows")

# TODO: derive year + month from event_ts, then write to ./lake/events/
# partitioned, snappy-compressed.
```

The target:

```text
Parquet output <50% the size of the CSV, with 3+ distinct (year, month) partitions
```

## How you'll approach it

1. **Derive partition columns.** Extract `year` and `month` from `event_ts` so the data can be laid out by date.
2. **Write partitioned Parquet.** Use pyarrow (or pandas + pyarrow) to write under `./lake/events/` partitioned by year/month with snappy compression.
3. **Confirm the layout.** The output is a Hive-style directory tree (`year=2024/month=01/...`) an engine can prune.
4. **Verify the win.** The Parquet footprint is well under half the CSV, and you can query a single partition without scanning everything.

## What you'll learn

- Why Parquet (columnar + compressed) beats CSV for analytics
- Hive-style partitioning and how partition pruning speeds queries
- Writing partitioned Parquet with pyarrow
- The bronze→silver lakehouse layering idea

## What it proves

You can convert raw data into the format modern analytics actually runs on - Parquet, partitioned and compressed - which every lake engine reads natively.

> Resume-ready: *Built a CSV-to-Parquet converter with snappy compression and year/month partitioning, cutting storage by half and enabling partition pruning.*

## On the roadmap

Part of the [Data Engineer Roadmap](../../roadmaps/data.md) - **Stage 4: File Formats & the Lakehouse** → Columnar formats & partitioning.

---

**Build it for real.** This is ticket **DE-113** on HeyDevJob - the real CSV-to-Parquet task above, in a cloud workspace you build from your browser. Free on the junior tier, no card, no setup. [Convert CSV to Parquet for a Lakehouse on HeyDevJob →](https://heydevjob.com/data)
