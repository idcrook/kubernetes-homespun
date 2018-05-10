# kubernetes-homespun

Build a kubernetes cluster

 - Ubuntu 18.04 LTS
 - kubernetes v1.10 (latest release)
   - `kubeadm` for cluster setup
   - RBAC
   - NFS for Storage
 - [kubernetes dashboard](https://github.com/kubernetes/dashboard) v1.8
 - [traefik](https://github.com/containous/traefik) v1.6 for ingress/load balancer
   - Uses Let's Encrypt for TLS certs


## Get it done

  - [CLUSTER.md] - setup kubernetes cluster itself
  - [BUILD.md](BUILD.md) for Ubuntu host setup (currently single machine with `kubeadm`)
  - [RUN.md](RUN.md) - configure the deployments and services
