# Ingress

Ingress is the solution that Kubernetes uses to route traffic "into" the cluster. It enables the functionality to make multiple web services available under one external IP address. There are various solutions available to satisfy the ingress functionality, which is basically a http reverse proxy. Most widely used is "ingress-nginx".

!!!Note "Not for local minikube"
  The setup instructions are not required in the minikube setup. For the minikube setup volumes are already made available. You can make sure with the following command `minikube addons enable ingress`. There is no experiment section in this chapter, as this was already performed in the basic section.

## Setup

Ingress is deployed in the form of a so-called ingress controller. An ingress controller will take care of transforming configured ingress Kubernetes objects into reverse proxy configurations.

To deploy the ingress controller copy and paste the following helm values into a file called `helm_ingress.yaml`.

```yaml
controller:
    hostNetwork: true
    kind: DaemonSet
    daemonset:
        useHostPort: true
    dnsPolicy: "ClusterFirstWithHostNet"
    reportNodeInternalIp: true
    config:
        proxy-body-size: "0"
        allow-snippet-annotations: true
```

Execute the following commands to install the ingress controller.

```sh
helm upgrade --install ingress-nginx ingress-nginx \
  --repo https://kubernetes.github.io/ingress-nginx \
  --namespace ingress-nginx --create-namespace
  -f helm_ingress.yaml
```

To verify the installation, check all pods are running with the command `kubectl get pods -n ingress-nginx`.
