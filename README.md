kubernetes-homespun
===================

Build a kubernetes cluster for home network

-	Ubuntu on Raspberry Pi Model 4 B
-	kubernetes v1.16.3 via
	-	k3s 1.0.0
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

-	4GB Raspberry Pi 4 B : ubuntu 19.10 (arm64) : k3s control plane and worker node
-	Raspberry Pi 3 B : raspbian stretch : postgresql DB (USB thumb drive for its storage)

Get it done
-----------

-	[RUN.md](RUN.md) - Start kubernetes by install `k3s`. Configure the deployments and services

-	[Original Blog post](https://idcrook.github.io/Kubernetes-Ubuntu-18.04-Bare-Metal-Single-Host/) and [CLUSTER.md](.archive/CLUSTER.md) - setup kubernetes cluster itself; uses `kubeadm`
