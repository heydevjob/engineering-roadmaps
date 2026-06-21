# Release a New Image With a Rolling Update

**Role:** Kubernetes Engineer · **Level:** Mid · **Type:** Build · **Skills:** kubectl, deployments, rollout, rolling-update

> A new version is ready to ship. The job isn't just changing the image tag - it's bumping it, applying, and *watching* the rollout drain the old replicas as the new ones come up, so not a single request drops.

## The scenario

The `api` Deployment runs `nginx:1.26-alpine` across 3 replicas. You bump the image to `nginx:1.27-alpine`, apply it, and watch the rolling update with `kubectl rollout status` to confirm all replicas transition without dropping traffic.

## The code

The image tag in `deployment.yaml`:

```yaml
containers:
- name: api
  # TODO: bump to nginx:1.27-alpine, then `kubectl apply -f deployment.yaml`
  image: nginx:1.26-alpine
  ports:
  - containerPort: 80
```

The task:

```text
3 replicas on nginx:1.26-alpine -> roll to 1.27-alpine with no dropped requests
```

## How you'll approach it

1. **Bump the tag.** Change the image to `nginx:1.27-alpine` in the manifest.
2. **Apply.** `kubectl apply -f deployment.yaml` triggers a rolling update - the default strategy replaces pods gradually, not all at once.
3. **Watch it.** `kubectl rollout status deploy/api` shows old replicas draining as new ones become ready; capacity never drops below the threshold.
4. **Verify.** All 3 replicas are on the new image and the rollout reports complete.

## What you'll learn

- How a RollingUpdate replaces pods gradually to avoid downtime
- Triggering a rollout by changing the pod template
- Watching `kubectl rollout status` to confirm a clean transition
- The defaults (`maxUnavailable`, `maxSurge`) that govern the pace

## What it proves

You can ship a new version with a zero-downtime rollout and confirm it - the baseline release skill for any production Kubernetes workload.

> Resume-ready: *Released a new image via a Kubernetes rolling update, watching the rollout drain old replicas while new ones came up with no dropped traffic.*

## On the roadmap

Part of the [Kubernetes Engineer Roadmap](../../roadmaps/kubernetes.md) - **Stage 6: Rollouts & Updates** → Rolling updates.

---

**Build it for real.** This is ticket **K8-207** on HeyDevJob - the real Deployment to roll above, in a cloud workspace where you run kubectl from your browser. Free on the junior tier, no card, no setup. [Release a New Image With a Rolling Update on HeyDevJob →](https://heydevjob.com/kubernetes)
