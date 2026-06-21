# Kubernetes Engineer Interview Questions

These are the questions that decide whether you get hired to operate a cluster. They follow the arc every Kubernetes interview tests: read why an object is broken, author the manifest that does it right, then harden it for production. Each answer below is the tight, correct version - what a strong candidate says out loud. Where a question maps to a real broken system you can go fix in a live cloud workspace, there's a "Practice it live" pointer to the HeyDevJob ticket.

Work them top to bottom: the junior debugging questions build the reflexes the senior questions assume.

---

## Pods & the debugging reflex (Junior)

**A pod is in `CrashLoopBackOff`. Walk me through your first 60 seconds.**
`kubectl describe pod <name>` to read the Events, then `kubectl logs <name>` and `kubectl logs <name> --previous` to see why the last instance died. The describe output gives you the exit code and restart count; the logs give you the application error. CrashLoopBackOff is a symptom (the container keeps exiting and the kubelet backs off restarting it), never a cause - the cause is in the exit code and logs. Confirm recovery by watching the pod reach `Running`, not just by re-applying.

Practice it live: [Fix a Kubernetes CrashLoopBackOff](https://heydevjob.com/kubernetes)

**What does exit code 127 tell you versus exit code 1 versus 137?**
127 means "command not found" - usually a typo in the container `command` or a binary missing from the image. 1 is a generic application error, so go read the logs. 137 is 128+9 (SIGKILL), almost always the OOMKiller - the container exceeded its memory limit, which `kubectl describe pod` confirms with `Reason: OOMKilled`. The exit code points you at the right place to look before you read a single log line.

**A pod is stuck in `ImagePullBackOff`. What are the usual causes and how do you confirm?**
`kubectl describe pod` shows the pull error in Events. The common causes are a misspelled image name or tag, a tag that doesn't exist in the registry, a private registry with no `imagePullSecret`, or a registry that's unreachable. The fix is almost always correcting the image reference and re-applying. A bad tag should never reach `kubectl apply` in the first place - reading the pull error is how you catch it in seconds.

Practice it live: [Fix a Kubernetes ImagePullBackOff](https://heydevjob.com/kubernetes)

**A pod sits in `Pending` forever. Where do you look?**
`kubectl describe pod` - the Events show the scheduler's "didn't fit" reason. Common causes: no node has enough CPU/memory to satisfy the pod's requests, a node selector or affinity rule matches nothing, an unsatisfied taint/toleration, or no node with the required volume. Pending failures are silent - nothing crashes, the pod just never gets placed - so you have to know to read the scheduler events rather than waiting.

Practice it live: [Schedule a Pending Kubernetes Pod](https://heydevjob.com/kubernetes)

**What's the difference between a Pod, a ReplicaSet, and a Deployment?**
A Pod is the smallest deployable unit - one or more co-located containers sharing a network namespace and storage. A ReplicaSet keeps a fixed number of identical pod replicas running. A Deployment manages ReplicaSets to give you declarative rolling updates and rollbacks: changing the pod template creates a new ReplicaSet and shifts replicas over gradually. You almost never create ReplicaSets directly - you create Deployments and let them manage the ReplicaSets.

## Services & networking (Junior)

**A Service is up but no traffic reaches the pods. What's your first check?**
`kubectl get endpoints <service>`. An empty endpoints list means the Service's `selector` doesn't match any pod labels, so it has nowhere to send traffic and silently drops everything. This is one of the most common "the service is down" causes. Compare the Service's `spec.selector` against the pods' labels (`kubectl get pods --show-labels`) and reconcile them.

Practice it live: [Heal a Service With No Endpoints](https://heydevjob.com/kubernetes)

**Explain ClusterIP, NodePort, and LoadBalancer.**
ClusterIP (the default) gives the Service a stable virtual IP reachable only inside the cluster - the building block for internal traffic. NodePort opens a fixed port on every node and forwards it to the Service, used for basic external access or behind your own load balancer. LoadBalancer asks the cloud provider to provision an external load balancer pointing at the Service. In production you usually front HTTP traffic with an Ingress (or Gateway API) rather than a LoadBalancer per service.

**How does a Service find its pods, and how does that decouple from pod lifecycle?**
By label selector, not by IP. The Service watches for pods whose labels match its `selector` and maintains an Endpoints (or EndpointSlice) list of their IPs. Pods come and go - rescheduled, scaled, redeployed - and the endpoints list updates automatically, while the Service's ClusterIP and DNS name stay stable. That's why you target a Service by DNS name, never a pod IP.

**What does the cluster DNS name `mysvc.myns.svc.cluster.local` resolve to?**
The ClusterIP of Service `mysvc` in namespace `myns`. Inside the same namespace you can shorten it to `mysvc`; across namespaces use `mysvc.myns`. CoreDNS resolves it, and kube-proxy (or the CNI) load-balances the ClusterIP across the Service's healthy endpoints. For a headless Service (`clusterIP: None`) the name instead resolves to the individual pod IPs.

## Configuration & secrets (Junior → Mid)

**A Deployment applies cleanly but the pod won't start, citing a ConfigMap. Why?**
ConfigMap and Secret references are validated lazily. The Deployment applies fine even with a wrong ConfigMap name; the failure only surfaces when the kubelet tries to start the pod and can't mount or env-load the missing reference - you'll see `CreateContainerConfigError`. Check `kubectl describe pod` for the exact missing object, then fix the reference name or create the ConfigMap.

Practice it live: [Reconnect a Pod to Its ConfigMap](https://heydevjob.com/kubernetes)

**ConfigMap versus Secret - what's the real difference?**
Functionally they're nearly identical key/value objects you mount as files or inject as env vars. Secrets are base64-encoded (not encrypted) and treated specially: they can be encrypted at rest via an EncryptionConfiguration, RBAC is usually tighter on them, and they're meant for credentials. Base64 is encoding, not security, so never treat a Secret as safe to commit. Use ConfigMaps for non-sensitive config, Secrets for credentials.

**If you change a mounted ConfigMap, does the pod pick it up?**
A ConfigMap mounted as a volume updates in the pod's filesystem automatically (eventually, with a sync delay), but the application only sees it if it re-reads the file - most apps read config once at startup, so they don't. ConfigMap values injected as environment variables never update on a running pod. The reliable pattern is to roll the Deployment when config changes (for example by stamping a config hash into the pod template annotation) so pods restart with the new values.

## Authoring workloads (Junior → Mid)

**Write me a production Deployment. What goes in beyond the spec?**
The interviewer is testing what you add when the spec is silent. A production Deployment has explicit `replicas` (more than 1 for availability), resource `requests` and `limits` (so the scheduler can place it and it can't starve neighbors), a readiness probe (so a Service only routes to ready pods), a liveness probe (so hung pods restart), and a sane `RollingUpdate` strategy. Labels and selectors must match, and the image should be a pinned tag, not `latest`.

Practice it live: [Write a Production Kubernetes Deployment](https://heydevjob.com/kubernetes)

**Why is `image: myapp:latest` a problem in production?**
`latest` is a mutable tag, so two pods created at different times can run different code, and a rollback has nothing concrete to roll back to. With `imagePullPolicy: Always` it also re-pulls on every restart. Pin an immutable tag or, better, a digest (`myapp@sha256:...`) so every replica runs exactly the bits you tested and rollouts/rollbacks are deterministic.

**What do resource `requests` and `limits` actually control?**
`requests` are what the scheduler uses to place the pod - it guarantees that much CPU/memory is reserved on the node. `limits` are the ceiling: exceeding the memory limit gets the container OOMKilled, exceeding the CPU limit gets it throttled (not killed). Setting requests too low overcommits the node; setting no limits lets one pod starve others. The gap between request and limit defines the pod's QoS class (Guaranteed, Burstable, or BestEffort).

## Health & probes (Mid)

**Difference between a liveness and a readiness probe - and what breaks if you skip each?**
Readiness gates traffic: while it fails, the pod is pulled from Service endpoints but not restarted - used for "warming up" or a dependency being down. Liveness gates restarts: while it fails past its threshold, the kubelet kills and restarts the container - used for a hung or deadlocked process. Skip readiness and the Service routes to pods that can't serve yet, so every deploy throws errors. Skip liveness and a hung process sits there forever doing nothing.

Practice it live: [Add Liveness and Readiness Probes](https://heydevjob.com/kubernetes)

**What's a startup probe for, and why can a liveness probe alone be dangerous on a slow-starting app?**
If an app takes 60 seconds to boot but the liveness probe fails after 10, the kubelet keeps killing it before it ever starts - a permanent CrashLoop you caused yourself. A startup probe runs first and disables the liveness/readiness checks until the app has booted, then hands off. It lets you keep tight liveness timings for steady state without murdering a slow cold start.

## Rollouts & updates (Mid)

**Walk me through a rolling update. How do you watch it and roll it back?**
Change the pod template (usually the image) and apply. The Deployment spins up a new ReplicaSet and shifts replicas over according to `maxSurge`/`maxUnavailable`, draining old pods as new ones go Ready. Watch it with `kubectl rollout status deployment/<name>`. If it goes wrong, `kubectl rollout undo deployment/<name>` reverts to the previous ReplicaSet. The readiness probe is what makes this safe - without it, the rollout marks pods "available" before they can serve.

Practice it live: [Release a New Image With a Rolling Update](https://heydevjob.com/kubernetes)

**`maxSurge` and `maxUnavailable` - what do they trade off?**
`maxSurge` is how many extra pods above the desired count you can create during the rollout; `maxUnavailable` is how many existing pods can be down at once. Higher surge = faster rollout but more peak capacity (and cost); higher unavailable = faster but less serving capacity mid-roll. For zero-downtime you set `maxUnavailable: 0` (never drop below full capacity) and `maxSurge: 1` or more (bring up the new pod first, then retire an old one).

## Autoscaling (Mid)

**How does the Horizontal Pod Autoscaler decide how many replicas to run?**
The HPA reads a metric (CPU utilization by default, via the metrics-server, or custom/external metrics) and scales replica count between `minReplicas` and `maxReplicas` to keep the metric near a target. The core formula is roughly `desired = ceil(current * (currentMetric / targetMetric))`. It requires the pod to have a resource `request` set for percentage-based CPU targets to mean anything, and it has stabilization windows to avoid flapping.

Practice it live: [Configure an HPA for Autoscaling](https://heydevjob.com/kubernetes)

**HPA versus VPA versus Cluster Autoscaler - which solves what?**
HPA changes the number of pods in response to load. VPA (Vertical Pod Autoscaler) changes the CPU/memory requests of pods, right-sizing them. Cluster Autoscaler changes the number of nodes when pods can't be scheduled for lack of capacity. They operate at different layers: HPA and Cluster Autoscaler compose well (more pods trigger more nodes); HPA and VPA on the same metric conflict and shouldn't both target CPU.

## Packaging with Helm (Mid)

**Describe a Helm chart's layout and what `values.yaml` is for.**
A chart is a directory with `Chart.yaml` (name, version, metadata), `values.yaml` (default configuration), and `templates/` (the manifests with Go templating). Templates reference `.Values.foo`, so the same chart renders different manifests per environment by overriding values. `helm install` renders templates + values into real Kubernetes objects and tracks them as a release; `helm upgrade`/`rollback` version that release. `helm lint` and `helm template` catch errors before anything hits the cluster.

Practice it live: [Write and Lint a Helm Chart](https://heydevjob.com/kubernetes)

**Why use Helm over plain `kubectl apply -f`?**
Templating (one chart, many environments via values), release management (versioned installs with upgrade and rollback), and packaging/sharing (a chart is a distributable unit with a dependency graph). Plain manifests give you none of that - you'd copy-paste per environment and have no atomic rollback. The trade-off is templating complexity; for simple cases Kustomize (overlays, no templating) is a lighter alternative.

## Senior: networking, reliability & security

**Kubernetes networking defaults to allow. How do you implement zero-trust, and what's the implicit-deny trick?**
By default any pod can reach any other pod by IP. You flip that with a NetworkPolicy that selects all pods (`podSelector: {}`), declares `policyTypes: [Ingress]`, and lists no ingress rules - a policy with no rules is a deny, not a no-op, so all inbound pod traffic is implicitly denied. From that default-deny baseline you add explicit allow policies for exactly the traffic that should flow. Deny first, allowlist second. Note this requires a CNI that enforces NetworkPolicy (Calico, Cilium); some CNIs ignore them entirely.

Practice it live: [Lock Down a Namespace With a NetworkPolicy](https://heydevjob.com/kubernetes)

**"We get 502s on every deploy." What four settings fix a zero-downtime rollout?**
Almost always one of these is missing. `maxUnavailable: 0` so you never drop below full capacity mid-roll. A readiness probe so the Service only routes to pods that can actually serve. A `preStop` hook (a short sleep or drain) so a terminating pod finishes in-flight requests before the proxy stops sending it traffic. And a `terminationGracePeriodSeconds` long enough for the drain to complete. The 502s come from the gap between "pod terminating" and "endpoints updated" - these settings close it.

Practice it live: [Configure a Zero-Downtime Rolling Update](https://heydevjob.com/kubernetes)

**What goes in a hardened `securityContext`, and why does an audit ask for exactly those fields?**
`runAsNonRoot: true` with a non-zero `runAsUser`, `allowPrivilegeEscalation: false`, `readOnlyRootFilesystem: true`, and `capabilities: { drop: ["ALL"] }`, plus `seccompProfile: RuntimeDefault`. These are exactly what the Pod Security Standards "restricted" profile enforces and what most compliance audits check, because they shrink the blast radius of a container compromise: a non-root process with no escalation, no write access to its own filesystem, and no Linux capabilities can do very little even if breached. It's the highest-signal, lowest-effort hardening on any workload.

Practice it live: [Harden a Pod With a securityContext](https://heydevjob.com/kubernetes)

**How does Pod Security Admission work, and what are the three levels?**
PSA is a built-in admission controller that enforces the Pod Security Standards at the namespace level via labels. The three profiles are `privileged` (no restrictions), `baseline` (blocks known privilege escalations like hostNetwork and privileged containers), and `restricted` (the hardened profile - non-root, dropped caps, no privilege escalation). You set `pod-security.kubernetes.io/enforce: restricted` on a namespace and any pod violating it is rejected at admission. It replaced the deprecated PodSecurityPolicy.

**How would you give a pod least-privilege access to the Kubernetes API?**
A dedicated ServiceAccount bound by RBAC to only the verbs and resources it needs, scoped to a single namespace with a Role + RoleBinding rather than a cluster-wide ClusterRole. Never mount the default ServiceAccount token if the pod doesn't call the API (`automountServiceAccountToken: false`). The principle is the same as everywhere else: name the exact resources and verbs, namespace-scope it, and audit it - a pod that only needs to read ConfigMaps in its own namespace should not be able to list Secrets cluster-wide.

**A namespace is stuck in `Terminating` and won't delete. What's going on?**
Almost always a finalizer. An object in the namespace (or the namespace itself) has a finalizer whose controller never completed - so Kubernetes blocks deletion waiting for cleanup that will never finish. Check `kubectl get namespace <ns> -o yaml` for `spec.finalizers` and the objects inside it for stuck finalizers. The real fix is to unblock or remove the controller; the blunt fix is to clear the finalizer, but that can orphan resources, so understand what it was protecting first.

---

## Build it for real

Reading the answer and shipping the fix are different skills, and interviewers can tell which one you have. Every question above maps to a real broken (or empty) cluster you can fix in a live cloud workspace on [HeyDevJob](https://heydevjob.com/kubernetes) - you run `kubectl` from your browser, no setup. The junior tier is free, no card. Each ticket you complete lands on a portfolio hiring managers can open and click.

**Start your portfolio →** [heydevjob.com/kubernetes](https://heydevjob.com/kubernetes)
