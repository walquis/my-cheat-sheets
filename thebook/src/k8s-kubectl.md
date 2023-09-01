## Kubernetes

Handy alias: `k`
```
$ alias k='kubectl --kubeconfig ~/.kube/apprenticeship.tr-lab-chhq-1.yaml'

$ k config set-context --current --namespace=bg-helloworld-reference-impl-ns
```
Now I don't have to say "-n bg-helloworld-reference-impl-ns" after every command

```
# SSH to a running pod in a k8s cluster...

$ k get pods

NAME                           READY   STATUS    RESTARTS   AGE
codelabs-wl-77f89d845f-vr649   1/1     Running   0          24h

$ k get pod codelabs-wl-77f89d845f-vr649

$ k exec --stdin --tty codelabs-wl-77f89d845f-vr649 -- /bin/bash
```

See pod logs:
```
$ k logs reference-helloworld-wl-597c5744b4-wbwsc --all-containers
```

kubectl keeps its config in `~/.kube/config`.  Some top-level config objects:
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

Miscellaneous debugging:
```
$ kubectl config view   # See what kubectl thinks your config is
```
