

### traefik

```
kubectl label node vader nginx-controller=traefik
kubectl get nodes -o wide --show-labels=true


cd ~/projects/kubernetes-single-node/conf/
kubectl apply -f traefik/traefik-rbac.yaml
kubectl apply -f traefik/traefik-deployment.yaml
kubectl --namespace=kube-system get pods
kubectl -n kube-system get services
curl ${IP_ADDR}:<NODEPORT>
# 404 page not found
```
