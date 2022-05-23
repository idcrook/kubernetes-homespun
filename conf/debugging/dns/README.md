deploy a debugging env for DNS inside cluster
=============================================

```
kubectl apply -f ./busybox.yaml
kubectl get pods busybox

# test
kubectl exec -ti busybox -- nslookup kubernetes.default
```

#### can't resolve 'kubernetes.default'

Errors such as the following indicate a problem with the coredns/kube-dns add-on or associated Services:

```
Server:    10.96.0.10
Address 1: 10.96.0.10

nslookup: can't resolve 'kubernetes.default'
```

debug
-----

### check local DNS

```
kubectl exec busybox cat /etc/resolv.conf
```

```
kubectl exec busybox cat /etc/resolv.conf
nameserver 10.96.0.10
search default.svc.cluster.local svc.cluster.local cluster.local
options ndots:5
```
