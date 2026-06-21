[Home](../../README.md) › **Backend Projects**

# Backend Engineer Projects

`13 projects` · `Build · Debug · Optimize · Harden · Design` · `Junior → Senior`

Real systems to build, debug, optimize, and harden - each one proves a specific backend skill and produces something you can put on a portfolio. Backend is a building discipline, so most of these have you *ship* something, not just patch it. Every project is grounded in a real [HeyDevJob](https://heydevjob.com/backend) ticket: the actual code, the real symptom, and a live cloud workspace where you build or fix it.

> [!NOTE]
> **Project types**
> 🔨 **Build** - create it from nothing · 🔍 **Debug** - find and fix what's wrong · ⚡ **Optimize** - make it fast and scalable · 🛡️ **Harden** - make it secure and reliable · 📐 **Design** - architect a system

Pairs with the [Backend Engineer Roadmap](../../roadmaps/backend.md): the roadmap tells you what to learn, these are where you prove it.

## 🟢 Junior projects

Start here. Build correct, secure, fast endpoints backed by a database.

| Project | Ticket | Type | Skills |
|---------|--------|------|--------|
| [Return the Correct HTTP Status Code](return-the-correct-http-status-code.md) | `BE-101` | 🔍 Debug | REST, HTTP status codes, API contracts |
| [Add JWT Authentication to an API](add-jwt-authentication.md) | `BE-112` | 🔨 Build | JWT, authentication, FastAPI |
| [Add Server-Side Form Validation](add-server-side-validation.md) | `BE-103` | 🛡️ Harden | validation, error handling, security |
| [Switch to Cursor-Based Pagination](switch-to-cursor-pagination.md) | `BE-116` | 🔨 Build | pagination, SQL, API design |
| [Cache a Hot Read Path With Redis](cache-a-hot-read-path-with-redis.md) | `BE-114` | ⚡ Optimize | caching, Redis, invalidation |
| [Index a Slow Postgres Query](index-a-slow-postgres-query.md) | `BE-115` | ⚡ Optimize | SQL, indexing, query planning |
| [Honor an Idempotency-Key Header on POST](honor-an-idempotency-key.md) | `BE-123` | 🛡️ Harden | idempotency, HTTP, fault tolerance |
| [Add a Rate Limiter to an API](add-a-rate-limiter.md) | `BE-113` | 🛡️ Harden | rate limiting, Redis, API design |

## 🟡 Mid projects

Multi-step work - make services fast and correct under concurrency.

| Project | Ticket | Type | Skills |
|---------|--------|------|--------|
| [Fix the N+1 Query](fix-the-n-plus-1-query.md) | `BE-203` | 🔍 Debug / ⚡ Optimize | SQL, query optimization, ORMs |
| [Stop a Double-Charge Race Condition](stop-a-double-charge-race-condition.md) | `BE-204` | 🔍 Debug | concurrency, transactions, PostgreSQL |

## 🔴 Senior projects

Architecture and scale - design systems that stay fast and consistent.

| Project | Ticket | Type | Skills |
|---------|--------|------|--------|
| [Rename a Postgres Column With Zero Downtime](rename-a-postgres-column-zero-downtime.md) | `BE-307` | 🔨 Build / 📐 Design | migrations, zero-downtime, expand-contract |
| [Design a URL Shortener (System Design)](design-a-url-shortener.md) | `BE-310` | 📐 Design | system design, storage, encoding |
| [Build an Order Saga With Compensating Actions](build-an-order-saga.md) | `BE-309` | 📐 Design | distributed systems, sagas, compensation |

> [!IMPORTANT]
> **Build it for real**
> Reading about idempotency isn't the same as watching a retry double-charge a customer and then fixing it so it can't. Every project here is a live ticket on [HeyDevJob](https://heydevjob.com/backend) - a real system in a cloud workspace, no setup. The junior tier is free, no card. Ship it, and it lands on a portfolio hiring managers can open and click.
>
> **Start the Backend Engineer projects on HeyDevJob →** [heydevjob.com/backend](https://heydevjob.com/backend)

---

**Explore Backend** · [📍 Roadmap](../../roadmaps/backend.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/backend.md) · [✅ Checklist](../../checklists/backend.md)
