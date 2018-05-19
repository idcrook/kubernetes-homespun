
Below are notes. I have a macOS workstation.


## Install docker and kubernetes executables

```
sudo apt install docker.io
sudo systemctl enable docker

sudo apt-get update \
  && sudo apt-get install -y apt-transport-https \
  && curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
# bionic 18.04 not yet available, so use 16.04 (xenial)
echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" \
  | sudo tee -a /etc/apt/sources.list.d/kubernetes.list \
  && sudo apt-get update
sudo apt install -y kubeadm  kubelet kubernetes-cni

# turn off swap
sudo swapoff -a
sudo rm -f /swapfile
sudo vi /etc/fstab
sudo swapon --summary
cat /proc/swaps

# initialize kubernetes
IP_ADDR=$(ip addr show eno1 | grep -Po 'inet \K[\d.]+')
echo $IP_ADDR
sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --apiserver-advertise-address=${IP_ADDR} --kubernetes-version stable-1.10
```


## Setup `kubectl`

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## setup cluster with flannel

```
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/k8s-manifests/kube-flannel-rbac.yml
```

## allow single node (master) cluster

```
kubectl taint nodes --all node-role.kubernetes.io/master-
# check that it is working
kubectl get all --namespace=kube-system
kubectl get all --namespace=kube-system -o wide
```

## Run a container

```
kubectl run guids --image=alexellis2/guid-service:latest --port 9000
kubectl get pods
kubectl describe pod guids-7cdb55649f-8mfsn
kubectl describe pod guids-7cdb55649f-8mfsn | grep IP:
curl http://10.244.0.3:9000/guid ; echo
```

Inspecting container
```
kubectl logs guids-7cdb55649f-8mfsn
kubectl exec -t -i guids-7cdb55649f-8mfsn sh
/ # head -n3 /etc/os-release
/ # exit
```


### Get security token

[https://github.com/kubernetes/dashboard/wiki/Creating-sample-user](https://github.com/kubernetes/dashboard/wiki/Creating-sample-user)

Put the snippets below into files.

#### create Service Account

create Service Account with name `admin-user` in namespace `kube-system` first

`admin-user.yaml`
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kube-system
```

`kubectl create -f admin-user.yaml`



#### Create ClusterRoleBinding

`cluster-role-binding.yaml`
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kube-system
```

`kubectl create -f cluster-role-binding.yaml`

#### obtain the token

```
# from another new terminal in  macOS
ssh kubernetes-master
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
```

Helpful [https://github.com/kubernetes/dashboard/wiki/Access-control](https://github.com/kubernetes/dashboard/wiki/Access-control)


### Tunnel to host to view UI in browser

```
# from new terminal in macOS, ssh tunnel to proxy
ssh -L 8001:127.0.0.1:8001 -N kubernetes-master

# navigate to in browser on macOS:
open http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/

# enter Token found here into the dashboard dialog below
```

## Viewing the dashboard UI

[https://github.com/kubernetes/dashboard](https://github.com/kubernetes/dashboard)

### Install dashboard

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml

# run proxy
kubectl proxy
```

![kubernetes 1.10 dashboard 1.8](https://idcrook.github.io/images/kubernetes-1.10-dashboard-09-May-2018.png)
