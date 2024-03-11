# OpenEBS & Volumes

As of now only stateless applications have been deloyed. To store data, like for databases, a "hard disc" is required. To do so, OpenEBS is used can be used for remote servers. Intructions available below.

The setup instructions are not required in the minikube setup. For the minikube setup volumes are already made available. You can make sure with the following command `minikube addons enable storage-provisioner`. Continue to the "experiment" chapter right away.

## Setup

Copy the following test into a file called `helm_openebs.yaml`.

```yaml
apiserver:
  enabled: true
analytics:
  enabled: false

ndmOperator:
  enabled: false
ndm:
  enabled: true
snapshotOperator:
  enabled: true
webhook:
  enabled: true
```

Copy the following text into a file called `openebs_storageclass.yaml`. If required, modify the location where you want to store the volumes to something different than `/var/openebs/local`.

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  annotations:
    meta.helm.sh/release-name: openebs 
    meta.helm.sh/release-namespace: openebs
    cas.openebs.io/config: "- name: StorageType\n  value: \"hostpath\"\n- name: BasePath\n
      \ value: /var/openebs/local \n"
    openebs.io/cas-type: local
    storageclass.kubernetes.io/is-default-class: "true"
  labels:
    app.kubernetes.io/managed-by: "Helm"
  name: openebs-hostpath
provisioner: openebs.io/local
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```

To apply all this we can use the following commands:

```sh
helm repo add openebs https://openebs.github.io/charts
helm repo update
helm upgrade --install --create-namespace -f helm_openebs.yaml openebs --namespace openebs openebs/openebs
# watch pods appearing with the following command
kubectl get pods -n openebs -w 
# apply the storage class
kubectl apply -f openebs_storageclass.yaml -n openebs
```

## Experiment

Experimenting with volumes isn't that much fun without an application. Especially as volume creation is usually wrapped in a chart. Therefore the following is only to create a volume and play bit around. Continue with the "database chapter" to see volumes in action.

!!!Note "Prerequisits"
    The next step requires the addon ingress to be installed and builds on the `deployment_service_ingress.yaml` from the basics chapter.

!!!Warn "Not scalable"
    After applying the following, the webserver deployment can no longer be scaled (number of replicas) >1.

Copy the following text to a file called `pvc.yaml`.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: webserver-pvc
  namespace: easterhegg21
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

You can check the pvc exists with the following command `kubectl get pvc -n easterhegg21`. This pvc can now be attached to the webser from the beginning. Use the following command:

```sh
kubectl patch deployment webserver-deployment -n easterhegg21 --patch '{
    "spec": {
        "template": {
            "spec": {
                "volumes": [
                    {
                        "name": "webserver-volume",
                        "persistentVolumeClaim": {
                            "claimName": "webserver-pvc"
                        }
                    }
                ],
                "containers": [
                    {
                        "name": "nginx",
                        "volumeMounts": [
                            {
                                "name": "webserver-volume",
                                "mountPath": "/usr/share/nginx/html"
                            }
                        ]
                    }
                ]
            }
        }
    }
}'
```

Watch the deployment re enrolling with the following command `kubectl get pods -n easterhegg21`. It will be relatively quick and might already have happened. Now a volume is created where the webser is expecting the html files. Refreshing the website on [https://webserver-127-0-0-1.nip.io](https://webserver-127-0-0-1.nip.io) (while still having executed `minukube tunnel`) will now show an error.

To fix this we can upload an `index.html` file to the pod. Copy and paste the following text to a file called `index.html`.

```html
<!DOCTYPE html>
<html>
<head>
    <title>easterhegg21</title>
</head>
<body>
    <h1>easterhegg21</h1>
</body>
</html>
```

Kubectl can be used to copy files from local to a pod. For that the name of the pod needs to be known. This can be done with the following commands.

```sh
❯ kubectl get pods -n easterhegg21
NAME                                    READY   STATUS    RESTARTS   AGE
webserver-deployment-55fbc888c9-4wnjm   1/1     Running   0          20m
❯ kubectl cp ./index.html easterhegg21/webserver-deployment-55fbc888c9-4wnjm:/usr/share/nginx/html/index.html
```

When now refreshing the website, "easterhegg21" is shown.
