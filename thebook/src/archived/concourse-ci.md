## Install `fly`, the Concourse-CI client
```
brew install fly
```

## Authenticate the client to Concourse

`-t` is for target -- an alias to the set of credentials in `~/.flyrc` that will authenticate you to the Concourse instance you want to work with (or "target").  `~/.flyrc` can store multiple sets of creds.

Since you have to specify target with every fly command, set up an alias...
```
alias f="fly -t bg-hw-deploy-refimpl"
```

Authentication is stored in `~/.flyrc`.
```
f login --team-name devops-apprentices --concourse-url https://concourse.drw
f sync   # Update fly CLI version to match Concourse server.
```

## Creating and activating a pipeline in Concourse
```
cd concourse
f set-pipeline -c pipeline.yml -p bg-hw-deploy-refimpl
f unpause-pipeline -p bg-hw-deploy-refimpl
```

## Some other things you can do with fly
```
f targets
f status
f userinfo
f teams
f logout
f sync
f workers
f containers
```

