# MailU

MailU is a project that sets up a complete mail stack, including everything basically.

!!!Note "Requires more than just servers"
    Please note that a valid mail server setup also requires a valid domain and some DNS entries to work propperly. Meaning this try out here has limitted use, but still shows how mailu works.

# Setup

Copy and paste the following value into `helm_mailu.yaml`.

```yaml
hostnames: 
    - mail-127-0-0-1.nip.io
domain: mail-127-0-0-1.nip.io
secretKey: verysecretkey

initialAccount:
  enabled: true
  username: hans
  domain: mail-127-0-0-1.nip.io
  password: "dampf"

ingress:
  enabled: true
  annotations:
    cert-manager.io/cluster-issuer: "selfsigned-issuer"
  tls: true

redis:
  enabled: true
  architecture: standalone

front:
  resources:
    requests:
      memory: 10Mi
      cpu: 10m
    limits:
      memory: 200Mi
      cpu: 200m

roundcube:
  enabled: true
```

Execute the following commands to install mailu:

```sh
helm repo add mailu https://mailu.github.io/helm-charts/
helm repo update
helm upgrade --install mailu mailu/mailu \
--create-namespace \
-n mailu --values helm_mailu.yaml
```

The output will print details about how to reach the server and how to retrieve the password.
