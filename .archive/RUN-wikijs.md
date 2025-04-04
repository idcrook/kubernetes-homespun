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

scp wikijs/wikijs-secrets.yaml              r64-01:projects/kubernetes-homespun/conf/wikijs/
```


