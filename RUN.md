Bring up applications on kubernetes
===================================

-	traefik - k8s ingress with Lets Encrypt support
-	phant - IoT datalogging (node.js)
-	lighthttpd - Static webserving
-	miniflux - feed reader
	-	external postgresql

setup
=====

traefik
-------

ssh to `rpif1` (which will be our ingress)

```
# traefik lets encrypt
sudo mkdir -p /srv/configs/acme/
sudo touch /srv/configs/acme/acme.json
sudo chmod 600 /srv/configs/acme/acme.json
```

NFS (nfs-common) was already installed on raspbian stretch lite/buster lite

```
sudo apt install -y nfs-common
```

deploy
======

https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets-as-environment-variables

traefik
-------

run on master (via `kubectl`\)

```
sudo apt install -y jq
# customize the DNS provider credentials (stored as secrets and passed as envariables)
cp -i conf/traefik/traefik-envariable-secret.example conf/traefik/traefik-envariable-secret.yaml
$EDITOR conf/traefik/traefik-envariable-secret.yaml

kubectl create -f conf/traefik/traefik-envariable-secret.yaml

# inspect
kubectl  get secret | grep traefik
  kubectl get secret traefik-envariable-secret -o yaml
  kubectl get secret traefik-envariable-secret -o json |\
    jq -r '.data.NAMECHEAP_API_USER' | base64 --decode

kubectl apply -f conf/traefik/traefik-crd-rbac.yaml
kubectl apply -f conf/traefik/traefik-middleware.yaml

kubectl create configmap traefik-config \
    --from-file=conf/traefik/traefik.toml
kubectl get cm

kubectl apply -f conf/traefik/traefik-service.yaml
grep v2  conf/traefik/traefik-deployment-raspi.yaml
kubectl apply -f conf/traefik/traefik-deployment-raspi.yaml
```

inplace update configmap

```
kubectl create configmap traefik-config \
    --from-file=conf/traefik/traefik.toml -o yaml --dry-run \
    | kubectl replace -f -

kubectl get cm traefik-config -o yaml
```

#### Debugging kubernetes and traefik helpers

```
kubectl get pods | grep traefik
kubectl describe pod traefik
kubectl get pods
kubectl get services

# IP_ADDR=$(ip addr show eth0 | grep -Po 'inet \K[\d.]+')
# LAN IP for nodeSelector kubernetes.io/hostname in conf/traefik/traefik-deployment-raspi.yaml

IP_ADDR=192.168.50.8
curl -i ${IP_ADDR}:80
# 404 page not found

kubectl describe pods \
    traefik-
kubectl logs \
    traefik-

# update config file after changing (see above for "in-place")
kubectl get configmaps
kubectl delete configmaps traefik-config
kubectl create configmap traefik-config \
    --from-file=conf/traefik/traefik.toml

kubectl get pods | grep traefik
kubectl logs traefik-
```

Delete deployment and restart

```
cd ~/projects/kubernetes-homespun/
kubectl delete -f conf/traefik/traefik-deployment-raspi.yaml
kubectl apply  -f conf/traefik/traefik-deployment-raspi.yaml

# kubectl delete -f conf/traefik/traefik-envariable-secret.yaml
```

phant
-----

https://hub.docker.com/r/dpcrook/phant_server-docker/

```shell
kubectl create --save-config -f conf/phant/phantserver-pv.yaml
kubectl create --save-config -f conf/phant/phantserver-pvc.yaml
kubectl get pv phantserver-persistent-volume
kubectl get pvc phantserver-persistent-claim
grep 0.1 conf/phant/phantserver-deployment-raspi.yaml
kubectl apply -f conf/phant/phantserver-deployment-raspi.yaml
kubectl apply -f conf/phant/phantserver-service.yaml
kubectl apply -f conf/phant/phantserver-ingress-tls.yaml
kubectl get po,svc,ep,ing -o wide

kubectl get pods
kubectl describe pod phantserver-
```

```shell
kubectl delete -f conf/phant/phantserver-ingress-tls.yaml
kubectl delete -f conf/phant/phantserver-service.yaml
kubectl delete -f conf/phant/phantserver-deployment-raspi.yaml
kubectl get po,svc,ep,ing

# if needed
kubectl delete -f conf/phant/phantserver-pvc.yaml
kubectl delete -f conf/phant/phantserver-pv.yaml
```

lighttpd static server
----------------------

https://hub.docker.com/r/dpcrook/alpine-lighttpd-static/

```shell
kubectl create --save-config -f conf/webstatic/lighttpd-pv.yaml
kubectl create --save-config -f conf/webstatic/lighttpd-pvc.yaml
kubectl get pv,pvc -o wide

grep 0.1 conf/webstatic/lighttpd-deployment-raspi.yaml
kubectl apply -f conf/webstatic/lighttpd-deployment-raspi.yaml

kubectl apply -f conf/webstatic/lighttpd-service.yaml
kubectl apply -f  conf/webstatic/lighttpd-ingress-tls.yaml
kubectl get po,svc,ep,ing -o wide

kubectl get pods -o wide
kubectl describe pod lighttpd-

```

```shell
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

```shell
kubectl exec lighttpd-   -it -- /bin/sh -i
cat /var/log/lighttpd/error.log
tail -f /var/log/lighttpd/access.log
# <Ctrl-C>
exit
```

postgresql (external service)
-----------------------------

run on master (via `kubectl`\)

```
cd ~/projects/kubernetes-homespun

kubectl apply -f conf/postgresql-service/postgresql-service.yaml
kubectl apply -f conf/postgresql-service/postgresql-endpoint.yaml

kubectl get svc,ep

kubectl delete -f conf/postgresql-service/postgresql-endpoint.yaml
kubectl delete -f conf/postgresql-service/postgresql-service.yaml

```

miniflux rss aggregator
-----------------------

```
cd ~/projects/kubernetes-homespun

cp -i conf/miniflux/miniflux-secret.example conf/miniflux/miniflux-secret.yaml

$EDITOR conf/miniflux/miniflux-secret.yaml

kubectl create -f conf/miniflux/miniflux-secret.yaml
kubectl apply -f conf/miniflux/miniflux-deployment-raspi.yaml
kubectl apply -f conf/miniflux/miniflux-service.yaml
kubectl apply -f conf/miniflux/miniflux-ingress-tls.yaml

# debugging
kubectl describe pod miniflux-
kubectl logs miniflux-
kubectl --namespace=kube-system logs traefik-ingress-controller-

# inspect
kubectl get secret | grep miniflux
  kubectl get secret miniflux-secret -o yaml
  kubectl get secret miniflux-secret -o json |\
    jq -r '.data.DATABASE_URL' | base64 --decode


kubectl get svc,ep,ing
kubectl get po,svc,deploy,ing,ep,secret
```

### break down miniflux

```
cd ~/projects/kubernetes-homespun

kubectl delete -f conf/miniflux/miniflux-deployment-raspi.yaml
kubectl delete -f conf/miniflux/miniflux-ingress-tls.yaml
kubectl delete -f conf/miniflux/miniflux-service.yaml
# kubectl delete -f conf/miniflux/miniflux-secret.yaml

kubectl get svc,ep
kubectl get po,svc,deploy,ing,ep,secret
```
