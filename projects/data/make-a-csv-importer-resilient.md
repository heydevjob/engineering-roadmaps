# Make a CSV Importer Resilient to Bad Data

**Role:** Data Engineer · **Level:** Junior · **Type:** Harden · **Skills:** CSV, data cleaning, error handling, ETL

> The vendor catalog import dies halfway through on a malformed row - and every good row after it never lands. Real data is always messy. You're making the importer skip the bad rows and log them, not crash on the first one.

## The scenario

A product-catalog import from a vendor CSV crashes partway through because some rows are malformed (missing fields, unparseable prices). When it crashes, the valid rows after the bad one are lost. You make it resilient: skip and log bad rows while letting good data through.

## The code

The crash-on-bad-row loop (`import_products.py`):

```python
for row in reader:
    sku = row['sku']
    name = row['name']
    category = row['category']
    price = float(row['price'])   # crashes here on a malformed price
    stock = int(row['stock'])

    cursor.execute("""
        INSERT INTO products (sku, name, category, price, stock)
        VALUES (%s, %s, %s, %s, %s)
        ON CONFLICT (sku) DO UPDATE SET ...
    """, (sku, name, category, price, stock))
    imported += 1
```

The symptom:

```text
import crashes partway with a parse error -> no rows after the bad one are imported
```

## How you'll approach it

1. **Isolate each row.** Wrap the parse-and-insert for a single row in `try/except` so one bad row can't kill the loop.
2. **Skip and log.** On a bad row, record it (row number + reason) and continue, instead of raising.
3. **Keep good data flowing.** Valid rows before *and* after the bad one all import.
4. **Report.** At the end, surface how many rows imported and how many were skipped (and why) so the bad data is diagnosable.

## What you'll learn

- Why real-world data is always messy and importers must expect it
- Per-row error handling so one anomaly doesn't lose the whole load
- Logging skipped rows for diagnosis rather than silently dropping them
- Balancing strictness (don't load garbage) with resilience (don't lose good data)

## What it proves

You can build an importer that survives messy real-world data - skipping and reporting the bad rows instead of dying on the first one.

> Resume-ready: *Made a CSV importer resilient with per-row error handling, skipping and logging malformed rows while loading all valid data.*

## On the roadmap

Part of the [Data Engineer Roadmap](../../roadmaps/data.md) - **Stage 2: ETL Reliability** → Resilience to bad data.

---

**Build it for real.** This is ticket **DE-104** on HeyDevJob - the real crashing importer above, in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Make a CSV Importer Resilient to Bad Data on HeyDevJob →](https://heydevjob.com/data)
