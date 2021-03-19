Bring up applications on kubernetes
===================================

`k3s` for kubernetes implementation

-	traefik - ingress with Lets Encrypt support
-	phant - IoT datalogging (node.js)
-	lighttpd - Static webserving
	-	webstatic
	-	build2020
-	miniflux - feed reader
	-	external postgresql
-	freshrss - feed reader
	-	external postgresql
-	wikijs - markdown writing and sharing
	-	external postgresql

setup
=====

cluster
-------

```shell
# install role=master without the traefik
curl -sfL https://get.k3s.io | INSTALL_K3S_CHANNEL=v1.19 INSTALL_K3S_EXEC="--disable=traefik" sh -
#  TODO: use the in-built traefik for my own config

# get info for other nodes
K3S_TOKEN=$(sudo cat /var/lib/rancher/k3s/server/node-token)
IP_ADDR=$(ip -4 addr show eth0 | grep -oP '(?<=inet\s)\d+(\.\d+){3}')
K3S_URL=https://"$IP_ADDR":6443

# run the command OUTPUT HERE on other nodes
echo curl -sfL https://get.k3s.io \| INSTALL_K3S_CHANNEL=v1.19 K3S_URL="${K3S_URL}" K3S_TOKEN="${K3S_TOKEN}"  sh -

# once cluster up, may taint the control node so things don't get scheduled onto it
kubectl taint node rpihp2 node-role.kubernetes.io/master=effect:NoSchedule
```

traefik
-------

ssh to `rpif1` (which will be our ingress)

```
# traefik lets encrypt storage
sudo mkdir -p /srv/configs/acme/
sudo touch     /srv/configs/acme/acme.json
sudo chmod 600 /srv/configs/acme/acme.json
sudo touch     /srv/configs/acme/acme-wc.json
sudo chmod 600 /srv/configs/acme/acme-wc.json
```

deploy
======

https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets-as-environment-variables

traefik
-------

run on control plane node (via `kubectl`\)

```shell
cd ~/projects/kubernetes-homespun

# customize the DNS provider credentials (stored as secrets and passed as envariables)
# cp -i conf/traefik/traefik-envariable-secrets.example.yaml \
#       conf/traefik/traefik-envariable-secrets.yaml
# $EDITOR conf/traefik/traefik-envariable-secrets.yaml

kubectl create -f conf/traefik/traefik-envariable-secrets.yaml

# inspect
kubectl  get secret | grep traefik
  kubectl get secret traefik-envariable-secrets -o yaml
  kubectl get secret traefik-envariable-secrets -o json |\
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

in-place update configmap

```shell
kubectl create configmap traefik-config \
    --from-file=conf/traefik/traefik.toml -o yaml --dry-run=client \
| kubectl replace -f -

kubectl get cm traefik-config -o yaml
```

#### Debugging kubernetes and traefik helpers

```shell
kubectl get pods | grep traefik
kubectl describe pod traefik
kubectl get pods -o wide
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

# kubectl delete -f conf/traefik/traefik-envariable-secrets.yaml
```

phant
-----

https://hub.docker.com/r/dpcrook/phant_server-docker/

```shell
cd ~/projects/kubernetes-homespun/
kubectl create --save-config -f conf/phant/phantserver-pv.yaml
kubectl create --save-config -f conf/phant/phantserver-pvc.yaml
kubectl get pv phantserver-persistent-volume
kubectl get pvc phantserver-persistent-claim
grep 0.1 conf/phant/phantserver-deployment-raspi.yaml
kubectl apply -f conf/phant/phantserver-deployment-raspi.yaml
kubectl apply -f conf/phant/phantserver-service.yaml
kubectl apply -f conf/phant/phantserver-ingress-tls.yaml
kubectl get po,svc,ep,ingressroutes -o wide

kubectl get pods -o wide
kubectl describe pod phantserver-
```

Delete

```shell
kubectl delete -f conf/phant/phantserver-ingress-tls.yaml
kubectl delete -f conf/phant/phantserver-service.yaml
kubectl delete -f conf/phant/phantserver-deployment-raspi.yaml
kubectl get po,svc,ep,ingressroutes

# if NFS server changes
kubectl delete -f conf/phant/phantserver-deployment-raspi.yaml
kubectl delete -f conf/phant/phantserver-pvc.yaml
kubectl delete -f conf/phant/phantserver-pv.yaml

