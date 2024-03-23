# Prometheus monitoring

Monitoring can be quite a complex task, as it not only requires tools and connecting them. It also requires dashboards and senseful alert thresholds. This is very time consuming and requires a lot of experience.

## Setup

For this the Kubernetes ecosystem has everything ready to go packages in the form of the "prometheus-stack" helm chart.

Copy the following text into a file called `helm_prometheus-stack.yaml`.

```yaml
prometheus:
  ingress:
    enabled: true
    hosts:
    - prometheus-127-0-0-1.nip.io
    tls:
    - hosts:
      - "prometheus-127-0-0-1.nip.io"
      secretName: prometheus-127-0-0-1-nip-io
    annotations:
      kubernetes.io/ingress.class: "nginx"
      nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
      cert-manager.io/cluster-issuer: "selfsigned-issuer"
  logLevel: debug
alertmanager:
  enabled: true
  ingress:
    enabled: true
    hosts:
    - alertmanager-127-0-0-1.nip.io
    tls:
    - hosts:
      - "alertmanager-127-0-0-1.nip.io"
      secretName: alertmanager--127-0-0-1-nip-io
    annotations:
      kubernetes.io/ingress.class: "nginx"
      nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
      cert-manager.io/cluster-issuer: "selfsigned-issuer"
grafana:
  persistence:
    enabled: true
  imageRenderer:
    enabled: true
  adminPassword: password123
  ingress:
    enabled: true
    hosts:
    - grafana-127-0-0-1.nip.de
    tls:
    - hosts:
      - "grafana-127-0-0-1.nip.io"
      secretName: grafana--127-0-0-1-nip-io
    annotations:
      kubernetes.io/ingress.class: "nginx"
      nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
      cert-manager.io/cluster-issuer: "selfsigned-issuer"
  env:
    GF_DATE_FORMATS_INTERVAL_DAY: "MMM YYYY"
```

Apply the chart with the following command:

```sh
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm upgrade --install --namespace monitoring --create-namespace \
prometheus-operator prometheus-community/kube-prometheus-stack \
-f helm_prometheus-stack.yaml
```

After some time (you can check with `kubectl get pods -n monitoring -w`) everything is up and running. You can access the following web apps:

* [grafana--127-0-0-1-nip-io](https://grafana--127-0-0-1-nip-io)
* [alertmanager-127-0-0-1.nip.io](https://alertmanager-127-0-0-1.nip.io)
* [prometheus-127-0-0-1.nip.io](https://prometheus-127-0-0-1.nip.io)

In addition to these apps a lot of dashboards have been deployed, as well as lots of Prometheus alerts.

## Play

Within the following section gives a short tour round the three mentioned tools.

### Prometheus

[Prometheus](https://prometheus.io/) is the standard monitoring tool originally developed by Soundcloud. [This video](https://www.youtube.com/watch?v=rT4fJNbfe14&t=12s) gives some nice impressions on the eco system. To get a real deep dive videos from PromCon can be watched ([2022](https://www.youtube.com/watch?v=2Wlza5jrS-U&list=PLoz-W_CUquUmVOjYTqWHX4CJ0jP61vif8),[2023](https://www.youtube.com/watch?v=QB09ztb14Pk&list=PLoz-W_CUquUnRmmz_BGgNfPNSKrazkzCR)).

In contrast to earlier days monitoring systems, Prometheus pulls metrics from so called "[exporters](https://prometheus.io/docs/instrumenting/exporters/)". You can watch the currently available exporters [on your local setup](https://prometheus-127-0-0-1.nip.io/targets?search=). Note that some exporters don't work as the setup from minikube differs from "normal" setups.

The helm chart used also configures a lot of alerts, see [your local setup](https://prometheus-127-0-0-1.nip.io/alerts?search=). Take a look at the alert `NodeCPUHighUsage`. You can click on the `expr` to open the viewer. The expression you are clicking on is so called "PromQL", [the Prometheus query language](https://prometheus.io/docs/prometheus/latest/querying/basics/). This is a dedicated query language to create monitoring expressions. Modify the query slightly to show the overall cpu usage `sum(rate(node_cpu_seconds_total{job="node-exporter", mode!="idle"}[2m])) by (instance)`. This will show you the cpu load of your node.

Copy the following into a file called `deployment_highcpu.yaml`. This will start a deployment that uses 1 cpu.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: stress-ng
  labels:
    app: stress-ng
spec:
  replicas: 1
  selector:
    matchLabels:
      app: stress-ng
  template:
    metadata:
      labels:
        app: stress-ng
    spec:
      containers:
      - name: stress-ng
        image: alexeiled/stress-ng:latest-ubuntu
        imagePullPolicy: Always
        command: ["stress-ng"]
        args:
        - "--cpu"
        - "1"
```

Apply the yaml file as follows:

```sh
# apply
kubectl apply -n default -f deployment_highcpu.yaml
# remove the pod in case something goes wrong as follows
kubectl delete deploy stress-ng -n default
```

Check the prometheus graph. A high cpu load should be shown. An alert will only be triggeres when CPU load is above 90% for longer than 15 minutes.

To get higher cpu usage scale up the deployment to start multiple containers. This can be done with the command `kubectl scale deploy stress-ng --replicas 8` to go for 8 pods. Note that by default the old deployment will be terminated AFTER all other pods are started.

You can wait these 15 minutes for the alert to trigger... or continue right away. Don't forget to clean up (see above) in case you are worried about a hot laptop.

### Grafana

Actually you can do the same (visualizing metrics) also in Grafana, but with a nicer interface. Head over to the explore section in Grafana to give it a try: [click here for deep link](https://grafana-127-0-0-1.nip.io/explore?schemaVersion=1&panes=%7B%22h9h%22:%7B%22datasource%22:%22prometheus%22,%22queries%22:%5B%7B%22refId%22:%22A%22,%22expr%22:%22sum%28rate%28node_cpu_seconds_total%7Bjob%3D%5C%22node-exporter%5C%22,%20mode%21%3D%5C%22idle%5C%22%7D%5B2m%5D%29%29%20by%20%28instance%29%22,%22range%22:true,%22instant%22:true,%22datasource%22:%7B%22type%22:%22prometheus%22,%22uid%22:%22prometheus%22%7D,%22editorMode%22:%22builder%22,%22legendFormat%22:%22__auto%22%7D%5D,%22range%22:%7B%22from%22:%22now-1h%22,%22to%22:%22now%22%7D%7D%7D&orgId=1))

Besides the explore feature the helm chart has already deployed a couple of dashboards. See [here](https://grafana-127-0-0-1.nip.io/dashboards) for a full list. See the [node overview](https://grafana-127-0-0-1.nip.io/d/adgj8llmso16od/node-exporter-nodes?orgId=1&refresh=30s) dashboard for an overview of the nodes. For minikube by default there is only one node.

To see some io load stress, edit the deployment to produce io load instead of cpu load. Replace `--cpu` with `--io` using the command `kubectl edit deploy -n default stress-ng`.

### Alertmanager

Where Grafana is mainly used for visualizing metrics, Prometheus for collecting metrics, Alertmanager is used to send alerts to various receivers.
