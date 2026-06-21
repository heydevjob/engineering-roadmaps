# Harden a Pod With a securityContext

**Role:** Kubernetes Engineer · **Level:** Senior · **Type:** Harden · **Skills:** kubectl, securityContext, non-root, hardening

> The `worker` runs as root, with a writable root filesystem and every Linux capability it was born with. If that container is ever compromised, the blast radius is huge. Four `securityContext` fields shrink it to almost nothing.

## The scenario

The `worker` Deployment runs as root with a writable root filesystem and full capabilities - a large attack surface. You add a container `securityContext` with `runAsNonRoot: true` + a `runAsUser`, `allowPrivilegeEscalation: false`, `readOnlyRootFilesystem: true`, and `capabilities.drop: ["ALL"]`.

## The code

The unhardened container in `deployment.yaml`:

```yaml
spec:
  containers:
  - name: worker
    image: busybox:1.36
    command: ["sleep", "86400"]
    # no securityContext -> runs as root, writable rootfs, all caps
```

The starting state:

```text
# worker runs as UID 0 (root), writable root filesystem, full Linux capabilities
```

## How you'll approach it

1. **Drop root.** `runAsNonRoot: true` and a `runAsUser` (e.g. 1000) so a compromise doesn't start with root.
2. **Block escalation.** `allowPrivilegeEscalation: false` so the process can't gain more privileges than it started with.
3. **Freeze the filesystem.** `readOnlyRootFilesystem: true` so an attacker can't write tools or modify the image at runtime.
4. **Drop capabilities.** `capabilities.drop: ["ALL"]` to remove every Linux capability the container doesn't explicitly need. Re-apply and confirm the pod still runs.

## What you'll learn

- The four high-signal `securityContext` hardening fields
- Why running as non-root and read-only rootfs shrink the attack surface
- Dropping all capabilities and adding back only what's needed
- What Pod Security Standards (restricted) and audits require

## What it proves

You can harden a workload to the standard security audits and Pod Security Standards demand - one of the highest-signal, lowest-effort security wins on any pod.

> Resume-ready: *Hardened a pod with a securityContext - non-root, no privilege escalation, read-only root filesystem, and all capabilities dropped - meeting restricted Pod Security Standards.*

## On the roadmap

Part of the [Kubernetes Engineer Roadmap](../../roadmaps/kubernetes.md) - **Stage 9: Senior - Security & Reliability** → Pod hardening.

---

**Build it for real.** This is ticket **K8-308** on HeyDevJob - the real root-running worker above, in a cloud workspace where you run kubectl from your browser. Each fix lands on a portfolio you can show. [Harden a Pod With a securityContext on HeyDevJob →](https://heydevjob.com/kubernetes)
