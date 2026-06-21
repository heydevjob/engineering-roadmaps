# kubectl Cheatsheet

The commands you actually reach for when a pod won't start, a rollout stalls, or you need to know why traffic isn't reaching a service. Grouped by the job in front of you, beginner to advanced.

## Get & describe resources

```bash
kubectl get pods                                  # list pods in the current namespace
kubectl get pods -o wide                          # add node, pod IP, and assigned node
kubectl get pods -A                               # every pod across all namespaces
kubectl get pods --show-labels                    # show labels so you can filter on them
kubectl get pods -l app=web                       # filter by label selector
kubectl get all                                   # pods, services, deployments, replicasets at once
kubectl describe pod <pod>                         # full state: events, conditions, container status
kubectl get pod <pod> -o yaml                      # the live manifest as the API sees it
kubectl get pod <pod> -o jsonpath='{.status.podIP}' # pull one field out cleanly
kubectl get events --sort-by=.lastTimestamp        # recent cluster events, newest last
```

## Pods & logs

```bash
kubectl logs <pod>                                # stdout/stderr from the pod's main container
kubectl logs <pod> -f                             # stream logs live (like tail -f)
kubectl logs <pod> -c <container>                 # a specific container in a multi-container pod
kubectl logs <pod> --previous                     # logs from the last crashed instance
kubectl logs <pod> --tail=100                     # last 100 lines only
kubectl logs -l app=web --max-log-requests=10     # logs from all pods matching a label
kubectl top pod                                   # live CPU/memory per pod (needs metrics-server)
```

## Exec & port-forward

```bash
kubectl exec -it <pod> -- bash                    # shell into the pod's main container
kubectl exec -it <pod> -c <container> -- sh       # shell into a named container
kubectl exec <pod> -- env                         # run a one-off command, no TTY
kubectl port-forward <pod> 8080:80                # forward localhost:8080 to pod port 80
kubectl port-forward svc/web 8080:80              # forward through a service instead
kubectl cp <pod>:/path/file ./file                # copy a file out of a pod
kubectl cp ./file <pod>:/path/file                # copy a file into a pod
```

## Deployments & rollouts

```bash
kubectl get deployments                           # list deployments and their ready counts
kubectl rollout status deployment/web             # wait for a rollout to finish (or hang)
kubectl rollout history deployment/web            # list revisions you can roll back to
kubectl rollout restart deployment/web            # restart pods without changing the spec
kubectl rollout undo deployment/web               # roll back to the previous revision
kubectl rollout undo deployment/web --to-revision=3 # roll back to a specific revision
kubectl set image deployment/web web=nginx:1.27   # update one container's image
kubectl rollout pause deployment/web              # freeze rollout to batch several edits
kubectl rollout resume deployment/web             # resume a paused rollout
```

## Scaling

```bash
kubectl scale deployment/web --replicas=5         # set replica count manually
kubectl scale deployment/web --replicas=0         # scale to zero to stop a workload
kubectl autoscale deployment/web --min=2 --max=10 --cpu-percent=70 # add an HPA
kubectl get hpa                                   # check current vs target replicas
```

## Config & secrets

```bash
kubectl get configmaps                            # list configmaps
kubectl create configmap app-cfg --from-file=app.conf # build a configmap from a file
kubectl get secret <name> -o jsonpath='{.data.password}' | base64 -d # decode a secret value
kubectl create secret generic db --from-literal=password=s3cr3t # create a secret inline
kubectl edit configmap app-cfg                    # edit a configmap in place
```

## Namespaces & context

```bash
kubectl get namespaces                            # list namespaces
kubectl get pods -n kube-system                   # target a specific namespace
kubectl config current-context                    # which cluster/user you're pointed at
kubectl config get-contexts                       # all contexts in your kubeconfig
kubectl config use-context <name>                 # switch clusters
kubectl config set-context --current --namespace=app # set a default namespace for this context
```

## Debugging

```bash
kubectl describe pod <pod>                         # start here: the Events section names the failure
kubectl get pod <pod> -o jsonpath='{.status.containerStatuses[0].state}' # exact wait reason
kubectl get events --field-selector involvedObject.name=<pod> # events for one object
```

CrashLoopBackOff - the container keeps starting and dying:

```bash
kubectl logs <pod> --previous                     # read the crashed instance's logs (the real cause)
kubectl describe pod <pod>                          # check restart count and last exit code
```

ImagePullBackOff - the image can't be fetched:

```bash
kubectl describe pod <pod>                          # the Events line names the registry/auth error
kubectl get pod <pod> -o jsonpath='{.spec.containers[0].image}' # confirm the image tag is right
```

Pending - the scheduler can't place the pod:

```bash
kubectl describe pod <pod>                          # "Insufficient cpu/memory" or taint mismatch shows here
kubectl get nodes -o wide                           # check node capacity and readiness
kubectl describe node <node>                        # allocatable vs requested resources
```

## Apply & delete

```bash
kubectl apply -f manifest.yaml                    # create or update from a file
kubectl apply -f ./k8s/                           # apply every manifest in a directory
kubectl diff -f manifest.yaml                     # preview what apply would change
kubectl delete -f manifest.yaml                   # delete what's defined in a file
kubectl delete pod <pod>                           # delete a pod (a controller will recreate it)
kubectl delete pod <pod> --grace-period=0 --force  # force-remove a stuck/terminating pod
kubectl apply -f manifest.yaml --dry-run=server   # validate against the API without applying
```

## Services & networking

```bash
kubectl get svc                                   # list services and their cluster IPs/ports
kubectl get endpoints <svc>                        # which pod IPs back the service (empty = no match)
kubectl describe svc <svc>                          # selector, ports, and endpoints in one view
kubectl expose deployment/web --port=80 --target-port=8080 # create a service for a deployment
kubectl get ingress                               # list ingress rules and hosts
kubectl run tmp --image=busybox -it --rm -- sh    # throwaway pod to test in-cluster DNS/connectivity
# from inside that pod:
wget -qO- http://web.app.svc.cluster.local        # resolve and hit a service by DNS name
```

## Practice it live

Reading about `kubectl describe` is not the same as staring at a real CrashLoopBackOff and fixing it. On [HeyDevJob](https://heydevjob.com) these commands run in real cloud workspaces with genuinely broken systems - a stalled rollout, a service with no endpoints, a pod stuck Pending - that you debug from your browser. Free to start, no card, no setup. [Practice on HeyDevJob →](https://heydevjob.com)
