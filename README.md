# foo

## Steps

Edit the domain/email values in the manifests.

1. `kubectl apply -f system/manifests/namespaces.yaml`
2. `kubectl apply -f system/manifests/api-token.raw.yaml`
3. `helm install argo-cd argo/argo-cd -n argo-cd --values system/manifests/argo-cd-values.yaml`
4. `kubectl apply -f system/manifests/bootstrap.yaml`

Good to go.
