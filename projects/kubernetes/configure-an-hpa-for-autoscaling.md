[Home](../../README.md) › [Kubernetes Projects](README.md) › **Configure an HPA for Autoscaling**

# Configure a Kubernetes HPA for Autoscaling

`Kubernetes` · `🟡 Mid` · `🔨 Build` · `Ticket K8-206`

**Skills** — kubectl · HPA · autoscaling · metrics

> The `api` Deployment is pinned at one replica. Under load it falls over; at 3am it wastes money. You're writing the HorizontalPodAutoscaler that grows it under traffic and shrinks it when the traffic leaves.

> [!NOTE]
> **The scenario**
> The `api` Deployment runs a single replica and needs to auto-scale. You write `hpa.yaml` - a `HorizontalPodAutoscaler` targeting the Deployment with min 2, max 10 replicas, and a 70% CPU utilization target - then apply it and verify with `kubectl get hpa`.

## 🧩 The code

The HPA template you fill in (`hpa.yaml`):

```yaml
# TODO: HorizontalPodAutoscaler named `api` targeting the api Deployment.
# Required spec fields shown in comments:
#   apiVersion: autoscaling/v2
#   minReplicas: 2
#   maxReplicas: 10
#   metrics: [cpu 70% utilization]
```

(Build-from-scratch ticket: you author the full HPA spec.)

## 🛠️ How you'll approach it

1. **Point it at the Deployment.** A `scaleTargetRef` referencing the `api` Deployment by name.
2. **Set the bounds.** `minReplicas: 2` (always-on capacity) and `maxReplicas: 10` (a ceiling so it can't scale unbounded).
3. **Choose the metric.** A CPU utilization target of 70% - the HPA adds replicas when average CPU exceeds it and removes them when it drops.
4. **Apply and verify.** `kubectl get hpa` shows the target, current utilization, and replica count.

## 🎓 What you'll learn

- Writing an `autoscaling/v2` HorizontalPodAutoscaler
- The `scaleTargetRef`, min/max bounds, and utilization target
- How the HPA reacts to a metric crossing its threshold
- Why static replica counts force over-provisioning or dropped load

## 🏆 What it proves

You can give a workload elasticity - scaling with demand instead of paying for peak capacity around the clock - a core production scaling skill.

> [!TIP]
> **Resume-ready** — *Configured a HorizontalPodAutoscaler (autoscaling/v2) with a 70% CPU target and 2-10 replica bounds, giving a Deployment demand-based elasticity.*

## 🗺️ On the roadmap

Part of the [Kubernetes Engineer Roadmap](../../roadmaps/kubernetes.md) - **Stage 7: Autoscaling** → Horizontal Pod Autoscaler.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **K8-206** on HeyDevJob - the real single-replica Deployment above, in a cloud workspace where you run kubectl from your browser. Free on the junior tier, no card, no setup.
> [Configure an HPA for Autoscaling on HeyDevJob →](https://heydevjob.com/kubernetes)

**Explore Kubernetes** · [📍 Roadmap](../../roadmaps/kubernetes.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/kubernetes.md) · [✅ Checklist](../../checklists/kubernetes.md)

[◀ Prev: Release a New Image With a Rolling Update](release-with-a-rolling-update.md) · [Next: Write and Lint a Helm Chart ▶](write-and-lint-a-helm-chart.md)
