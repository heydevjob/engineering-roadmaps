# Fix a Kubernetes CrashLoopBackOff

**Role:** Kubernetes Engineer · **Level:** Junior · **Type:** Debug · **Skills:** kubectl, pods, deployments, debugging

> The `web` pods keep dying and restarting - `CrashLoopBackOff`. The container fails the instant it starts. The whole case is in `describe` and `logs`, if you build the reflex to look there first.

## The scenario

A Deployment named `web` has pods stuck in `CrashLoopBackOff` because the container command fails immediately on startup. You read the pod's events (`kubectl describe pod`) and logs (`kubectl logs`) to find the real cause, fix the manifest, and re-apply so it starts cleanly.

## The code

The container command in `deployment.yaml`:

```yaml
containers:
- name: web
  image: busybox:1.36
  command: ["sleeep", "86400"]
```

`sleeep` (three e's) isn't a command - the container exits with code 127 the instant it starts.

The symptom:

```text
$ kubectl get pods
NAME                   READY   STATUS             RESTARTS
web-xxxx               0/1     CrashLoopBackOff   5
$ kubectl describe pod -l app=web   # Events: exit code 127 (command not found)
```

## How you'll approach it

1. **Describe first.** `kubectl describe pod -l app=web` - the Events show the container terminating with exit code 127.
2. **Read the logs.** `kubectl logs` (and `--previous` for the crashed instance) confirm the command isn't found.
3. **Fix the manifest.** Correct `sleeep` → `sleep` in the `command:` field.
4. **Re-apply and watch.** `kubectl apply -f deployment.yaml`, then `kubectl get pods -w` until it's `Running`.

## What you'll learn

- The `describe → logs → events` reflex for any failing pod
- Reading container exit codes (127 = command not found)
- Why `CrashLoopBackOff` is a symptom, not a cause
- Confirming recovery by watching the pod reach `Running`, not just applying

## What it proves

You can debug the most common Kubernetes failure state from the events and logs - the on-call reflex that defines a competent Kubernetes engineer.

> Resume-ready: *Diagnosed a CrashLoopBackOff to a bad container command via pod events and logs, corrected the Deployment, and restored the workload.*

## On the roadmap

Part of the [Kubernetes Engineer Roadmap](../../roadmaps/kubernetes.md) - **Stage 1: The Object Model & Debugging Pods** → CrashLoopBackOff.

---

**Build it for real.** This is ticket **K8-101** on HeyDevJob - the real CrashLooping Deployment above, in a cloud workspace where you run kubectl from your browser. Free on the junior tier, no card, no setup. [Fix a Kubernetes CrashLoopBackOff on HeyDevJob →](https://heydevjob.com/projects/kubernetes-crashloopbackoff-portfolio-project)
