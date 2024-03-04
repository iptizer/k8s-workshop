# Explore

Within the last chapter basic concepts have been demonstrated and a local development environment has been set up. This chapter will introduce new concepts, new software and explorative examples that can be deployed easily in Kubernetes.

* Volumes with OpenEBS.
* Wordpress as a common example.
* Cert-manager for certificates.
* Velero as a backup tool.
* Minio as a storage solution.
* Monitoring with Prometheus.
* Gitea as a git server.
* Nextcloud.
* MailU as a mail environment.
* Phpmyadmin and postgresql databases as a common example.
* Appsmith as a fancy try-out example.

## helm

To deploy all these packages [the tool helm](https://helm.sh/docs/intro/quickstart/) is used. Helm is called "the package manager of Kubernetes". It works with a `values.yaml` file, which is used to template Kubernetes resources (other yamls).

```mermaid
graph LR;
    A[values.yaml] -->|Used by| B(Template Engine)
    B -->|Generates| C[Deployment]
    B -->|Generates| D[PVC]
    B -->|Generates| E[ConfigMap]

    style A fill:#f9f,stroke:#333,stroke-width:2px
    style B fill:#bbf,stroke:#333,stroke-width:2px
    style C fill:#dfd,stroke:#333,stroke-width:2px
    style D fill:#dfd,stroke:#333,stroke-width:2px
    style E fill:#dfd,stroke:#333,stroke-width:2px
```


