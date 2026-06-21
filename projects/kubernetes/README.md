[Home](../../README.md) › **Kubernetes Projects**

# Kubernetes Engineer Projects

`13 projects` · `Debug · Build · Harden · Design` · `Junior → Senior`

Real broken or empty clusters to fix and build - each one proves a specific Kubernetes skill and produces something you can put on a portfolio. The arc runs from debugging the common failure states to authoring production manifests to hardening them. Every project is grounded in a real [HeyDevJob](https://heydevjob.com/kubernetes) ticket: the actual manifest, the real `kubectl` symptom, and a live workspace where you run kubectl from your browser.

> [!NOTE]
> **Project types**
> 🔨 **Build** - author the manifest from scratch · 🔍 **Debug** - find and fix a broken object · 🛡️ **Harden** - make a workload reliable and secure · 📐 **Design** - architect for production

Pairs with the [Kubernetes Engineer Roadmap](../../roadmaps/kubernetes.md): the roadmap tells you what to learn, these are where you prove it.

## 🟢 Junior projects

Start here. Debug the failure states every Kubernetes engineer reads daily, then author your first Deployment.

| Project | Ticket | Type | Skills |
|---------|--------|------|--------|
| [Fix a Kubernetes CrashLoopBackOff](fix-a-kubernetes-crashloopbackoff.md) | `K8-101` | 🔍 Debug | kubectl, pods, debugging |
| [Fix a Kubernetes ImagePullBackOff](fix-a-kubernetes-imagepullbackoff.md) | `K8-103` | 🔍 Debug | kubectl, images, registries |
| [Schedule a Pending Kubernetes Pod](schedule-a-pending-pod.md) | `K8-104` | 🔍 Debug | kubectl, scheduling, nodeSelector |
| [Heal a Service With No Endpoints](heal-a-service-with-no-endpoints.md) | `K8-102` | 🔍 Debug | kubectl, services, selectors |
| [Reconnect a Pod to Its ConfigMap](reconnect-a-pod-to-its-configmap.md) | `K8-105` | 🔍 Debug | kubectl, ConfigMap |
| [Write a Production Kubernetes Deployment](write-a-production-deployment.md) | `K8-111` | 🔨 Build | deployments, services, probes |

## 🟡 Mid projects

Author real manifests - health checks, rollouts, autoscaling, and Helm packaging.

| Project | Ticket | Type | Skills |
|---------|--------|------|--------|
| [Add Liveness and Readiness Probes](add-liveness-and-readiness-probes.md) | `K8-208` | 🛡️ Harden | probes, health checks |
| [Release a New Image With a Rolling Update](release-with-a-rolling-update.md) | `K8-207` | 🔨 Build | deployments, rollout |
| [Configure an HPA for Autoscaling](configure-an-hpa-for-autoscaling.md) | `K8-206` | 🔨 Build | HPA, autoscaling, metrics |
| [Write and Lint a Helm Chart](write-and-lint-a-helm-chart.md) | `K8-203` | 🔨 Build | Helm, templating, charts |

## 🔴 Senior projects

Production security and reliability - lock it down and make it resilient.

| Project | Ticket | Type | Skills |
|---------|--------|------|--------|
| [Lock Down a Namespace With a NetworkPolicy](lock-down-a-namespace-with-networkpolicy.md) | `K8-303` | 🛡️ Harden | NetworkPolicy, zero-trust |
| [Configure a Zero-Downtime Rolling Update](configure-a-zero-downtime-rolling-update.md) | `K8-305` | 🔨 Build / 📐 Design | rolling-update, probes, graceful shutdown |
| [Harden a Pod With a securityContext](harden-a-pod-with-securitycontext.md) | `K8-308` | 🛡️ Harden | securityContext, non-root, hardening |

> [!IMPORTANT]
> **Build it for real**
> Reading about a CrashLoopBackOff isn't the same as running `kubectl describe` on a real one and finding the typo in the events. Every project here is a live ticket on [HeyDevJob](https://heydevjob.com/kubernetes) - a real cluster in a workspace, no setup. The junior tier is free, no card. Fix it, and it lands on a portfolio hiring managers can open and click.
>
> **Start the Kubernetes Engineer projects on HeyDevJob →** [heydevjob.com/kubernetes](https://heydevjob.com/kubernetes)

---

**Explore Kubernetes** · [📍 Roadmap](../../roadmaps/kubernetes.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/kubernetes.md) · [✅ Checklist](../../checklists/kubernetes.md)
