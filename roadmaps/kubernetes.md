# Kubernetes Engineer Roadmap

The path from "I can read `kubectl get pods`" to "I can run a workload safely in production" - the skills, in order, that get you hired to operate Kubernetes and trusted with a cluster.

Kubernetes is a discipline of **debugging the object model, then authoring it well**. The hireable arc is: read why a pod is broken, write the manifest that does it right, then harden it for production. This roadmap follows that arc. Every node tells you **what** to learn, **why it matters**, and **how you'd prove it**, tagged with the kind of work it is:

- **Build** - author the manifest from scratch
- **Debug** - find and fix a broken object
- **Harden** - make a workload reliable and secure
- **Design** - architect for production (senior)

Work top to bottom. You learn to read the failure states before you author the fixes.

---

## Stage 1 - The Object Model & Debugging Pods

The three failure states you'll debug forever. The muscle memory is `describe → logs → events`.

### CrashLoopBackOff
- *What:* A container that starts, dies, and backs off in a loop - and reading why from events and logs.
- *Why it matters:* "Why is this pod CrashLooping?" is the most-asked question in any Kubernetes on-call. The describe→logs→events reflex is 30 seconds vs 30 minutes.
- *Prove it:* [Fix a Kubernetes CrashLoopBackOff](../projects/kubernetes/fix-a-kubernetes-crashloopbackoff.md) *(Debug)*

### ImagePullBackOff
- *What:* A pod that can't pull its image - a bad tag, a missing registry, or a typo.
- *Why it matters:* A misspelled image tag should never reach `kubectl apply`. Reading the pull error is how you catch it fast.
- *Prove it:* [Fix a Kubernetes ImagePullBackOff](../projects/kubernetes/fix-a-kubernetes-imagepullbackoff.md) *(Debug)*

### Pending pods & scheduling
- *What:* A pod the scheduler can't place, and reading the "didn't fit" reason.
- *Why it matters:* Scheduling failures are silent - the pod just sits in `Pending` forever. You have to know to look at the events.
- *Prove it:* [Schedule a Pending Kubernetes Pod](../projects/kubernetes/schedule-a-pending-pod.md) *(Debug)*

## Stage 2 - Services & Networking

How traffic reaches a pod - and why it sometimes doesn't.

### Services, selectors & endpoints
- *What:* How a Service finds pods by label, and what an empty endpoints list means.
- *Why it matters:* A Service whose selector doesn't match the pod labels has no endpoints and silently drops all traffic - the #2 "service is down" cause.
- *Prove it:* [Heal a Service With No Endpoints](../projects/kubernetes/heal-a-service-with-no-endpoints.md) *(Debug)*

## Stage 3 - Configuration

Getting config and secrets into a pod, correctly.

### ConfigMaps
- *What:* Mounting config into a pod and what happens when the reference is wrong.
- *Why it matters:* ConfigMap/Secret references are validated lazily - the Deployment applies cleanly even with a bad name, then the pod can't start.
- *Prove it:* [Reconnect a Pod to Its ConfigMap](../projects/kubernetes/reconnect-a-pod-to-its-configmap.md) *(Debug)*

## Stage 4 - Authoring Workloads

Stop fixing other people's manifests and write your own, correctly.

### Write a production Deployment
- *What:* Authoring a Deployment + Service from spec - replicas, rolling update, resource limits, a readiness probe.
- *Why it matters:* Manifest-from-spec is the day-one task at every platform job. The signal is knowing what to add even when the spec doesn't say.
- *Prove it:* [Write a Production Kubernetes Deployment](../projects/kubernetes/write-a-production-deployment.md) *(Build)*

## Stage 5 - Health & Probes

How Kubernetes knows a pod is ready to serve and still alive.

