# kubernetes-homespun

Build a kubernetes cluster for home network

 - Raspbian on Raspberry Pi Model 3 B/B+s
 - kubernetes v1.14
   - `kubeadm` for cluster setup, now using RBAC
   - NFS for Storage
   - "external" postgresql database
 - [traefik](https://github.com/containous/traefik) v1.7.x for ingress
   - including Let's Encrypt (TLS certificates)

Apps deployed include:

  - [phant](https://hub.docker.com/r/dpcrook/phant_server-docker) - IoT data logging
  - [lighttpd](https://hub.docker.com/r/dpcrook/alpine-lighttpd-static) - Static webpage server
  - [miniflux](https://hub.docker.com/r/miniflux/miniflux) - RSS Feed aggregator and syncing
    - uses postgres db

Hardware:

 - Raspberry Pi Model 3 B+ : kubernetes master
   - also ssh gateway, apt-cacher-ng (USB thumb drive for its storage), gitolite
 - Two (2) Raspberry Pi Model 3 B : kubernetes workers
 - Raspberry Pi Model 3 B : postgresql DB (USB thumb drive for its storage)


## Get it done

Refer to https://github.com/alexellis/k8s-on-raspbian for setting up kubernetes cluster on Raspberry Pi's, including [GUIDE.md](https://github.com/alexellis/k8s-on-raspbian/blob/master/GUIDE.md)

  - [RUN-raspi.md](RUN-raspi.md) - Configure the deployments and services

  - [Original Blog post](https://idcrook.github.io/Kubernetes-Ubuntu-18.04-Bare-Metal-Single-Host/) and [CLUSTER.md](Archive/CLUSTER.md) - setup kubernetes cluster itself; uses `kubeadm`
