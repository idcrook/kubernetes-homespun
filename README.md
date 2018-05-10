# kubernetes-homespun

Build a kubernetes cluster

 - Ubuntu 18.04 LTS
 - kubernetes v1.10
   - `kubeadm` for cluster setup
   - using RBAC
   - NFS for Storage
 - [kubernetes dashboard](https://github.com/kubernetes/dashboard) v1.8
 - [traefik](https://github.com/containous/traefik) v1.6 for ingress/load balancer
   - Uses Let's Encrypt for TLS certs

 Apps deployed include:

  - phant - IoT data server
  - lighttpd - static webpage server


## Get it done

  - [Blog post](http://github.crookster.org/Kubernetes-Ubuntu-18.04-Bare-Metal-Single-Host/) and [CLUSTER.md](CLUSTER.md) - setup kubernetes cluster itself; uses `kubeadm`
  - [BUILD.md](BUILD.md) - Ubuntu host setup (currently, single machine as master+node)
  - [RUN.md](RUN.md) - configure the deployments and services
