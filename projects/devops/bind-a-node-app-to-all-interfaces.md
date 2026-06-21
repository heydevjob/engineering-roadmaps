[Home](../../README.md) › [DevOps Projects](README.md) › **Bind a Node App to 0.0.0.0**

# Bind a Node App to 0.0.0.0 (All Interfaces)

`DevOps` · `🟢 Junior` · `🔍 Debug` · `Ticket DO-104`

**Skills** — networking · Node.js · nginx · reverse proxy

> 502 Bad Gateway through nginx. The app starts fine, no errors. The catch: it's listening on `127.0.0.1`, so it answers itself and nobody else - including nginx.

> [!NOTE]
> **The scenario**
> The site returns `502 Bad Gateway` through nginx. The Node.js backend was just deployed and starts cleanly. nginx can't connect to the upstream even though the app is running, and its error log says "connection refused." The app is bound to loopback only, so nothing on another interface - like nginx - can reach it.

## 🧩 The code

The app binds to loopback (`server.js`):

```javascript
server.listen(PORT, '127.0.0.1', () => {
    console.log(`Server running on port ${PORT}`);
});
```

The symptom:

```text
$ curl http://localhost:80/
502 Bad Gateway

$ ss -tlnp
LISTEN  0  511  127.0.0.1:3000   ...   node     # loopback only, not 0.0.0.0
```

`127.0.0.1` accepts connections only from the same interface. nginx connecting via another path gets refused.

## 🛠️ How you'll approach it

1. **Confirm the app is actually up.** It starts with no errors - so this isn't a crash.
2. **Check what it's listening on.** `ss -tlnp` shows `127.0.0.1:3000`, not `0.0.0.0:3000`. That single detail is the bug.
3. **Bind to all interfaces.** Change the listen address to `0.0.0.0` (or omit the host) so the app accepts connections from nginx, then restart.
4. **Verify end to end.** `ss` now shows `0.0.0.0:3000`, and the request through nginx returns 200.

One config flag - but it's a 20-minute mystery if you don't know to check `ss`/`netstat` first.

## 🎓 What you'll learn

- The difference between binding to `127.0.0.1` and `0.0.0.0` and why it matters behind a proxy
- Using `ss`/`netstat` to see exactly what address and port a service listens on
- Why "the app starts fine" and "the app is reachable" are two different questions
- A top-5 containerized-deploy gotcha and how to spot it fast

## 🏆 What it proves

You can debug a service that's running but unreachable - distinguishing a crash from a bind/networking problem - which is exactly the reasoning real deploy incidents need.

> [!TIP]
> **Resume-ready** — *Diagnosed an nginx 502 to a backend bound on loopback only and rebound it to all interfaces, restoring upstream connectivity.*

## 🗺️ On the roadmap

Part of the [DevOps Engineer Roadmap](../../roadmaps/devops.md) - **Stage 2: Networking & DNS** → TCP, ports & connectivity.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **DO-104** on HeyDevJob - the real unreachable app above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup.
> [Bind a Node App to 0.0.0.0 on HeyDevJob →](https://heydevjob.com/devops)

**Explore DevOps** · [📍 Roadmap](../../roadmaps/devops.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/devops.md) · [✅ Checklist](../../checklists/devops.md)

[◀ Prev: Recover a Crashed Linux Service](recover-a-crashed-linux-service.md) · [Next: Shrink a Bloated Docker Image ▶](shrink-a-bloated-docker-image.md)
