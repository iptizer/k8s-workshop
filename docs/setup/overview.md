# Development Setup

Exploring and trying out things hands-on is crucial to understand how things work. Thanks to modern development workflows running a Kubernetes cluster even on small computers is easily doable. This chapter focuses on setting up a local Kubernetes cluster on your Laptop.

A few tools will be mentioned. The possibilities are endless. In case you are already experienced, feel free to diverge from the recommendations. In case you want to be sure to follow allong, stick to the recommendations.

During the installation a few questions may come up. Write them down to ask them later. They should be answered in the "basics" section. The workshop will start with the development setup, so that you can already do some hands on in the basics chapter.

It is recommended to go with Linux or MacOs, but Windows should also be fine (with WSL2). Though, Windows is not tested thoroughly.

## The cluster

The following section describes the setup of a local Kubernetes cluster. This cluster will be used for learning and exploring. The actual setup for the use within a home or remote server will be explained in the chapter "homeserver".

This workshop will show how minikube can be used to start a local Kubernetes cluster. Go to the URL below and follow install instructions:

* [minikube](https://minikube.sigs.k8s.io/docs/start/)

Alternatives would be:

* [kind](https://kind.sigs.k8s.io/)
* [Rancher Desktop](https://rancherdesktop.io/)

### Setting up the cluster

Minikube supports various ["drivers"](https://minikube.sigs.k8s.io/docs/start/) where the cluster can be started. Usually the default option is the best suitable one.

Setting up the cluster may look as follows:

```sh
❯ minikube start
😄  minikube v1.32.0 on Darwin 14.3.1 (arm64)
    ▪ KUBECONFIG=/Users/uib53682/.kube/config
✨  Automatically selected the docker driver
📌  Using Docker Desktop driver with root privileges
👍  Starting control plane node minikube in cluster minikube
🚜  Pulling base image ...
💾  Downloading Kubernetes v1.28.3 preload ...
    > preloaded-images-k8s-v18-v1...:  341.16 MiB / 341.16 MiB  100.00% 25.42 M
    > gcr.io/k8s-minikube/kicbase...:  410.58 MiB / 410.58 MiB  100.00% 15.58 M
🔥  Creating docker container (CPUs=2, Memory=7887MB) ...
🐳  Preparing Kubernetes v1.28.3 on Docker 24.0.7 ...
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
🔗  Configuring bridge CNI (Container Networking Interface) ...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🔎  Verifying Kubernetes components...
🌟  Enabled addons: storage-provisioner, default-storageclass
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```

## Client tools

When a cluster is running we could already send commands to the cluster. To do so, various client tools are required. At least kubectl and helm. K9s and kubens make it easier and helm with switching between clusters.

* kubectl (equivalent to the docker cli)
    * Visit [this page](https://kubernetes.io/docs/tasks/tools/#kubectl) to install kubectl locally.
* helm (install "packages" on Kubernetes)
    * Visit [this page](https://helm.sh/docs/intro/install/#from-script) to install helm locally.
    * For those who have trust: `curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash`
* k9s (ncurse client application to quickly operate your cluster)
    * Download the binary and execute, for example from github.com.
    * Detailed instructions [here](https://k9scli.io/topics/install/).

## Verify installation

The following commands may be used to verify that everything is working as expected.

```sh
# Verify tools are installed with help commands
kuebctl -h
helm -h
k9s -h
# kubectl - query for all running containers in cluster
# (-A means: In all namespaces.)
kubectl get pods -A
# query all installed helm charts in cluster (no output expected)
helm list -A
# start k9s
k9s
# ":po" && "0" => check all pods
# ctrl +c => to close
```
