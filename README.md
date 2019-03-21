kubectl-admin create role psp:restricted \
    --verb=use \
    --resource=podsecuritypolicy \
    --resource-name=restricted

kubectl-admin create rolebinding fake-user:psp:restricted \
    --role=psp:restricted \
    --serviceaccount=psp-example:fake-user


pod=$(kubectl-admin get pods | grep qrcode | awk '{ print $1 }')

kubectl-admin get rolebindings -o json | jq -r '
  .items[] | 
  select(
    .subjects // [] | .[] | 
    [.kind,.namespace,.name] == ["ServiceAccount","kube-system","node-controller"]
  ) |
  .metadata.name'

## How to enable admission controllers plugins
https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#how-do-i-turn-on-an-admission-control-plug-in

on the master node
```
sudo vim /etc/kubernetes/manifests/kube-apiserver.yaml
```

add **PodSecurityPolicy** as plugin
```
- --enable-admission-plugins=NodeRestriction,PodSecurityPolicy
```

get list of admission controllers enabled plugins
```
kubectl describe pod kube-apiserver-master-1 -n kube-system
```