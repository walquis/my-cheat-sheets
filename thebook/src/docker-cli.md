## Docker
```
alias d=docker  # Save typing

d inspect the-image-name-or-ID | jq '.[].Config.Cmd'
d image ls
d ps
```
Managing storage:
```
d history <image>
d system df

d image prune # clears images w/o tags (dangling images)
d builder prune # clears image build cache

d manifest inspect <image>  # on a remote registry only--not local
d inspect <container> | jq '.[].Config'
```
Other stuff:
```
d commit <container> <new-image-name>  # Snapshot a running container to an image

d volume create demo1 # Persistent volumes
```

Building and running; SSH'ing into a running container
```
$ d build --progress=plain -t tt .
$ d run -p 3000:3000 -it tt  # run the container
$ d run -p 3000:3000 -it tt /bin/bash  # Debug the container; overrides the Dockerfile's CMD or ENTRYPOINT directive
$ d exec -it $(docker ps | tail -1 | rev | cut -d'\'' '\'' -f1 | rev) /bin/bash' # SSH into the first running container
```

Build and tag an image at the same time, and publish to Artifactory, with a git sha as an image tag...
```
image_tag=`git rev-parse --short HEAD`
docker_tag=<company-artifactory-url>/apprenticeship-docker-local-default/bg-hw-deploy-refimpl:$image_tag
d build -t $docker_tag .
d push $docker_tag
```

The following command helped me debug an `exec format error` issue for a pod trying to start a container from my image; it showed that my `helloworld` image was an `arm64` image.
```
d inspect helloworld | grep -i arch
```
I needed to tell Docker on my M2 mac to build for the linudx/amd64 platform, which I did using the `--platform=linux/amd64` option to the FROM directive in my Dockerfile.

