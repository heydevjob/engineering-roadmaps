# Configure a Zero-Downtime Rolling Update

**Role:** Kubernetes Engineer · **Level:** Senior · **Type:** Build/Design · **Skills:** kubectl, rolling-update, probes, graceful shutdown

> "Why do we get 502s every deploy?" The Deployment uses the `Recreate` strategy - it kills every pod, then starts new ones, leaving a gap with zero capacity. You're rebuilding it into the canonical zero-downtime recipe.

## The scenario

The `api` Deployment uses the `Recreate` strategy and drops requests on every rollout. You rewrite it for zero downtime: RollingUpdate (`maxUnavailable: 0`, `maxSurge: 1`), a readiness probe, a `preStop` drain hook, and a `terminationGracePeriodSeconds` long enough to finish in-flight requests.

## The code

The downtime-causing strategy in `deployment.yaml`:

```yaml
spec:
  replicas: 3
  strategy:
    type: Recreate            # kills all pods, then starts new ones -> a gap
  template:
    spec:
      containers:
      - name: api
        image: nginx:1.27-alpine
        ports:
        - containerPort: 80
        # no readiness probe, no preStop, no grace period
```

The symptom:

```text
every deploy -> brief window with zero Ready pods -> 502s
```

## How you'll approach it

1. **Switch the strategy.** `RollingUpdate` with `maxUnavailable: 0` (never drop below full capacity) and `maxSurge: 1` (bring up a new pod before retiring an old one).
2. **Add a readiness probe.** So a new pod takes traffic only once it actually serves - the rollout waits for ready, not just started.
3. **Drain on shutdown.** A `preStop` hook (e.g. `sleep 5`) lets in-flight requests finish before the container stops.
4. **Give it grace.** Set `terminationGracePeriodSeconds` longer than the drain so Kubernetes doesn't kill the pod mid-drain. Verify a rollout drops zero requests.

## What you'll learn

- The four settings behind the canonical zero-downtime Deployment
- Why `Recreate` and a missing readiness probe cause deploy-time 502s
- Draining in-flight requests with a `preStop` hook and grace period
- How `maxUnavailable: 0` + `maxSurge` keep capacity through a rollout

## What it proves

You can build the zero-downtime Deployment recipe from the ground up - the fix for the classic "502s on every deploy" ticket and a senior reliability signal.

> Resume-ready: *Reconfigured a Deployment for zero-downtime rollouts - RollingUpdate with maxUnavailable 0, readiness probe, preStop drain, and grace period - eliminating deploy-time 502s.*

## On the roadmap

Part of the [Kubernetes Engineer Roadmap](../../roadmaps/kubernetes.md) - **Stage 9: Senior - Security & Reliability** → Zero-downtime rollouts.

---

**Build it for real.** This is ticket **K8-305** on HeyDevJob - the real Recreate-strategy Deployment above, in a cloud workspace where you run kubectl from your browser. Each fix lands on a portfolio you can show. [Configure a Zero-Downtime Rolling Update on HeyDevJob →](https://heydevjob.com/kubernetes)
