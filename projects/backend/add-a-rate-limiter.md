# Add a Rate Limiter to an API

**Role:** Backend Engineer · **Level:** Junior · **Type:** Harden · **Skills:** rate limiting, Redis, API design

> One client just burst 1,000 requests a second at the search endpoint. There's no limit, so they can exhaust the database, the downstream calls, and the budget for everyone. You're capping it.

## The scenario

`/api/search` is open with no throttling, and a client was observed bursting 1,000 req/s. You add per-client rate limiting - 5 requests per 60-second window - returning `429 Too Many Requests` with a `Retry-After` header when a caller exceeds it. The client is identified by an `X-Client-Id` header, falling back to the request IP.

## The code

The unlimited endpoint with the requirements (`main.py`):

```python
@app.get("/api/search")
def search(q: str, request: Request):
    """
    TODO: Add rate limiting (5 req / 60s per client).

    Client identifier:
      - prefer the X-Client-Id header
      - fall back to request.client.host

    On limit exceeded:
      - raise HTTPException(status_code=429,
            detail={...}, headers={"Retry-After": "<seconds>"})
    """
    matches = [item for item in INDEX if q.lower() in item["title"].lower()]
    return {"query": q, "results": matches, "count": len(matches)}
```

The symptom:

```text
$ for i in $(seq 1 1000); do curl -s /api/search?q=x & done
# all 1000 succeed - no throttling, no 429
```

## How you'll approach it

1. **Identify the client.** Prefer `X-Client-Id`, fall back to the IP - that's the key you count against.
2. **Count in a window.** Track requests per client per 60s (a Redis counter / token bucket works across instances, unlike an in-process dict).
3. **Reject over the limit.** Past 5 in the window, return `429` with `Retry-After` telling the client when to come back.
4. **Verify.** The 6th request in a minute gets `429`; after the window resets, requests succeed again.

## What you'll learn

- Rate-limiting algorithms: fixed window, sliding window, token bucket
- Counting per-client in a shared store (Redis) so it works across instances
- The correct response: `429`, `Retry-After`, and rate-limit headers
- Choosing a limit that stops abuse without blocking legitimate bursts

## What it proves

You can protect a service from being overwhelmed by a single client - basic survival for any public API, and a system-design interview staple.

> Resume-ready: *Added per-client rate limiting (5 req/60s) with 429 + Retry-After responses, protecting an endpoint from request-flood abuse.*

## On the roadmap

Part of the [Backend Engineer Roadmap](../../roadmaps/backend.md) - **Stage 5: Caching & Performance** → Rate limiting.

---

**Build it for real.** This is ticket **BE-113** on HeyDevJob - the real unthrottled endpoint above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Add a Rate Limiter to an API on HeyDevJob →](https://heydevjob.com/backend)
