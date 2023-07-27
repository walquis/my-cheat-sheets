## Docker
```
$ docker build --progress=plain -t tt .
$ docker run -p 3000:3000 -it tt  # run the container
$ docker run -p 3000:3000 -it tt /bin/bash  # Debug the container; overrides the Dockerfile's CMD or ENTRYPOINT directive
$ docker exec -it $(docker ps | tail -1 | rev | cut -d'\'' '\'' -f1 | rev) /bin/bash' # SSH into the first running container
```

