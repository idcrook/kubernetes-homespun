

## traefik

```
git submodule add -b master https://github.com/containous/traefik.git
git commit -a -m "added traefik"
```

###

```
kubectl label node vader nginx-controller=traefik
kubectl get nodes -o wide --show-labels=true
```
