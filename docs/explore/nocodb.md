# nocodb

[Nocodb](https://docs.nocodb.com/) is an opensource alternative to Airtable. It is a web application, that offers spread sheet like interface to manage data.

Copy the following into a file called `helm_nocodb.yaml`.

```yaml
ingress:
  enabled: true
  port: 8080
  annotations:
    cert-manager.io/cluster-issuer: "selfsigned-issuer"
  hosts:
    - host: nocodb-127-0-0-1.nip.io
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
              name: nocodb
              port:
                number: 8080
  tls:
   - secretName: nocodb-127-0-0-1-nip-io
     hosts:
       - nocodb-127-0-0-1.nip.io
extraEnvs:
  NC_PUBLIC_URL: https:/nocodb-127-0-0-1.nip.io

extraSecretEnvs:
  NC_AUTH_JWT_SECRET: secretString
  NC_DB: "mysql2://mysql:3306?u=nocodb&p=secretPass&d=nocodb"
mysql:
  enabled: true
```

Usually helm charts are offered within a helm repository. This is not the case for nocodb. So installation is a bit more complicated. This offers the ability to look deeper into what a helm chart is. Execute the following commands to install nocodb from helm chart.

```sh
# clone nocodb repo, the chart is in a sub folder
git clone --depth 1 https://github.com/nocodb/nocodb.git
# get dependencies
helm dependency build ./nocodb/charts/nocodb
# install from local directory
helm upgrade --install --create-namespace nocodb \
--namespace nocodb ./nocodb/charts/nocodb \
-f helm_nocodb.yaml
```
