# DevOps Engineer Roadmap

The path from "I can write a script" to "I keep production running" - the skills, in order, that get you hired as a DevOps engineer and paid to keep systems alive.

This roadmap is opinionated. It skips the trivia and the 40-tool logo walls you've seen elsewhere. It covers the 20% of skills that show up in real DevOps work and real interviews, in the order that actually builds on itself. Every node tells you **what** to learn, **why it matters**, and **how you'd prove it**. The linked project is where you prove it on a real broken system.

**How to read a node:**
- *What* - the skill in one line
- *Why it matters* - what breaks in production when you don't have it
- *Prove it* - the project that turns the skill into a portfolio piece

Work top to bottom. Foundations are not optional - senior engineers are fast at the basics, not ignorant of them.

---

## Stage 1 - Linux & the Command Line (Foundations)

Everything you'll ever debug runs on Linux. If you're slow here, you're slow everywhere.

### Processes, signals & the boot path
- *What:* How a Linux box starts, what `systemd` does, how services are supervised, how processes get signals.
- *Why it matters:* "The service won't start" is the single most common production page. The fix is almost never the app - it's a unit file, a permission, a port, or a dependency order.
- *Prove it:* [Recover a Crashed Linux Service](../projects/devops/recover-a-crashed-linux-service.md)

### Filesystems, permissions & disk pressure
- *What:* Ownership, modes, mounts, what fills a disk, how to find it (`du`, `df`, `lsof`).
- *Why it matters:* A full disk takes down databases, logging, and deploys silently. "Why did writes start failing?" is usually `/` at 100%.
- *Prove it:* [Stop a Worker From Filling the Disk](../projects/devops/stop-a-worker-filling-the-disk.md)

### Text, logs & pipelines
- *What:* `grep`, `awk`, `sed`, `journalctl`, `jq` - reading and slicing logs fast.
- *Why it matters:* Incident response is log reading under pressure. The person who finds the error line first leads the call.

## Stage 2 - Networking & DNS

Most "it works on my machine" problems are network problems wearing a costume.

### TCP, ports & connectivity
- *What:* The path of a request, listening sockets, `ss`/`netstat`, `curl -v`, firewalls.
- *Why it matters:* "Service A can't reach service B" is a daily question. Knowing whether it's DNS, routing, a firewall, or a dead listener saves hours.
- *Prove it:* [Bind a Node App to 0.0.0.0 (All Interfaces)](../projects/devops/bind-a-node-app-to-all-interfaces.md)

### DNS - the thing that's always to blame
- *What:* Resolution order, records (A/CNAME/SRV), TTLs, `/etc/resolv.conf`, in-cluster DNS.
- *Why it matters:* It's always DNS. Stale records, wrong search domains, and bad TTLs cause outages that look like everything else.

## Stage 3 - Web Servers & Load Balancing

The front door. Where traffic, TLS, and routing live.

### nginx as reverse proxy
- *What:* `server` blocks, `proxy_pass`, upstreams, headers, timeouts.
- *Why it matters:* nginx fronts a huge share of the internet. A wrong `proxy_pass` or missing header is a 502 in front of a million users.
- *Prove it:* [Fix the Nginx 502 Bad Gateway](../projects/devops/fix-the-nginx-502-bad-gateway.md)

### TLS & certificates
- *What:* How HTTPS works, cert chains, expiry, SNI, renewals.
- *Why it matters:* Expired certs are an outage class of their own - fully avoidable, deeply embarrassing, and on the front page when they happen.

## Stage 4 - Databases in Production

You won't write the queries, but you'll keep the database alive - and you'll be blamed when it's slow.

### Postgres operations
- *What:* Connections, `pg_hba.conf`, config, restarts, slow-query basics, backups.
- *Why it matters:* Connection exhaustion and bad config quietly throttle every app on top. Backups are the difference between an incident and a resignation.
- *Prove it:* [Resolve Postgres Pool Exhaustion Under Load](../projects/devops/resolve-postgres-pool-exhaustion.md)

### Redis & caching layers
- *What:* What a cache is for, eviction, persistence, when it's the bottleneck.
- *Why it matters:* A misconfigured cache turns into a thundering herd against your database the moment it restarts.

## Stage 5 - Containers

Packaging the app so it runs the same everywhere - and shrinking your 2GB image.

