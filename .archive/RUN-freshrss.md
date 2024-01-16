
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

