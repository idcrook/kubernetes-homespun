

### traefik

```
kubectl label node vader nginx-controller=traefik
kubectl get nodes -o wide --show-labels=true


cd ~/projects/kubernetes-single-node/

kubectl apply -f conf/traefik/traefik-rbac.yaml

kubectl --namespace=kube-system create configmap traefik-config \
	--from-file=conf/traefik/traefik.toml
kubectl --namespace=kube-system get cm

kubectl apply -f conf/traefik/traefik-deployment.yaml
```

DEBUG commands

```
kubectl --namespace=kube-system get pods
kubectl -n kube-system get services
IP_ADDR=$(ip addr show eno1 | grep -Po 'inet \K[\d.]+')
curl -i ${IP_ADDR}:8081
# 404 page not found

kubectl --namespace=kube-system describe pods \
	traefik-ingress-controller-6bdcd59d4b-lcbmk
kubectl --namespace=kube-system logs \
	traefik-ingress-controller-6bdcd59d4b-lcbmk

# update config file after changing
kubectl --namespace=kube-system get configmaps
kubectl --namespace=kube-system delete configmaps traefik-config
kubectl --namespace=kube-system create configmap traefik-config \
	--from-file=conf/traefik/traefik.toml
 ```


ime="2018-05-10T16:45:03Z" level=info msg="Starting provider *kubernetes.Provider {\"Watch\":true,\"Filename\":\"\",\"Constraints\":[],\"Trace\":false,\"TemplateVersion\":0,\"DebugLogGeneratedTemplate\":false,\"Endpoint\":\"\",\"Token\":\"\",\"CertAuthFilePath\":\"\",\"DisablePassHostHeaders\":false,\"EnablePassTLSCert\":false,\"Namespaces\":null,\"LabelSelector\":\"\",\"IngressClass\":\"\"}"


## TBD: monitoring

https://github.com/kubernetes/heapster/blob/master/docs/influxdb.md

Run Heapster in a Kubernetes cluster with an InfluxDB backend and a Grafana UI


## apps

### phant

```shell
kubectl create -f conf/phant/phantserver-pv.yaml
kubectl create -f conf/phant/phantserver-pvc.yaml
kubectl get pv phantserver-persistent-volume
kubectl get pvc phantserver-persistent-claim
kubectl apply -f conf/phant/phantserver-deployment.yaml
kubectl apply -f conf/phant/phantserver-service.yaml
kubectl apply -f conf/phant/phantserver-ingress-tls.yaml
kubectl get po,svc,ep,ing
```


```shell
kubectl delete -f conf/phant/phantserver-ingress-tls.yaml
kubectl delete -f conf/phant/phantserver-service.yaml
kubectl delete -f conf/phant/phantserver-deployment.yaml
kubectl get po,svc,ep,ing
```
