# Kubeinvaders

A deployment in Kubernetes takes care of restarting a pod in case it crashes. The tool Kubeinvaders starts a game where you can shoot at pods and watch Kubernetes restarting the pods.

## Setup

```sh
helm repo add kubeinvaders https://lucky-sideburn.github.io/helm-charts/
helm repo update
# create namespace
kubectl create namespace kubeinvaders
# install Kubeinvaders
helm install kubeinvaders --set-string config.target_namespace="easterhegg21" \
-n kubeinvaders kubeinvaders/kubeinvaders --set ingress.enabled=true --set ingress.hostName=kubeinvaders-127-0-0-1.nip.io --set deployment.image.tag=v1.9.6
```

## Play

When heading over to [https://kubeinvaders-127-0-0-1.nip.io](https://kubeinvaders-127-0-0-1.nip.io) you will be prompted with a small webinterface where you can shoot at pods.

By default only one pod is shown. You can watch the pods in the easterhegg21 namespace with `kubectl get pods -n easterhegg21 -w`. When shooting at the pod a restart will be shown.

You can scale up the easterhegg21 deployment with the command `kubectl scale deploy -n easterhegg21 --replicas=3`.

## Tear down

```sh
helm uninstall -n kubeinvaders kubeinvaders
```