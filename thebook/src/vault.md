### Retrieving secrets from Vault
In the bg-hw-deploy-refimpl project, the Concourse git resource has a couple of references characterized by surrounding double-parens (( ... ))):
```
...
  - name: bg-hw-deploy-refimpl-app-repo
    type: git
    icon: github
    webhook_token: ((git-app-repo.webhook_token))
    check_every: 4h
    source:
      uri: git@git.drwholdings.com:apprenticeship/bg-hw-deploy-refimpl-app.git
      private_key: ((git-app-repo.deploy_key))
      branch: main
...
```
These are [Concourse Vars](https://concourse-ci.org/vars.html#var-syntax), referencing secrets in Vault.  Vault references are controlled by a [Concourse-cluster-wide credential manager](https://concourse-ci.org/vars.html#cluster-wide-credential-manager), which requires Concourse secrets to live somewhere under https://vault.drw, in the `/unix/concourse` namespace (there is no https:vault-uat.drw available to Concourse, unfortunately).


More Vault info:
- [Referencing Vault Secrets with Concourse Vars](https://concourse-ci.org/vars.html#var-syntax)

For credential managers that support path-based lookup, a secret-path without a leading / may be queried relative to a predefined set of path prefixes. This is how the Vault credential manager currently works; foo will be queried under `/concourse/(team-name)/(pipeline-name)/foo`.

If you need to get hold of your cluster's CA chain, for purposes of configuring a k8s-resource in Concourse, https://technekey.com/how-to-get-kubernetes-ca-certificate/ has a one-liner ...
```
$ kubectl config view --raw -o go-template='{{index ((index (index .clusters 0) "cluster")) "certificate-authority-data"|base64decode}}'
```
