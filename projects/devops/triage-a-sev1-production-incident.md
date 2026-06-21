[Home](../../README.md) › [DevOps Projects](README.md) › **Triage a SEV1 Production Incident**

# Triage a SEV1 Production Incident

`DevOps` · `🔴 Senior` · `🔍 Debug` · `Ticket DO-308`

**Skills** — incident response · triage · dependency chains · multi-service debugging

> Production is fully down. Every request is a 502. Several services are dead at once. It looks like everything broke - but a cascade almost always has a single root, and your job is to fix the deepest thing first.

> [!NOTE]
> **The scenario**
> Production is completely down - every request returns `502 Bad Gateway` and multiple services are in a failed state. The server was rebooted an hour ago for a kernel patch; no app code changed. The symptoms are everywhere (nginx, Flask, Redis, Postgres), but they form a dependency chain, and one service at the bottom is the actual cause.

## 🧩 The code

The app sits on top of both Postgres and Redis (`app.py`):

```python
def get_db():
    return psycopg2.connect(**DB_CONFIG)

def get_redis():
    return redis.Redis(**REDIS_CONFIG)
```

The symptom:

```text
$ curl -s -o /dev/null -w "%{http_code}" http://localhost/      # 502
$ ps aux | grep -E "nginx|python|postgres|redis"
# multiple services stopped / not running after the reboot
```

The Flask code is fine - the failure is a service it depends on never coming back after the reboot, and the failure propagates upward.

## 🛠️ How you'll approach it

1. **Map the dependency chain.** nginx → Flask → (Redis, Postgres). A 502 at the top usually means something below didn't start.
2. **Work bottom-up.** Check the deepest dependencies first (`ps`, service status, logs in `/var/log/incident.log`). Fixing a symptom higher up while the root is still down just wastes the outage.
3. **Find and fix the root.** Identify the service that failed to start after the reboot, fix why, and bring it up.
4. **Restore in order.** Start services from the bottom of the chain up, verifying each before the next, until the top-level request returns 200.

## 🎓 What you'll learn

- Reading a cascading failure as a dependency chain, not a pile of unrelated breakages
- The bottom-up triage discipline: fix the deepest dependency first
- Restoring a multi-service stack in the correct order
- Why a clean reboot can leave services down (start order, missing enable, config)

## 🏆 What it proves

You can lead a SEV1 - find the single root cause under a wall of symptoms and restore a full stack in the right order - which is the clearest senior-vs-mid signal in operations.

> [!TIP]
> **Resume-ready** — *Triaged a SEV1 outage by working the service dependency chain bottom-up, isolating the root cause, and restoring the full stack in correct order.*

## 🗺️ On the roadmap

Part of the [DevOps Engineer Roadmap](../../roadmaps/devops.md) - **Stage 9: Observability & Incident Response** → Reading an incident.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **DO-308** on HeyDevJob - the real downed stack above, waiting in a cloud workspace you triage from your browser. Each fix lands on a portfolio you can show.
> [Triage a SEV1 Production Incident on HeyDevJob →](https://heydevjob.com/devops)

**Explore DevOps** · [📍 Roadmap](../../roadmaps/devops.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/devops.md) · [✅ Checklist](../../checklists/devops.md)

[◀ Prev: Automate a Node.js Deploy Pipeline](automate-a-nodejs-deploy-with-rollback.md) · [Next: Build a Versioned Secret-Rotation System ▶](build-a-secret-rotation-system.md)
