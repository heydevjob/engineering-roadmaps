# Block an SSRF in a URL Fetcher

**Role:** Security Engineer · **Level:** Mid · **Type:** Harden · **Skills:** SSRF, network security, cloud metadata

> A link-preview endpoint fetches any URL you give it - including `http://127.0.0.1:8000/` and `http://169.254.169.254/`. The server becomes your proxy into its own internal network and the cloud metadata endpoint that hands out credentials. You're slamming that door.

## The scenario

A `/fetch` endpoint makes a server-side request to any URL the user supplies, with no restriction - Server-Side Request Forgery. An attacker points it at internal services or the cloud metadata endpoint and the server fetches them on their behalf. You confirm it (the server can reach its own loopback), then add validation that blocks internal destinations.

## The code

The unrestricted fetch (`app.py`):

```python
url = request.args.get("url", "")
# VULNERABLE: fetches whatever the user asked for, internal or not.
resp = requests.get(url, timeout=3)
return jsonify({"url": url, "status": resp.status_code, "length": len(resp.content)})
```

The exploit:

```text
# make the server fetch its own internal service:
$ curl "http://localhost:8000/fetch?url=http://127.0.0.1:8000/health"
{"url": "http://127.0.0.1:8000/health", "status": 200, "length": 16}
#  the server reached a loopback address an external user never could
```

## How you'll approach it

1. **Prove the SSRF.** Point `/fetch` at `http://127.0.0.1:8000/health` - the server fetches itself and returns `200`. It can reach internal addresses you can't.
2. **Know the prize.** `http://169.254.169.254/` is the cloud metadata endpoint - on many hosts it hands out credentials. SSRF is how attackers reach it.
3. **Validate the target.** Parse the URL, require http/https, and block the host when it's loopback, a private range (`10.x`, `192.168.x`, `172.16-31.x`), or link-local (`169.254.x`) - return `403` before any request is made.
4. **Verify.** Loopback, `localhost`, metadata, and private IPs now `403`; a genuine public URL still passes the validator.

## What you'll learn

- How SSRF turns a server into a proxy into the internal network
- The cloud-metadata attack and why it's so dangerous on cloud hosts
- Validating the destination - scheme plus loopback/private/link-local blocking - with Python's `ipaddress`
- Why naive string blocklists fail (encodings, DNS rebinding, redirects)

## What it proves

You understand one of the most impactful modern vulnerability classes - the one behind major cloud breaches - and can harden a request-making feature against it.

> Resume-ready: *Hardened a URL-fetching endpoint against SSRF by validating the target and blocking loopback, private, and link-local addresses, closing an internal-network and cloud-credential path.*

## On the roadmap

Part of the [Security Engineer Roadmap](../../roadmaps/security.md) - **Stage 2: Injection** → Server-side request forgery (SSRF).

---

**Build it for real.** This is ticket **SE-217** on HeyDevJob - the real SSRF-prone fetcher above, in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Block an SSRF on HeyDevJob →](https://heydevjob.com/security)
