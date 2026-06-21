[Home](../../README.md) › [Kubernetes Projects](README.md) › **Reconnect a Pod to Its ConfigMap**

# Reconnect a Pod to Its ConfigMap

`Kubernetes` · `🟢 Junior` · `🔍 Debug` · `Ticket K8-105`

**Skills** — kubectl · ConfigMap · debugging

> The Deployment applied without a complaint, then the pod refused to start - `CreateContainerConfigError`. It's reaching for a ConfigMap that doesn't exist by that name. Kubernetes only checked when it tried to build the container.

> [!NOTE]
> **The scenario**
> The `web` Deployment won't start - the pod fails with `CreateContainerConfigError` because it references a ConfigMap that doesn't exist. You read the events, list the ConfigMaps that actually exist in the namespace, and fix the reference in the manifest to match.

## 🧩 The code

The ConfigMap reference in `deployment.yaml`:

```yaml
containers:
- name: web
  image: busybox:1.36
  command: ["sleep", "86400"]
  envFrom:
  - configMapRef:
      name: app-config
```

It references `app-config`, but the ConfigMap defined in the namespace is actually named `app-cfg`.

The symptom:

```text
$ kubectl get pods
NAME        READY   STATUS                       RESTARTS
web-xxxx    0/1     CreateContainerConfigError   0
$ kubectl describe pod -l app=web   # Events: couldn't find ConfigMap "app-config"
```

## 🛠️ How you'll approach it

1. **Read the error.** `CreateContainerConfigError` plus the events point straight at a missing ConfigMap named `app-config`.
2. **List what exists.** `kubectl get configmaps` shows the real name is `app-cfg`.
3. **Fix the reference.** Update `configMapRef.name` to `app-cfg` and re-apply.
4. **Confirm.** The pod can now build its container and reaches `Running`.

## 🎓 What you'll learn

- Why a bad ConfigMap/Secret reference applies cleanly but fails at pod start
- Reading `CreateContainerConfigError` from events
- Listing namespace ConfigMaps to find the real name
- That Kubernetes validates these references lazily, not at apply time

## 🏆 What it proves

You understand that a manifest applying successfully doesn't mean it runs - and you can trace a config reference error to the missing object.

> [!TIP]
> **Resume-ready** — *Diagnosed a CreateContainerConfigError to a misnamed ConfigMap reference and corrected the Deployment so the pod started.*

## 🗺️ On the roadmap

Part of the [Kubernetes Engineer Roadmap](../../roadmaps/kubernetes.md) - **Stage 3: Configuration** → ConfigMaps.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **K8-105** on HeyDevJob - the real misreferenced ConfigMap above, in a cloud workspace where you run kubectl from your browser. Free on the junior tier, no card, no setup.
> [Reconnect a Pod to Its ConfigMap on HeyDevJob →](https://heydevjob.com/kubernetes)

**Explore Kubernetes** · [📍 Roadmap](../../roadmaps/kubernetes.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/kubernetes.md) · [✅ Checklist](../../checklists/kubernetes.md)

[◀ Prev: Heal a Service With No Endpoints](heal-a-service-with-no-endpoints.md) · [Next: Write a Production Kubernetes Deployment ▶](write-a-production-deployment.md)
