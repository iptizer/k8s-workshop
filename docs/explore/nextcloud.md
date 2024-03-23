# Nextcloud

Nextcloud is a feature rich collaboration solution.

## Setup

Copy the following text to a file called `helm_nextcloud.yaml`.

```yaml
ingress:
  enabled: true
  ingressClass: nginx
  annotations:
    spec.ingressClassName: "nginx"
    nginx.ingress.kubernetes.io/cors-allow-headers: "X-Forwarded-For"
    cert-manager.io/cluster-issuer: "selfsigned-issuer"
    nginx.ingress.kubernetes.io/proxy-body-size: 4G

  tls:
  - hosts:
    - "nextcloud-127-0-0-1.nip.io"
    secretName: nextcloud-nip-io

nextcloud:
  host: "nextcloud-127-0-0-1.nip.io"
  username: admin
  password: password123
  configs:
    proxy.config.php: |-
      <?php
      $CONFIG = array (
        'trusted_proxies' => array(
          0 => '127.0.0.1',
        ),
        'forwarded_for_headers' => array('HTTP_X_FORWARDED_FOR'),
      );
    overwrite.config.php: |-
      <?php
      $CONFIG = array(
        /**
        * When generating URLs, Nextcloud attempts to detect whether the server is
        * accessed via ``https`` or ``http``. However, if Nextcloud is behind a proxy
        * and the proxy handles the ``https`` calls, Nextcloud would not know that
        * ``ssl`` is in use, which would result in incorrect URLs being generated.
        * Valid values are ``http`` and ``https``.
        */
        'overwritehost' => 'nextcloud-127-0-0-1.nip.io',
        'overwriteprotocol' => 'https',
      );
    region.config.php: |-
     <?php
     $CONFIG = array(
      'default_phone_region' => 'DE',
     );
cronjob:
  enabled: true
internalDatabase:
  enabled: false
externalDatabase:
  host: nextcloud-mariadb
  user: nextcloud
  password: password123
mariadb:
  enabled: true
  replication:
    enabled: false
  auth:
    rootPassword: password123
    name: nextcloud
    user: nextcloud
    password: password123
  primary:
    persistence:
      enabled: true
redis:
  enabled: false
  cluster:
    enabled: false
persistence:
  enabled: true
  size: 5Gi
```

Apply the chart with the following commands:

```sh
helm repo add nextcloud https://nextcloud.github.io/helm/
helm repo update
helm upgrade --install --create-namespace nextcloud nextcloud/nextcloud -n nextcloud -f helm_nextcloud.yaml
```

Watch what happens with the command `kubectl get pods -n nextcloud -w`. When everything went well, nextcloud can be visited on [nextcloud-127-0-0-1.nip.io](https://nextcloud-127-0-0-1.nip.io). "username=admin" and "password=password123".

Now a nextcloud instance is running on the local Kubernetes cluster with a dedicated mariadb database. You can observe as follows:

```sh
❯ kubectl get all -n nextcloud
NAME                             READY   STATUS    RESTARTS   AGE
pod/nextcloud-79fc89d669-7frsj   2/2     Running   0          6m8s
pod/nextcloud-mariadb-0          1/1     Running   0          6m8s

NAME                        TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/nextcloud           ClusterIP   10.102.99.255   <none>        8080/TCP   6m8s
service/nextcloud-mariadb   ClusterIP   10.111.97.194   <none>        3306/TCP   6m8s

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nextcloud   1/1     1            1           6m8s

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/nextcloud-79fc89d669   1         1         1       6m8s

NAME                                 READY   AGE
statefulset.apps/nextcloud-mariadb   1/1     6m8s
```

## Tear down

To remove nextcloud again, execute as follows:

```sh
helm uninstall -n nextcloud nextcloud
```
