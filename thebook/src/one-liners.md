One-liners (or few-liners) I use, but not enough to remember. 

## Docker commands
```
$ docker build --progress=plain -t tt .
$ docker run -p 3000:3000 -it tt  # run the container
$ docker run -p 3000:3000 -it tt /bin/bash  # Debug the container; overrides the Dockerfile's CMD or ENTRYPOINT directive
$ docker exec -it $(docker ps | tail -1 | rev | cut -d'\'' '\'' -f1 | rev) /bin/bash' # SSH into the first running container
```

## Kubernetes
```
# SSH to a running pod in a k8s cluster...

$ k get pods

NAME                           READY   STATUS    RESTARTS   AGE
codelabs-wl-77f89d845f-vr649   1/1     Running   0          24h

$ k get pod codelabs-wl-77f89d845f-vr649

$ k exec --stdin --tty codelabs-wl-77f89d845f-vr649 -- /bin/bash
```

## Find a process listening on a port
On a Mac, usually because I want to kill it. From [find-the-process-listening-to-port-on-mac-os-x/](https://www.btaz.com/mac-os-x/find-the-process-listening-to-port-on-mac-os-x/).
```
$ lsof -nP -iTCP -sTCP:LISTEN | grep 8080
```

## Linux date commands
```
$ date +%s
1447428499
$ date -d @1447428499  # Or on Mac, date -r 1447428499
Fri Nov 13 09:28:19 CST 2015
$ date
Fri Nov 13 09:28:35 CST 2015
$ date -d "Thu Nov 12 17:30:00 CST 2015"  # On Mac, something else
Thu Nov 12 17:30:00 CST 2015
$ date -d "Thu Nov 12 17:30:00 CST 2015" +%s
1447371000
# On Mac...
$ date -j -f "%Y-%m-%d %H:%M" "2015-11-12 17:30" +%s    
1447371052

# Or use https://www.epochconverter.com/
```
