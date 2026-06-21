# Design a URL Shortener (System Design)

**Role:** Backend Engineer · **Level:** Senior · **Type:** Design · **Skills:** system design, storage, encoding

> "Design a URL shortener" is the classic system-design interview. Here you don't whiteboard it - you build the core, pick a short-code scheme, and defend the trade-off.

## The scenario

Build a URL shortener with three endpoints: `POST /shorten` to create a short code, `GET /<code>` to redirect (302) and count the click, and `GET /<code>/stats` to return link stats. You choose the code-generation scheme (base62 of the row id, a hash, or random) and implement dedup so the same URL always returns the same code.

## The code

You implement the stubbed endpoints (`shortener.py`):

```python
@app.post("/shorten")
def shorten():
    # TODO
    abort(501)

@app.get("/<code>")
def follow(code):
    # TODO - 302 redirect + clicks++
    abort(501)

@app.get("/<code>/stats")
def stats(code):
    # TODO
    abort(501)
```

The symptom:

```text
$ curl -X POST /shorten -d '{"url":"https://example.com"}'
501 Not Implemented   # nothing built yet
```

## How you'll approach it

1. **Pick a code scheme and justify it.** Base62 of the row id is short, collision-free, and sequential; hashing hides ordering but can collide; random needs a uniqueness check. Choose and be ready to defend it.
2. **`POST /shorten`.** Store the URL, generate the code, return it. Dedup: the same URL submitted twice returns the same code (look it up before inserting).
3. **`GET /<code>`.** Resolve the code, increment the click counter, `302` redirect to the long URL (`404` if unknown).
4. **`GET /<code>/stats`.** Return the URL and click count. Note the workload is read-heavy (redirects vastly outnumber creates) - which is where caching would go.

## What you'll learn

- Short-code schemes and their collision/scaling/ordering trade-offs
- Designing for a read-heavy workload (redirects >> creates)
- Dedup so the same URL maps to one code
- Where caching, custom aliases, and analytics fit - and where they don't belong yet

## What it proves

You can take the canonical system-design prompt from idea to working core and defend every decision - exactly what a senior interview is testing.

> Resume-ready: *Designed and built a URL-shortening service with base62 code generation, dedup, click tracking, and a read-optimized access pattern.*

## On the roadmap

Part of the [Backend Engineer Roadmap](../../roadmaps/backend.md) - **Stage 10: Senior - System Design & Scale** → Designing for scale.

---

**Build it for real.** This is ticket **BE-310** on HeyDevJob - the real stubbed service above, waiting in a cloud workspace you build from your browser. Each fix lands on a portfolio you can show. [Design a URL Shortener on HeyDevJob →](https://heydevjob.com/backend)
