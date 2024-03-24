# Appsmith

Appsmith is a low-code platform that enables easy and quick set-up of web applications. Use-cases can be admin tooling or general visualizations.

!!!Note "Not working in local setup"
    Appsmith does not work in local setup due to unknown reason.

## Setup

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
redis:
  enabled: false

mongodb:
  enabled: false
```

Appsmith can be installed with the following command:

```sh
# opensource edition
helm repo add stable-appsmith http://helm.appsmith.com
helm repo update
helm upgrade --install appsmith stable-appsmith/appsmith \
  --namespace appsmith --create-namespace \
  -f helm_appsmith.yaml
# below is the enterprise edition (which can also be used)
helm repo add appsmith-ee https://helm-ee.appsmith.com
helm repo update
helm upgrade --install appsmith appsmith-ee/appsmith \
  --namespace appsmith --create-namespace \
  -f helm_appsmith.yaml
```
