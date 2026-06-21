[Home](../README.md) › **Kubernetes Checklist**

# Kubernetes Engineer Job-Readiness Checklist

`Checklist` · `9 stages` · `Tick what you can prove`

This is the concrete skill inventory that gets you hired to operate a cluster - not "understand Kubernetes" but "I can debug this failure, author that manifest, harden this workload." It follows the [Kubernetes Engineer Roadmap](../roadmaps/kubernetes.md) stage by stage, junior to senior. Tick what you can already do unaided. Whatever you can't tick yet is your next ticket: every item maps to a real broken system you can fix in a live cloud workspace on [HeyDevJob](https://heydevjob.com/kubernetes).

Don't tick from memory. Tick it if you could do it right now in a terminal, against a real cluster, without looking it up.

## 🐞 Stage 1 - The object model & debugging pods (Junior)

- [ ] Run the `describe → logs → events` reflex on any failing pod without thinking about it
- [ ] Read a container exit code and know where it points (127 = command not found, 137 = OOMKilled, 1 = app error)
- [ ] Diagnose a `CrashLoopBackOff` from pod events and logs, including `kubectl logs --previous`
- [ ] Diagnose an `ImagePullBackOff` (bad tag, missing image, registry auth) from the pull error
- [ ] Diagnose a `Pending` pod by reading the scheduler's "didn't fit" reason in Events
- [ ] Explain the difference between a Pod, a ReplicaSet, and a Deployment
- [ ] Use `kubectl get/describe/logs/exec` and label selectors (`-l app=web`) fluently
- [ ] Confirm a fix by watching a pod reach `Running`, not just by re-applying

## 🔌 Stage 2 - Services & networking (Junior)

- [ ] Check a Service's endpoints (`kubectl get endpoints`) and know an empty list means a selector mismatch
- [ ] Reconcile a Service `selector` against pod labels to restore routing
- [ ] Explain ClusterIP vs NodePort vs LoadBalancer and when to use each
- [ ] Explain how a Service decouples from pod IPs via label selectors and EndpointSlices
- [ ] Resolve a service by cluster DNS name (`svc.namespace.svc.cluster.local`)
- [ ] Know why you target a Service by DNS name, never a pod IP

## 🗂️ Stage 3 - Configuration (Junior → Mid)

- [ ] Mount a ConfigMap as a volume and inject one as environment variables
- [ ] Diagnose a `CreateContainerConfigError` back to a missing/misnamed ConfigMap or Secret
- [ ] Explain why config references are validated lazily (Deployment applies, pod fails)
- [ ] Explain ConfigMap vs Secret and that base64 is encoding, not encryption
- [ ] Know that env-var config never live-updates and roll a Deployment to pick up changed config

## 📝 Stage 4 - Authoring workloads (Junior → Mid)

- [ ] Author a Deployment + Service from a spec, with matching labels and selectors
- [ ] Add explicit `replicas`, resource `requests`, and `limits` by default
- [ ] Pin an immutable image tag or digest instead of `latest`, and explain why
- [ ] Explain how requests drive scheduling and limits drive OOMKill/throttling
- [ ] Explain QoS classes (Guaranteed / Burstable / BestEffort) from requests vs limits
- [ ] Set a sensible `RollingUpdate` strategy on a Deployment

## ❤️ Stage 5 - Health & probes (Mid)

- [ ] Add a readiness probe that gates a pod out of Service endpoints until it can serve
- [ ] Add a liveness probe that restarts a hung container
- [ ] Explain exactly what breaks when each probe is missing
- [ ] Add a startup probe so a slow-booting app isn't killed before it's ready
- [ ] Tune `initialDelaySeconds`, `periodSeconds`, and `failureThreshold` for an app's real timing

## 🚀 Stage 6 - Rollouts & updates (Mid)

- [ ] Trigger a rolling update by bumping an image and watch it with `kubectl rollout status`
- [ ] Roll back a bad deploy with `kubectl rollout undo`
- [ ] Explain the `maxSurge` / `maxUnavailable` trade-off
- [ ] Explain why the readiness probe is what makes a rollout safe

## 📈 Stage 7 - Autoscaling (Mid)

- [ ] Write an HPA that scales replicas on CPU between `minReplicas` and `maxReplicas`
- [ ] Explain the HPA scaling formula and why a resource `request` is required for CPU targets
- [ ] Distinguish HPA vs VPA vs Cluster Autoscaler and which problem each solves
- [ ] Reason about stabilization windows and why HPAs flap without them

## 📦 Stage 8 - Packaging (Mid)

- [ ] Lay out a Helm chart (`Chart.yaml`, `values.yaml`, `templates/`) from scratch
- [ ] Parameterize manifests with `.Values` so one chart serves multiple environments
- [ ] Run `helm lint` and `helm template` to catch errors before applying
- [ ] Install, upgrade, and roll back a release, and explain why that beats `kubectl apply -f`
- [ ] Know when Kustomize (overlays, no templating) is the lighter choice

## 🛡️ Stage 9 - Senior: security & reliability

- [ ] Author a default-deny ingress NetworkPolicy (`podSelector: {}` + `policyTypes` + no rules)
- [ ] Add explicit allow NetworkPolicies on top of a default-deny baseline
- [ ] Know that NetworkPolicy enforcement depends on the CNI (Calico/Cilium vs none)
- [ ] Configure a zero-downtime rollout: `maxUnavailable: 0`, readiness probe, `preStop` drain, grace period
- [ ] Explain where deploy-time 502s come from (the endpoints-update gap) and how each setting closes it
- [ ] Harden a pod with a `securityContext`: non-root, no privilege escalation, read-only root FS, dropped capabilities
- [ ] Map those `securityContext` fields to the Pod Security Standards "restricted" profile
- [ ] Configure Pod Security Admission on a namespace (`enforce: restricted`)
- [ ] Grant a pod least-privilege API access with a namespace-scoped ServiceAccount + Role + RoleBinding
- [ ] Disable `automountServiceAccountToken` for pods that don't call the API
- [ ] Diagnose a namespace stuck in `Terminating` back to a stuck finalizer and reason about the safe fix

> [!IMPORTANT]
> **Can't tick it? Prove it.**
> Every unchecked box is a ticket. On [HeyDevJob](https://heydevjob.com/kubernetes) each item above is a real broken or empty cluster in a live cloud workspace - you run `kubectl` from your browser, no setup, no card on the junior tier. Fix it for real, and it lands on a portfolio you can show a hiring manager instead of a checkbox you ticked yourself.
>
> **Start your portfolio →** [heydevjob.com/kubernetes](https://heydevjob.com/kubernetes)

---

**Explore Kubernetes** · [📍 Roadmap](../roadmaps/kubernetes.md) · [🛠️ Projects](../projects/kubernetes/README.md) · [💬 Interview](../interview/kubernetes.md) · [✅ Checklist](kubernetes.md)
