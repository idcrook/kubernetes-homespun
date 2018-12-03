
# Bring up applications on kubernetes

 - traefik - for ingress and load-balancer management
 - phant - IoT server
 - lighthttpd - Static webserving


# setup


## traefik

ssh to rpih3 (which will be our ingress)

```
# traefik lets encrypt
sudo mkdir -p /srv/configs/acme/
sudo touch /srv/configs/acme/acme.json
sudo chmod 600 /srv/configs/acme/acme.json
```

NFS (nfs-common) was already installed on raspbian stretch lite

```
sudo apt install -y nfs-common
```

# deploy

## traefik

run on master (via `kubectl`)
```
kubectl label node rpih3 nginx-controller=traefik
kubectl get nodes -o wide --show-labels=true
cd ~/projects/kubernetes-homespun

kubectl apply -f conf/traefik/traefik-rbac.yaml

kubectl --namespace=kube-system create configmap traefik-config \
    --from-file=conf/traefik/traefik.toml
kubectl --namespace=kube-system get cm

kubectl apply -f conf/traefik/traefik-deployment-raspi.yaml
```

#### Debugging kubernetes and traefik helpers

```
kubectl --namespace=kube-system get pods | grep traefik
kubectl --namespace=kube-system  describe pod traefik-ingress-controller
kubectl --namespace=kube-system get pods
kubectl -n kube-system get services
# IP_ADDR=$(ip addr show eth0 | grep -Po 'inet \K[\d.]+')
IP_ADDR=10.0.1.103
curl -i ${IP_ADDR}:8081
# 404 page not found / dashboard redirect

kubectl --namespace=kube-system describe pods \
    traefik-ingress-controller-
kubectl --namespace=kube-system logs \
    traefik-ingress-controller-

# update config file after changing
kubectl --namespace=kube-system get configmaps
kubectl --namespace=kube-system delete configmaps traefik-config
kubectl --namespace=kube-system create configmap traefik-config \
    --from-file=conf/traefik/traefik.toml

kubectl --namespace=kube-system get pods | grep traefik
kubectl --namespace=kube-system logs traefik-
```

```
kubectl delete -f conf/traefik/traefik-deployment-raspi.yaml
kubectl apply  -f conf/traefik/traefik-deployment-raspi.yaml
```


## phant

https://hub.docker.com/r/dpcrook/phant_server-docker/

```shell
kubectl create -f conf/phant/phantserver-pv.yaml
kubectl create -f conf/phant/phantserver-pvc.yaml
kubectl get pv phantserver-persistent-volume
kubectl get pvc phantserver-persistent-claim
kubectl apply -f conf/phant/phantserver-deployment-raspi.yaml
kubectl apply -f conf/phant/phantserver-service.yaml
kubectl apply -f conf/phant/phantserver-ingress-tls.yaml
kubectl get po,svc,ep,ing

kubectl get pods
kubectl describe pod phantserver-
```


```shell
kubectl delete -f conf/phant/phantserver-ingress-tls.yaml
kubectl delete -f conf/phant/phantserver-service.yaml
kubectl delete -f conf/phant/phantserver-deployment-raspi.yaml
kubectl get po,svc,ep,ing

# if needed
kubectl delete -f conf/phant/phantserver-pv.yaml
kubectl delete -f conf/phant/phantserver-pvc.yaml
```


## lighttpd static server

https://hub.docker.com/r/dpcrook/alpine-lighttpd-static/

``` shell
kubectl create -f conf/webstatic/lighttpd-pv.yaml
kubectl create -f conf/webstatic/lighttpd-pvc.yaml
kubectl get pv,pvc -o wide

kubectl apply -f conf/webstatic/lighttpd-deployment-raspi.yaml
kubectl apply -f conf/webstatic/lighttpd-service.yaml
kubectl apply -f  conf/webstatic/lighttpd-ingress-tls.yaml
kubectl get po,svc,ep,ing -o wide

kubectl get pods -o wide
kubectl describe pod lighttpd-

```


``` shell
kubectl delete -f  conf/webstatic/lighttpd-ingress-tls.yaml
kubectl delete -f conf/webstatic/lighttpd-service.yaml
kubectl delete -f conf/webstatic/lighttpd-deployment-raspi.yaml
kubectl get po,svc,ep,ing -o wide

kubectl delete -f conf/webstatic/lighttpd-pvc.yaml
kubectl delete -f conf/webstatic/lighttpd-pv.yaml
kubectl get pv,pvc -o wide
```



#### Debugging

Get a bash shell and look at logs

``` shell
kubectl exec lighttpd-   -it -- /bin/sh -i
cat /var/log/lighttpd/error.log
tail -f /var/log/lighttpd/access.log
# <Ctrl-C>
exit
```