# Crossplane

Crossplane is a new approach to use Kubernetes to deploy not only Kubernetes, but also AWS, GCP or Azure (and more) applications. It offers a similar things as it is possible with helm, but with a completely different approach and the ability to add cloud provider resources.

Find more information here: [https://docs.crossplane.io/](https://docs.crossplane.io/)

Find a suitable tutorial showing what is described [here](https://www.trion.de/news/2023/12/21/crossplane-tutorial.html).

## Setup

!!!warn "Running into certificate issues"
    Currently this setup is still work in progress and provider installation runs into a certificate issue.

Crossplane does not necessarily require configurations and can be installed plain. Execute as follows. See [install instructions](https://docs.crossplane.io/latest/software/install/):

```sh
helm repo add crossplane-stable https://charts.crossplane.io/stable
helm repo update
helm upgrade --install crossplane \
--namespace crossplane-system \
--create-namespace crossplane-stable/crossplane 
# install crossplane cli (for those who have trust)
#    other look here: https://raw.githubusercontent.com/crossplane/crossplane/master/install.sh
curl -sL "https://raw.githubusercontent.com/crossplane/crossplane/master/install.sh" | sh
# install the Kubernetes provider.
crossplane xpkg install provider crossplanecontrib/provider-kubernetes:v0.12.1
```

The last commands installs the crossplane Kubernetes provider. Other providers can be found [here](https://docs.crossplane.io/latest/concepts/providers/) or in the [crossplane marketplace](https://marketplace.upbound.io/providers).

To get the Kubernetes provider running, crossplane needs to be vonfigured to access the current cluster. TODO.

## Play

We can now use Crossplane to create Kubernetes resources. The use-case for this might look a bit strange in the first sight. But comnsider the fact that the cluster where the resource is applied and the cluster where it is configured is not necessarily the same. Also not that namespace is just a very easy example.

To deploy a namespace you can use the following manifest. Copy the lines to `crossplane_namespace.yaml`.

```yaml
apiVersion: kubernetes.crossplane.io/v1alpha2
kind: Object
metadata:
  name: easterhegg21
spec:
  forProvider:
    manifest:
      apiVersion: v1
      kind: Namespace
      metadata:
        # name in manifest is optional and defaults to Object name
        # name: some-other-name
        labels:
          easgterhegg: "true"
          specialApp: "true"
  providerConfigRef:
    name: easterhegg21
```