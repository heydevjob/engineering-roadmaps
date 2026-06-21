[Home](../../README.md) › [Kubernetes Projects](README.md) › **Heal a Service With No Endpoints**

# Heal a Kubernetes Service With No Endpoints

`Kubernetes` · `🟢 Junior` · `🔍 Debug` · `Ticket K8-102`

**Skills** — kubectl · services · selectors · debugging

> The pods are Running. The Service exists. But nothing can reach the app, because the Service's selector points at a label no pod has - so its endpoints list is empty. A one-character typo, a full outage.

> [!NOTE]
> **The scenario**
> A `web` Service has no endpoints, so nothing can reach it - even though the pods are Running. The Service's `selector` doesn't match the pods' labels. You compare the two, fix whichever is wrong so they align, and watch the endpoints repopulate.

## 🧩 The code

The Service selector in `deployment.yaml`:

```yaml
spec:
  selector:
    app: webb
```

The selector says `app: webb`, but the pods are labelled `app: web` (one `b`) - so the Service matches zero pods.

The symptom:

```text
$ kubectl get endpoints web
NAME   ENDPOINTS
web    <none>            # pods are Running, but the Service finds none
```

## 🛠️ How you'll approach it

1. **Check the endpoints.** `kubectl get endpoints web` returns `<none>` - the Service isn't selecting any pods.
2. **Compare labels.** `kubectl get pods --show-labels` shows `app=web`; the Service selector says `app=webb`. There's the mismatch.
3. **Align them.** Fix the selector (or the pod label) so they match exactly.
4. **Confirm.** `kubectl get endpoints web` now lists the pod IPs, and traffic flows.

## 🎓 What you'll learn

- How a Service finds pods by label selector
- Why an empty endpoints list means a selector/label mismatch
- Reading and comparing labels with `--show-labels`
- That a Running pod and a reachable pod are two different things

## 🏆 What it proves

You can debug the #2 "the service is down" cause - a selector that matches nothing - which is pure object-model reasoning, not luck.

> [!TIP]
> **Resume-ready** — *Diagnosed a Service with no endpoints to a selector/label mismatch and realigned them, restoring traffic to a Running workload.*

## 🗺️ On the roadmap

Part of the [Kubernetes Engineer Roadmap](../../roadmaps/kubernetes.md) - **Stage 2: Services & Networking** → Services, selectors & endpoints.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **K8-102** on HeyDevJob - the real endpoint-less Service above, in a cloud workspace where you run kubectl from your browser. Free on the junior tier, no card, no setup.
> [Heal a Service With No Endpoints on HeyDevJob →](https://heydevjob.com/kubernetes)

**Explore Kubernetes** · [📍 Roadmap](../../roadmaps/kubernetes.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/kubernetes.md) · [✅ Checklist](../../checklists/kubernetes.md)

[◀ Prev: Schedule a Pending Kubernetes Pod](schedule-a-pending-pod.md) · [Next: Reconnect a Pod to Its ConfigMap ▶](reconnect-a-pod-to-its-configmap.md)
