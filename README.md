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
