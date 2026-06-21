# DevOps Engineer Interview Questions

The questions that decide whether you get the DevOps offer - grouped by the skills that actually show up in production, junior to senior. Each answer is what a hiring manager wants to hear, and where a question maps to a real broken system you can fix, there's a pointer to the live ticket.

## Linux & the Command Line

**A service won't start after a config change. Walk me through your first five minutes.**
Check the unit status and logs first: `systemctl status <svc>` then `journalctl -u <svc> -n 50`. The error is almost always there - a bad config path, a permission, a port already in use, or a failed dependency. Confirm the port with `ss -tlnp` and the file's ownership with `ls -l` before you touch the application. The fix is rarely the app itself; it's the environment around it.
Practice it live: [Recover a Crashed Linux Service](https://heydevjob.com/devops)

**Writes started failing across the box at 3am. Where do you look?**
A full disk is the classic silent killer - databases, logs, and deploys all fail at once with confusing errors. Run `df -h` to find the full mount, then `du -sh /*` (or `du -xh / | sort -h | tail`) to walk down to the offender. The usual suspect is an unrotated log or a runaway worker writing temp files. Free space, then fix what caused the growth so it doesn't refill.
Practice it live: [Stop a Worker From Filling the Disk](https://heydevjob.com/devops)

**What's the difference between a process getting SIGTERM and SIGKILL, and why does it matter for deploys?**
SIGTERM asks a process to shut down gracefully - it can finish in-flight requests, flush buffers, and close connections. SIGKILL is non-catchable and immediate, so any in-flight work is lost. Deploys and orchestrators send SIGTERM first and only escalate to SIGKILL after a grace period, which is why honoring SIGTERM (and setting a sane termination grace period) is what makes a rolling restart drop zero requests.

**How do you find which process is holding a port or a deleted file?**
`ss -tlnp` (or `lsof -i :<port>`) maps a listening port to its PID and command. For a file that's been deleted but is still consuming disk because a process holds it open, `lsof | grep deleted` finds it - restarting that process releases the space. These two commands resolve a huge share of "the port is taken" and "I deleted logs but disk is still full" incidents.

## Networking & DNS

**Service A can't reach Service B. How do you isolate whether it's DNS, routing, a firewall, or a dead listener?**
Work the request path layer by layer. Resolve the name first (`dig`/`nslookup`) to rule out DNS, then test the TCP connection (`nc -zv host port` or `curl -v`) to separate routing/firewall from a dead service. If the port answers but the app doesn't, it's the app; if the connection hangs or refuses, it's network or the listener. Naming the failing layer in one pass is the skill - it turns an hour of guessing into a five-minute diagnosis.

**A Node app works on localhost but is unreachable from other hosts. What's wrong?**
It's almost certainly bound to `127.0.0.1` (loopback only) instead of `0.0.0.0` (all interfaces). A process on loopback accepts connections only from the same host, so anything off-box - a load balancer, another service, your laptop - gets connection refused. Bind to `0.0.0.0` (or the specific interface) and confirm with `ss -tlnp` that the listen address changed.
Practice it live: [Bind a Node App to 0.0.0.0](https://heydevjob.com/devops)

**Why is "it's always DNS" a real meme and not just a joke?**
DNS sits under everything and fails in non-obvious ways: stale records after a migration, a wrong search domain appending the wrong suffix, a TTL so long that a fix takes hours to propagate, or a resolver pointing at a dead server. The symptom looks like an app or network outage, so people debug the wrong layer for hours. Checking resolution first - and knowing TTLs gate how fast any DNS change lands - saves that time.

## Web Servers & Load Balancing

**You're getting 502 Bad Gateway but the app is confirmed up. What's happening?**
A 502 means the reverse proxy reached a point where it couldn't get a valid response from its upstream - usually the proxy is pointed at the wrong port or host, or the upstream died after the config was written. Read the proxy error log first; nginx names the exact upstream address it failed to connect to. Match `proxy_pass`/`upstream` to where the app actually listens (`ss -tlnp`), then `nginx -t` and `nginx -s reload`.
Practice it live: [Fix the Nginx 502 Bad Gateway](https://heydevjob.com/projects/nginx-502-bad-gateway-portfolio-project)

**How do you reload nginx config without dropping traffic, and how is that different from a restart?**
`nginx -s reload` (or `systemctl reload nginx`) tells the master process to load the new config and spin up new workers while old workers finish their in-flight requests, so no connection is dropped. A full restart tears down the listening sockets and can drop connections during the gap. Always run `nginx -t` first - reloading a config that fails to parse will leave you serving the old one or, worse, refuse to start on the next restart.

**How does TLS work at a high level, and what's the most common cert outage?**
The client and server do a handshake where the server presents a certificate chain, the client validates it against a trusted CA root, and they negotiate a session key for encryption. SNI lets one IP serve certs for many hostnames. The most common - and most embarrassing - outage is an expired certificate: fully avoidable, front-page when it happens, and the reason cert expiry monitoring and auto-renewal (ACME/Let's Encrypt) are non-negotiable.

## Databases in Production

**The app is throwing "too many connections" or "remaining connection slots reserved." What's going on and how do you fix it?**
The database hit its `max_connections` ceiling, usually because every app instance opens its own connections without pooling and they pile up under load. The right fix is a connection pooler (PgBouncer or an app-side pool) so a small, bounded set of connections is reused, rather than just raising `max_connections` until the box runs out of memory. Each Postgres connection costs real RAM, so the pool, not a bigger number, is the production answer.
Practice it live: [Resolve Postgres Pool Exhaustion Under Load](https://heydevjob.com/devops)

**What lives in pg_hba.conf and when does it bite you?**
`pg_hba.conf` controls client authentication: which hosts, users, and databases can connect and with what method (trust, scram-sha-256, md5, reject). It bites when a new app host can reach the database on the network but gets "no pg_hba.conf entry for host" because no rule matches its source. The rules are evaluated top-to-bottom and the change needs a reload (`pg_ctlcluster ... reload` or `SELECT pg_reload_conf()`), not a restart.

**Why is a cache restart dangerous for your database?**
When a cache like Redis restarts cold, every read that used to hit the cache now misses and falls through to the database simultaneously - a thundering herd that can overwhelm a database sized for cache-protected traffic. Mitigations include warming the cache, staggering TTLs so keys don't all expire at once, request coalescing so one miss repopulates the key, and persistence (RDB/AOF) so the cache comes back warm. The lesson: the cache is load-bearing infrastructure, not an optional optimization.

## Containers

**Your Docker image is 2GB and slowing every deploy. How do you shrink it?**
Use a multi-stage build: compile or install dependencies in a heavy builder stage, then copy only the runtime artifacts into a slim final stage (`-slim`, `alpine`, or `distroless`). Order layers so rarely-changing dependencies are cached above frequently-changing source, add a `.dockerignore` to keep build context lean, and never bake secrets into a layer - they persist in history even if a later layer deletes them. Smaller images mean faster pulls, faster cold starts, and a smaller attack surface.
Practice it live: [Shrink a Bloated Docker Image](https://heydevjob.com/devops)

**What's the difference between an image and a container, and what are layers?**
An image is an immutable, layered template - a stack of read-only filesystem layers plus metadata. A container is a running (or stopped) instance of an image with a thin writable layer on top. Each Dockerfile instruction creates a layer, and Docker caches them, so understanding layer ordering is how you make builds fast and images small.

**"It runs locally but not in the container." What are the usual causes?**
Almost always networking or missing state: the app binds to localhost instead of `0.0.0.0` so it's unreachable inside the container's network namespace, a port isn't published, an environment variable or config file present on your machine isn't in the image, or data that lived on the host disk isn't mounted as a volume. Reproduce by `docker exec` into the container and checking the listen address, env, and mounts from the inside.

## Kubernetes

**Walk me through debugging a pod stuck in CrashLoopBackOff.**
CrashLoopBackOff means the container starts, exits, and Kubernetes keeps restarting it with growing backoff. Run `kubectl describe pod` for events and the last exit code, then `kubectl logs <pod> --previous` to see the crashed container's output. Common roots are a bad command/entrypoint, a missing config or secret, a failed dependency at startup, or a failing liveness probe killing a healthy app. Fix the root, don't just bump the restart policy.
Practice it live: [Fix a Kubernetes CrashLoopBackOff](https://heydevjob.com/kubernetes)

**A Service returns nothing - no endpoints. What's broken?**
A Service routes to pods by label selector; if the selector doesn't match any running, ready pod, the Service has zero endpoints and every request fails. Check `kubectl get endpoints <svc>` - if it's empty, compare the Service's `selector` to the pods' actual labels, and confirm the pods are Ready (a failing readiness probe pulls a pod out of the endpoint list). The fix is usually a label mismatch or a probe that's failing.
Practice it live: [Heal a Service With No Endpoints](https://heydevjob.com/kubernetes)

**What do requests and limits do, and what happens when you set them wrong?**
Requests are what the scheduler reserves to place a pod; limits are the hard ceiling enforced at runtime. Set requests too high and pods sit Pending because no node can fit them; too low and nodes get oversubscribed and starved. Exceed a memory limit and the pod is OOM-killed; exceed a CPU limit and it's throttled, not killed. Getting these right is the difference between stable scheduling and 3am pages.

**Why do readiness and liveness probes both exist?**
A readiness probe decides whether a pod should receive traffic - failing it pulls the pod out of the Service's endpoints without killing it, which is what you want during startup or a temporary dependency blip. A liveness probe decides whether the container is dead and should be restarted. Confusing them is a classic outage: a liveness probe that's really a readiness check restart-loops a healthy-but-busy pod, and a missing readiness probe ships traffic to a pod before it's ready.

## Infrastructure as Code

**`terraform plan` shows drift after someone hand-edited a resource during an incident, and the team wants to keep the change. What do you do?**
Drift means the real infrastructure no longer matches the Terraform state and config, so `plan` wants to revert it. If the manual change is being kept, you update the Terraform code (and, if needed, the state) to match reality - not `apply`, which would stomp the kept change. Read the `~` diff to see exactly what differs, align the code to the on-disk value, and re-run until `plan` reports no changes. "No changes" is the only plan you trust after reconciling.
Practice it live: [Reconcile Terraform State Drift](https://heydevjob.com/devops)

**What is Terraform state, and why is it so dangerous?**
State is Terraform's record mapping your config to real-world resource IDs - it's how `plan` knows what already exists. It's dangerous because a corrupted, lost, or stale state file can make Terraform try to recreate or delete live infrastructure, and a careless `apply` can take down production. That's why state lives in a remote backend with locking (so two applies can't race), is never edited by hand, and is treated as sensitive (it can contain secrets).

**How do you read a Terraform plan safely before applying?**
Read every line of the plan, not just the summary count. `+` creates, `-` destroys, `~` updates in place, and `-/+` means destroy-and-recreate - that last one is the one that bites, because an in-place-looking change can actually replace a database or load balancer. Confirm the destroy/recreate list is intentional, and in CI gate `apply` behind a saved plan artifact so what you reviewed is exactly what runs.

## CI/CD

**Compare rolling, blue-green, and canary deploys. When would you pick each?**
Rolling replaces instances in batches - simple and the default, but a bad release reaches some users before you catch it. Blue-green stands up a full new environment and flips traffic all at once, giving instant rollback by flipping back, at the cost of double the infrastructure. Canary sends a small percentage of traffic to the new version, watches metrics, and ramps up - the safest for risky changes but the most tooling to run. Pick by blast-radius tolerance and what your platform supports.

**A pipeline with no rollback is a loaded gun. What makes a deploy pipeline safe?**
Automated tests gate the deploy, the build is immutable and versioned so you can redeploy any prior artifact, health checks confirm the new version is actually serving before traffic shifts, and rollback is a one-command (or automatic) path to the last good version. The deploy strategy bounds the blast radius and the rollback bounds the time-to-recover - together they decide whether a bad release is a blip or an outage.
Practice it live: [Automate a Node.js Deploy Pipeline With Rollback](https://heydevjob.com/devops)

**What's the difference between continuous delivery and continuous deployment?**
Continuous delivery means every change that passes the pipeline is automatically built, tested, and made ready to release - but a human approves the final push to production. Continuous deployment removes that gate: every green build goes straight to production automatically. The choice is about how much you trust your tests and your rollback, not about tooling.

## Observability & Incident Response

**What are the three pillars of observability, and what do you alert on?**
Metrics (aggregated numbers over time, cheap, good for trends and alerts), logs (discrete events, good for the detail of what happened), and traces (a request's path across services, good for finding where latency lives). Alert on symptoms that map to user pain - error rate, latency, saturation, traffic (the SRE "golden signals") - not on every internal metric, or you train the team to ignore pages. Alerting on the wrong thing means noise at 3am and silence when it actually breaks.

**Production is fully down, every request is a 502, and several services are dead at once. How do you triage?**
A cascade almost always has a single root, so map the dependency chain (proxy → app → datastores) and work bottom-up - fix the deepest dead dependency first, because patching a symptom higher up while the root is down just wastes the outage. Restore services from the bottom of the chain, verifying each before the next, until the top-level request returns 200. How you behave in the first ten minutes - calm, systematic, root-first - is most of what separates senior from mid.
Practice it live: [Triage a SEV1 Production Incident](https://heydevjob.com/devops)

**What makes a postmortem useful, and why blameless?**
A useful postmortem nails the timeline, the root cause, the contributing factors, and concrete action items with owners - so the same class of failure can't recur. Blameless means it focuses on the system and process gaps, not on punishing the person who pushed the button, because fear makes people hide information and that's how you lose the real cause. Good incident culture optimizes for honest, fast learning over assigning fault.

## Senior: Reliability, Scaling & Security

**Design a system that survives a node dying. What are the moving parts?**
Make the application stateless so any instance can serve any request, and push state to redundant, replicated stores (a database with replicas and failover, a distributed cache). Run multiple instances behind a load balancer with health checks so a dead node is pulled from rotation automatically, spread instances across availability zones so one zone failing isn't total, and use an orchestrator (Kubernetes) or autoscaler to reschedule and replace lost capacity. The whole senior conversation is removing single points of failure and proving the failover path actually works.

**How do you rotate a secret with zero downtime?**
Rotate in versions, not in place: introduce the new secret alongside the old, make consumers accept both during an overlap window, cut traffic over to the new one, and only then retire the old version. Versioning is what makes the change reversible and lets you roll back instantly if something can't read the new value. Doing it as a hard swap - delete old, create new - guarantees an outage for anything that hadn't picked up the new value yet.
Practice it live: [Build a Versioned Secret-Rotation System](https://heydevjob.com/devops)

**What does least privilege mean in practice, and why do most breaches start there?**
Least privilege means every identity - a user, a service account, an IAM role - gets exactly the permissions it needs and nothing more, scoped to specific resources and actions. Most breaches start with an over-permissioned role or a leaked long-lived key, so a single compromise becomes lateral movement across the whole account. In practice that means narrow IAM policies, short-lived credentials, no wildcards on sensitive actions, and network policies that default-deny and allowlist only what's required.

## Build it for real

Memorizing answers gets you through a screen; fixing real broken systems gets you the offer. Every question above maps to a live ticket on [HeyDevJob](https://heydevjob.com/devops) - a real broken production system in a cloud workspace you fix from your browser, and each fix lands on a portfolio hiring managers can open. The junior tier is free, no card, no setup.

**Start your portfolio →** [heydevjob.com/devops](https://heydevjob.com/devops)
