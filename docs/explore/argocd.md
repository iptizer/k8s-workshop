# ArgoCD

ArgoCD is a tool for gitops. It works by utilizing a git repository where you defined your infrastructure (e.g. Kubernetes deployments). ArgoCD will then take care of deploying these resources in your cluster.

## Setup

To deploy argocd copy and paste the following text into a file called `helm_argocd.yaml`.

```yaml
global:
  domain: argocd-127-0-0-1.nip.io

configs:
  params:
    server.insecure: true

server:
  ingress:
    enabled: true
    ingressClassName: nginx
    annotations:
      nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
      nginx.ingress.kubernetes.io/backend-protocol: "HTTP"
      cert-manager.io/cluster-issuer: "selfsigned-issuer"
    extraTls:
      - hosts:
        - argocd-127-0-0-1.nip.io
        # Based on the ingress controller used secret might be optional
        secretName: argocd-127-0-0-1-nip-io
```

Deploy argocd with the following commands:

```sh
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
helm upgrade --install --create-namespace -f helm_argocd.yaml \
argocd --namespace argocd argo/argo-cd
```

## Play

As printed by the chart you can login with user=admin and retrieve the password as follows:

```sh
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

Use the following web URL: [https://argocd-127-0-0-1.nip.io](https://argocd-127-0-0-1.nip.io)

Delete the easterhegg21 namespace with the following command `kubectl delete ns easterheg21` and verify with `kubectl get pods -n easterhegg21` which may take some time.

To add a demo app you can use the WebUI and proceed as follows:

1. Edit the local file `./files/argocd_easterheggapp.yaml`.
2. Adjust the following parameters:
    * In case you want your own repo, fork the repository and modify the URL.
3. `kubectl apply -f ./files/argocd_easterheggapp.yaml`.
4. Watch the app being deployed: [https://argocd-127-0-0-1.nip.io](https://argocd-127-0-0-1.nip.io)
5. Access your app: [webserver-127-0-0-1.nip.io](https://webserver-127-0-0-1.nip.io)
6. As nginx itself is a bit boring you can now either modify your Kubernetes manifests and push to the main branch. Or you change the branch that ArgoCD uses. Either via the web page. Or with applying a different manifest with `kubectl apply -f ./files/argocd_easterheggapp_kuardgreen.yaml`. (Warning: The new app uses arm64 as architecture, which may be different for you.)

Watch the diff between the two branches with this link: [https://github.com/iptizer/argocd-demo/compare/kuardgreen](https://github.com/iptizer/argocd-demo/compare/kuardgreen)

## Tear down

Remove argocd & the deployed app as follows:

```sh
helm uninstall -n argocd argocd
kubectl remove namespace easterhegg21
```