### Docker fundamentals
- *What:* Images vs containers, layers, `Dockerfile` hygiene, multi-stage builds, image size.
- *Why it matters:* Slow, bloated images slow every deploy and every cold start. A leaked secret in a layer is a breach.
- *Prove it:* [Shrink a Bloated Docker Image (Multi-Stage Build)](../projects/devops/shrink-a-bloated-docker-image.md)

### Container networking & volumes
- *What:* How containers talk, port mapping, persistent data, env config.
- *Why it matters:* "It runs locally but not in the container" is almost always networking or a missing volume.

## Stage 6 - Kubernetes

The default way to run containers at scale. Where most DevOps salaries live now.

### Pods, Deployments & Services
- *What:* The object model, how a Deployment rolls out, how a Service routes.
- *Why it matters:* `CrashLoopBackOff`, `ImagePullBackOff`, and `Pending` are the three states you'll debug forever. Knowing the object model is how you read them.
- *Prove it:* hands-on pod debugging (CrashLoopBackOff, ImagePullBackOff) lives in the [Kubernetes Engineer projects](../projects/kubernetes/).

### Config, secrets & resource limits
- *What:* ConfigMaps, Secrets, requests/limits, probes.
- *Why it matters:* A missing readiness probe ships broken pods into rotation. A bad memory limit gets your pod OOM-killed at 3am.

> Going deep on Kubernetes? See the dedicated [Kubernetes roadmap](kubernetes.md).

## Stage 7 - Infrastructure as Code

Click-ops doesn't scale and can't be reviewed. Real infra is code.

### Terraform fundamentals
- *What:* Providers, resources, state, plan/apply, variables, modules.
- *Why it matters:* State drift and a bad `apply` can delete production. Reading a plan correctly is a load-bearing skill.
- *Prove it:* [Reconcile Terraform State Drift](../projects/devops/reconcile-terraform-state-drift.md)

## Stage 8 - CI/CD

How code gets from a laptop to production safely, many times a day.

### Pipelines & deployment strategies
- *What:* Build/test/deploy stages, rolling vs blue-green vs canary, rollbacks.
- *Why it matters:* A pipeline with no rollback is a loaded gun. The deploy strategy decides whether a bad release is a blip or an outage.
- *Prove it:* [Automate a Node.js Deploy Pipeline With Rollback](../projects/devops/automate-a-nodejs-deploy-with-rollback.md)

## Stage 9 - Observability & Incident Response

You can't fix what you can't see. Senior DevOps is measured here.

### Metrics, logs & alerts
- *What:* The three pillars (metrics/logs/traces), what to alert on, signal vs noise.
- *Why it matters:* Alerting on the wrong thing means pages at 3am for nothing - and silence when it actually breaks.

### Reading an incident
- *What:* Triage, narrowing the blast radius, the blameless postmortem.
- *Why it matters:* How you behave in the first 10 minutes of an outage is most of what separates mid from senior.
- *Prove it:* [Triage a SEV1 Production Incident](../projects/devops/triage-a-sev1-production-incident.md)

## Stage 10 - Senior: Reliability, Scaling & Security (System Design)

Where you stop fixing single boxes and start designing systems that don't fall over.

### Scaling & high availability
- *What:* Horizontal scaling, statelessness, load balancing, failover, multi-AZ thinking.
- *Why it matters:* This is the senior interview. "Design a system that survives a node dying" is the whole conversation.

### Secrets, least privilege & supply chain
- *What:* Secret rotation, IAM least-privilege, image provenance, network policy.
- *Why it matters:* Most breaches are a leaked key or an over-permissioned role. Senior DevOps owns this.
- *Prove it:* [Build a Versioned Secret-Rotation System](../projects/devops/build-a-secret-rotation-system.md)

---

## Where you are on the path

| Stage | You can... | Hiring level |
|-------|-----------|--------------|
| 1-3 | Keep a single box and its services healthy | Junior |
| 4-6 | Run containerized apps and databases in production | Junior → Mid |
| 7-9 | Automate infra, ship safely, respond to incidents | Mid |
| 10 | Design systems that scale and survive failure | Senior |

## Build it for real

Every project linked above is a live ticket on [HeyDevJob](https://heydevjob.com/devops) - a real broken system in a cloud workspace, fixed from your browser. The junior tier is free, no card, no setup. Each fix you ship lands on a portfolio you can show.

**Start your portfolio →** [heydevjob.com/devops](https://heydevjob.com/devops)
