One-liners (or few-liners) I use, but not enough to remember. 

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
## Add a final newline to Python files in a repo.
VSCode has a nasty habit of saving text files w/o a final newline.  This snippet will help fix a code base suffering from this malady...
```
find tests -name '*.py' | while read f; do tail -n1 $f | read -r _ || echo >> $f; done
```
