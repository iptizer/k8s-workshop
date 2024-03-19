# Appsmith

Appsmith is a low-code platform that enables easy and quick set-up of web applications. Use-cases can be admin tooling or general visualizations.

Copy the following code into a file called `helm_appsmith.yaml`.

```yaml
ingress:
  enabled: true
  annotations:
    cert-manager.io/cluster-issuer: "selfsigned-issuer"
  hosts:
  - host: appsmith-127-0-0-1.nip.io
  tls: true
  certManager: true
  certManagerTls:
    - hosts:
        - appsmith-127-0-0-1.nip.io
      secretName: appsmith-127-0-0-1-nip-io
```

Appsmith can be installed with the following command:

```sh
helm repo add stable-appsmith http://helm.appsmith.com
helm repo update
helm upgrade --install appsmith stable-appsmith/appsmith \
  --namespace appsmith --create-namespace \
  -f helm_appsmith.yaml
```