kubectl create --save-config -f conf/phant/phantserver-pv.yaml
kubectl create --save-config -f conf/phant/phantserver-pvc.yaml
kubectl apply -f conf/phant/phantserver-deployment-raspi.yaml
```

lighttpd static server
----------------------

https://hub.docker.com/r/dpcrook/alpine-lighttpd-static/

### webstatic

```shell
kubectl create --save-config -f conf/webstatic/lighttpd-pv.yaml
kubectl create --save-config -f conf/webstatic/lighttpd-pvc.yaml
kubectl get pv,pvc -o wide

grep 0.1 conf/webstatic/lighttpd-deployment-raspi.yaml
kubectl apply -f conf/webstatic/lighttpd-deployment-raspi.yaml

kubectl apply -f conf/webstatic/lighttpd-service.yaml
kubectl apply -f conf/webstatic/lighttpd-ingress-tls.yaml
kubectl get po,svc,ep,ingressroutes -o wide

kubectl get pods -o wide
kubectl describe pod lighttpd-

```

Delete

```shell
kubectl delete -f  conf/webstatic/lighttpd-ingress-tls.yaml
kubectl delete -f conf/webstatic/lighttpd-service.yaml
kubectl delete -f conf/webstatic/lighttpd-deployment-raspi.yaml
kubectl get po,svc,ep,ingressroutes -o wide

# if NFS server changes
kubectl delete -f conf/webstatic/lighttpd-deployment-raspi.yaml
kubectl delete -f conf/webstatic/lighttpd-pvc.yaml
kubectl delete -f conf/webstatic/lighttpd-pv.yaml
kubectl get pv,pvc -o wide

kubectl create --save-config -f conf/webstatic/lighttpd-pv.yaml
kubectl create --save-config -f conf/webstatic/lighttpd-pvc.yaml
kubectl apply -f conf/webstatic/lighttpd-deployment-raspi.yaml
```

### build2020

```shell
kubectl create --save-config -f conf/build2020/build2020-pv.yaml
kubectl create --save-config -f conf/build2020/build2020-pvc.yaml
kubectl get pv,pvc -o wide

grep 0.1 conf/build2020/build2020-deployment-raspi.yaml
kubectl apply -f conf/build2020/build2020-deployment-raspi.yaml

kubectl apply -f conf/build2020/build2020-service.yaml
kubectl apply -f conf/build2020/build2020-ingress-tls.yaml
kubectl get po,svc,ep,ingressroutes -o wide

kubectl get pods -o wide
kubectl describe pod build2020-

# if NFS server changes, e.g.
kubectl delete -f conf/build2020/build2020-deployment-raspi.yaml
kubectl delete -f conf/build2020/build2020-pvc.yaml
kubectl delete -f conf/build2020/build2020-pv.yaml
kubectl create --save-config -f conf/build2020/build2020-pv.yaml
kubectl create --save-config -f conf/build2020/build2020-pvc.yaml
kubectl apply -f conf/build2020/build2020-deployment-raspi.yaml
```

### Debugging

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

below is run on control node (via `kubectl`\)

```shell
cd ~/projects/kubernetes-homespun

kubectl apply -f conf/postgresql-service/postgresql-service.yaml
kubectl apply -f conf/postgresql-service/postgresql-endpoint.yaml

kubectl get svc,ep
kubectl get svc,ep | grep postgres

kubectl delete -f conf/postgresql-service/postgresql-endpoint.yaml
kubectl delete -f conf/postgresql-service/postgresql-service.yaml

```

miniflux rss aggregator
-----------------------

https://hub.docker.com/r/miniflux/miniflux

```shell
cd ~/projects/kubernetes-homespun

# cp -i conf/miniflux/miniflux-secrets.example.yaml \
#       conf/miniflux/miniflux-secrets.yaml
# $EDITOR conf/miniflux/miniflux-secrets.yaml

kubectl create -f conf/miniflux/miniflux-secrets.yaml
kubectl apply -f conf/miniflux/miniflux-deployment-raspi.yaml
kubectl apply -f conf/miniflux/miniflux-service.yaml
kubectl apply -f conf/miniflux/miniflux-ingress-tls.yaml

# debugging
kubectl describe pod miniflux-
kubectl logs miniflux-
kubectl logs traefik-

