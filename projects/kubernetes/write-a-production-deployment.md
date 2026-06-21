# Write a Production Kubernetes Deployment

**Role:** Kubernetes Engineer · **Level:** Junior · **Type:** Build · **Skills:** kubectl, deployments, services, probes, resource limits

> You're handed a spec and an empty workspace. Write the Deployment and Service from scratch - and the signal isn't just matching the spec, it's knowing what a production manifest needs even when the spec doesn't spell it out.

## The scenario

You author two manifests from nothing: `deployment.yaml` and `service.yaml`. The Deployment (`api`, 2 replicas, busybox) needs a RollingUpdate strategy with `maxUnavailable: 0`, CPU/memory requests and limits, and a readiness probe; the Service is a matching ClusterIP. You apply both and verify with `kubectl rollout status` and `kubectl get pods,svc`.

## The code

The Deployment spec you build toward (`deployment.yaml`):

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api
spec:
  replicas: 2
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 0
      maxSurge: 1
  template:
    spec:
      containers:
      - image: busybox:1.36
        resources:
          requests: {cpu: 50m, memory: 64Mi}
          limits: {cpu: 200m, memory: 128Mi}
        readinessProbe:
          exec:
            command: ["/bin/true"]
          initialDelaySeconds: 5
```

(Build-from-scratch ticket: you write the Deployment + a matching ClusterIP Service.)

## How you'll approach it

1. **Author the Deployment.** Name, replicas, label selector matching the pod template, the container, and the RollingUpdate strategy with `maxUnavailable: 0` (so a rollout never drops below capacity).
2. **Add the production bits.** Resource requests/limits (so the scheduler can place it and it can't starve neighbors) and a readiness probe (so traffic waits for ready pods).
3. **Write the Service.** A ClusterIP whose selector matches the Deployment's pod labels.
4. **Apply and verify.** `kubectl apply`, then `kubectl rollout status deploy/api` and `kubectl get pods,svc`.

## What you'll learn

- Authoring a Deployment + Service from a spec
- Why `maxUnavailable: 0`, resource limits, and a readiness probe belong in every production manifest
- Matching label selectors between Deployment and Service
- Verifying a rollout instead of assuming it worked

## What it proves

You can write a production-shaped manifest from scratch - the day-one task at any platform-engineering job - and you know what to include beyond the bare spec.

> Resume-ready: *Authored a production Kubernetes Deployment and Service from spec with zero-surge rolling updates, resource limits, and a readiness probe.*

## On the roadmap

Part of the [Kubernetes Engineer Roadmap](../../roadmaps/kubernetes.md) - **Stage 4: Authoring Workloads** → Write a production Deployment.

---

**Build it for real.** This is ticket **K8-111** on HeyDevJob - the real from-scratch authoring task above, in a cloud workspace where you run kubectl from your browser. Free on the junior tier, no card, no setup. [Write a Production Kubernetes Deployment on HeyDevJob →](https://heydevjob.com/kubernetes)
