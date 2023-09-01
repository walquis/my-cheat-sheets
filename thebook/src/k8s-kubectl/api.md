# k8s API and kubectl
Kubectl is a command-line wrapper around the [k8s REST API](https://kubernetes.io/docs/reference/kubernetes-api/), which is actually many API's, organized into purpose-specific **API Groups**.  The `apiGroups` list in the yaml for an RBAC role definition corresponds to a component of the REST URL.  For instance, this `ClusterRole` resource definition. 


```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  # "namespace" is omitted since ClusterRoles are not namespaced; thus,
  # entities in various namespaces can be associated (or "bound") to the app-deployer role.
  name: app-deployer
rules:
- apiGroups: ["apps","networking.k8s.io",""]
  # at the HTTP level, the name of the resource for accessing Secret objects is "secrets"
  resources: ["pods", "deployments", "ingresses", "secrets", "services"]
  # These correspond mostly to HTTP verbs, although k8s implements "create" and "update" with PUT
  verbs: ["get", "watch", "list", "patch", "delete", "create", "update"]
```
(`""` represents the Core apiGroup, whose REST endpoints have no apiGroup component in their paths.  In the URL, as the [k8s api docs]("https://kubernetes.io/docs/reference/using-api/api-concepts/") put it, 
> core resources use `/api` instead of `/apis` and omit the GROUP path segment.

## Calling the k8s REST API directly with HTTPS
Or in other words, let's pretend to be a browser (like kubectl), and use `curl` to make HTTP calls.

[This post](https://iximiuz.com/en/posts/kubernetes-api-call-simple-http-client/) has a pretty good walk-through.

How about getting info for a specific pod, as described in the k8s pod reference under [Operations](https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/pod-v1/#Operations)?

Assuming you have some pods running, first "cheat" and get a pod name with `kubectl get pods`:
```
kubectl get pods
NAME                                   READY   STATUS    RESTARTS   AGE
blue-helloworld-wl-84c98bfbdf-29hh7    1/1     Running   0          25h
green-helloworld-wl-7fd747c8cb-h5cnx   1/1     Running   0          26h
```

Now, using the API reference, let's construct a URL.  You'll need these pieces of info:
- The pod name
- The namespace name
- The base URL, which is your cluster's value of `server` in your `~/.kube/config`.  From there, tack on the reference's indicated URL path, beginning with `/apis`.  For instance...
```
https://047C9C71F223E20B1299471649359259.sk1.us-east-2.eks.amazonaws.com/api/v1/namespaces/appr-bg-hw-deploy-refimpl-ns/pods/blue-helloworld-wl-84c98bfbdf-29hh7
```
(Note that `pods` is a core API resource; we can tell because it uses `/api` instead of `/apis` in its REST path, and that path has no `apiGroup` component).

You'll also need an authorization token associated with your user, or with a service account having sufficient permissions.

Postman is a great tool with which to poke at APIs.  I used it to generate this curl command:
```
token=aLongAuthorizationTokenStringAssociatedWithYourServiceAccount
curl -s --header "Authorization: Bearer $token" --cacert /tmp/ap-dev-use2-1.crt https://047C9C71F223E20B1299471649359259.sk1.us-east-2.eks.amazonaws.com/api/v1/namespaces/appr-bg-hw-deploy-refimpl-ns/pods/blue-helloworld-wl-84c98bfbdf-29hh7
```

Notice that I also passed a Certificate Authority (CA) cert into curl, so that it wouldn't fail with an SSL error.  This cert file came from the `certificate-authority-data` field in `~/.kube/config` for the cluster; because it's encoded, it required decoding first:

```
encoded_token=aLongCopyPastedCACertFromKubeSlashConfig
echo $encoded_token | base64 -d > /tmp/ap-dev-use2-1.crt
```
