# Sut

A cluster setup with Traefik, ArgoCD and Prometheus + Grafana.

## Customization

Stuff that should be changed to your values:

* [Let's Encrypt email](https://github.com/valerauko/sut/blob/28f8a21e7c486a3327328c17dd1384777c510c9a/apps/10-traefik.yaml#L27)
* [dnsProvider settings](https://github.com/valerauko/sut/blob/28f8a21e7c486a3327328c17dd1384777c510c9a/apps/10-traefik.yaml#L37-L38) and the [API token](https://go-acme.github.io/lego/dns/) secret in `system/manifests/api-token.raw.yaml`
* domain names for [Traefik](https://github.com/valerauko/sut/blob/28f8a21e7c486a3327328c17dd1384777c510c9a/apps/10-traefik.yaml#L32-L35), [ArgoCD](https://github.com/valerauko/sut/blob/28f8a21e7c486a3327328c17dd1384777c510c9a/apps/20-argo-cd.yaml#L39-L45) (same in the [manual values](https://github.com/valerauko/sut/blob/28f8a21e7c486a3327328c17dd1384777c510c9a/system/manifests/argo-cd-values.yaml#L22-L28)) and [Grafana](https://github.com/valerauko/sut/blob/28f8a21e7c486a3327328c17dd1384777c510c9a/apps/31-grafana.yaml#L23-L27)
* [your repo URL](https://github.com/valerauko/sut/blob/28f8a21e7c486a3327328c17dd1384777c510c9a/system/manifests/bootstrap.yaml#L11)

## Steps

Edit the domain/email values in the manifests.

1. `kubectl apply -f system/manifests/namespaces.yaml`
2. `kubectl apply -f system/manifests/api-token.raw.yaml`
3. `helm install argo-cd argo/argo-cd -n argo-cd --values system/manifests/argo-cd-values.yaml`
4. `kubectl apply -f system/manifests/bootstrap.yaml`

### 1. Namespaces

It just creates the necessary `argo-cd` and `monitoring` namespaces.

### 2. API token

Adds the API token secret for the hosting provider. I know, this step [should be improved](https://github.com/valerauko/sut/issues/3).

### 3. Install Argo CD

Argo can't just install itself, so first use Helm to install it (with the same values). Be sure to save the admin password (the server pod name) after it's done. The next step might re-install Argo resulting in a different pod name. [Resetting the password](https://argoproj.github.io/argo-cd/faq/#i-forgot-the-admin-password-how-do-i-reset-it) after that can be annoying.

### 4. App of apps

Applies the Argo CD app [that then in turn installs](https://argoproj.github.io/argo-cd/operator-manual/cluster-bootstrapping/) all the other stuff.
