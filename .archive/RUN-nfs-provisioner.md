

NFS client provisioner
----------------------

```
kubectl create -f conf/nfs-client/rbac.yaml
kubectl apply  -f conf/nfs-client/deployment-arm.yaml
kubectl apply  -f conf/nfs-client/class.yaml
```

sets storage class of `managed-nfs-storage` so PVC should have an annotation that matches

#### early testing

```
kubectl create -f conf/nfs-client/test-claim.yaml -f conf/nfs-client/test-pod.yaml
```

now check server for file `SUCCESS`

```
kubectl delete -f conf/nfs-client/test-pod.yaml -f conf/nfs-client/test-claim.yaml
```

now check file `SUCCESS` is deleted

```
kubectl create -f conf/nfs-client/rbac.yaml
kubectl delete  -f conf/nfs-client/deployment-arm.yaml
kubectl delete  -f conf/nfs-client/class.yaml
```