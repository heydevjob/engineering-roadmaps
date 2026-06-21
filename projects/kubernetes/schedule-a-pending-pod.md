[Home](../../README.md) › [Kubernetes Projects](README.md) › **Schedule a Pending Pod**

# Schedule a Pending Kubernetes Pod

`Kubernetes` · `🟢 Junior` · `🔍 Debug` · `Ticket K8-104`

**Skills** — kubectl · scheduling · nodeSelector · debugging

> The pod has been `Pending` for ten minutes. It's not crashing, not pulling - the scheduler simply can't place it anywhere, and it'll sit there silently forever until you read why.

> [!NOTE]
> **The scenario**
> The `web` Deployment's pod is stuck in `Pending` because the scheduler can't fit it on any node. You read the events from `kubectl describe pod` to see which constraint is blocking placement, then fix or remove the impossible `nodeSelector` so the pod gets scheduled.

## 🧩 The code

The scheduling constraint in `deployment.yaml`:

```yaml
spec:
  nodeSelector: {disktype: ssd}
```

The `nodeSelector` requires a node labelled `disktype=ssd`, but no node carries that label - so no node matches.

The symptom:

```text
$ kubectl get pods
NAME        READY   STATUS    RESTARTS
web-xxxx    0/1     Pending   0
$ kubectl describe pod -l app=web
# Events: 0/N nodes available: didn't match Pod's node affinity/selector
```

## 🛠️ How you'll approach it

1. **Recognize silent failure.** `Pending` produces no logs and no crash - the only signal is in the scheduler's events.
2. **Read the reason.** `kubectl describe pod` Events say nodes "didn't match Pod's node affinity/selector."
3. **Fix the constraint.** Remove the impossible `nodeSelector` (or label a node to match) so the scheduler has somewhere to place it.
4. **Confirm placement.** The pod transitions from `Pending` to `Running`.

## 🎓 What you'll learn

- Why a `Pending` pod is a scheduling problem, not a runtime one
- Reading the scheduler's "didn't fit" reasons from events
- How `nodeSelector` / affinity constrain placement
- That `Pending` fails silently - you must know to look

## 🏆 What it proves

You can debug a pod the scheduler refuses to place - a failure mode that gives no logs and no crash, so it's pure events-reading skill.

> [!TIP]
> **Resume-ready** — *Diagnosed a Pending pod to an unsatisfiable nodeSelector from scheduler events and corrected the Deployment so the pod scheduled.*

## 🗺️ On the roadmap

Part of the [Kubernetes Engineer Roadmap](../../roadmaps/kubernetes.md) - **Stage 1: The Object Model & Debugging Pods** → Pending pods & scheduling.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **K8-104** on HeyDevJob - the real Pending pod above, in a cloud workspace where you run kubectl from your browser. Free on the junior tier, no card, no setup.
> [Schedule a Pending Kubernetes Pod on HeyDevJob →](https://heydevjob.com/kubernetes)

**Explore Kubernetes** · [📍 Roadmap](../../roadmaps/kubernetes.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/kubernetes.md) · [✅ Checklist](../../checklists/kubernetes.md)

[◀ Prev: Fix a Kubernetes ImagePullBackOff](fix-a-kubernetes-imagepullbackoff.md) · [Next: Heal a Service With No Endpoints ▶](heal-a-service-with-no-endpoints.md)
