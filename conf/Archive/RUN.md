
# Bring up applications on kubernetes

 - traefik - for ingress and load-balancer management
 - phant - IoT server
 - lighthttpd - Static webserving



## TBD: monitoring

https://github.com/kubernetes/heapster/blob/master/docs/influxdb.md

Run Heapster in a Kubernetes cluster with an InfluxDB backend and a Grafana UI


## traefik

```
kubectl label node vader nginx-controller=traefik
kubectl get nodes -o wide --show-labels=true

cd ~/projects/kubernetes-homespun

kubectl apply -f conf/traefik/traefik-rbac.yaml

kubectl --namespace=kube-system create configmap traefik-config \
    --from-file=conf/traefik/traefik.toml
kubectl --namespace=kube-system get cm

kubectl apply -f conf/traefik/traefik-deployment.yaml
```

#### Debugging kubernetes and traefik helpers

```
kubectl --namespace=kube-system get pods
kubectl --namespace=kube-system get pods | grep traefik
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

kubectl --namespace=kube-system get pods | grep traefik
kubectl --namespace=kube-system    logs
 ```


## phant

https://hub.docker.com/r/dpcrook/phant_server-docker/

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

# if needed
kubectl delete -f conf/phant/phantserver-pvc.yaml
kubectl delete -f conf/phant/phantserver-pv.yaml
```


## lighttpd static server

https://hub.docker.com/r/dpcrook/alpine-lighttpd-static/

``` shell
kubectl create -f conf/webstatic/lighttpd-pv.yaml
kubectl create -f conf/webstatic/lighttpd-pvc.yaml
kubectl get pv,pvc -o wide

kubectl apply -f conf/webstatic/lighttpd-deployment.yaml
kubectl apply -f conf/webstatic/lighttpd-service.yaml
kubectl apply -f  conf/webstatic/lighttpd-ingress-tls.yaml
kubectl get po,svc,ep,ing -o wide
```

```shell
kubectl delete -f  conf/webstatic/lighttpd-ingress-tls.yaml
kubectl delete -f conf/webstatic/lighttpd-service.yaml
kubectl delete -f conf/webstatic/lighttpd-deployment.yaml
kubectl get po,svc,ep,ing -o wide

kubectl delete -f conf/webstatic/lighttpd-pvc.yaml
kubectl delete -f conf/webstatic/lighttpd-pv.yaml
kubectl get pv,pvc -o wide
```


## delete cluster

```
kubectl delete -f conf/traefik/traefik-deployment.yaml
kubectl get pv,pvc,po,svc,ep,ing -o wide --all-namespaces
kubectl drain vader --delete-local-data --force --ignore-daemonsets
kubectl delete node vader
sudo kubeadm reset
```

#### Debugging

Get a bash shell and look at logs

``` shell
kubectl exec lighttpd-     -it -- /bin/sh -i
cat /var/log/lighttpd/error.log
tail -f /var/log/lighttpd/access.log
# <Ctrl-C>
exit
```
