# Resolve Postgres Pool Exhaustion Under Load

**Role:** DevOps Engineer · **Level:** Mid · **Type:** Debug · **Skills:** PostgreSQL, connection pooling, SQLAlchemy, fault tolerance

> One user hits `/reports` and it's fine. Twenty users hit it at once and every single request hangs forever. The pool is too small *and* it's leaking the few connections it has.

## The scenario

The `/reports` endpoint works for a single user but collapses under modest concurrency - 20 simultaneous requests and everything hangs. Two problems stack: the connection pool is sized at only 5, and the endpoint leaks connections by opening a raw `psycopg2` connection outside the pool and closing it in a path that doesn't run when a query raises.

## The code

The undersized pool and the leak (`app.py`):

```python
engine = create_engine(
    "postgresql://postgres@localhost/app",
    pool_size=5,
    max_overflow=0,
)

@app.route("/reports/<int:n>")
def report(n: int):
    # BUG: opens its own psycopg2 conn (not from the pool).
    # The conn.close() lives outside any try/finally, so if the
    # query raises (or anything in between), it leaks.
    conn = psycopg2.connect("dbname=app user=postgres host=/var/run/postgresql")
    cur = conn.cursor()
    cur.execute("SELECT pg_sleep(0.3), %s::text", (str(n),))
    row = cur.fetchone()
    conn.close()
    return jsonify({"n": n, "value": row[1]})
```

The symptom:

```text
$ seq 1 20 | xargs -P 20 -I {} curl -s --max-time 10 http://localhost:8000/reports/{}
# every request hangs; Postgres connection count climbs to the cap and stays there
```

## How you'll approach it

1. **Reproduce it under load.** A single request is fine; the parallel `xargs` burst makes it hang - the signal that this is a pool/concurrency problem.
2. **Find the leak.** The endpoint opens its own connection and only closes it on the happy path. Any error leaves the connection orphaned and never returned.
3. **Fix both issues.** Use the pooled engine instead of a raw connection, ensure the connection is always released (context manager / `try`/`finally`), and size the pool for real concurrency.
4. **Verify under the same burst.** Re-run the 20-way load - requests complete and connections return to the pool.

## What you'll learn

- Reproducing a concurrency bug deterministically instead of "it's slow sometimes"
- Why a connection closed outside `try`/`finally` leaks on any error
- Using the pool correctly rather than opening raw connections that bypass it
- Sizing a pool against real load and the role of a pooler like PgBouncer

## What it proves

You can resolve connection-pool exhaustion - find the leak, fix the lifecycle, and size for load - which is a classic production page and a strong mid-level signal.

> Resume-ready: *Diagnosed and fixed PostgreSQL pool exhaustion under load by closing a connection leak and right-sizing the pool, restoring the endpoint under concurrency.*

## On the roadmap

Part of the [DevOps Engineer Roadmap](../../roadmaps/devops.md) - **Stage 4: Databases in Production** → Postgres operations.

---

**Build it for real.** This is ticket **DO-223** on HeyDevJob - the real leaking endpoint above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Resolve Postgres Pool Exhaustion on HeyDevJob →](https://heydevjob.com/devops)