# inspect
kubectl get secret | grep miniflux
  kubectl get secret miniflux-secret -o yaml
  kubectl get secret miniflux-secret -o json |\
    jq -r '.data.DATABASE_URL' | base64 --decode


kubectl get svc,ep,ingressroutes
kubectl get po,svc,deploy,ingressroutes,ep,secret
```

break down miniflux

```shell
cd ~/projects/kubernetes-homespun

kubectl delete -f conf/miniflux/miniflux-deployment-raspi.yaml
kubectl delete -f conf/miniflux/miniflux-ingress-tls.yaml
kubectl delete -f conf/miniflux/miniflux-service.yaml
# kubectl delete -f conf/miniflux/miniflux-secrets.yaml

kubectl get svc,ep
kubectl get po,svc,deploy,ing,ep,secret
```

freshrss rss aggregator
-----------------------

https://hub.docker.com/r/freshrss/freshrss

```shell
cd ~/projects/kubernetes-homespun

kubectl create --save-config -f conf/freshrss/freshrss-pv.yaml
kubectl create --save-config -f conf/freshrss/freshrss-pvc.yaml
kubectl get pv,pvc -o wide

kubectl apply -f conf/freshrss/freshrss-deployment-raspi.yaml
kubectl apply -f conf/freshrss/freshrss-service.yaml
kubectl apply -f conf/freshrss/freshrss-ingress-tls.yaml

# debugging
kubectl describe pod freshrss-
kubectl logs freshrss-
kubectl logs traefik-

# inspect
kubectl get secret | grep freshrss
  kubectl get secret freshrss-secret -o yaml
  kubectl get secret freshrss-secret -o json |\
    jq -r '.data.DATABASE_URL' | base64 --decode


kubectl get svc,ep,ingressroutes
kubectl get po,svc,deploy,ingressroutes,ep,secret
```

break down freshrss

```shell
cd ~/projects/kubernetes-homespun

kubectl delete -f conf/freshrss/freshrss-deployment-raspi.yaml
kubectl delete -f conf/freshrss/freshrss-ingress-tls.yaml
kubectl delete -f conf/freshrss/freshrss-service.yaml
kubectl delete -f  conf/freshrss/freshrss-pv.yaml
kubectl delete -f  conf/freshrss/freshrss-pvc.yaml

kubectl get svc,ep
kubectl get po,svc,deploy,ing,ep,secret
```

wikijs markdown wiki
--------------------

```shell
cd ~/projects/kubernetes-homespun

# cp -i conf/wikijs/wikijs-example.yaml \
#       conf/wikijs/wikijs-secrets.yaml
# $EDITOR conf/wikijs/wikijs-secrets.yaml

kubectl create -f conf/wikijs/wikijs-secrets.yaml

kubectl apply -f conf/wikijs/wikijs-deployment-raspi.yaml
kubectl apply -f conf/wikijs/wikijs-service.yaml
kubectl apply -f conf/wikijs/wikijs-ingress-tls.yaml

# debugging
kubectl describe pod wikijs-
kubectl logs wikijs-
kubectl logs traefik-

# inspect
kubectl get secret | grep wikijs
  kubectl get secret wikijs-secret -o json |\
    jq -r '.data.DB_PASS' | base64 --decode


kubectl get svc,ep,ingressroutes
kubectl get po,svc,deploy,ingressroutes,ep,secret
```

#### debugging

```shell
kubectl exec wikijs-   -it -- /bin/sh -i
# <Ctrl-C>
exit
```

### break down wikijs

```shell
cd ~/projects/kubernetes-homespun

kubectl delete -f conf/wikijs/wikijs-deployment-raspi.yaml
kubectl delete -f conf/wikijs/wikijs-ingress-tls.yaml
kubectl delete -f conf/wikijs/wikijs-service.yaml
# kubectl delete -f conf/wikijs/wikijs-secrets.yaml

kubectl get svc,ep
kubectl get po,svc,deploy,ing,ep,secret
```

copying secrets
---------------

on donor

```shell
cd ~/projects/kubernetes-homespun/conf

scp traefik/traefik-envariable-secrets.yaml rpif2:projects/kubernetes-homespun/conf/traefik/
scp miniflux/miniflux-secrets.yaml          rpif2:projects/kubernetes-homespun/conf/miniflux/
scp wikijs/wikijs-secrets.yaml              rpif2:projects/kubernetes-homespun/conf/wikijs/
```
