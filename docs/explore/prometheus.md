# Prometheus monitoring

Monitoring can be quite a complex task, as it not only requires tools and connecting them. It also requires dashboards and senseful alert thresholds. This is very time consuming and requires a lot of experience.

For this the Kubernetes ecosystem has everything ready to go packages in the form of the "prometheus-stack" helm chart.

Copy the following text into a file called `helm_prometheus-stack.yaml`.

```yaml
prometheus:
  ingress: 
    enabled: true
    hosts: 
    - prometheus-127-0-0-1.nip.de
    tls:
    - hosts:
      - "prometheus-127-0-0-1.nip.de"
      secretName: prometheus-127-0-0-1-nip-de
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
    - alertmanager-127-0-0-1.nip.de
    tls:
    - hosts:
      - "alertmanager-127-0-0-1.nip.de"
      secretName: alertmanager--127-0-0-1-nip-de
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
      - "grafana-127-0-0-1.nip.de"
      secretName: grafana--127-0-0-1-nip-de
    annotations:
      kubernetes.io/ingress.class: "nginx"    
      nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
      cert-manager.io/cluster-issuer: "selfsigned-issuer"
  env:
    GF_DATE_FORMATS_INTERVAL_DAY: "MMM YYYY"
```
