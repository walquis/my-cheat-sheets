# The Vault CLI - reading key-values

Vault does a lot of things.  This page focuses on retrieving key-value pairs from a Vault "secret".

## Get the Vault CLI
```
sudo apt install vault
```
If the install fails, you may need to add the repository to your machine.

See:
- [Vault Getting Started](https://learn.hashicorp.com/tutorials/vault/getting-started-install)
- [Announcing the Hashicorp Linux Repository](https://www.hashicorp.com/blog/announcing-the-hashicorp-linux-repository)

## Get a Vault token
Assuming that ...

1. You have Key-Values stored in a secret within a "v2 Key-Value" secrets engine on https://vault.mycompany.com (such as "ansible"), and
1. You belong to a group with access to that engine, and
1. Your group's policy lets you at least "read" and "list",
...then when you obtain authentication token, you'll be able to read those KV's.

```
$ export VAULT_ADDR=https://vault.mycompany.com
$ export VAULT_NAMESPACE=unix
$ export VAULT_TOKEN=         # Make sure VAULT_TOKEN is not set, otherwise the Vault CLI will use it instead of ~/.vault-token
$ vault login -method=ldap username=$(id -un)
Password (will be hidden):
Success! You are now authenticated. The token information displayed below
is already stored in the token helper. You do NOT need to run "vault login"
again. Future Vault requests will automatically use this token.

Key                    Value
---                    -----
token                  s.EqpuvurstSkoL8Xtnst0OKkB
token_accessor         XMueQHm6hVr77sHLubc6Q5Az
token_duration         1h
token_renewable        true
token_policies         ["default"]
identity_policies      []
policies               ["default"]
token_meta_username    cwalquist
```

## Use the token for Vault key-value retrieval
### Retrieve with curl
One approach is to curl Vault, using the token for authentication.  This request returns a JSON bag of ALL the keys in the path (along with some metadata). jq is handy for parsing out what you need (in this case, the value of haproxy_license_key).

(Note the use of the Vault CLI to spit out a curl command).

```
$ vault read -output-curl-string concourse/devops-apprentices/bg-hw-deploy-refimpl

curl -H "X-Vault-Namespace: unix/" -H "X-Vault-Token: $(vault print token)" -H "X-Vault-Request: true" https://vault.mycompany.com/v1/concourse/devops-apprentices/bg-hw-deploy-refimpl

# Add a "-s" to suppress extraneous curl output...
curl -s -H "X-Vault-Namespace: unix/" -H "X-Vault-Token: $(vault print token)" -H "X-Vault-Request: true" \
  https://vault.mycompany.com/v1/concourse/devops-apprentices/bg-hw-deploy-refimpl \
  | jq .data.k8s_server_url

"https://rancherqa.chhq.kube.mycompany.com/k8s/clusters/c-pwwrh"
```


```
# Use the Vault CLI to spit out json format directly
$ vault read -format=json concourse/devops-apprentices/bg-hw-deploy-refimpl \
  | jq .data.k8s_server_url
```

### Retrieve the key's value with the Vault CLI

```
$ vault kv get -field k8s_server_url concourse/devops-apprentices/bg-hw-deploy-refimpl
https://rancherqa.chhq.kube.mycompany.com/k8s/clusters/c-pwwrh
```


### Concourse - Retrieving secrets from Vault
In the bg-hw-deploy-refimpl Kubernetes project, the Concourse git resource has a couple of references characterized by surrounding double-parens (( ... ))):
```
...
  - name: bg-hw-deploy-refimpl-app-repo
    type: git
    icon: github
    webhook_token: ((git-app-repo.webhook_token))
    check_every: 4h
    source:
      uri: git@github.com:apprenticeship/bg-hw-deploy-refimpl-app.git
      private_key: ((git-app-repo.deploy_key))
      branch: main
...
```
These are [Concourse Vars](https://concourse-ci.org/vars.html#var-syntax), referencing secrets in Vault.

At mycompany.com/ $
Vault references are controlled by a [Concourse-cluster-wide credential manager](https://concourse-ci.org/vars.html#cluster-wide-credential-manager), which requires Concourse secrets to live somewhere under the `concourse` path in the `/unix` namespace at https://vault.mycompany.com.


More Vault info:
- [Referencing Vault Secrets with Concourse Vars](https://concourse-ci.org/vars.html#var-syntax)

For credential managers that support path-based lookup, a secret-path without a leading / may be queried relative to a predefined set of path prefixes. This is how the Vault credential manager currently works; foo will be queried under `/concourse/(team-name)/(pipeline-name)/foo`.

If you need to get hold of your cluster's CA chain, for purposes of configuring a k8s-resource in Concourse, https://technekey.com/how-to-get-kubernetes-ca-certificate/ has a one-liner ...
```
$ kubectl config view --raw -o go-template='{{index ((index (index .clusters 0) "cluster")) "certificate-authority-data"|base64decode}}'
```
