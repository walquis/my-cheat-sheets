# My Cheat sheets
One-liners I like to remember. 

## Docker command lines
```
$ docker build --progress=plain -t tt .
$ docker run -p 3000:3000 -it tt  
$ docker run -p 3000:3000 -it tt /bin/bash
```

## Find a process listening on a port
Usually to kill it...on a Mac...from https://www.btaz.com/mac-os-x/find-the-process-listening-to-port-on-mac-os-x/
```
$ lsof -nP -iTCP -sTCP:LISTEN | grep $1
```

## Unix date commands
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
# Or use https://www.epochconverter.com/
```
