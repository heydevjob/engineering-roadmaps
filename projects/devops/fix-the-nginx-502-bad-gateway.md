[Home](../../README.md) › [DevOps Projects](README.md) › **Fix the Nginx 502 Bad Gateway**

# Fix the Nginx 502 Bad Gateway

`DevOps` · `🟢 Junior` · `🔍 Debug` · `Ticket DO-101`

**Skills** — nginx · reverse proxy · Linux · log reading

> The site is throwing `502 Bad Gateway`. The app is up. nginx just can't reach it - and the answer is sitting in the error log, if you read it before you start guessing.

> [!NOTE]
> **The scenario**
> After a deployment, users start getting `502 Bad Gateway`. The backend application is confirmed running, but nginx - sitting in front of it as a reverse proxy - can't connect to it. Nothing has crashed; the proxy and the upstream simply disagree about *where* the app is.

## 🧩 The broken config

This is the nginx config as you find it (`/etc/nginx/nginx.conf`):

```nginx
http {
    upstream backend {
        server 127.0.0.1:8080;
    }

    server {
        listen 80;
        location / {
            proxy_pass http://backend;
            proxy_set_header Host $host;
        }
    }
}
```

And the symptom:

```text
$ curl http://localhost/
502 Bad Gateway

$ cat /var/log/nginx/error.log
connect() failed (111: Connection refused) while connecting to upstream,
upstream: "http://127.0.0.1:8080/"
```

The app is alive - just not on `8080`.

## 🛠️ How you'll approach it

1. **Confirm the symptom and read the log first.** `curl http://localhost/`, then `/var/log/nginx/error.log`. The `connect() failed ... upstream: 8080` line is the whole case - it names the exact address nginx is failing to reach.
2. **Find where the app actually listens.** `ss -tlnp` (or the app's own config) shows the real port. It isn't `8080`.
3. **Reconcile the two.** Point the nginx `upstream` at the port the app really uses.
4. **Validate and reload with no dropped traffic.** `nginx -t` to check the config, then `nginx -s reload`.

The fix is one line. The skill being tested is reading the error log first instead of guessing - 90% of 502s tell you exactly which upstream failed and why.

## 🎓 What you'll learn

- How a reverse proxy routes a request and where a 502 actually originates
- Reading nginx `upstream` / `proxy_pass` blocks and matching them to reality
- Isolating the layer: proving the backend is healthy independently before touching nginx
- Reloading nginx safely (`nginx -t` then `nginx -s reload`)

## 🏆 What it proves

You can debug the most common production web-tier failure - a proxy that can't reach its backend - by reading the evidence, and tell an app problem from an infra problem.

> [!TIP]
> **Resume-ready** — *Diagnosed an nginx 502 to an upstream port mismatch via the error log, corrected the reverse-proxy config, and restored traffic with a zero-downtime reload.*

## 🗺️ On the roadmap

Part of the [DevOps Engineer Roadmap](../../roadmaps/devops.md) - **Stage 3: Web Servers & Load Balancing** → nginx as reverse proxy.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **DO-101** on HeyDevJob - the real broken proxy above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup.
> [Fix the Nginx 502 Bad Gateway on HeyDevJob →](https://heydevjob.com/projects/nginx-502-bad-gateway-portfolio-project)

**Explore DevOps** · [📍 Roadmap](../../roadmaps/devops.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/devops.md) · [✅ Checklist](../../checklists/devops.md)

[Next: Recover a Crashed Linux Service ▶](recover-a-crashed-linux-service.md)
