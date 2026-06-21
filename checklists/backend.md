# Backend Engineer Job-Readiness Checklist

This is the concrete skill list that gets you hired as a backend engineer, ordered by the [Backend Engineer Roadmap](../roadmaps/backend.md) from junior to senior. These are not topics to read about - they are things you can do in production. Tick what you've actually built or fixed; for anything you can't, there's a live HeyDevJob ticket that makes you prove it.

---

## Stage 1 - HTTP & API Fundamentals

- [ ] Return the correct 2xx/4xx/5xx status code for create, update, delete, and error cases
- [ ] Use `201 Created` with a `Location` header for resource creation
- [ ] Design a consistent JSON response and error shape across all endpoints
- [ ] Know when to return `401` vs `403` vs `404` (including hiding existence with `404`)
- [ ] Implement offset pagination and explain why it degrades at scale
- [ ] Implement cursor (keyset) pagination backed by an index
- [ ] Version an API and evolve it without breaking existing clients

## Stage 2 - Databases & SQL

- [ ] Write joins, aggregates, and subqueries without reaching for an ORM
- [ ] Read an `EXPLAIN ANALYZE` plan and spot a sequential scan
- [ ] Add the right single-column and composite index for a given query
- [ ] Explain when an index hurts (write cost, low selectivity) and gets ignored
- [ ] Identify and fix an N+1 query with eager loading or a batch fetch
- [ ] Use a transaction to make multiple writes atomic
- [ ] Explain the SQL isolation levels and the anomaly each one prevents
- [ ] Design a normalized schema with the right foreign keys and constraints

## Stage 3 - Authentication & Authorization

- [ ] Implement password hashing with bcrypt/argon2 (never plaintext, never plain SHA)
- [ ] Issue a signed JWT on login with an expiry
- [ ] Verify the signature AND expiry on every protected request
- [ ] Pin the expected algorithm to block the `alg: none` bypass
- [ ] Explain stateless JWT vs server-side session tradeoffs (revocation, scale)
- [ ] Store tokens in `httpOnly` `Secure` `SameSite` cookies and add CSRF protection
- [ ] Enforce authorization (not just authentication) so users can't access others' data
- [ ] Implement a refresh-token flow with short-lived access tokens

## Stage 4 - Validation & Error Handling

- [ ] Validate and coerce all request input at the boundary
- [ ] Return clear `400` errors naming the field that failed
- [ ] Re-validate server-side regardless of client-side checks
- [ ] Use parameterized queries everywhere to prevent SQL injection
- [ ] Allowlist dynamic identifiers (sort columns, table names) that can't be parameterized
- [ ] Handle and log unexpected errors without leaking stack traces to clients

## Stage 5 - Caching & Performance

- [ ] Implement the cache-aside pattern with TTLs
- [ ] Invalidate or update the cache correctly on writes (no stale reads)
- [ ] Prevent a cache stampede with locking, jittered TTLs, or stale-while-revalidate
- [ ] Implement a token-bucket or sliding-window rate limiter
- [ ] Rate limit per-user / per-API-key and return `429` with `Retry-After`
- [ ] Measure latency before and after an optimization instead of guessing

## Stage 6 - Concurrency & Reliability

- [ ] Identify a check-then-act race condition under concurrent requests
- [ ] Fix it with a row lock, atomic conditional update, or unique constraint
- [ ] Choose between optimistic and pessimistic locking for the contention level
- [ ] Implement an `Idempotency-Key` header so retries don't double-process
- [ ] Make every state-changing write safe to retry
- [ ] Apply timeouts and retries with backoff to outbound calls

## Stage 7 - Async & Messaging

- [ ] Move slow work (email, reports, uploads) off the request thread to a queue
- [ ] Build a worker that consumes jobs with retries and backoff
- [ ] Configure a dead-letter queue for poison messages
- [ ] Reason about at-least-once delivery and make consumers idempotent

## Stage 8 - Observability

- [ ] Emit structured (JSON) logs with a correlation/request ID
- [ ] Propagate a request ID across services to trace one request end-to-end
- [ ] Instrument and expose the metrics that matter (latency, error rate, throughput)
- [ ] Add distributed tracing across service boundaries
- [ ] Define an alert that fires on a real symptom, not noise

## Stage 9 - Testing

- [ ] Write unit tests for business logic with test doubles
- [ ] Write integration tests against a real database
- [ ] Know what's worth testing and what isn't (the senior judgment call)
- [ ] Test the failure paths, not just the happy path

## Stage 10 - Senior: System Design & Scale

- [ ] Rename or change a live schema with zero downtime (expand/contract, batched backfill)
- [ ] Dual-write and migrate reads safely across an app rollout
- [ ] Decompose a system in a design interview (URL shortener, rate limiter, feed)
- [ ] Reason about storage choice, caching, and read/write ratios under load
- [ ] Keep data consistent across services with the saga pattern and compensating actions
- [ ] Route reads to replicas and handle replication lag / read-after-write
- [ ] Explain horizontal scaling, partitioning, and where consistency breaks down

## Can't tick it? Prove it.

Every unchecked box above is a skill you can go earn right now. The projects on the [Backend Engineer Roadmap](../roadmaps/backend.md) are live tickets on [HeyDevJob](https://heydevjob.com/backend) - real broken and unbuilt systems in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup, and every fix you ship lands on a portfolio hiring managers can click.

**Start your portfolio →** [heydevjob.com/backend](https://heydevjob.com/backend)
