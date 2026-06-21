[Home](../../README.md) › [Kubernetes Projects](README.md) › **Fix a Kubernetes ImagePullBackOff**

# Fix a Kubernetes ImagePullBackOff

`Kubernetes` · `🟢 Junior` · `🔍 Debug` · `Ticket K8-103`

**Skills** — kubectl · images · registries · debugging

> The pod is stuck in `ImagePullBackOff`. It's not crashing - it can't even start, because the image tag it's reaching for doesn't exist. The error is right there in the events.

> [!NOTE]
> **The scenario**
> The `web` Deployment's pod is stuck in `ImagePullBackOff` because the image reference points to a non-existent tag. You read the events from `kubectl describe pod` to see the pull error, fix the image tag in the manifest, and re-apply so the pull succeeds.

## 🧩 The code

The image reference in `deployment.yaml`:

```yaml
containers:
- name: web
  image: busybox:9.99
  command: ["sleep", "86400"]
```

`busybox:9.99` is not a real tag in the registry - the kubelet can't pull it.

The symptom:

```text
$ kubectl get pods
NAME        READY   STATUS             RESTARTS
web-xxxx    0/1     ImagePullBackOff   0
$ kubectl describe pod -l app=web   # Events: failed to pull image "busybox:9.99"
```

## 🛠️ How you'll approach it

1. **Tell it apart from a crash.** `ImagePullBackOff` means the container never started - this is a pull problem, not a runtime one.
2. **Read the pull error.** `kubectl describe pod -l app=web` Events name the exact image and why the pull failed.
3. **Fix the tag.** Correct `busybox:9.99` to a real tag (e.g. `busybox:1.36`).
4. **Re-apply and confirm.** The pod pulls and reaches `Running`.

## 🎓 What you'll learn

- Telling an image-pull failure apart from a runtime crash
- Reading the pull error from pod events
- Why a misspelled tag applies cleanly but never runs
- Catching bad image references before they reach `kubectl apply`

## 🏆 What it proves

You can debug an `ImagePullBackOff` from the events - one of the three failure states every Kubernetes engineer reads on a daily basis.

> [!TIP]
> **Resume-ready** — *Diagnosed an ImagePullBackOff to an invalid image tag via pod events and corrected the Deployment to a valid reference.*

## 🗺️ On the roadmap

Part of the [Kubernetes Engineer Roadmap](../../roadmaps/kubernetes.md) - **Stage 1: The Object Model & Debugging Pods** → ImagePullBackOff.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **K8-103** on HeyDevJob - the real ImagePullBackOff above, in a cloud workspace where you run kubectl from your browser. Free on the junior tier, no card, no setup.
> [Fix a Kubernetes ImagePullBackOff on HeyDevJob →](https://heydevjob.com/projects/kubernetes-imagepullbackoff-portfolio-project)

**Explore Kubernetes** · [📍 Roadmap](../../roadmaps/kubernetes.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/kubernetes.md) · [✅ Checklist](../../checklists/kubernetes.md)

[◀ Prev: Fix a Kubernetes CrashLoopBackOff](fix-a-kubernetes-crashloopbackoff.md) · [Next: Schedule a Pending Kubernetes Pod ▶](schedule-a-pending-pod.md)
