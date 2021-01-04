
hedgedoc markdown sharing
--------------------------

```shell
cd ~/projects/kubernetes-homespun

#cp -i conf/hedgedoc/hedgedoc-example.yaml \
#      conf/hedgedoc/hedgedoc-secrets.yaml

$EDITOR conf/hedgedoc/hedgedoc-secrets.yaml
echo    conf/hedgedoc/hedgedoc-secrets.yaml >> .git/info/exclude

kubectl create -f conf/hedgedoc/hedgedoc-secrets.yaml

kubectl create --save-config -f conf/hedgedoc/hedgedoc-pv.yaml
kubectl create --save-config -f conf/hedgedoc/hedgedoc-pvc.yaml
kubectl get pv  hedgedoc-persistent-volume
kubectl get pvc hedgedoc-persistent-claim

kubectl apply -f conf/hedgedoc/hedgedoc-deployment-raspi.yaml
kubectl apply -f conf/hedgedoc/hedgedoc-service.yaml
kubectl apply -f conf/hedgedoc/hedgedoc-ingress-tls.yaml

# debugging
kubectl describe pod hedgedoc-
kubectl logs hedgedoc-
kubectl logs traefik-

# inspect
kubectl get secret | grep hedgedoc
  kubectl get secret hedgedoc-secret -o json |\
    jq -r '.data.CMD_DB_URL' | base64 --decode


kubectl get svc,ep,ingressroutes
kubectl get po,svc,deploy,ingressroutes,ep,secret
```

#### debugging

```shell
kubectl exec hedgedoc-   -it -- /bin/sh -i
ls /opt/hedgedoc/public
# <Ctrl-C>
exit
```

### break down hedgedoc

```shell
cd ~/projects/kubernetes-homespun

kubectl delete -f conf/hedgedoc/hedgedoc-deployment-raspi.yaml
kubectl delete -f conf/hedgedoc/hedgedoc-ingress-tls.yaml
kubectl delete -f conf/hedgedoc/hedgedoc-service.yaml
# kubectl delete -f conf/hedgedoc/hedgedoc-secrets.yaml

# if NFS server changes
kubectl delete -f conf/hedgedoc/hedgedoc-deployment-raspi.yaml
kubectl delete -f conf/hedgedoc/hedgedoc-pvc.yaml
kubectl delete -f conf/hedgedoc/hedgedoc-pv.yaml

kubectl get svc,ep
kubectl get po,svc,deploy,ing,ep,secret
```



## copying secrets

on donor

```shell
cd ~/projects/kubernetes-homespun/conf
scp hedgedoc/hedgedoc-secrets.yaml rpif2:projects/kubernetes-homespun/conf/hedgedoc/
```

on control plane node

```shell
cd ~/projects/kubernetes-homespun/
echo    conf/hedgedoc/hedgedoc-secrets.yaml >> .git/info/exclude
```