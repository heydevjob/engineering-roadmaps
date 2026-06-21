[Home](../../README.md) › [Kubernetes Projects](README.md) › **Write and Lint a Helm Chart**

# Write and Lint a Helm Chart

`Kubernetes` · `🟡 Mid` · `🔨 Build` · `Ticket K8-203`

**Skills** — Helm · templating · charts · values

> Copy-pasting manifests per environment doesn't scale. You're packaging the `web` Deployment as a Helm chart - image tag and replica count pulled from `values.yaml` - and validating it with `helm lint` and `helm template`.

> [!NOTE]
> **The scenario**
> You create a minimal Helm chart at `chart/` with three files: `Chart.yaml` (metadata), `values.yaml` (image, tag, replicaCount), and `templates/deployment.yaml` (a Deployment that reads `.Values.*`). You validate with `helm lint chart/` and render with `helm template web chart/`.

## 🧩 The code

The scaffold you fill in:

```text
# Chart.yaml      -> TODO: apiVersion, name, version, appVersion
# values.yaml     -> TODO: image, tag, replicaCount
# templates/deployment.yaml
#                 -> TODO: a Deployment that reads image, tag, replicaCount
#                    from .Values
```

(Build-from-scratch ticket: the scaffold files contain TODOs; you author the chart.)

## 🛠️ How you'll approach it

1. **Write `Chart.yaml`.** The metadata: `apiVersion`, `name`, `version`, `appVersion` - the chart's identity.
2. **Write `values.yaml`.** The knobs: `image`, `tag`, `replicaCount` - the values you'll template.
3. **Template the Deployment.** In `templates/deployment.yaml`, reference `.Values.image`, `.Values.tag`, `.Values.replicaCount` so one chart serves many configs.
4. **Validate.** `helm lint chart/` checks the structure; `helm template web chart/` renders the final manifest so you can see the values substituted.

## 🎓 What you'll learn

- The Helm chart layout: `Chart.yaml`, `values.yaml`, `templates/`
- Templating manifests with `.Values` so one chart serves many environments
- Validating with `helm lint` and previewing with `helm template`
- Why values-driven manifests beat copy-pasted YAML per environment

## 🏆 What it proves

You can package a workload as a Helm chart - the de-facto Kubernetes package format - which is table stakes for any Kubernetes role.

> [!TIP]
> **Resume-ready** — *Authored a values-driven Helm chart (Chart.yaml, values.yaml, templates) for a Deployment and validated it with helm lint and helm template.*

## 🗺️ On the roadmap

Part of the [Kubernetes Engineer Roadmap](../../roadmaps/kubernetes.md) - **Stage 8: Packaging** → Helm charts.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **K8-203** on HeyDevJob - the real chart scaffold above, in a cloud workspace where you run helm from your browser. Free on the junior tier, no card, no setup.
> [Write and Lint a Helm Chart on HeyDevJob →](https://heydevjob.com/kubernetes)

**Explore Kubernetes** · [📍 Roadmap](../../roadmaps/kubernetes.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/kubernetes.md) · [✅ Checklist](../../checklists/kubernetes.md)

[◀ Prev: Configure an HPA for Autoscaling](configure-an-hpa-for-autoscaling.md) · [Next: Lock Down a Namespace With a NetworkPolicy ▶](lock-down-a-namespace-with-networkpolicy.md)
