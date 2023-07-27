## gcloud

### Install gcloud
```
$ brew install --cask google-cloud-sdk            
$ gcloud components install gke-gcloud-auth-plugin
$ gcloud init
```

### Create a cluster
```
$ gcloud container clusters create my-cluster
```

Update ~/.kube/config to point kubectl at a specific cluster in GKE.
```
$ gcloud container clusters get-credentials my-cluster
```

Example of building an image tagged for pushing to my Google container registry repo
```
$ docker build -t us-east1-docker.pkg.dev/k8s-cluster-learning-393915/bg-hw-deploy-refimpl/app-image:1.2.10 .
```

### Use gcloud to set up Google container registry repo for Docker images
```
$ gcloud services enable artifactregistry.googleapis.com

# Create the repo...
$ gcloud artifacts repositories create bg-hw-deploy-refimpl --repository-format=docker --description="blue/green helloworld app" --location=us-east1

# Use gcloud to add a credential helper to your ~/.docker/config.json...
$ gcloud auth configure-docker us-east1-docker.pkg.dev

# Now you can 'docker push' the tagged image...
$ docker push us-east1-docker.pkg.dev/k8s-cluster-learning-393915/bg-hw-deploy-refimpl/app-image

```

Set up some gcloud defaults to make subsequent commands easier...
```
$ gcloud config set artifacts/location us-east1 
$ gcloud config set artifacts/repository bg-hw-deploy-refimpl

# These were already set during 'gcloud init' ...
$ gcloud config get compute/region
us-east1
$ gcloud config get compute/zone  
us-east1-b
```

Look at the pushed image...
```
$ gcloud artifacts docker images list

```

kubectl looking-around commands...
```
$ kubectl get pods
$ kubectl get namespaces
$ kubectl config current-context
```
