# Labels, Selectors, Annotations

## Labels
### Needed for gluing k8s resources together
For instance, a `deployment` resource must define a label in its pod template metadata, so that a `service` resource can select pods by-label for routing traffic to them.

### Handy for getting organized - querying diverse k8s resources by-custom-label
For instance, a `pod` resource might belong to several categories of label:
```
labels:
  app: guestbook
  tier: backend
  role: master
  environment: qa
```
You can query pods by-label like this:
```
# -l <query> - this one shows pods in production AND qa environments;
# -Lrole displays value of ROLE label as a column in the output
#
$ k get pods -l 'environment in (production,qa)' -Lrole
```
- Label prefixes (`some-prefix/some-label`) are optional.  Handy for avoiding naming conflicts
- Label values can be empty
- Total length must be 64 chars or less


## Selectors
[Some Kubernetes objects, such as services and replicationcontrollers, also use label selectors to specify sets of other resources, such as pods.](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#set-references-in-api-objects)

Below is an example of a service selecting a pod by its `my-app` app label.
```
---
apiVersion: apps/v1
kind: Deployment
spec:
  template:
    metadata:
      labels:
        app: my-app
...

---
apiVersion: v1
kind: Service
spec:
  selector:
    app: my-app
```

## Annotations
Annotations, unlike labels, are NOT used for resources queries.

## matchLabels, matchExpressions
For specifying set-based requirements in selectors.  Supported by newer resources (but not by `Service`, which supports [only equality-based selectors](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#service-and-replicationcontroller)).

## Labels and Selectors in Deployment manifests
Looking at deployment yaml, there are three different `labels` sections.  Their content often tends to be similar or identical, so it's puzzling why they live in so many places.  What are their purposes?  (This explanation is inspired by a helpful [goglides.dev](https://www.goglides.dev/bkpandey/matchlabels-labels-and-selectors-in-deployment-5fk1) post).

Consider this snippet of a typical Deployment yaml:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
  labels:
    app: my-web-app
    app_env: development
  name: the-web-app
  namespace: the-web-app-ns
spec:
  selector:
    matchLabels:
      app: my-web-app
      app_env: development
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: my-web-app
        app_env: development
    spec:
      containers:
      ...
```
`labels` sections live in three places in a Deployment yaml:
1. metadata.labels
2. spec.selector.matchLabels
3. spec.template.metadata.labels

### Deployment metadata.labels
The deployment's metadata.labels support operations that want to select that deployment; for instance, querying with the `-l` flag to delete a deployment returned by the query.

### Deployment spec.selector.matchLabels
A deployment uses its `matchLabels` selector to find the pods it’s responsible for managing.  If no pods with those labels when the deployment is first created, the deployment will do nothing. But if there are, the deployment will manage those pods.

### Deployment spec.template.metadata.labels
The deployment uses the pod template, including its metadata.labels, to create pods that can be selected by a service for routing traffic.

