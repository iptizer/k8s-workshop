# cert-manager

Certificates are crucial for deploying home or remote servers. With a certificate users can savely browse your website + you can be sure that your server is your server and not some malicious attacker in between. In ancient times this was either a very expensive or very complicatd procedure. Maybe even both.

Due to maturing of the Kubernetes eco system, there is now software available that makes generating certificates easy and comfortable. Together with [Let's Encrypt](https://letsencrypt.org/about/) the tool [cert-manager](https://cert-manager.io/docs/) can be used. Cert-manager is a generic certificate handling software for Kubernetes. One of its key features is generating [Let's Encrypt](https://letsencrypt.org/about/) certificates for your domains.

!!!Note "Development environment with self signed certificates."
    The procedure to get a certificate with Let's Encrypt does not work on our local system. But it is still possible to work with self-signed certificcates which works the same way as for "official certificates". This tutorial will explain and provide all information to actually deploy on a remote server.

## Setup

Copy the following yaml into a local file and call it `helm_cert-manager.yaml`.

```yaml
replicaCount: 1
webhook:
  replicaCount: 1
podLabels:
  app: cert-manager
prometheus:
  enabled: false
installCRDs: true
```

Copy the following yaml text into a local file and call it `cert-manager_issuer.yaml`. Replace `change-to-your@email.com` with your actual EMail address (only required for real deployments).

```yaml
# SelfSigned Issuer
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: selfsigned-issuer
  namespace: cert-manager
spec:
  selfSigned: {}
---
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
  namespace: cert-manager
spec:
  acme:
    # The ACME server URL
    server: https://acme-v02.api.letsencrypt.org/directory
    # Email address used for ACME registration
    email: change-to-your@email.com
    # Name of a secret used to store the ACME account private key
    privateKeySecretRef:
      name: letsencrypt-prod
    # Enable the HTTP-01 challenge provider
    solvers:
    - http01:
        ingress:
          class: nginx
---
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
  namespace: cert-manager
spec:
  acme:
    # The ACME server URL
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    # Email address used for ACME registration
    email: change-to-your@email.com
    # Name of a secret used to store the ACME account private key
    privateKeySecretRef:
      name: letsencrypt-staging
    # Enable the HTTP-01 challenge provider
    solvers:
    - http01:
        ingress:
          class:  nginx
```

To deploy cert-manager and the issuers execute the following commands.

```sh
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm upgrade --install --create-namespace cert-manager jetstack/cert-manager -n cert-manager -f ./helm_cert-manager.yaml
kubectl get pods -n cert-manager -w
# wait until all pods are running
kubectl apply -f cert-manager_issuer.yaml
# check for issuers - acme encrypters may fail, not a problem for this tutorial
kubectl get clusterissuer
```

## Experiment

With cert-manager installed it is now possible to create a certificate with applying a yaml file. But this is not even required.

!!!Note "Prerequisits"
    The next step requires the addon ingress to be installed and builds on the `deployment_service_ingress.yaml` from the basics chapter.

Inspect the shown certificate when heading over to [https://webserver-127-0-0-1.nip.io/](https://webserver-127-0-0-1.nip.io/). You need to accept the risk, which is fine in this situation. You will se a dummy certificate.

To now attach a valid certificate use the following command:

```sh
kubectl patch ingress webserver-ingress -n easterhegg21 --type=json -p='[{"op": "add", "path": "/metadata/annotations", "value": {"nginx.ingress.kubernetes.io/force-ssl-redirect": "true", "cert-manager.io/cluster-issuer": "selfsigned-issuer"}}]'
kubectl patch ingress webserver-ingress -n easterhegg21 --type=json -p='[{"op": "add", "path": "/spec/tls", "value": [{"hosts": ["webserver-127-0-0-1.nip.io"], "secretName": "webserver-127-0-0-1-nip-io-tls"}]}]'
```

You can check the changed ingress with the command ```kubectl get ingress -n easterhegg21 webserver-ingress -o yaml```, you can check the newly issued certificate with the command ```kubectl get certificate -n easterhegg21 webserver-127-0-0-1-nip-io-tls -o yaml```. Note that the actual certificate is stored as a secret and can be shown with the command ```kubectl get secret -n easterhegg21 webserver-127-0-0-1-nip-io-tls -o yaml```.

Refreshing the website [https://webserver-127-0-0-1.nip.io/](https://webserver-127-0-0-1.nip.io/) and refreshing will now show a different certificate. It is issued by the cert-manager self-signed issuer. To let this certificate sign by the "Let's encrypt" issuer, the only thing to change is the "cluster-issuer" to "letsencrypt-prod".
