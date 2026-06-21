[Home](../README.md) › **Backend Interview**

# Backend Engineer Interview Questions

`Interview prep` · `Click a question to reveal the answer`

The questions below track the [Backend Engineer Roadmap](../roadmaps/backend.md) from junior to senior. They are the ones that actually decide a backend interview: API contracts, SQL, auth, concurrency, and the system-design round. Each has a tight, correct answer. The fastest way to lock these in is to ship the fix in a real system, so most link to a live HeyDevJob ticket you can do for free.

## 🌐 HTTP & API Fundamentals

<details>
<summary><b>When do you return 401 vs 403 vs 404?</b></summary>

<br>

`401 Unauthorized` means "I don't know who you are" - missing or invalid credentials. `403 Forbidden` means "I know who you are and you're not allowed." `404 Not Found` means the resource doesn't exist. A common security move is returning `404` instead of `403` so you don't reveal that a resource exists to someone not allowed to see it.

</details>

<details>
<summary><b>What status code do you return for a successful POST that creates a resource, and what should the response include?</b></summary>

<br>

`201 Created`, with a `Location` header pointing at the new resource and the created entity in the body. Use `200 OK` for a successful action that doesn't create a resource, and `202 Accepted` when you've queued the work but not finished it. Returning `200` for everything is the classic junior tell.

