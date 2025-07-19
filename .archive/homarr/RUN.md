
### homarr

```shell
kubectl create -f conf/homarr/homarr-secrets.yaml

kubectl create --save-config -f conf/homarr/homarr-pv.yaml
kubectl create --save-config -f conf/homarr/homarr-pvc.yaml
kubectl get pv,pvc -o wide

grep v1 conf/homarr/homarr-deployment.yaml
kubectl apply -f conf/homarr/homarr-deployment.yaml

kubectl apply -f conf/homarr/homarr-service.yaml
kubectl apply -f conf/homarr/homarr-ingress-tls.yaml
kubectl get po,svc,ep,ingressroutes -o wide

kubectl get pods -o wide
kubectl describe pod homarr-

# if NFS server changes, e.g.
kubectl delete -f conf/homarr/homarr-deployment.yaml
kubectl delete -f conf/homarr/homarr-pvc.yaml
kubectl delete -f conf/homarr/homarr-pv.yaml
kubectl create --save-config -f conf/homarr/homarr-pv.yaml
kubectl create --save-config -f conf/homarr/homarr-pvc.yaml
kubectl apply -f conf/homarr/homarr-deployment.yaml

# break down
kubectl delete -f conf/homarr/homarr-deployment.yaml
kubectl delete -f conf/homarr/homarr-service.yaml
kubectl delete -f conf/homarr/homarr-ingress-tls.yaml
kubectl delete -f conf/homarr/homarr-pvc.yaml
kubectl delete -f conf/homarr/homarr-pv.yaml
kubectl delete -f conf/homarr/homarr-secrets.yaml

```