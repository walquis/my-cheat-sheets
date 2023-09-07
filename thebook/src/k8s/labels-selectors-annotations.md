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
