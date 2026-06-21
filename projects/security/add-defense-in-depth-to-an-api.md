[Home](../../README.md) › [Security Projects](README.md) › **Add Defense-in-Depth to a Public API**

# Add Defense-in-Depth to a Public API

`Security` · `🟡 Mid` · `🛡️ Harden` · `Ticket SE-205`

**Skills** — rate limiting · API hardening · fault tolerance

> The endpoint is wide open: no rate limit, no size cap. One attacker can flood it with millions of requests or a single multi-megabyte payload and the server just... takes it. You're adding the two cheap defenses that stop most abuse cold.

> [!NOTE]
> **The scenario**
> The `/api/echo` endpoint has no rate limiting and no body-size cap, so it absorbs unlimited requests and arbitrarily large payloads. You add two layers: a per-IP rate limit (10 requests / 60s → `429`) and a request-size cap (reject bodies over 10KB → `413`), checking `Content-Length` before reading the body.

## 🧩 The code

The wide-open endpoint with the requirements (`app.py`):

```python
"""Public API endpoint - wide open. Add rate limit + body cap."""
from flask import Flask, request, jsonify
app = Flask(__name__)

# TODO: add a before_request hook that:
#   1. Reads Content-Length; if > 10 * 1024 -> return ("", 413)
#   2. Tracks recent request timestamps per IP (request.remote_addr).
#      If the same IP has made >= 10 requests in the last 60 seconds,
#      return ("", 429).
```

The symptom:

```text
# no throttle, no cap
$ for i in $(seq 1 100000); do curl -s /api/echo & done   # all absorbed
$ curl -X POST /api/echo --data @50mb.bin                 # server reads it all
```

## 🛠️ How you'll approach it

1. **Cap the body first.** In a `before_request` hook, read `Content-Length` and reject anything over 10KB with `413` - before reading the body, so a payload bomb never gets buffered.
2. **Rate-limit per IP.** Track recent request timestamps per `remote_addr` in a sliding 60s window; past 10, return `429`.
3. **Know where this really lives.** In production these run at the edge (Cloudflare / WAF), but implementing them teaches the mechanism.
4. **Verify.** The 11th request in a minute gets `429`; an oversized body gets `413`; normal traffic passes.

## 🎓 What you'll learn

- Layering cheap defenses that block most brute-force and payload-bomb abuse
- Sliding-window rate limiting keyed per client
- Checking `Content-Length` before buffering the body
- Where these belong in real architecture (edge vs app)

## 🏆 What it proves

You think in defense-in-depth - stacking simple controls that each cut off a category of abuse - which is the daily mindset of hardening a public surface.

> [!TIP]
> **Resume-ready** — *Hardened a public API with per-IP sliding-window rate limiting (429) and a request-size cap (413), blocking flood and payload-bomb abuse.*

## 🗺️ On the roadmap

Part of the [Security Engineer Roadmap](../../roadmaps/security.md) - **Stage 8: Hardening & Secure Configuration** → API defense-in-depth.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **SE-205** on HeyDevJob - the real wide-open API above, waiting in a cloud workspace you harden from your browser. Free on the junior tier, no card, no setup.
> [Add Defense-in-Depth to a Public API on HeyDevJob →](https://heydevjob.com/security)

**Explore Security** · [📍 Roadmap](../../roadmaps/security.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/security.md) · [✅ Checklist](../../checklists/security.md)

[◀ Prev: Move a Plaintext Secret to AWS Secrets Manager](move-a-secret-to-aws-secrets-manager.md) · [Next: Build a Security Scan Pipeline (SAST + SCA) ▶](build-a-security-scan-pipeline.md)
