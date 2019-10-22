# nginx-ingress with metallb layer 2


## Install `nginx-ingress`

```bash
kubectl apply -f conf/nginx-ingress/mandatory.yaml
kbi get all
kbi get pods
```

`kbi` is `kubectl` alias that uses `--namespace=ingress-nginx`


## Install `metallb`

```bash
kubectl apply -f conf/metallb/metallb.yaml
kubectl apply -f conf/metallb/metallb-config.yaml
kubectl logs -l component=speaker -n metallb-system
kbb get pods
```

`kbb` is `kubectl` alias that uses `--namespace=metallb-system`

## Test a service

```bash
kubectl logs -l component=speaker -n metallb-system
# nginx port 8080
kubectl apply -f conf/metallb/nginxlb.yml
kubectl get service nginx
curl http://10.0.1.240:8080
```


## tear it all down

```shell
kubectl delete -f conf/metallb/nginxlb.yml
kubectl delete -f conf/metallb/metallb.yaml
# namespace deletion also removes this configmap
#kubectl delete -f conf/metallb/metallb-config.yaml
kubectl logs -l component=speaker -n metallb-system
kubectl delete -f conf/nginx-ingress/mandatory.yaml
```
