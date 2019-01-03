# kubernetes-homespun

Build a kubernetes cluster for home network

 - Raspbian on Raspberry Pi Model 3 B/B+s
 - kubernetes v1.13
   - `kubeadm` for cluster setup, now using RBAC
   - NFS for Storage
   - "external" postgresql database
 - [traefik](https://github.com/containous/traefik) v1.7.x for ingress
   - including Let's Encrypt (TLS certificates)

 Apps deployed include:

  - phant - IoT data server
  - lighttpd - Static webpage server
  - miniflux - RSS Feed aggregator and syncing
    - uses postgres db

## Get it done

Refer to https://github.com/alexellis/k8s-on-raspbian for setting up kubernetes cluster on Raspberry Pi's, including [GUIDE.md](https://github.com/alexellis/k8s-on-raspbian/blob/master/GUIDE.md)

  - [RUN-raspi.md](RUN-raspi.md) - Configure the deployments and services


### DEPRECATED - Original amd64 Ubuntu versions

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
