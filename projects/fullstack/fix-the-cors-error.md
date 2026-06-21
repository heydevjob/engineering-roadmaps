# Fix the CORS Error Blocking Your API Calls

**Role:** Fullstack Engineer · **Level:** Junior · **Type:** Debug · **Skills:** CORS, browser security model, Flask, fetch

> The React app's fetch is blocked: "No 'Access-Control-Allow-Origin' header is present." It works in curl. It works in Postman. Only the browser refuses - because CORS is a browser policy, and the fix is a response header on the server, not a frontend change.

## The scenario

A React dashboard on one origin calls a Flask API on another, and the browser blocks it with a CORS error - the API never sends `Access-Control-Allow-Origin`. `curl` works fine (proving it's not a network problem). You add the CORS headers and handle the preflight `OPTIONS` request on the server.

## The code

The endpoint returning JSON with no CORS headers (`app.py`):

```python
@app.get("/api/stats")
def stats():
    # BUG: returns JSON with NO CORS headers
    return jsonify(STATS)
```

The symptom:

```text
browser console:
  Access to fetch at '.../api/stats' has been blocked by CORS policy:
  No 'Access-Control-Allow-Origin' header is present
# curl http://localhost:8000/api/stats  -> works fine (not a network issue)
```

## How you'll approach it

1. **Confirm it's CORS, not the network.** `curl` succeeds; only the browser blocks it - that's the same-origin policy at work.
2. **Add the response header.** Send `Access-Control-Allow-Origin` (the allowed origin) on the API's responses - this is a *server* change, not a frontend one.
3. **Handle preflight.** For non-simple requests the browser sends an `OPTIONS` preflight first; answer it with the allow headers/methods.
4. **Verify.** The React fetch now succeeds; the dashboard loads.

## What you'll learn

- What CORS actually is and why the browser enforces it (and tools don't)
- Why the fix is a response header on the server, not a frontend change
- Preflight `OPTIONS` and which headers satisfy it
- Scoping `Access-Control-Allow-Origin` instead of blanket `*`

## What it proves

You understand the browser security model well enough to fix the single most common fullstack confusion - and you fix it in the right place (the server).

> Resume-ready: *Resolved a cross-origin fetch failure by adding correct CORS response headers and preflight handling to the API.*

## On the roadmap

Part of the [Fullstack Engineer Roadmap](../../roadmaps/fullstack.md) - **Stage 3: Connecting to the Backend** → CORS & the browser security model.

---

**Build it for real.** This is ticket **FS-106** on HeyDevJob - the real CORS-blocked setup above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Fix the CORS Error on HeyDevJob →](https://heydevjob.com/fullstack)
