## Docker
```
$ docker build --progress=plain -t tt .
$ docker run -p 3000:3000 -it tt  # run the container
$ docker run -p 3000:3000 -it tt /bin/bash  # Debug the container; overrides the Dockerfile's CMD or ENTRYPOINT directive
$ docker exec -it $(docker ps | tail -1 | rev | cut -d'\'' '\'' -f1 | rev) /bin/bash' # SSH into the first running container
```

Build and tag an image at the same time, and publish to Artifactory, with a git sha as an image tag...
```
image_tag=`git rev-parse --short HEAD`
docker_tag=artifacts.drwholdings.com/apprenticeship-docker-local-default/bg-hw-deploy-refimpl:$image_tag
docker build -t $docker_tag .
docker push $docker_tag
```

The following command helped me debug an `exec format error` issue for a pod trying to start a container from my image; it showed that my `helloworld` image was an `arm64` image.
```
docker inspect helloworld | grep -i arch
```
I needed to tell Docker on my M2 mac to build for the linudx/amd64 platform, which I did using the `--platform=linux/amd64` option to the FROM directive in my Dockerfile.