### Liveness & readiness probes
- *What:* Adding readiness (route traffic only when ready) and liveness (restart when hung) probes.
- *Why it matters:* Without readiness, a Service hits a pod before the app serves - every deploy throws errors. Without liveness, a hung process sits there forever.
- *Prove it:* [Add Liveness and Readiness Probes](../projects/kubernetes/add-liveness-and-readiness-probes.md) *(Harden)*

## Stage 6 - Rollouts & Updates

Shipping a new version without dropping traffic.

### Rolling updates
- *What:* Bumping an image and watching the rollout drain old replicas as new ones come up.
- *Why it matters:* Zero-downtime rollout is the baseline for any serious deploy. Knowing how to watch `kubectl rollout status` means you ship with confidence.
- *Prove it:* [Release a New Image With a Rolling Update](../projects/kubernetes/release-with-a-rolling-update.md) *(Build)*

## Stage 7 - Autoscaling

Matching capacity to load instead of paying for peak 24/7.

### Horizontal Pod Autoscaler
- *What:* Writing an HPA that scales replicas on CPU (or a custom metric) between a min and max.
- *Why it matters:* Static replica counts force you to over-provision for peak or drop requests. HPA is the cheapest way to get elasticity.
- *Prove it:* [Configure an HPA for Autoscaling](../projects/kubernetes/configure-an-hpa-for-autoscaling.md) *(Build)*

## Stage 8 - Packaging

Templating and shipping an app as a reusable unit.

### Helm charts
- *What:* The chart layout - `Chart.yaml`, `values.yaml`, `templates/` - and values-driven manifests.
- *Why it matters:* Helm is the de-facto Kubernetes package format. Knowing the chart layout is interview table stakes for any Kubernetes role.
- *Prove it:* [Write and Lint a Helm Chart](../projects/kubernetes/write-and-lint-a-helm-chart.md) *(Build)*

## Stage 9 - Senior: Security & Reliability

Production-grade workloads - locked down and resilient.

### Default-deny networking
- *What:* A NetworkPolicy that denies all ingress by default, so traffic flows only where allowed.
- *Why it matters:* Kubernetes defaults to allow - any pod can reach any other by IP. Default-deny ingress is the foundation of zero-trust networking.
- *Prove it:* [Lock Down a Namespace With a NetworkPolicy](../projects/kubernetes/lock-down-a-namespace-with-networkpolicy.md) *(Harden)*

### Zero-downtime rollouts
- *What:* The full recipe - RollingUpdate with `maxUnavailable: 0`, a readiness probe, a preStop drain hook, and a grace period.
- *Why it matters:* "Why do we get 502s every deploy?" is a classic ticket, and the answer is almost always one of these four settings missing.
- *Prove it:* [Configure a Zero-Downtime Rolling Update](../projects/kubernetes/configure-a-zero-downtime-rolling-update.md) *(Build/Design)*

### Pod hardening
- *What:* A `securityContext` - non-root, no privilege escalation, read-only root filesystem, dropped capabilities.
- *Why it matters:* Pod Security Standards (restricted) and most audits require exactly these fields. It's the highest-signal, lowest-effort hardening on any workload.
- *Prove it:* [Harden a Pod With a securityContext](../projects/kubernetes/harden-a-pod-with-securitycontext.md) *(Harden)*

---

## Where you are on the path

| Stage | You can... | Hiring level |
|-------|-----------|--------------|
| 1-3 | Debug the common pod, service, and config failures | Junior |
| 4-5 | Author correct Deployments with health checks | Junior → Mid |
| 6-8 | Ship rollouts, autoscaling, and Helm packaging | Mid |
| 9 | Harden and design workloads for production | Senior |

## Build it for real

Every project linked above is a live ticket on [HeyDevJob](https://heydevjob.com/kubernetes) - a real broken or empty cluster in a workspace where you run `kubectl` from your browser. The junior tier is free, no card, no setup. Each one you complete lands on a portfolio you can show.

**Start your portfolio →** [heydevjob.com/kubernetes](https://heydevjob.com/kubernetes)
