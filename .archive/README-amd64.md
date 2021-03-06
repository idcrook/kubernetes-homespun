
###  Original amd64 Ubuntu versions

Build a kubernetes cluster

 - Ubuntu 18.04 LTS
 - kubernetes v1.11
   - `kubeadm` for cluster setup
   - using RBAC
   - NFS for Storage
 - [traefik](https://github.com/containous/traefik) v1.6.x for ingress/load balancer
   - Uses Let's Encrypt for TLS certs

Optional:
  - [kubernetes dashboard](https://github.com/kubernetes/dashboard) v1.8


## Get it done

  - [Blog post](https://idcrook.github.io/Kubernetes-Ubuntu-18.04-Bare-Metal-Single-Host/) and [CLUSTER.md](CLUSTER.md) - setup kubernetes cluster itself; uses `kubeadm`
  - [BUILD.md](BUILD.md) - Ubuntu host setup (currently, single machine as master+node)
  - [RUN.md](RUN.md) - configure the deployments and services
  - [RUN-macOS.md](RUN-macOS.md) - macOS local testing only (not for Internet serving)