🛠️ *Practice it live:* [Return the Correct HTTP Status Code](https://heydevjob.com/backend)

</details>

<details>
<summary><b>Why is cursor-based pagination better than offset pagination at scale?</b></summary>

<br>

`OFFSET 100000` forces the database to scan and discard 100,000 rows on every page, so deep pages get linearly slower, and rows shifting under you cause skipped or duplicated results. Cursor (keyset) pagination uses a `WHERE created_at < $last_seen` clause backed by an index, so every page is the same fast seek and the result is stable under inserts. The tradeoff is you lose random "jump to page 500" access.

🛠️ *Practice it live:* [Switch to Cursor-Based Pagination](https://heydevjob.com/backend)

</details>

<details>
<summary><b>What makes an API idempotent, and which HTTP methods should be?</b></summary>

<br>

An idempotent operation produces the same result whether it runs once or five times. `GET`, `PUT`, and `DELETE` should be idempotent by definition; `POST` is not, which is why retried POSTs are dangerous. You make a POST safe to retry with an `Idempotency-Key` header that dedupes the request server-side.

</details>

## 🗄️ Databases & SQL

<details>
<summary><b>How does an index speed up a query, and when does one hurt?</b></summary>

<br>

An index is a sorted structure (usually a B-tree) that lets the database seek to matching rows instead of scanning the whole table, turning O(n) into O(log n) lookups. It hurts on writes - every insert, update, and delete must also update the index - and a low-selectivity index (e.g. on a boolean) the planner will just ignore. You index columns you filter, join, and sort on, not everything.

</details>

<details>
<summary><b>How do you diagnose and fix a slow query?</b></summary>

<br>

Run `EXPLAIN ANALYZE` and read the plan: a `Seq Scan` on a large table where you expected a filter is the smoking gun. Match the index to the query's `WHERE` and `ORDER BY` - a composite index on `(status, created_at)` covers both a filter on status and a sort on created_at. Re-run the plan to confirm it switched to an Index Scan; prove the win from timing, not by guessing.

🛠️ *Practice it live:* [Index a Slow Postgres Query](https://heydevjob.com/backend)

</details>

<details>
<summary><b>What is the N+1 query problem and how do you fix it?</b></summary>

<br>

You run one query to fetch N parent rows, then one more query per row to fetch each child - 1 + N queries where 2 would do. It's the most common hidden performance killer, usually hidden behind an ORM lazy-load inside a loop. Fix it by eager-loading (a `JOIN` or `IN (...)` batch) so a page that fired 200 queries fires 2.

🛠️ *Practice it live:* [Fix the N+1 Query](https://heydevjob.com/backend)

</details>

<details>
<summary><b>Explain the SQL isolation levels and what anomaly each prevents.</b></summary>

<br>

`READ COMMITTED` (Postgres default) prevents dirty reads but allows non-repeatable reads. `REPEATABLE READ` stops a row changing under you mid-transaction. `SERIALIZABLE` makes concurrent transactions behave as if they ran one at a time, preventing phantom reads and write skew, at the cost of more aborts you must retry. You raise the level only where correctness needs it.

</details>

<details>
<summary><b>When would you use a transaction, and what does ACID guarantee?</b></summary>

<br>

Wrap multiple writes that must succeed or fail together - a transfer that debits one account and credits another. ACID guarantees atomicity (all or nothing), consistency (constraints hold), isolation (concurrent transactions don't corrupt each other), and durability (committed data survives a crash). The classic bug is doing the debit and credit in two separate transactions, so a crash between them loses money.

</details>

## 🔐 Authentication & Authorization

<details>
<summary><b>What's inside a JWT, and what does the signature actually protect?</b></summary>

<br>

A JWT has three base64url parts: header (algorithm), payload (claims like `sub` and `exp`), and signature. The signature proves the token wasn't tampered with and was issued by someone holding the secret - it does NOT encrypt the payload, so never put secrets in the claims. The load-bearing step is verifying that signature and the expiry on every single request, not just issuing the token.

🛠️ *Practice it live:* [Add JWT Authentication to an API](https://heydevjob.com/backend)

</details>

<details>
<summary><b>What is the JWT `alg: none` attack and how do you stop it?</b></summary>

<br>

An attacker sets the token header to `"alg": "none"` and strips the signature; a naive verifier that trusts the header's algorithm accepts the unsigned token as valid, forging any identity. You stop it by pinning the expected algorithm server-side (`algorithms=["HS256"]`) and never letting the token's own header pick the verification method.

🛠️ *Practice it live:* [Patch the JWT None-Algorithm Bypass](https://heydevjob.com/security)

</details>

<details>
<summary><b>Stateless JWTs vs server-side sessions - what's the tradeoff?</b></summary>

<br>

JWTs are stateless and scale horizontally with no shared session store, but you can't revoke one before it expires without adding a denylist. Server-side sessions are instantly revocable and easy to invalidate, but every request hits the session store. A common middle ground is short-lived access tokens plus a revocable refresh token.

</details>

<details>
<summary><b>Where should you store a JWT in a browser, and why not localStorage?</b></summary>

<br>

In an `httpOnly`, `Secure`, `SameSite` cookie. `localStorage` is readable by any JavaScript on the page, so a single XSS bug exfiltrates the token; an `httpOnly` cookie is invisible to JS. The cost is you now need CSRF protection, which `SameSite=Lax/Strict` plus a CSRF token covers.

🛠️ *Practice it live:* [Move JWT to httpOnly Cookies](https://heydevjob.com/security)

</details>

## 🛡️ Validation & Error Handling

<details>
<summary><b>Why validate input at the boundary, and what does "fail loudly" mean?</b></summary>

<br>

Unvalidated input is where injection, crashes, and corrupt data come from, so you reject bad requests at the edge before they touch business logic or the database. "Fail loudly" means returning a clear `400` with which field was wrong rather than silently coercing or half-processing it. Validation is the cheapest security you'll ever write.

🛠️ *Practice it live:* [Add Server-Side Form Validation](https://heydevjob.com/backend)

</details>

<details>
<summary><b>Why is client-side validation never enough?</b></summary>

<br>

Client-side validation is a UX nicety - anyone can bypass it with curl or by editing the request, so the server must re-validate everything. The rule is "never trust the client": treat every incoming request as potentially hostile and validate it server-side regardless of what the frontend already checked.

</details>

<details>
<summary><b>How do you prevent SQL injection?</b></summary>

<br>

Use parameterized queries (bind parameters) so user input is always data, never concatenated into the SQL string. Never build queries with string formatting, even for "safe-looking" values like sort columns - use an allowlist for those. An ORM helps but doesn't make you immune if you drop to raw SQL.

🛠️ *Practice it live:* [Close a SQL Injection in a Search Endpoint](https://heydevjob.com/security)

</details>

## ⚡ Caching & Performance

<details>
<summary><b>Explain the cache-aside pattern and its hardest problem.</b></summary>

<br>

On a read, check the cache first; on a miss, read the database, populate the cache with a TTL, and return. The hardest problem is invalidation - on a write you must evict or update the cached entry, or you serve stale data until the TTL expires. A cache you can't reliably invalidate is a correctness bug waiting to happen.

🛠️ *Practice it live:* [Cache a Hot Read Path With Redis](https://heydevjob.com/backend)

</details>

<details>
<summary><b>What is a cache stampede and how do you prevent it?</b></summary>

<br>

When a hot key expires, many concurrent requests all miss at once and hammer the database with the same expensive query (the "thundering herd"). You prevent it with a short lock so only one request recomputes while others wait, jittered TTLs so keys don't all expire together, or serving slightly stale data while one worker refreshes.

</details>

<details>
<summary><b>How does a token-bucket rate limiter work, and why per-user?</b></summary>

<br>

A bucket holds tokens that refill at a fixed rate; each request consumes one, and an empty bucket returns `429 Too Many Requests`. It allows short bursts (up to bucket size) while bounding the long-run rate, which fits real traffic better than a hard fixed window. You key it per-user or per-API-key so one client (or one bug) can't exhaust capacity for everyone.

🛠️ *Practice it live:* [Add a Rate Limiter to an API](https://heydevjob.com/backend)

</details>

## 🔁 Concurrency & Reliability

<details>
<summary><b>What is a race condition, and how do you fix a double-charge?</b></summary>

<br>

Two concurrent requests read the same state, both decide it's safe to act, and both act - double-charging a customer or overselling stock. The "check-then-act" gap is the bug. Fix it by making the operation atomic: a database row lock (`SELECT ... FOR UPDATE`), an atomic conditional update (`UPDATE ... WHERE balance >= amount`), or a unique constraint that rejects the duplicate.

🛠️ *Practice it live:* [Stop a Double-Charge Race Condition](https://heydevjob.com/backend)

</details>

<details>
<summary><b>Optimistic vs pessimistic locking - when do you use each?</b></summary>

<br>

Pessimistic locking takes a lock up front (`SELECT ... FOR UPDATE`) so no one else can touch the row; use it under high contention where conflicts are likely. Optimistic locking adds a version column and only fails at write time if the version changed, retrying on conflict; use it under low contention where locks would be wasted overhead. Optimistic scales better when collisions are rare.

</details>

<details>
<summary><b>How do you make a payment endpoint safe to retry?</b></summary>

<br>

Require an `Idempotency-Key` header; on the first request you store the key with its result, and any retry with the same key returns the stored result instead of charging again. The key plus a unique constraint guarantees at-most-once processing even when the network retries a request whose response was lost. Every write that moves money or state needs this.

🛠️ *Practice it live:* [Honor an Idempotency-Key Header on POST](https://heydevjob.com/backend)

</details>

## 📨 Async & Messaging

<details>
<summary><b>When do you move work off the request thread, and how?</b></summary>

<br>

Anything slow or failure-prone that the user doesn't need to wait for - sending email, generating a report, processing an upload - goes to a background queue so the request returns fast. A producer enqueues a job, a pool of workers consumes it, and failures retry with backoff. Doing this work inline blocks the user and topples the service under load.

</details>

<details>
<summary><b>What is a dead-letter queue and why do you need one?</b></summary>

<br>

A job that keeps failing after its retry budget gets moved to a dead-letter queue instead of looping forever or silently vanishing. It quarantines poison messages so one bad job doesn't block the pipeline, and gives you a place to inspect, fix, and replay. Without it, a malformed message either jams the queue or disappears with no trace.

</details>

## 📊 Observability

<details>
<summary><b>What makes a log line useful in production?</b></summary>

<br>

Structured (JSON) logs with consistent fields - a correlation/request ID, user ID, route, latency, and status - so you can filter and aggregate instead of grepping prose. The request ID is the load-bearing part: it lets you trace one request across every service it touched. When a request fails at 3am, good logs are a five-minute fix versus a five-hour guess.

</details>

<details>
<summary><b>What are the three pillars of observability?</b></summary>

<br>

Logs (discrete events, "what happened"), metrics (aggregated numbers over time, "how much/how fast" - your dashboards and alerts), and traces (one request's path across services with timing per hop). Metrics tell you something is wrong, traces tell you where, logs tell you why. You need all three to operate a distributed system.

</details>

## 🏛️ Senior: System Design & Scale

<details>
<summary><b>How do you rename a column in a live Postgres table with zero downtime?</b></summary>

<br>

You can't take production down, so you expand-then-contract: add the new column, dual-write to both old and new, backfill existing rows in batches, switch reads to the new column, then drop the old one in a later deploy. Each step is backward-compatible so old and new app versions both work during the rollout. A bare `ALTER TABLE RENAME` would break every running instance still reading the old name.

🛠️ *Practice it live:* [Rename a Postgres Column With Zero Downtime](https://heydevjob.com/backend)

</details>

<details>
<summary><b>Walk me through designing a URL shortener.</b></summary>

<br>

Hash or base62-encode an incrementing ID into a short slug, store the slug-to-URL mapping in a key-value store, and `301`/`302` redirect on lookup. Reads vastly outnumber writes, so cache hot slugs and consider read replicas; pick the storage for the mapping and a strategy to avoid collisions. The interviewer wants your reasoning about scale, storage choice, and tradeoffs, not a perfect answer.

🛠️ *Practice it live:* [Design a URL Shortener](https://heydevjob.com/backend)

</details>

<details>
<summary><b>How do you keep data consistent across services without a distributed transaction?</b></summary>

<br>

You can't hold one ACID transaction across multiple services, so you use the saga pattern: a sequence of local transactions where each step, if a later step fails, triggers a compensating action that undoes the prior work (refund the charge, release the reservation). It trades atomicity for eventual consistency and forces you to design every "undo." This is the senior gate for distributed systems.

🛠️ *Practice it live:* [Build an Order Saga With Compensating Actions](https://heydevjob.com/backend)

</details>

<details>
<summary><b>When would you reach for a read replica, and what's the catch?</b></summary>

<br>

When read traffic dominates and is overloading the primary, you route reads to replicas and keep writes on the primary. The catch is replication lag: a user who just wrote and immediately reads may hit a replica that hasn't caught up and see stale data. You handle it by routing read-after-write to the primary or pinning the session to the primary briefly after a write.

</details>

> [!IMPORTANT]
> **Build it for real**
> Reading the answers gets you through the screen. Shipping the fix gets you hired. Every question above maps to a live ticket on [HeyDevJob](https://heydevjob.com/backend) - a real broken (or unbuilt) system in a cloud workspace you fix from your browser, no card and no setup on the free junior tier. Each one you ship lands on a portfolio hiring managers can open and click.
>
> **Start your portfolio →** [heydevjob.com/backend](https://heydevjob.com/backend)

---

**Explore Backend** · [📍 Roadmap](../roadmaps/backend.md) · [🛠️ Projects](../projects/backend/README.md) · [💬 Interview](backend.md) · [✅ Checklist](../checklists/backend.md)
