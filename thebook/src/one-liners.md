One-liners (or few-liners) I use, but not often enough to memorize. 

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
# ZSH

## Word splitting
Unlike most other shells, zsh does not perform word splitting during variable expansion by default. Do this to make it so:
```
pkgs="aom apr-util"

for pkg in ${=pkgs}; do  # <-- Special syntax for word splitting; see "man zshexpn"
  echo "$pkg"
done

aom
apr-util
```

## Juggle filename components using zsh array
```
# Split filename by '.' into fn, a zsh array (zsh arrays start at 1, not 0)...
for i in *.JPG; do fn=("${(@s/./)i}"); convert $i -resize '600x800>'  ${fn[1]}-resized.JPG;  done

# Rename .yaml files to .yml ...
for i in *.yaml; do fn=("${(@s/./)i}"); mv $i ${fn[1]}.yml;  done

# Convert extensions from lower-case to upper-case...
for i in *.png; do fn=("${(@s/./)i}"); git mv $i ${fn[1]}.PNG;  done
```
## OR, just grab basename and extension directly
```
for i in *.JPG; do base=${i:r}; ext=${i:e}; echo magick $i -resize '600x800>' "${base}-smaller.${ext}";  done
```
