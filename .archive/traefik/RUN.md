#### prometheus and grafana

Adapted from <https://traefik.io/blog/capture-traefik-metrics-for-apps-on-kubernetes-with-prometheus/> / <https://github.com/traefik-tech-blog/traefik-sre-metrics>

```
kubectl  apply -f conf/traefik/prometheus-rbac.yaml
kubectl  apply -f conf/traefik/prometheus-config.yaml
kubectl  apply -f conf/traefik/prometheus.yaml
```

access

```
kubectl get svc | grep prometheus
kubectl port-forward --address 127.0.0.1 service/prometheus 8080:9090
```

grafana

```
kubectl  apply -f conf/traefik/grafana-config.yaml
kubectl  apply -f conf/traefik/grafana.yaml

kubectl port-forward --address 192.168.50.17 service/grafana 3000:3000
```
