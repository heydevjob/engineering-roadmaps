# Lock Down a Namespace With a NetworkPolicy

**Role:** Kubernetes Engineer · **Level:** Senior · **Type:** Harden · **Skills:** NetworkPolicy, security, zero-trust

> By default, any pod in the namespace can talk to any other pod by IP - the network is wide open. You're writing the default-deny NetworkPolicy that flips it shut, so traffic only flows where you explicitly allow it.

## The scenario

Any pod in the namespace can currently reach any other pod - Kubernetes defaults to allow. You write a `NetworkPolicy` that implements default-deny ingress: it matches all pods (`podSelector: {}`), declares `policyTypes: [Ingress]`, and has no ingress rules, so all inbound pod traffic is implicitly denied.

## The code

The policy template you author (`networkpolicy.yaml`):

```yaml
# TODO: NetworkPolicy that default-denies all ingress to all pods.
# Required spec:
#   apiVersion: networking.k8s.io/v1
#   podSelector: {}
#   policyTypes: [Ingress]
#   (no ingress rules -> implicit deny-all)
```

The starting state:

```text
# no NetworkPolicy exists -> any pod can reach any other pod by IP
```

## How you'll approach it

1. **Understand the implicit-deny shape.** A NetworkPolicy that selects pods and declares `Ingress` but lists *no* ingress rules denies all inbound traffic to those pods - that's the trick.
2. **Select everything.** `podSelector: {}` matches every pod in the namespace.
3. **Declare the type, omit the rules.** `policyTypes: [Ingress]` with no `ingress:` block = deny all inbound.
4. **Apply and reason about it.** From here you'd add explicit allow policies for the traffic that *should* flow - default-deny first, allowlist second.

## What you'll learn

- Why Kubernetes networking is default-allow and why that's a risk
- The implicit-deny NetworkPolicy shape (`podSelector: {}` + `policyTypes` + no rules)
- The zero-trust pattern: deny by default, allow explicitly
- That a NetworkPolicy without rules is a deny, not a no-op

## What it proves

You can implement the foundational zero-trust networking control - default-deny ingress - the starting point of any real Kubernetes network-security posture.

> Resume-ready: *Authored a default-deny ingress NetworkPolicy to lock down a namespace, establishing a zero-trust baseline where traffic flows only where explicitly allowed.*

## On the roadmap

Part of the [Kubernetes Engineer Roadmap](../../roadmaps/kubernetes.md) - **Stage 9: Senior - Security & Reliability** → Default-deny networking.

---

**Build it for real.** This is ticket **K8-303** on HeyDevJob - the real wide-open namespace above, in a cloud workspace where you run kubectl from your browser. Each fix lands on a portfolio you can show. [Lock Down a Namespace With a NetworkPolicy on HeyDevJob →](https://heydevjob.com/kubernetes)
