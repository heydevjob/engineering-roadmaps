[Home](../../README.md) › [Data Projects](README.md) › **Build an Exactly-Once Kafka Pipeline**

# Build an Exactly-Once Kafka Pipeline

`Data` · `🔴 Senior` · `📐 Design` · `Ticket DE-308`

**Skills** — Kafka · streaming · exactly-once · idempotency

> Kafka delivers at-least-once - retries and redelivery mean the same order event can arrive twice. If "process order" runs twice, you ship twice or charge twice. You're building the producer/consumer pair that processes each event exactly once.

> [!NOTE]
> **The scenario**
> You build a real-time order pipeline where the producer publishes order events (with duplicates, simulating retries) and the consumer processes each exactly once. The producer sends `order_id` 1..100 to the `orders` topic twice each (200 messages). The consumer reads from offset 0, tracks seen ids in a set, and writes only new ids to `processed.txt`.

## 🧩 The code

The dedup-in-consumer pattern you build:

```python
# consumer.py
consumer = KafkaConsumer(TOPIC, bootstrap_servers=BROKER,
    auto_offset_reset="earliest", consumer_timeout_ms=5000,
    value_deserializer=lambda b: json.loads(b.decode()))
seen = set()
with open("/workspace/processed.txt", "w") as f:
    for msg in consumer:
        oid = msg.value["order_id"]
        if oid in seen:
            continue            # duplicate -> skip
        seen.add(oid)
        f.write(f"{oid}\n")
```

The target:

```text
producer sends 200 messages (100 ids x 2)  ->  processed.txt has exactly 100 unique ids
```

## 🛠️ How you'll approach it

1. **Produce with duplicates.** Send each `order_id` twice to simulate at-least-once redelivery.
2. **Consume from the start.** Read from offset 0 with a timeout so the consumer drains the topic and stops.
3. **Dedup on a key.** Track seen `order_id`s in a set; skip any you've already processed - so the side effect happens once.
4. **Verify.** Exactly 100 unique ids land in `processed.txt`, even though 200 messages arrived.

## 🎓 What you'll learn

- Why Kafka's default delivery is at-least-once, not exactly-once
- That exactly-once *effects* are the consumer's job via dedup
- The seen-set dedup pattern keyed on a business id
- Why production systems persist the seen-state (Redis/Postgres) so it survives restarts

## 🏆 What it proves

You can build correct streaming under at-least-once delivery - deduping so each event has exactly one effect - the bread-and-butter pattern of real-time data engineering.

> [!TIP]
> **Resume-ready** — *Built an exactly-once Kafka pipeline - a producer/consumer pair with consumer-side deduplication - so duplicate-delivered events are each processed once.*

## 🗺️ On the roadmap

Part of the [Data Engineer Roadmap](../../roadmaps/data.md) - **Stage 8: Senior - Modeling & Streaming** → Exactly-once streaming.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **DE-308** on HeyDevJob - the real streaming task above, in a cloud workspace you build from your browser. Each fix lands on a portfolio you can show.
> [Build an Exactly-Once Kafka Pipeline on HeyDevJob →](https://heydevjob.com/data)

**Explore Data** · [📍 Roadmap](../../roadmaps/data.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/data.md) · [✅ Checklist](../../checklists/data.md)

[◀ Prev: Backfill a Column Safely](backfill-a-column-safely.md)
