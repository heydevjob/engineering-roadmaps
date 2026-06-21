# DevOps Engineer Job-Readiness Checklist

The concrete skills that make you hireable as a DevOps engineer, junior to senior. These aren't topics to have heard of - they're things you can do, on demand, on a real system. Tick what you can prove, and go fix the rest.

## Stage 1 - Linux & the Command Line

- [ ] Read a failing service's status and logs with `systemctl status` and `journalctl -u`
- [ ] Diagnose why a service won't start (bad unit, permission, port conflict, dependency order)
- [ ] Find what's filling a disk with `df -h` and `du`, and free it safely
- [ ] Find a deleted-but-open file still consuming space with `lsof | grep deleted`
- [ ] Identify the process holding a port with `ss -tlnp` or `lsof -i`
- [ ] Send and reason about process signals (SIGTERM vs SIGKILL) and graceful shutdown
- [ ] Read and fix file ownership and permission problems (`ls -l`, `chmod`, `chown`)
- [ ] Slice logs fast under pressure with `grep`, `awk`, `sed`, `journalctl`, and `jq`

## Stage 2 - Networking & DNS

- [ ] Trace a request's path and tell DNS, routing, firewall, and dead-listener failures apart
- [ ] Test a TCP connection with `nc -zv` or `curl -v` and read the result
- [ ] List listening sockets and their processes with `ss -tlnp`
- [ ] Bind an app to `0.0.0.0` vs `127.0.0.1` and explain the difference
- [ ] Resolve a name and inspect records with `dig`/`nslookup` (A, CNAME, SRV)
- [ ] Explain how TTLs gate how fast a DNS change propagates
- [ ] Diagnose a wrong search domain or stale record in `/etc/resolv.conf`

## Stage 3 - Web Servers & Load Balancing

- [ ] Diagnose an nginx 502 from the error log instead of guessing
- [ ] Read and correct an nginx `upstream` / `proxy_pass` block
- [ ] Validate and reload nginx with no dropped traffic (`nginx -t`, `nginx -s reload`)
- [ ] Configure a reverse proxy with correct headers and timeouts
- [ ] Explain the TLS handshake, cert chains, and SNI at a high level
- [ ] Detect an expiring certificate and renew it (ACME / Let's Encrypt)

## Stage 4 - Databases in Production

- [ ] Diagnose and fix Postgres connection exhaustion with a connection pooler
- [ ] Read and edit `pg_hba.conf` to allow a new client, then reload (not restart)
- [ ] Start, stop, restart, and reload Postgres and tell which one a change needs
- [ ] Find a slow query and read an `EXPLAIN` plan at a basic level
- [ ] Take and restore a Postgres backup
- [ ] Explain cache eviction, persistence, and the thundering-herd risk on cache restart

## Stage 5 - Containers

- [ ] Explain images vs containers and what a layer is
- [ ] Shrink a bloated image with a multi-stage build and a `.dockerignore`
- [ ] Order Dockerfile layers so dependency caching actually works
- [ ] Avoid baking secrets into image layers and explain why deletion doesn't remove them
- [ ] Debug "runs locally but not in the container" (bind address, ports, env, volumes)
- [ ] Map ports and mount persistent volumes correctly
- [ ] `docker exec` into a running container and inspect its state from the inside

## Stage 6 - Kubernetes

- [ ] Debug a CrashLoopBackOff with `describe`, `logs --previous`, and exit codes
- [ ] Heal a Service with no endpoints by fixing the label selector or readiness
- [ ] Diagnose an ImagePullBackOff (bad tag, missing registry auth, wrong image)
- [ ] Schedule a Pending pod (resource requests, taints, node capacity)
- [ ] Wire a pod to a ConfigMap and a Secret and verify the values land
- [ ] Set sensible requests and limits and explain OOM-kill vs CPU throttling
- [ ] Add correct liveness and readiness probes and explain why both exist
- [ ] Read a Deployment rollout and roll it back

## Stage 7 - Infrastructure as Code

- [ ] Read a `terraform plan` line by line (`+`, `-`, `~`, `-/+`) before applying
- [ ] Reconcile state drift by aligning code to a deliberately-kept change
- [ ] Explain Terraform state, remote backends, and state locking
- [ ] Use variables and modules to keep infra DRY and reviewable
- [ ] Recognize a destroy-and-recreate that would replace a live resource

## Stage 8 - CI/CD

- [ ] Build a pipeline with build, test, and deploy stages
- [ ] Implement a one-command (or automatic) rollback to the last good artifact
- [ ] Compare rolling, blue-green, and canary deploys and pick the right one
- [ ] Gate `apply`/deploy behind a reviewed, saved plan artifact
- [ ] Explain continuous delivery vs continuous deployment

## Stage 9 - Observability & Incident Response

- [ ] Name the three pillars (metrics, logs, traces) and what each is good for
- [ ] Alert on the golden signals (error rate, latency, saturation, traffic), not noise
- [ ] Triage a multi-service cascade bottom-up to the single root cause
- [ ] Restore a downed stack in dependency order, verifying each layer
- [ ] Write a blameless postmortem with a timeline, root cause, and owned action items

## Stage 10 - Senior: Reliability, Scaling & Security

- [ ] Design a stateless, horizontally-scaled system that survives a node dying
- [ ] Remove single points of failure across compute, data, and load balancing
- [ ] Reason about multi-AZ placement and automatic failover
- [ ] Rotate a secret with zero downtime using versioning and an overlap window
- [ ] Write least-privilege IAM policies (scoped resources, no wildcards on sensitive actions)
- [ ] Lock down a namespace or network with default-deny and explicit allowlists

## Can't tick it? Prove it.

Every unchecked box is a live ticket on [HeyDevJob](https://heydevjob.com/devops) - a real broken production system you fix from your browser, with each fix landing on a portfolio you can show. Stop reading about it and go do it. The junior tier is free, no card, no setup.

**Start your portfolio →** [heydevjob.com/devops](https://heydevjob.com/devops)
