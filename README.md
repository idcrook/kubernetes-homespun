kubernetes-homespun
===================

Build a kubernetes cluster for home network

-	Raspbian on Raspberry Pi Model 3 B+/4
-	kubernetes v1.16.2
	-	`kubeadm` for cluster setup
	-	"external" NFS for persistent storage
	-	"external" postgresql database
-	[traefik](https://github.com/containous/traefik) v2.x for ingress
	-	including Let's Encrypt (TLS certificates)

Apps deployed include:

-	[phant](https://hub.docker.com/r/dpcrook/phant_server-docker) - IoT data logging
-	[lighttpd](https://hub.docker.com/r/dpcrook/alpine-lighttpd-static) - Static webpage server
-	[miniflux](https://hub.docker.com/r/miniflux/miniflux) - RSS Feed aggregator and syncing
	-	uses postgres db

Hardware:

-	Raspberry Pi Model 3 B+ : kubernetes control plane node
-	One (1) Raspberry Pi Model 4 B (4GB) : kubernetes worker node
-	Raspberry Pi Model 3 B : postgresql DB (USB thumb drive for its storage)

Get it done
-----------

Refer to https://github.com/teamserverless/k8s-on-raspbian for setting up kubernetes cluster on Raspberry Pi's, including [GUIDE.md](https://github.com/teamserverless/k8s-on-raspbian/blob/master/GUIDE.md)

-	[RUN.md](RUN.md) - Configure the deployments and services

-	[Original Blog post](https://idcrook.github.io/Kubernetes-Ubuntu-18.04-Bare-Metal-Single-Host/) and [CLUSTER.md](Archive/CLUSTER.md) - setup kubernetes cluster itself; uses `kubeadm`
