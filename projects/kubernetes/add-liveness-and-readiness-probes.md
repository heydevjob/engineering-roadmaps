# Add Liveness and Readiness Probes to a Deployment

**Role:** Kubernetes Engineer · **Level:** Mid · **Type:** Harden · **Skills:** kubectl, probes, health checks, deployments

> The Deployment has no probes, so Kubernetes sends traffic to pods the instant they start - before the app can serve - and never restarts one that hangs. You're giving it the two signals it needs: "ready to serve" and "still alive."

## The scenario

The `web` Deployment runs nginx with no health probes, so the Service routes traffic to pods before they're ready and hung pods are never restarted. You add both a readiness probe and a liveness probe (HTTP GET `/` on port 80) with sensible timing, then re-apply.

## The code

The probe-less container in `deployment.yaml`:

```yaml
spec:
  containers:
  - name: web
    image: nginx:1.27-alpine
    ports:
    - containerPort: 80
    # no readinessProbe, no livenessProbe
```

The symptom:

```text
# Service sends traffic to pods the moment they start (before nginx serves)
# -> deploy-time errors; a hung pod is never restarted
```

## How you'll approach it

1. **Add readiness.** An HTTP GET `/` readiness probe so the Service only routes to a pod once it actually serves - this is what stops deploy-time errors.
2. **Add liveness.** An HTTP GET `/` liveness probe so a hung container gets restarted instead of sitting dead in rotation.
3. **Tune the timing.** Reasonable `initialDelaySeconds`, `periodSeconds`, and thresholds - aggressive enough to catch failures, gentle enough not to kill a healthy-but-slow pod.
4. **Re-apply and verify.** Pods only become `Ready` once the probe passes.

## What you'll learn

- The difference between readiness (route traffic) and liveness (restart) probes
- Why no readiness probe means every deploy throws errors
- Why an over-aggressive liveness probe kills healthy-but-slow pods
- Tuning probe timing sensibly

## What it proves

You can make a workload behave correctly during deploys and failures - the reliability baseline every production Deployment needs.

> Resume-ready: *Added readiness and liveness probes to a Deployment, ensuring traffic only reaches ready pods and hung pods are restarted automatically.*

## On the roadmap

Part of the [Kubernetes Engineer Roadmap](../../roadmaps/kubernetes.md) - **Stage 5: Health & Probes** → Liveness & readiness probes.

---

**Build it for real.** This is ticket **K8-208** on HeyDevJob - the real probe-less Deployment above, in a cloud workspace where you run kubectl from your browser. Free on the junior tier, no card, no setup. [Add Liveness and Readiness Probes on HeyDevJob →](https://heydevjob.com/kubernetes)
