# wordpress

Wordpress is an easy way to set up your website. Installation with Kubernetes works in minutes.

!!!Note "cert-amanger required"
    Pre-installed cert-manager is required. See previous chapter.

## Setup

Copy and paste the following lines into a file called `helm_wordpress.yaml`.

```yaml
ingress:
  enabled: true
  hostname: wordpress-127-0-0-1.nip.io
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: "self-signed"
  tls: true

certManager:
  enabled: true
```

Apply the chart with the following command:

```sh
helm upgrade --install --namespace wordpress --create-namespace \
wordpress oci://registry-1.docker.io/bitnamicharts/wordpress \
--values helm_wordpress.yaml
```

As printed by the chart the password name may be retrieved as follows. The username is `user`:

```sh
echo $(kubectl get secret --namespace wordpress wordpress -o jsonpath="{.data.wordpress-password}" | base64 -d)
```

## Teare down

To remove wordpress again execute as follows:

```sh
helm uninstall -n wordpress wordpress
```
