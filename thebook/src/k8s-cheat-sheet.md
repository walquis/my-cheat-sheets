## Kubernetes
```
# SSH to a running pod in a k8s cluster...

$ k get pods

NAME                           READY   STATUS    RESTARTS   AGE
codelabs-wl-77f89d845f-vr649   1/1     Running   0          24h

$ k get pod codelabs-wl-77f89d845f-vr649

$ k exec --stdin --tty codelabs-wl-77f89d845f-vr649 -- /bin/bash
```

kubectl keeps its config in ~/.kube/config.  Some top-level config objects:
- A list of clusters
- A list of users
- A list of contexts
- The current context
- Preferences

kubectl looking-around commands
```
$ kubectl get pods
$ kubectl describe pod <pod-id>
$ kubectl get namespaces
$ kubectl config current-context
```
