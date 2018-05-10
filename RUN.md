

### traefik

```
kubectl label node vader nginx-controller=traefik
kubectl get nodes -o wide --show-labels=true


cd ~/projects/kubernetes-single-node/

kubectl apply -f conf/traefik/traefik-rbac.yaml

kubectl --namespace=kube-system create configmap traefik-config \
	--from-file=conf/traefik/traefik.toml
kubectl --namespace=kube-system get cm

kubectl apply -f conf/traefik/traefik-deployment.yaml
kubectl --namespace=kube-system get pods
kubectl -n kube-system get services
curl ${IP_ADDR}:<NODEPORT>
# 404 page not found
```

DEBUG commands

```
kubectl --namespace=kube-system describe pods \
	traefik-ingress-controller-6bdcd59d4b-lcbmk
kubectl --namespace=kube-system logs \
	traefik-ingress-controller-6bdcd59d4b-lcbmk

# update after changing config file
kubectl --namespace=kube-system get configmaps
kubectl --namespace=kube-system delete configmaps traefik-config
kubectl --namespace=kube-system create configmap traefik-config \
	--from-file=conf/traefik/traefik.toml
 ```

basic config test example
```
echo "${IP_ADDR} traefik-ui.minikube" | sudo tee -a /etc/hosts
kubectl apply -f conf/traefik/ui.yaml

```
