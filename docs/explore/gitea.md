# Gitea

Gitea is an open source git server.

## Setup

It can easily be installed using helm. Copy the following text into a file called `helm_gitea.yaml`.

```yaml
ingress:
  enabled: true
  # className: nginx
  className:
  annotations:
    cert-manager.io/cluster-issuer: "selfsigned-issuer"
    # kubernetes.io/ingress.class: nginx
    # kubernetes.io/tls-acme: "true"
  hosts:
    - host: gitea-127-0-0-1.nip.io
      paths:
        - path: /
          pathType: Prefix
  tls:
    - secretName: gitea-127-0-0-1-nip-io-tls
      hosts:
        - gitea-127-0-0-1.nip.io
gitea:
  admin:
    username: gitea_admin
    password: password123
    email: "gitea@local.domain"
postgresql-ha:
  enabled: false
postgresql:
  enabled: true
```

Execute the following command to install gitea.

```sh
helm repo add gitea-charts https://dl.gitea.com/charts/
helm repo update
helm upgrade --install --create-namespace \
-n gitea -f helm_gitea.yaml gitea gitea-charts/gitea
```

Follow the setup with the command `kubectl get pods -n gitea -w`. When all pods are in running mode, access to gitea is possible via [gitea-127-0-0-1.nip.io](https://gitea-127-0-0-1.nip.io). This requires `minikube tunnel` to be active. Use the credentials you did set in the values file above.

Using the git server with ssh (port 22) is a bit more complex and will not be covered in this tutorial. Use http connection for try outs.

## Play

You can now add gitea as another remote to the current repository you are in and push it to gitea.

## Tear down

Remove the helm chat as follows.

```sh
helm uninstall -n gitea gitea
```
